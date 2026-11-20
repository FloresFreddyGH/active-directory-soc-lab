# Portfolio summary

**Title:** Active Directory SOC Lab

**Status:** In progress — domain infrastructure, client domain join, and first employee sign-in verified

I built a Windows Server 2025 domain controller and Windows 11 Enterprise Evaluation client on Ubuntu/QEMU/KVM. I created soclab.test with DNS, verified domain services and discovery records, organized OUs, and created employee accounts and a security group.

I troubleshot guest clipboard integration and a stopped/unsynchronized Windows Time service, configured client DNS, and joined CLIENT01 to the domain. I verified domain membership separately from the local sign-in identity, then created my Freddy Flores domain account, added it to GG_Lab_Employees, completed its required first password change, and confirmed soclab\freddy.flores was signed in on CLIENT01.

The project demonstrates Windows domain administration, DNS troubleshooting, time synchronization checks, account provisioning, group membership management, and evidence-based documentation. It is the foundation for future SOC investigations; auditing configuration, Wazuh collection, policy exercises, and detection results are not yet completed.

Evidence: screenshots 01–36 and journal entries through November 19, 2026. The Lamine Yamal account is a fictional test identity. This project is separate from my completed Wazuh and Azure labs.

Suggested website milestone: **Joined a Windows 11 client to an Active Directory domain and verified a first domain-user sign-in after troubleshooting DNS, guest integration, and time synchronization.**
