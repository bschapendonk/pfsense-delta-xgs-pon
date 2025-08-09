# pfSense+ configuration for DELTA using a Nokia XS-010X-Q

## RTFM! "pfSense has excellent documentation"
* [pfSense Documentation](https://docs.netgate.com/pfsense/en/latest/)
* [Vrije modemkeuze](https://www.delta.nl/klantenservice/vrije-modemkeuze/)
* [DELTA Fiber FTTH PON Interface Specification](https://www.delta.nl/media/12gp0gk4/deltafiber_vrijemodemkeuze_pon-v1-2.pdf)
* **IPv6 is not the same as IPv4, there are fundamental differences, be aware of this!**
    * For example SLAAC is required, DHCPv6 is optional, for address configuration
  * All client support SLAAC, but not all support DHCPv6
    * Android does not support DHCPv6
        * [Support for DHCPv6 (RFC 3315)](https://issuetracker.google.com/issues/36949085?pli=1), this feature request is open since 2012
* Packet Pushers [IPv6 Buzz](https://packetpushers.net/podcast/ipv6-buzz/) podcast

## Setup
* pfSense+ 25.07
* Hardware (whitebox from AliExpress, don't judge me)
    * Intel Celeron Processor N5100
    * 8GB DDR4
    * 256GB NVMe SSD
    * 4x 2.5Gbit Intel Ethernet Controller I226-V
* Interfaces
    * WAN igc0.100
        * DELTA on igc0 VLAN 100
    * LAN igc1.1000
    * IOT igc1.1001 (opt1)
    * ONT igc0 (opt2)
        * **DO NOT CHANGE ANYTHING HERE**
        * Gives access to the admin interface of the Nokia XS-010X-Q
        * Only useful to view the **Active Software Version**
        * Only accepts traffic from 192.168.100.0/24, hence the outbound NAT rule
* Firewall
    * Mostly accept incoming ICMP and ICMPv6 traffic on all interfaces (except ONT)
    * LAN allows all incoming traffic, except to the ONT subnet, exceptions are in the ont_access (alias)
    * IOT allows internet incoming traffic, by rejecting all other firewall subnets and vpn_networks (alias)
    * ONT blocks all incoming traffic
* Services
    * DHCPv4 Server
        * DNS Registration
            * Disabled, manually register IPv4 and IPv6 addresses, since SLAAC is stateless but is mostly static
    * DHCPv6 Server
        * Disabled, KISS since SLAAC is enough
    * DNS Resolver
    * **Router Advertisement**
        * Unmanaged, SLAAC only and no DHCPv6 Server
            * Requires Windows 11 24H2 or newer for working RDNSS
                * Older versions of Windows only have working RDNSS if IPv4 network stack is disabled, hence require DHCPv6 to receive a IPv6 DNS server if the IPv4 network stack is enabled

## DELTA Requirements

> **XGSPON Glasvezelnetwerk**
> 
> Het glasvezelmodem voor PON moet voldoen aan de volgende eisen:
> 
> * Aan te sluiten op Attema FTU = inclusief simplex single mode (9/125 µm) glasvezelkabel met geschikte “slede” > voor in de Attema FTU en een SC/APC glasconnector.
> * BB.247 gecertificeerd
> * ITU-T G.984, ITU-T G.987, ITU-T G.988
> * TR-156 & TR-247
> * Voldoet aan de DELTA Fiber FTTH PON Interface Specification

source [Vrije modemkeuze](https://www.delta.nl/klantenservice/vrije-modemkeuze/)

## pfSense+ Configuration (screenshots)
* [System](SYSTEM.md)
* [Interfaces](INTERFACES.md)
* Firewall
    * [NAT](FIREWALL_NAT.md)
    * [Rules](FIREWALL_RULES.md)
* Services
    * [DHCPv4 Server](SERVICES_DHCPv4.md)
    * [DHCPv6 Server](SERVICES_DHCPv6.md)
    * [DNS Resolver](SERVICES_DNS.md)
    * [Router Advertisement](SERVICES_RA.md)

### Nokia ONT XS-010X-Q
![ont](/assets/images/Nokia_XS-010X-Q.png)