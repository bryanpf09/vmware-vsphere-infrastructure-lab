# vCenter Server Appliance deployment

I deployed the vCenter Server Appliance (VCSA) from `VMware-VCSA-all-6.7.0-8217866.iso` on the nested `esxi-est01.lab.local` host. I used an embedded Platform Services Controller (PSC), so vCenter and the PSC run together in the `vcenter` VM. [Lab architecture](lab-architecture.md) · [Installer media filename](../evidence/vcenter/vcsa-installer-media.png)

## Stage 1 · Appliance deployment

I pointed the installer at the lab's ESXi host, `192.168.1.10`. [Stage 1 deployment review](../evidence/vcenter/vcsa-stage1-datastore.png)

| Setting | Value |
|---|---|
| VM name and deployment type | `vcenter`, embedded PSC |
| Deployment and storage sizes | `Tiny` and `Default` |
| Datastore and disk mode | `DATA-LAB`, thin provisioning |
| ESXi port group | `VM Network` |
| Appliance hostname and IPv4 | `vcenter.lab.local`, static `192.168.1.20/24` |
| DNS server | `192.168.1.5` (`dc01.lab.local`) |
| Default gateway field | `192.168.1.5` as a placeholder for the required field |

The appliance and lab management endpoints share the isolated `192.168.1.0/24` subnet. DC01 provides DNS and time on that subnet and has no routing role. The Stage 2 review shows the appliance address, DNS server, and placeholder gateway. [Appliance network settings](../evidence/vcenter/vcsa-stage2-dns-and-time.png)

## Installer handoff failure

Stage 1 reported a successful deployment with the embedded PSC. At the handoff, the installer reported that it couldn't connect to the vCenter Appliance Management Interface (VAMI) to start Stage 2. It displayed `https://vcenter.lab.local:5480/` as the continuation URL. [Installer result and warning](../evidence/vcenter/vcsa-installer-handoff-warning.png)

I opened `https://192.168.1.20:5480/` in the browser. The Getting Started page loaded, and I used **Set up** to continue Stage 2 on the deployed appliance. [VAMI setup by IP](../evidence/vcenter/vcsa-vami-setup-by-ip.png) · [Stage 2 review](../evidence/vcenter/vcsa-stage2-dns-and-time.png)

The root cause is still undetermined. The lab DNS checks queried DC01 directly. The installer's name resolution and the appliance's service state at the time of the handoff failure weren't captured. [DNS validation](dns-and-time.md)

## Stage 2 · Appliance configuration

I selected **Synchronize time with NTP servers** and entered DC01 at `192.168.1.5`. I left SSH disabled and set up the `vsphere.local` Single Sign-On (SSO) domain with its local `administrator` account. [Stage 2 DNS and time settings](../evidence/vcenter/vcsa-stage2-dns-and-time.png) · [Stage 2 access settings](../evidence/vcenter/vcsa-stage2-access-settings.png)

The progress screen was at 18% when it showed the VMware Analytics Service starting. Runtime NTP peer selection and clock offset still need verification on both the VCSA and ESXi. [Stage 2 progress](../evidence/vcenter/vcsa-stage2-service-startup.png) · [NTP verification status](dns-and-time.md#synchronization-verification)

## vSphere Client access

The welcome page at `https://192.168.1.20/` offered the HTML5 vSphere Client. I signed in as `Administrator@VSPHERE.LOCAL` and checked **Version Information**. The running vCenter Server reported **6.7.0, build `8170161`**. [HTML5 client launch](../evidence/vcenter/vcsa-html5-client-launch.png) · [Signed-in client and server build](../evidence/vcenter/vcenter-first-session.png)
