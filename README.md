# Active Directory SOC Lab

**Status: In progress — Windows Server installed and first login completed.**

I am building this lab to understand Windows domain administration and investigate identity-related activity as I prepare for a SOC analyst internship. I perform the VM setup and lab exercises myself, using step-by-step guidance, and document what I can verify.

This project follows my [Wazuh SOC lab](https://github.com/FloresFreddyGH/wazuh-soc-lab) and [Azure security lab](https://github.com/FloresFreddyGH/azure-security-lab).

## Completed on October 30, 2026

- Checked host CPU, memory, storage, and existing VMs
- Created `ad-dc01` in virt-manager on Ubuntu using QEMU/KVM
- Allocated 4 vCPUs, 6144 MiB RAM, and an 80 GiB virtual disk
- Selected Q35 and UEFI firmware, with the default NAT network
- Installed Windows Server 2025 Standard Evaluation (Desktop Experience)
- Completed the first login and reached Server Manager

![First login and Server Manager](screenshots/05-ad-dc01-first-login.png)

## Environment

| Component | Configuration | Status |
|---|---|---|
| Ubuntu host | Intel i5-14400F, 16 logical CPUs, 30 GiB RAM; 763 GB free disk at initial check | Verified |
| `ad-dc01` | Windows Server 2025, 4 vCPUs, 6 GiB RAM, 80 GiB disk | Installed; not yet a domain controller |
| Existing Wazuh server | From the previous lab | Planned monitoring destination; not connected to this server yet |
| New Windows domain client | Planned 2 vCPUs, 6 GiB RAM, 80 GiB disk | Not created |

The name `ad-dc01` currently identifies the VM in virt-manager. Renaming the Windows computer to `DC01` is the next step. The default NAT network is shared with existing lab VMs; it is not a dedicated isolated AD network.

## Planned next steps

1. Rename the Windows computer, verify updates, activation, networking, and time
2. Plan a stable server IP without conflicting with DHCP
3. Install Active Directory Domain Services and DNS, then create the lab domain
4. Join a Windows client and practice users, groups, policies, and permissions
5. Configure security auditing and Wazuh collection
6. Generate controlled authentication and account/group changes, investigate the resulting records, and document conclusions

These are planned activities, not completed detections. No domain, domain client, or AD security investigation has been completed yet.

## Documentation

- [Lab journal](docs/lab-journal.md)
- [Setup notes and resume point](docs/setup-notes.md)
- [Screenshot index](screenshots/README.md)
- [Portfolio summary](docs/portfolio-summary.md)
