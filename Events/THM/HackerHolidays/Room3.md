# Hacker Holidays room 3 - Complimentary

**Category:** Cloud (AWS Cognito / DynamoDB)

**Difficulty:** Easy/Medium

**Tools used:** Browser dev tools, AWS CLI

**Skills practiced:** reading client-side JS for cloud config, Cognito identity pools, IAM misconfiguration, DynamoDB enumeration

## Summary

A wellness app webpage silently issued AWS guest credentials via a Cognito Identity Pool. The pool's IAM permissions were misconfigured to allow a full table scan instead of restricting guests to their own record, which let me dump the entire DynamoDB table and find the flag in another guest's data.

## Objective

Figure out how the "Byte Lotus Wellness" app already knew who I was with no login, and see whether its access controls actually restricted what I could see.

## Walkthrough

1. **Recon**

I opened the target site: 

```
http://complimentary-wellness-app-332173347248.s3-website-us-east-1.amazonaws.com/
```

The site already seemed to know who I was on load, suggesting it was fetching guest-specific data automatically rather than asking me to log in.

2. **Finding the Vulnerability**

Using the browser dev tools, I opened the debugger and opened `app.js`. Comments in the code explained that every visitor automatically recieves temporary AWS guest credentials from a Cognito Identity Pool "to save wellness data." The file also hard-coded three values I'd need to replicate myself:

- The Cognito Identity Pool ID
- The AWS region
- The DynamoDB table name

3. **Exploitation**

First, I got a guest identity ID from the pool using the identity pool ID and AWS region.

```bash
aws cognito-identity get-id --identity-pool-id us-east-1:836c0949-292d-485b-b532-52d5ca7bb688 --region us-east-1
```

This returned an `IdentityId` of `us-east-1:4d571309-b084-caa7-2d45-b309404594a5` 

Now I could exchange that identity for temporary AWS credentials, the same way the website does automatically, using the returned identity and AWS region and saving those AWS credentials to a `creds.json` file.

```bash
aws cognito-identity get-credentials-for-identity --identity-id us-east-1:4d571309-b084-caa7-2d45-b309404594a5 --region us-east-1 > creds.json
```

By opening `creds.json`, I can find an `AccessKeyId`, `SecretKey`, and `SessionToken`. I exported them as environment variables so the AWS CLI would use them:

```bash
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...
```
(The variable values are redacted as they're too long to include it in here, this is just for giving the idea of what parameters are being used for AWS CLI.)

I was now authenticated to AWS as an anonymous guest. The front-end only ever showed my own profile, so I tested whether the backend actually enforced that restriction by scanning the whole table instead of querying a single item using the AWS region and DynamoDB table name.

```bash
aws dynamodb scan --table-name complimentary-GuestWellnessProfiles --region us-east-1
```

That command returned every guest's data. passwords, location, notes, guest ID, email and phone. Confirming the guest IAM role had unrestricted Scan access to the table. With the data of all guests, we can find the flag in notes of the guest `Guest VIP-042`.

**Flag:** `THM{...}` 

The flag is redacted in respect for THMs TOS (also in the screenshot).

## Root cause

The unauthenticated/guest IAM role attached to the Cognito Identity Pool was granted `dynamodb:Scan` on the entire table, rather than being scoped to read only the calling identity's own item.

## How to fix it

- Apply least privilaged IAM policies to the Cognito guest role, allow `GetItem`/`Query` only, never `Scan` on sensitive tables.

- Use a fine grained access control such as `dynamodb:LeadingKeys` conditions tied to the Cognito identity ID, so each guest can only read their own partition key.

- Avoid issuing broadly scoped AWS credentials to anonymous web visitors in the first place; proxy data access through a backend that enforces authorization on the server side.

- Don't store sensitive data (passwords, PII) in plaintext fields accessible to a guest-tier role at all.

## What I struggled with and learned

### The trouble I had
- **Trouble:** Mainly the problems I had with this room originate from the fact this is my first time working with Cognito Identity Pools and AWS, so I didn't really know how to use commands or data that is given to me.

  **The solution:** The main approach I had taken for this is watching the given room's walkthrough and completing the room beside it and learning what every command I'm running is actually doing, what results do I get and how do I use them. Mainly before doing any challenge or operation I should read before about what the challenge should be working with and is about.

### What I learned

I understood that the website's "automatic" personalization was really just it silently performing the same `get-id` -> `get-credentials-for-identity` flow in the browser was the key insight, one I saw that in `app.js`, I could just replicate the same calls myself from the terminal (logging in as a guest)
