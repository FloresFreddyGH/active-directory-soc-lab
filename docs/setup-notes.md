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

`ipconfig /all` confirmed DHCP Enabled No. IPv6 remained enabled. DNS will need to be configured for the AD domain during the next stage.

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

## Resume here

Check updates, evaluation activation, and time synchronization. Then choose the lab domain name, plan DNS, and promote DC01 to create the domain. The current role installation alone does not make DC01 a domain controller.

Domain creation, DNS configuration for AD, client domain join, auditing, and Wazuh integration remain pending. No VM shutdown or snapshot is claimed by these notes.
