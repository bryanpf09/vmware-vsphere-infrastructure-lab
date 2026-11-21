# vCenter identity and inventory

I joined `vcenter.lab.local` to the `lab.local` Active Directory domain, tested a domain sign-in, and then brought the ESXi host into vCenter from a separate local administrator session. [VCSA deployment](vcenter-deployment.md) · [Lab architecture](lab-architecture.md)

## Active Directory and sign-in

Under **Administration → Single Sign On → Configuration**, the **Active Directory Domain** tab shows `vcenter.lab.local` joined to `LAB.LOCAL`. I added `lab.local` as a vCenter identity source. The **Identity Sources** tab lists it as an **External Domain** of type **Active Directory (Windows Integrated Authentication)**, with alias `lab.local`. The vCenter system domain listed there is `vsphere.local`. [Appliance domain membership](../evidence/vcenter/vcenter-ad-domain.png) · [vCenter identity sources](../evidence/vcenter/vcenter-ad-identity-source.png)

I verified domain authentication by signing in as `vsphereadmin@LAB.LOCAL` and checking the account in the vSphere Client header. Verification of that account's AD group membership and of the vCenter role assignment, permission scope, and propagation settings for `LAB\vSphere-Admins` remains pending. [Domain sign-in](../evidence/vcenter/domain-sign-in.png)

## Datacenter, cluster, and host onboarding

I used `Administrator@VSPHERE.LOCAL` for the inventory changes. I created `DC-LAB` under `vcenter.lab.local`, created `CLUSTER-LAB` inside the datacenter, and then added `esxi-est01.lab.local` to the cluster. [Local SSO session](../evidence/vcenter/esxi-host-connected.png)

The **Add Host** review showed `CLUSTER-LAB` as the target and **ESXi 6.7.0, build `8169922`** as the host version. It listed `VM Network`, `datastore1`, and `DATA-LAB` on the host. [Add Host review](../evidence/vcenter/vcenter-add-host-review.png)

Recent Tasks showed **Create datacenter**, **Create cluster**, and **Add host** completed. Their targets were `vcenter.lab.local`, `DC-LAB`, and `CLUSTER-LAB`, respectively. Each **Initiator** field displays `VSPHERE.LOCAL\Administr...`. The host summary shows `esxi-est01.lab.local` under `DC-LAB` → `CLUSTER-LAB` with state **Connected**. [Completed inventory tasks](../evidence/vcenter/vcenter-inventory-creation-tasks.png) · [Connected host and inventory](../evidence/vcenter/esxi-host-connected.png)
