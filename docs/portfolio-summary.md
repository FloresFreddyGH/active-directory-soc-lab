# Portfolio summary

**Title:** Active Directory SOC Lab

**Status:** In progress — AD DS role installed; domain promotion pending

I am building an Active Directory lab on Ubuntu with QEMU/KVM to practice Windows domain administration and eventually investigate authentication and account changes in Wazuh. I configured and installed a Windows Server 2025 VM, renamed it DC01, resolved a VirtIO/guest-tools clipboard issue, and configured a static IP backed by a libvirt DHCP reservation. I verified gateway reachability, DNS resolution, and outbound TCP connectivity before installing Active Directory Domain Services and its management tools.

This stage demonstrates server preparation, basic network troubleshooting, and documenting verified results. DC01 has not yet been promoted to a domain controller. Domain creation, a joined client, security auditing, and Wazuh investigations remain planned work.

Evidence: screenshots 01–11 and the October 30–31 journal entries. This is a separate project from my completed Wazuh and Azure labs.
