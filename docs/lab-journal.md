# Lab journal

## October 30, 2026 — Building the Windows Server VM myself

Today I started my Active Directory lab. After working with Wazuh and Azure, I want to understand the Windows accounts and authentication behind the events I will investigate. My goal is to build experience I can explain in a SOC internship interview.

I wanted to do the setup myself and understand each step, so I followed instructions, checked the results, and took my own screenshots along the way.

First, I checked my Ubuntu computer's resources. It showed an i5-14400F with 16 logical CPUs, 30 GiB of RAM with about 26 GiB available, and 763 GB of free disk space. My existing Wazuh and Windows 11 VMs were shut off. That gave me enough room to plan a server, a domain client, and Wazuh running together later.

I downloaded the Windows Server 2025 evaluation ISO and created a new VM called `ad-dc01`. I gave it 4 CPUs, 6144 MiB of RAM, and an 80 GiB disk. I used the default NAT network and checked the option to customize the hardware before installing.

One thing I caught was that virt-manager initially detected Windows Server 2022. I turned off automatic detection and selected Windows Server 2025. I learned that the OS selection is a hardware preset; it does not change the installer inside the ISO.

Before starting installation, I changed the firmware from BIOS to the UEFI entry ending in `OVMF_CODE_4M.ms.fd`. Then I selected Windows Server 2025 Standard Evaluation with Desktop Experience. I chose the graphical desktop because I want to learn my way around the administration tools while also practicing commands.

Windows detected the empty 80 GB disk, and I let the installer create the partitions. After installation, I set the Administrator password privately and signed in. Seeing Server Manager open was my stopping point for this session.

The server is installed, but Active Directory is not configured yet. I also learned that naming the VM `ad-dc01` does not automatically rename Windows inside it. Next time I will start by renaming the Windows computer to `DC01`, then work through networking and time before creating the domain.

Evidence: [screenshots 01–05](../screenshots/README.md).

## October 31, 2026 — Preparing DC01 and installing the AD DS role

I renamed the Windows computer to `DC01`, restarted it, and verified the name with `hostname`. I also ran into the same practical problem as in my earlier lab: I could not copy text from my Ubuntu computer into the Windows VM.

Device Manager showed a PCI Simple Communications Controller with Code 28, meaning its driver was missing. Its hardware ID identified the VirtIO serial device. I mounted the VirtIO Windows driver ISO, installed the serial driver, and confirmed that Device Manager listed VirtIO Serial Driver without the warning. The SPICE service lookup initially returned no results, so I ran the guest-tools installer from the ISO. After that, I tested copying into the VM and it worked.

During troubleshooting, I accidentally pasted Device Manager's explanation into PowerShell. PowerShell tried to run the text as commands and returned errors. That helped me understand the difference between diagnostic text and a command I should execute. I also clarified that the Ubuntu “host” means my physical computer, while Windows Server is the guest VM.

Next, I checked `ipconfig /all`. DC01 had obtained `192.168.122.44` through DHCP. On Ubuntu, I inspected the default virtual network and found that its DHCP range covered `.2` through `.254`. Before setting a static address, I backed up the network XML and added a reservation tying `.44` to DC01's MAC address. The command confirmed that both the live network and persistent configuration were updated.

Inside Windows, I set the address to `192.168.122.44`, mask `255.255.255.0`, gateway `192.168.122.1`, and temporary DNS `192.168.122.1`. I verified that DHCP Enabled changed to No. A gateway ping returned all four replies, `nslookup microsoft.com` returned addresses, and the TCP port 443 test returned True.

I wanted to understand why this matters for Active Directory. A future domain controller needs a predictable address, and domain computers use DNS to locate domain services. I also learned how the work overlaps with different roles: server and network preparation supports the infrastructure, managing identities and access connects to IAM, and investigating suspicious account activity connects to SOC work.

Finally, I installed Active Directory Domain Services with its management tools. The result showed Success True, Restart Needed No, and the verification command showed the role as Installed. This is my stopping point: the software is installed, but I have not promoted DC01 or created a domain yet.

Evidence: screenshots 06–11. Clipboard success was confirmed by my copying test; no service-status verification or full device-warning cleanup is claimed. Shutdown and snapshots have not been verified.
