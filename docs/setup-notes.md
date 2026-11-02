# Setup notes

## Host checks

Run on the Ubuntu host:

```bash
lscpu | grep -E 'Model name|CPU\(s\)|Core|Thread|Virtualization:'
free -h
df -h /
virsh -c qemu:///system list --all
```

These read-only checks show CPU/virtualization support, available memory, disk space, and VM state.

## VM configuration recorded

- VM name: `ad-dc01`
- Hypervisor: QEMU/KVM; architecture: x86_64
- Chipset: Q35
- Firmware selected: `/usr/share/OVMF/OVMF_CODE_4M.ms.fd`
- Memory: 6144 MiB; virtual CPUs: 4
- Virtual disk: 80 GiB, SATA
- Network: default NAT
- OS: Windows Server 2025 Standard Evaluation (Desktop Experience)
- Installation source: [Microsoft Evaluation Center](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2025)

The selected firmware is the Microsoft-key UEFI variant. Secure Boot status inside Windows has not yet been independently verified.

## October 31 configuration and checks

Windows hostname: `DC01` (verified after renaming and restarting).

Clipboard troubleshooting: mounted `virtio-win-0.1.302`, installed the missing serial driver from `D:\vioserial`, then ran `virtio-win-guest-tools` from the disc. Copying from Ubuntu into Windows worked afterward. Driver source: [Fedora VirtIO Windows downloads](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/).

### Address reservation on Ubuntu

Inspected the network and current leases before making changes:

```bash
virsh -c qemu:///system net-dumpxml default
virsh -c qemu:///system net-dhcp-leases default
virsh -c qemu:///system net-dumpxml default > ~/default-network-before-dc01.xml
virsh -c qemu:///system net-update default add-last ip-dhcp-host "<host mac='52:54:00:23:0e:23' name='DC01' ip='192.168.122.44'/>" --live --config
virsh -c qemu:///system net-dumpxml default --inactive
```

The default DHCP range remains `192.168.122.2`–`192.168.122.254`. The reservation maps DC01's MAC to `.44`. These commands record a completed change; do not rerun the add command blindly.

### Windows static IPv4 settings

Opened `ncpa.cpl`, then Ethernet Properties → Internet Protocol Version 4 Properties.

| Setting | Value |
|---|---|
| IP | `192.168.122.44` |
| Subnet mask | `255.255.255.0` |
| Gateway | `192.168.122.1` |
| Preferred DNS (temporary) | `192.168.122.1` |
| Alternate DNS | Blank |

`ipconfig /all` confirmed DHCP Enabled No. IPv6 remained enabled. These were the pre-promotion settings; see the November 1 results below for the current DNS configuration.

```powershell
ipconfig /all
ping 192.168.122.1
nslookup microsoft.com
Test-NetConnection microsoft.com -Port 443
```

Results: four ping replies with no loss, successful DNS lookup, and `TcpTestSucceeded : True`. The TCP check verifies connectivity to that port, not a complete HTTPS transaction.

### AD DS role installation

Ran in elevated Windows PowerShell:

```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
Get-WindowsFeature AD-Domain-Services
```

Installation returned Success True, Restart Needed No, Exit Code Success. Verification showed `[X] Active Directory Domain Services` and Installed.

Reference: [Microsoft AD DS installation guidance](https://learn.microsoft.com/windows-server/identity/ad-ds/deploy/install-active-directory-domain-services--level-100-).

## November 1 domain configuration

- Domain/forest: `soclab.test`; NetBIOS: `SOCLAB`
- Domain controller: `DC01.soclab.test`; both functional levels: Windows Server 2025
- DNS and Global Catalog enabled; no DNS delegation
- Database/logs: `C:\Windows\NTDS`; SYSVOL: `C:\Windows\SYSVOL`
- Time service reported time.windows.com with a successful sync before promotion
- Time zone changed with `Set-TimeZone -Id "Eastern Standard Time"`
- Windows Update displayed up to date; evaluation activation displayed Active
- Post-promotion DNS client: `::1`, `127.0.0.1`; IPv4 remains `192.168.122.44`

### Verification commands performed

```powershell
whoami
Get-ADDomain | Select-Object DNSRoot, NetBIOSName, DomainMode, PDCEmulator
Get-Service NTDS,DNS,Netlogon
Get-NetIPConfiguration
Resolve-DnsName DC01.soclab.test -Server 192.168.122.44
Resolve-DnsName -Type SRV _ldap._tcp.dc._msdcs.soclab.test -Server 192.168.122.44
Get-SmbShare -Name SYSVOL,NETLOGON
Resolve-DnsName microsoft.com
```

Results confirmed domain Administrator sign-in, domain configuration, all three services running, DC01's IPv4 record, its LDAP SRV target, both shares, and external DNS resolution. The hostname lookup also returned a link-local IPv6 record. Client-side connectivity has not yet been tested.

### Directory organization

```text
soclab.test
├── Domain Controllers
│   └── DC01
└── SOCLab
    ├── LabUsers
    │   └── Lamine Yamal
    ├── Workstations
    └── Groups
        └── GG_Lab_Employees
```

Lamine is a fictional lab account. Initial Domain Users membership and subsequent GG_Lab_Employees membership were verified in the GUI. Global/Security was instructed for the new group; verify its General tab if collecting evidence of group scope/type. The member screenshot alone does not prove those attributes or effective resource access.

The Workstations OU was moved out of LabUsers after an Access denied error. Instructions included disabling protection only on Workstations and restoring it afterward; verify the restored checkbox next session.

### Client installation

- VM: `ad-client01`; intended Windows hostname: `CLIENT01` (not yet verified)
- Windows 11 Enterprise Evaluation; desktop reached as local `labadmin`
- 2 vCPUs, 6144 MiB RAM, 80 GiB SATA virtual disk
- Q35, UEFI entry ending `OVMF_CODE_4M.ms.fd`, emulated CRB TPM 2.0
- Default NAT network, shared with DC01
- Installer selected from the existing CLIENTENTERPRISEEVAL ISO in Downloads
- Guest Secure Boot/TPM status, activation, and installed OS build not independently queried

## Resume here

1. Start DC01 first, then ad-client01. Verify the client's Windows hostname, network configuration, time zone, and clock synchronization.
2. Rename the client to CLIENT01 if necessary. Check updates, evaluation activation, and guest tools/clipboard.
3. Configure the client's DNS to use `192.168.122.44`, verify domain discovery, and join `soclab.test`.
4. Move the client object into SOCLab/Workstations. Verify the user's logon name and password-change requirement before testing the first domain sign-in.
5. Verify restored Workstations OU deletion protection and plan auditing/Wazuh collection before detection exercises.

No client domain join, shutdown, snapshot, policy deployment, or Wazuh integration is claimed by these notes. The November 1 documentation stops at the client desktop.
