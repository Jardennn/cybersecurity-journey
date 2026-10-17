# Kali Linux attacker VM

A VM running Kali Linux, serves as an attacker machine and is the main attacker machine for all homelab operations.

## Machine specs

- OS: Kali Linux
- Proxmox VM ID: 101
- RAM: 3.50GB | Disk: 100GB | CPUs: 2
- NICs: vmbr0 (internet connection, DHCP) | vmbr1 (lab, 192.168.100.50)

## Purpose
Primary attacker machine for all homelab exercises and online labs.
Not my daily driver (obviously) only used for security practice.

## Key paths

## Installed tools (beyond kali prebuilts)

## Network config

### Connections:
- `Default`: using eth0 | on DHCP | used for connecting to the internet
- `Lab`: using eth1 | on IP `192.168.100.50`, netmask `255.255.255.0` | used for connecting to the homelab

## Wordlist installed

## Rebuild checklist

## Notes
