# Portfolio summary

**Title:** Active Directory SOC Lab

**Status:** In progress — domain infrastructure and workstation authentication monitoring verified

I built a Windows Server 2025 domain controller and Windows 11 client on Ubuntu/QEMU/KVM, configured soclab.test, created users and groups, and joined CLIENT01. I moved the client into a workstation OU and verified an auditing GPO with gpresult and auditpol.

I connected CLIENT01 to Wazuh and investigated controlled failed and successful domain-user logons. Windows events 4625 and 4624 appeared as Wazuh rules 60122 and 60118. I verified target identities, initiating accounts, source addresses, timestamps, and logon types, then preserved JSON evidence and a CSV summary.

Troubleshooting included VM identity confusion, guest clipboard integration, Windows Time, password-change requirements, dashboard access, and search time ranges. Domain-controller collection and account/group-change exercises remain pending.

See [the investigation](authentication-investigation.md) for evidence and limitations. Lab account names do not imply actual affiliations. This is a guided hands-on lab, with AI assistance for instructions and documentation.
