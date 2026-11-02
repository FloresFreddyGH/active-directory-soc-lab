# Portfolio summary

**Title:** Active Directory SOC Lab

**Status:** In progress — domain created and verified; client installed, domain join pending

I built a Windows Server 2025 domain controller on Ubuntu/QEMU/KVM and created the soclab.test domain with DNS. I verified domain authentication for the Administrator account, AD services, DNS host and service records, SYSVOL/NETLOGON shares, and external name resolution. I organized OUs, corrected a misplaced OU after troubleshooting deletion protection, created a fictional employee account, and verified security-group membership.

I also installed a separate Windows 11 Enterprise Evaluation client with UEFI and an emulated TPM 2.0, reaching the desktop as a local user. Earlier work included driver/clipboard troubleshooting, a static server IP, and a matching libvirt DHCP reservation.

This project develops the identity and Windows administration foundation for future SOC investigations. Client domain join, employee domain sign-in, audit configuration, Wazuh collection, and detection exercises remain pending. Celebrity-inspired account names are fictional lab identities, not affiliations.

Evidence: screenshots 01–29 and the October 30, October 31, and November 1 journal entries. This is separate from my completed Wazuh and Azure labs.
