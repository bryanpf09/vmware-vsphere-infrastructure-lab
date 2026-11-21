# Lab evidence

The screenshots are grouped by the part of the lab they document. Numbered checkpoints follow the university course guide.

## ESXi and storage

[Lab architecture](../docs/lab-architecture.md) · [ESXi host and storage](../docs/esxi-and-storage.md)

| Checkpoint | What the capture shows | Screenshot |
|---|---|---|
| Nested ESXi hardware | One virtual socket with four cores, 20 GB of RAM, and hardware-assisted virtualization enabled. | [Workstation VM settings](foundation/esxi-vm-resources.png) |
| Phase 0 · Host-only network | `VMnet2` is host-only on `192.168.1.0/24`, with a host adapter attached and VMware DHCP disabled. | [Virtual Network Editor](foundation/vmnet2-host-only.png) |
| Phase 0 · Workstation address | PowerShell returns `192.168.1.50/24` for `VMware Network Adapter VMnet2`. | [Windows host adapter query](foundation/host-vmnet2-address.png) |
| 1.1 · ESXi IPv4 | Manual address `192.168.1.10/24` with the default gateway unset. | [DCUI IPv4 settings](foundation/esxi-dcui-ipv4.png) |
| 1.1 · ESXi hostname and DNS | `esxi-est01.lab.local` with primary DNS server `192.168.1.5`. | [DCUI DNS settings](foundation/esxi-dcui-dns.png) |
| 1.1 · VMkernel management | The Host Client lists `192.168.1.10` on `vmk0`. | [Host Client networking](foundation/esxi-vmk0.png) |
| 1.1B · Datastores | `DATA-LAB` has 399.75 GB and `datastore1` has 12.5 GB. Both show VMFS6, **Single** access, and thin provisioning supported. The 412.25 GB total matches the host summary. | [Datastore layout](foundation/esxi-datastore-layout.png) |

The Ubuntu ISO inventory remains pending a Datastore Browser view with the path and filenames under `DATA-LAB`.

## DNS and time

[DNS and time checks](../docs/dns-and-time.md)

| Checkpoint | What the capture shows | Screenshot |
|---|---|---|
| 1.2B · Initial queries | Both ESXi and vCenter resolve in both directions. The DNS server name appears as `UnKnown`. | [Initial queries to DC01](foundation/dns-initial-query.png) |
| 1.2B · Reverse zone | DNS Manager shows PTR records for ESXi, vCenter, and DC01. | [Reverse lookup zone](foundation/dns-reverse-zone.png) |
| 1.2B · Repeated queries | Explicit lookups against `192.168.1.5` return the expected names and addresses for ESXi and vCenter. | [Forward and reverse answers](foundation/esxi-dns-lookups.png) |
| Active Directory discovery | The LDAP SRV answer points to `dc01.lab.local` on port `389`. | [LDAP SRV query](foundation/dns-dc-srv-lookup.png) |
| 1.1A · DC01 time source | Windows Time is running and reports `Local CMOS Clock`. | [Windows Time settings and status](foundation/dc01-windows-time.png) |
| 1.1A · ESXi time service | ESXi lists `192.168.1.5` as the NTP server. The client is enabled and the service is running. | [ESXi Time & date](foundation/esxi-ntp-service.png) |
| VCSA Stage 2 · DNS and NTP | The installer review lists `192.168.1.5` for both services. | [VCSA DNS and time settings](vcenter/vcsa-stage2-dns-and-time.png) |

Runtime NTP peer selection and clock offset remain pending verification on ESXi and the VCSA.

## vCenter deployment

[VCSA deployment and handoff recovery](../docs/vcenter-deployment.md)

| Checkpoint | What the capture shows | Screenshot |
|---|---|---|
| Installer media | `VMware-VCSA-all-6.7.0-8217866.iso` in the file listing. | [Installer filename](vcenter/vcsa-installer-media.png) |
| Stage 1 · Deployment settings | The installer targets ESXi at `192.168.1.10` and selects `DATA-LAB`, thin provisioning, and `VM Network` for the appliance. | [Storage and network selection](vcenter/vcsa-stage1-datastore.png) |
| Stage 1 · Handoff | The installer reports a successful deployment and a connection failure when it tries to start Stage 2. | [Handoff warning](vcenter/vcsa-installer-handoff-warning.png) |
| Stage 2 · Browser recovery | The appliance management interface loads at `192.168.1.20:5480` and offers **Set up**. | [VAMI by IP](vcenter/vcsa-vami-setup-by-ip.png) |
| Stage 2 · Access settings | SSH is disabled and the local SSO account is `administrator` in `vsphere.local`. | [SSH and SSO settings](vcenter/vcsa-stage2-access-settings.png) |
| Stage 2 · Service startup | Setup is at 18%, with the VMware Analytics Service starting. | [Stage 2 progress](vcenter/vcsa-stage2-service-startup.png) |
| Post-setup · Welcome page | The page at `192.168.1.20` offers the HTML5 vSphere Client. | [HTML5 client launch](vcenter/vcsa-html5-client-launch.png) |
| Post-setup · Client sign-in | The signed-in `Administrator@VSPHERE.LOCAL` session reports vCenter Server 6.7.0, build `8170161`. | [Client session and server version](vcenter/vcenter-first-session.png) |

## vCenter identity and inventory

[Identity and inventory configuration](../docs/vcenter-identity-and-inventory.md)

| Checkpoint | What the capture shows | Screenshot |
|---|---|---|
| 2.1 · Appliance domain join | The Active Directory Domain tab lists `LAB.LOCAL` for `vcenter.lab.local`. | [Appliance domain membership](vcenter/vcenter-ad-domain.png) |
| 2.1 · Identity source | `lab.local` is an external domain of type **Active Directory (Windows Integrated Authentication)**, with alias `lab.local`. | [vCenter identity sources](vcenter/vcenter-ad-identity-source.png) |
| 2.1 · Domain sign-in | The vSphere Client header identifies the session as `vsphereadmin@LAB.LOCAL`. | [Domain account session](vcenter/domain-sign-in.png) |
| 2.2 · Add Host review | The wizard targets `CLUSTER-LAB` for `esxi-est01.lab.local` and identifies ESXi 6.7.0, build `8169922`. | [Add Host review](vcenter/vcenter-add-host-review.png) |
| 2.2 · Inventory tasks | **Create datacenter**, **Create cluster**, and **Add host** show as completed under the local `VSPHERE.LOCAL` administrator. | [Recent Tasks](vcenter/vcenter-inventory-creation-tasks.png) |
| 2.2 · Connected host | The separate `Administrator@VSPHERE.LOCAL` session shows the host **Connected** under `DC-LAB` → `CLUSTER-LAB`, with **Add host** completed. | [Host summary and completed task](vcenter/esxi-host-connected.png) |

The vCenter role assignment, permission scope, and propagation settings for `LAB\vSphere-Admins` remain pending verification.
