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
