# Lab network

# Bridges and devices

- `vmbr0`: Home network | used for internet access | DHCP
- `vmbr1`: Isolated lab network | used for connecting machines in the lab together without touching my home network | 192.168.100.0/24

## IP assignments
IP assignments for machines connected under vmbr1

|Host  |IP               |Role      |
|------|-----------------|----------|
|Kali  |192.168.100.50   |Attacker  |
|DC01  |192.168.100.10   |Domain Controller |
|WS01  |192.168.100.20   |Workstation|


## Design decisions
- Lab VMs have no internet access, for being safer and prevent outside intrusion.
- Kali has two network devices so it can use the internet for downloading tools and still be connected to the lab.
- All machines have static IPs for consistancy.
