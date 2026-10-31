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

## Confirmed stopping point

First login completed and Server Manager opened. Screenshot 05 includes its Windows Admin Center/Azure Arc introductory popup; this does not mean Azure Arc was configured.

## Resume here

Rename the Windows computer to `DC01` and verify it after restarting. Then check updates, evaluation activation, current network settings, and time synchronization. Plan the fixed IP against the host's DHCP configuration before changing it. Do not assume the old Windows endpoint's IP belongs to this VM.

AD DS installation, DNS/domain setup, client domain join, auditing, and Wazuh integration are still pending. No VM shutdown or snapshot is claimed by these notes.
