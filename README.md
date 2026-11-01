# Active Directory SOC Lab

**Status: In progress — AD DS role installed; domain promotion pending.**

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

## Completed on October 31, 2026

- Renamed Windows to `DC01` and verified the hostname
- Resolved missing VirtIO serial driver and installed guest tools; host-to-VM clipboard copying now works
- Added a persistent and live libvirt DHCP reservation for DC01
- Applied and verified a static IPv4 address inside Windows
- Verified gateway reachability, DNS resolution, and outbound TCP connectivity on port 443
- Installed AD DS and its management tools; installation succeeded with no restart required

![AD DS role installation and verification](screenshots/11-ad-dc01-ad-ds-role-installed.png)

Installing the role does not create a domain or promote the server. Those steps are still pending.

## Environment

| Component | Configuration | Status |
|---|---|---|
| Ubuntu host | Intel i5-14400F, 16 logical CPUs, 30 GiB RAM; 763 GB free disk at initial check | Verified |
| `ad-dc01` | Windows Server 2025, 4 vCPUs, 6 GiB RAM, 80 GiB disk | Installed; not yet a domain controller |
| Existing Wazuh server | From the previous lab | Planned monitoring destination; not connected to this server yet |
| New Windows domain client | Planned 2 vCPUs, 6 GiB RAM, 80 GiB disk | Not created |

The VM is named `ad-dc01` in virt-manager and Windows is named `DC01`. Its static IPv4 address is `192.168.122.44/24`, with gateway and temporary DNS `192.168.122.1`. A matching DHCP reservation protects that address from reassignment. The default NAT network is shared with existing lab VMs; it is not a dedicated isolated AD network.

## Planned next steps

1. Verify updates, evaluation activation, and time synchronization
2. Plan the domain name and DNS configuration
3. Promote DC01 to a domain controller and create the lab domain with DNS
4. Join a Windows client and practice users, groups, policies, and permissions
5. Configure security auditing and Wazuh collection
6. Generate controlled authentication and account/group changes, investigate the resulting records, and document conclusions

These are planned activities, not completed detections. No domain, domain client, or AD security investigation has been completed yet.

## Documentation

- [Lab journal](docs/lab-journal.md)
- [Setup notes and resume point](docs/setup-notes.md)
- [Screenshot index](screenshots/README.md)
- [Portfolio summary](docs/portfolio-summary.md)
