# ESXi host and storage

I installed ESXi 6.7.0, build `8169922`, as `esxi-est01.lab.local` inside VMware Workstation Pro. The nested host provides compute and VMFS storage for the lab and runs the vCenter Server Appliance (VCSA).

## VM hardware

I configured the ESXi VM with one virtual socket and four cores, 20 GB of RAM, a virtual network adapter on `VMnet2`, and hardware-assisted virtualization enabled. The initial Workstation settings also list a 20 GB SCSI virtual disk. The vSphere Client later reported four logical processors, 20 GB of memory capacity, and one host NIC. [Workstation VM settings](../evidence/foundation/esxi-vm-resources.png) · [ESXi host summary](../evidence/vcenter/esxi-host-connected.png)

## Management network

The workstation, ESXi VM, and domain controller share the host-only `VMnet2` subnet, `192.168.1.0/24`. The Virtual Network Editor shows the Windows host adapter attached and VMware DHCP disabled. On the Windows host, PowerShell returned `192.168.1.50/24` for `VMware Network Adapter VMnet2`. [VMnet2 settings](../evidence/foundation/vmnet2-host-only.png) · [PowerShell adapter query](../evidence/foundation/host-vmnet2-address.png)

In the Direct Console User Interface (DCUI), I set the ESXi management address to `192.168.1.10/24` and left the default gateway unset. I set the hostname to `esxi-est01.lab.local` and the primary DNS server to DC01 at `192.168.1.5`. The alternate DNS field was also unset. [DCUI IPv4 settings](../evidence/foundation/esxi-dcui-ipv4.png) · [DCUI DNS settings](../evidence/foundation/esxi-dcui-dns.png)

I reached the Host Client and checked that it listed the management IP on `vmk0` and the expected hostname and DNS server. Management traffic between the lab addresses stays on the directly connected subnet. The VCSA installer lists `VM Network` as the appliance's guest port group. Verification of the `SERVERS-LAB` port group is in progress. [Host Client networking](../evidence/foundation/esxi-vmk0.png) · [VCSA network selection](../evidence/vcenter/vcsa-stage1-datastore.png)

## Datastores

The Host Client's **Storage → Datastores** view lists two VMFS6 datastores. Both show **Single** for access and **Supported** for thin provisioning. [Datastore layout](../evidence/foundation/esxi-datastore-layout.png)

| Datastore | Capacity |
|---|---:|
| `datastore1` | 12.5 GB |
| `DATA-LAB` | 399.75 GB |
| **ESXi host total** | **412.25 GB** |

The total matches the storage capacity shown in the host summary. The 20 GB figure in Workstation is the size of the initial SCSI virtual disk. The mapping between the datastores and their backing virtual disks remains unverified. [ESXi host summary](../evidence/vcenter/esxi-host-connected.png)

I selected `DATA-LAB` and thin provisioning for the `vcenter` VM in the Stage 1 installer. [VCSA storage selection](../evidence/vcenter/vcsa-stage1-datastore.png)

An Ubuntu Server ISO upload was reported during the lab. The media portion of checkpoint **1.1B** still needs a Datastore Browser capture with the path and `.iso` filenames under `DATA-LAB`.

## Checks after setup

- Forward and reverse lookups against `192.168.1.5` matched `esxi-est01.lab.local` and `192.168.1.10`. [PowerShell lookup results](../evidence/foundation/esxi-dns-lookups.png)
- **Time & date** showed the NTP client enabled and the service running, with DC01 at `192.168.1.5` as the configured server. Active peer selection and measured clock offset remain pending. [ESXi time service](../evidence/foundation/esxi-ntp-service.png)
- After I added the host from a local SSO administrator session, vCenter listed it as **Connected** under `DC-LAB` → `CLUSTER-LAB`. Recent Tasks showed **Add host** completed. [Host summary and completed task](../evidence/vcenter/esxi-host-connected.png)
