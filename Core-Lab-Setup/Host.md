# Proxmox host machine

The specifications of the host machine 

## Machine specs
- OS: Proxmox VE 9.2.4
- CPU: Intel i5 10400
- RAM: 16GB DDR4  
- Storage:
    1. `local`: 100GB SSD partition used for storing ISO images
    2. `local-lvm`: 348GB SSD partition used for storing VMs
    3. `storage`: 1TB HDD partition used for storing backups and larger files.

## Pools
I use pools for managing permissions of VMs and organizing those VMs

- Cybersecurity-practice-lab (has all of my lab's VM) (Will expand it so there will be a pool for Windows and advanced pentesting and another for Web hacking)
