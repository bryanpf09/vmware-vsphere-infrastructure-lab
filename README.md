# VMware vSphere Infrastructure Lab

**Status:** In progress

I started this nested lab on one Windows workstation for a university virtualization course. I wanted to bring an ESXi host under vCenter management and work through the DNS, storage, and authentication checks along the way. The course guide targets vSphere 6.0. My lab runs **VMware ESXi 6.7.0** and **vCenter Server 6.7.0**.

## Lab setup

VMware Workstation Pro hosts the Windows Server 2019 domain controller alongside the nested ESXi host. The vCenter Server Appliance (VCSA) runs on the ESXi host it manages. I access the lab from the workstation through host-only `VMnet2` on `192.168.1.0/24`. DC01 provides Active Directory, DNS, and the local time reference on that subnet. [Topology and addresses](docs/lab-architecture.md)

## What I verified

- The ESXi Host Client opened at `192.168.1.10` and listed `DATA-LAB` as a 399.75 GB VMFS6 datastore. [ESXi host and storage](docs/esxi-and-storage.md)
- Forward and reverse queries against DC01 returned the expected ESXi and vCenter names and addresses. An LDAP SRV query also identified DC01 as the domain controller. [DNS and time services](docs/dns-and-time.md)
- After joining vCenter to `lab.local` and adding the Active Directory identity source, I signed in as `vsphereadmin@LAB.LOCAL`. [Domain sign-in](evidence/vcenter/domain-sign-in.png)
- In a separate local SSO administrator session, I created `DC-LAB` and `CLUSTER-LAB`, then added `esxi-est01.lab.local`. The host appeared as **Connected** and the Add host task completed. [Identity and inventory](docs/vcenter-identity-and-inventory.md) · [Connected host](evidence/vcenter/esxi-host-connected.png)

## How I resumed vCenter setup

Stage 1 deployed the appliance, then the installer reported that it could not reach the appliance management interface. I opened the interface by IP at `https://192.168.1.20:5480/`, resumed Stage 2, and later signed in to the HTML5 vSphere Client as the local SSO administrator. The cause of the original handoff failure remains undetermined. [Deployment and recovery](docs/vcenter-deployment.md)

## Remaining work

- Verify the `SERVERS-LAB` port group and capture the Ubuntu ISO listing in `DATA-LAB`.
- Check runtime NTP peer selection and clock offset on ESXi and the VCSA. Verify the role assignment, permission scope, and propagation settings for `LAB\vSphere-Admins`.
- Provision Ubuntu Server, build a Linux VM template, and exercise cloning and snapshots.
- Test custom role-based access control (RBAC), configure monitoring and alarms, review host logs, and demonstrate remote VM management through VMware Workstation Pro.

[Evidence index](evidence/README.md)
