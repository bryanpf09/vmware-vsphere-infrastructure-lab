# DNS and time services

I used `dc01.lab.local` (`192.168.1.5`) for DNS and as the local time reference on the isolated VMnet2 network. The ESXi Direct Console User Interface (DCUI) and the VCSA Stage 2 review both list DC01 as their DNS server. [ESXi DNS configuration](../evidence/foundation/esxi-dcui-dns.png) · [VCSA DNS and time settings](../evidence/vcenter/vcsa-stage2-dns-and-time.png)

## DNS checks

DNS Manager on DC01 shows the `lab.local` and `_msdcs.lab.local` forward zones and the `1.168.192.in-addr.arpa` reverse zone for `192.168.1.0/24`. The reverse zone contains PTR records for ESXi, vCenter, and DC01. [DNS Manager reverse zone](../evidence/foundation/dns-reverse-zone.png)

I sent the four `nslookup` queries in checkpoint **1.2B** directly to `192.168.1.5`. Both ESXi and vCenter resolved by name and by IP. [Forward and reverse lookup results](../evidence/foundation/esxi-dns-lookups.png)

| Command | Answer |
|---|---|
| `nslookup esxi-est01.lab.local 192.168.1.5` | `192.168.1.10` |
| `nslookup 192.168.1.10 192.168.1.5` | `esxi-est01.lab.local` |
| `nslookup vcenter.lab.local 192.168.1.5` | `192.168.1.20` |
| `nslookup 192.168.1.20 192.168.1.5` | `vcenter.lab.local` |

The earlier output showed `UnKnown` for the DNS server, while the four queries still returned the expected answers. The repeat checks identified the server as `dc01.lab.local`. The later DNS Manager view also lists the PTR record for DC01. [Initial query](../evidence/foundation/dns-initial-query.png) · [Repeated queries](../evidence/foundation/esxi-dns-lookups.png)

I also queried `_ldap._tcp.dc._msdcs.lab.local` on DC01. Its SRV record returned `dc01.lab.local` on port `389` for domain controller discovery, and the additional A record gave the address `192.168.1.5`. [LDAP SRV lookup](../evidence/foundation/dns-dc-srv-lookup.png)

## Windows Time on DC01

I set Windows Time (`W32Time`) to start automatically, enabled the `NtpServer` provider, and marked DC01 as a reliable source in Windows Time. I set `Type=NoSync` to use the local clock without upstream synchronization and allowed inbound NTP on UDP port `123` from `192.168.1.0/24`. [DC01 Windows Time setup](../evidence/foundation/dc01-windows-time.png) · [Microsoft Windows Time settings reference](https://learn.microsoft.com/en-us/windows-server/networking/windows-time-service/windows-time-service-tools-and-settings)

`Get-Service W32Time` showed the service running. The `w32tm /query /status` output reported `Local CMOS Clock` with reference `LOCL`. DC01's local clock serves as the lab reference. Its accuracy against an external time source remains unverified. [DC01 Windows Time status](../evidence/foundation/dc01-windows-time.png)

## Synchronization verification

For checkpoint **1.1A**, **Manage → System → Time & date** showed the ESXi NTP client **Enabled** and the service **Running**, with `192.168.1.5` configured. I also selected **Synchronize time with NTP servers** and entered `192.168.1.5` in the VCSA Stage 2 wizard. Runtime peer selection and measured clock offset remain pending on both ESXi and the VCSA. [ESXi NTP service state](../evidence/foundation/esxi-ntp-service.png) · [VCSA Stage 2 time settings](../evidence/vcenter/vcsa-stage2-dns-and-time.png)
