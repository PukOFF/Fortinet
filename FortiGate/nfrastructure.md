
# Routing
***
## Configuring ECMP (equal cost multi-path)

###### ***Default: source-ip-based***
***
	config system settings
	set v4-ecmp-mode
* `source-ip-based` - Select next hop based on source IP.
* `weight-based` - Select next hop based on weight.
* `usage-based`- Select next hop based on usage.
* `source-dest-ip-based` - Select next hop based on both source and destination IPs
***
## For weight-based ECMP
***

	config system interface 
		edit <id>
			set weight <0 to 255>

	config router static
		edit <id>
  			weight <0 to 255>
---
## For spillover ECMP
***
	config system interface
		edit <interface_name>
			set spillover-threshold <0 to 16776000> 
---
## Configuring RPF (Reverse path Forwarding)
###### ***Default: Loose RPF***
***
	config ssytem setting
		set strict-src-check [disable|enable]

	config system interface
		edit <interface>
			set src-check [enable|disable]
***
## Configuring Link Health Monitor
***
	config system link-monitor
		edit <name>
			set srcintf <interface>
				set server <server_ip> - Сервер для мониторинга
				set gateway-ip <gateway_ip>
				set protocol [ ping | tcp-echo | udp-echo | twapm | http ]
				set update-static-route [ enable | disable ] - Удаление статического маршрута при сбое проверки
***
## Best Practice - Network Design
***
Use distance attribute to ensure that only one route is activate is a time

***
### Diagnostics
***
##### Active routes

S* 192.168.1.0/24 [10/0] (Distance/Metric) via 192.168.1.254, port2, [25/0] (Priority/Weight)
Active and inactive Routes

    get router info routing-table database
	*> - Active routes
	__ - inactive routes
***
### Policy Routes and ISDB Routes
***
* Protocol
* Source address
* Source ports
* Destination ports
* Type of service (TOS) bits
***
    diagnose firewall proute list
***

##### Packet Capture
***
	diagnose sniffer packet <interface> '<filter>' <verbosity> <count> <timestamp> <frame size>
		<interface> - [ any, port, internal ]
		<filter> - tcpdump format
		<level> - how much information to capture
		<count> - number of packets
		<timestamp> - print timestamp
			a - print absolute timestamp
			l - print local timestamp
		<frame size> - specify length
	
Verbosity level:
	
1) IP Headers
2) IP Headers | Packet Payloads
3) IP Headers | Packet Payloads | Ethernet Headers
4) IP Headers |                 |                  |Interface Name
5) IP Headers | Packet Payloads | Interface Name
6) IP Headers | Packet Payloads | Ethernet Headers |Interface Name
***
***
# [SD-WAN]
***

### SD-WAN Load Balancing Methods

	config system virtual-wan-link
	set load-balance-mode <load balance mode>
		* Source IP
		* Source-Destination IP
		* Usage (Spillover) - Use one interface until threshold is reached; then, use the next interface
		* Weight - Traffic will be distributed based on weights assigned on the interfaces
		* Volume - Sessions are distributed so that traffic volume is distributed by the interface weight
***
### Perfomance SLA
***
### [Virtual Domain (VDOM)]
***
##### Enabling VDOMs
***
	config system global
		set vdom-mode no-vdom/split-vdom/multi-vdom
	config vdom-mode
		edit <vdom>
			config system settings
				set optmode [ nat | transparent ]
***
	
	Enabling VDOMs won’t reboot your FortiGate, butwill log out all active admin sessions.
	Enabling VDOMsrestructures both the GUI and CLI, which you will see when you log in again.
	This also does not affect anytraffic passing throughFortiGate.***

***
##### Accessing Global and Per-VDOM Settings
***
	config global
		(global)#
	config vdom
		(vdom)# edit <vdom name>
		(vdom-name)#

	sudo [ global | vdom-name ] [ diagnose | execute | show | get ]
***
##### Global Security Profiles
***
- Antivirus
- Application Control
- Data Leak Prevention
- Inrusion prevention
- Web Filtering
---------------------------------------------------------------------------------------------------
Perform a sniffer trace
# diagnose sniffer packer <interface_name> <'filter'> <verbose> <count>

Perform a packet flow trace
# diagnose debug enable
# diagnose debug flow filter addrs <PC1>
# diagnose debug flow trace start 100
# diagnose deebug enable
===================================================================================================
===================================================================================================
[Layer 2 Switching]
===================================================================================================
===================================================================================================
Forward Domains
===================================================================================================
# config system interface_name
	edit <interface_name>
		set forward-domain <domain_ID>
===================================================================================================
Transparent Mode MAC Table
===================================================================================================
# diagnose netlink brctl name host <vdom name>.b
===================================================================================================
Virtual Wire Pair configuration
===================================================================================================
Wildcard VLAN
	- If Wildcard VLANis enabled, the virtual wire pair policies are applied equally to the 
	physical interfacesand VLANs
	- If Wildcard VLANis disabled, the virtual wire pair policies are applied only to the 
	physical interfaces.Traffic with any VLAN tag is denied
===================================================================================================
Software switch
===================================================================================================
Can group multiple physical and wireless interfaces into a single virtual switch interface
===================================================================================================
STP Configuration
===================================================================================================
# config system stp
	set config-revision <revision-number>
	set forward-delay <seconds>
	set hello-time <seconds>
	set max-age <seconds>
	set region-name <region>
	set status [ enable | disable ]
! Supported only on models that have switch interface
===================================================================================================
STP Forward
===================================================================================================
# config system interface
	edit <interface name>
	set stpforward [ enable | disable ]
===================================================================================================
===================================================================================================
[VPN]
===================================================================================================
===================================================================================================
IKE - UDP/500 (NAT-T UDP/4500)
===================================================================================================
ADVPN Configuration
===================================================================================================
Phase 1 settings
---------------------------------------------------------------------------------------------------
Spokes:
# auto-discovery-receiver
Hub <-> Spoke:
# auto-discovery-sender
Hub <-> Hub:
# auto-discovery-forwarder
===================================================================================================
VPN Offload
---------------------------------------------------------------------------------------------------
# config vpn ipsec phase1-interface
	edit ToRemote
		set npu-offload [ enable | disable ]
---------------------------------------------------------------------------------------------------
IPSec Best Practice
# config vpn ipsec [ phase1 | phase1-interface ]
	edit <vpn_name>
		set mesh-selector-type subnet

# diagnose vpn tunnel list
	* npu_flag=00 - Ingress and egress ESP packets not offloaded
	* npu_flag=01 - Only egress ESP packets can be offloaded
	* npu_flag=02 - Only ingress ESP packets can be offloaded
	* npu_flag=03 - Ingress and egress ESP packets offloaded
	* npu_flag=20 - Encryption algorithm in use is not supported for offload
===================================================================================================
TroubleShooting
---------------------------------------------------------------------------------------------------
IKE real-time debug
---------------------------------------------------------------------------------------------------
# diagnose vpn ike log filter dst-addr4 <remote_peer_IP> 
# diagnose debug application ike -1
# diagnose debug enable
---------------------------------------------------------------------------------------------------
# diagnose debug reset
# diagnose debug disable
===================================================================================================
===================================================================================================
[FSSO]
Default:
	Username
	Hostname
	IP Address
	User group(s)
===================================================================================================
===================================================================================================
DC Agent Mode
(USER -> DC with DC agent [UDP/8002]-> Collector [TCP/8000]-> FG)
===================================================================================================
C:\Windows\System32\dcagent.dll - for each Windows DC
---------------------------------------------------------------------------------------------------
Disable double resolve the DNS
---------------------------------------------------------------------------------------------------
HKLM/Software/Fortinet/FSAE/dcagent/donot_resolve=1[DWORD]
===================================================================================================
Collector Agent-Based
(USER -> DC without DC agent <[TCP/445] Collector [TCP/8000]-> FG)
===================================================================================================
Three methods:
	* NetAPI
	* WinSecLog
	* WMI
===================================================================================================
Agentless Polling mode
(USER -> DC <-[TCP/445] FG)
===================================================================================================
	Windows 2008/2012 Event IDs: 4768, 4769*, 4776, 4624, 4770 **
	Windows 2003 Event IDs: 672, 673*, 680, 528, 540 **
---------------------------------------------------------------------------------------------------
FortiGate (FGT)  polling mode also uses windows security events, however currently the supported event subset is  smaller.
    Windows 2008/2012 Event IDs: 4768, 4769
    Windows 2003 Event IDs: 672, 673
---------------------------------------------------------------------------------------------------
FortiAuthenticator supports the following event IDs:
    Windows 2008/2012 Event IDs: 4768, 4776, 4624*
    Windows 2003 Event IDs: 672, 680, 528*, 540*
===================================================================================================
NTLM Authenticate
===================================================================================================
Notice that FortiGate cannot do NTLM authentication on its own
NTLM Authentication - fallback
---------------------------------------------------------------------------------------------------
Internet Explorer
---------------------------------------------------------------------------------------------------
Automatically send NTLM
(IE > Internet Options > Custom Level > Settings > User Authetication Logon > 
> Automatic logon with current username and password)
===================================================================================================
FSSO Setting
===================================================================================================
The FSSO agents are available on the Fortinet Support website. There you will find the following:
	* The DC agent
	* The collector agent for Microsoft servers: FSSO_Setup,
	* The collector agent for Novell directories: FSSO_Setup_edirectory
	* The terminal server agent (TSagent) installer for Citrix and terminal servers: TSAgent_Setup
---------------------------------------------------------------------------------------------------
Advanced mode
---------------------------------------------------------------------------------------------------
Security profile - user, user groups, organization unit(OU)
---------------------------------------------------------------------------------------------------
Standard mode
---------------------------------------------------------------------------------------------------
Security profile - user groups
===================================================================================================
Troubleshooting
===================================================================================================
Open ports:
	* 139 (workstation verification)
	* 445 (workstation verification and event log polling)
	* 389 (LDAP)
	* 445 & 363 (LDAPS)
---------------------------------------------------------------------------------------------------
# diagnose debug authd fsso list
# execute fsso refresh	
---------------------------------------------------------------------------------------------------
# diagnose debug enable
# diagnose debug authd fsso server-status
---------------------------------------------------------------------------------------------------
# diagnose debug fsso-polling detail
---------------------------------------------------------------------------------------------------
Agentless polling mode real-time debug 
---------------------------------------------------------------------------------------------------
# diagnose debug application fssod -1

===================================================================================================
===================================================================================================
[High Availability (HA)]
===================================================================================================
Override Enable
# config system ha
# set override enable

---------------------------------------------------------------------------------------------------
Primary FortiGate Selection: Override Disable

Connected Monitored ports > HA Uptime > Priority > Serial Number
(Upper Primary, Lower Secondary)
---------------------------------------------------------------------------------------------------
Lower Uptime ia a standby node
# diagnose sys ha dump-by vcluster

Force failover
# diagnose sys ha reset-uptime
---------------------------------------------------------------------------------------------------
Primary FortiGate Selection: Override Enable
---------------------------------------------------------------------------------------------------
Connected Monitored ports > Priority > HA Uptime > Serial Number
(Upper Primary, Lower Secondary)
===================================================================================================
Hearbeat Interface IP Address
---------------------------------------------------------------------------------------------------
169.254.0.1 - highest serial number
169.254.0.2 - second highest serial number
===================================================================================================
Session Synchronization
===================================================================================================
Only session without proxy(most TCP and IPSec VPN session)
# config system ha
# set session-pickup enable
---------------------------------------------------------------------------------------------------
UDP and ICMP session
# config system ha
# set session-pickup enable
# set session-pickup-connectionless enable
---------------------------------------------------------------------------------------------------
Multicast session
# config system ha
# set multicast-ttl <5 - 3600 sec>
---------------------------------------------------------------------------------------------------
SSL VPN sessions are not synchronized
===================================================================================================
Active-Active Traffic Flow(Proxy Inspection)
---------------------------------------------------------------------------------------------------
1. srcMAC X, dstMAC primary-virtual MAC-port1, TCP SYN dport 80
2. srcMAC primary-physical MAC-port1, dstMAC secondary-physical MAC-port1, TCP SYN dport 80
3a. srcMAC secondary-physical MAC-port2, dstMAC Y, TCP SYN dport80
3b. srcMAC secondary-physical MAC-port2, dstMAC Y, TCP SYN/ACK sport80
4. srcMAC X, dstMAC primary-virtual MAC-port1, TCP ACK dport 80
5. srcMAC primary-physical MAC-port1, dstMAC secondary-physical MAC-port1, TCP ACK dport 80
6. srcMAC Y, dstMAC primary-virtual MAC-port2, TCP SYN ACK dport 80
7. srcMAC primary-physical MAC-port2, dstMAC secondary-physical MAC-port2, TCP SYN dport 80
8. srcMAC secondary-physical MAC-port2, dstMAC Y, TCP ACK dport 80
---------------------------------------------------------------------------------------------------
Active-Active Traffic Flow(Proxy Inspection)
---------------------------------------------------------------------------------------------------
# load-balance-all
---------------------------------------------------------------------------------------------------
HA Status
---------------------------------------------------------------------------------------------------
Show HA statistics
# diagnose system ha status
---------------------------------------------------------------------------------------------------
Show HA checksum for all members
---------------------------------------------------------------------------------------------------
# diagnose system ha checksum cluster
---------------------------------------------------------------------------------------------------
Show HA checksum of individual FortiGate
---------------------------------------------------------------------------------------------------
# diagnose system ha checksum show
---------------------------------------------------------------------------------------------------
Recalculate checksum of cluster members
---------------------------------------------------------------------------------------------------
# diagnose sys ha checksum recalculate
# diagnose system ha checksum show
---------------------------------------------------------------------------------------------------
Switch to a Secondary's CLI
---------------------------------------------------------------------------------------------------
# exeute ha manage <HA_device_index> <Admin_Username>
---------------------------------------------------------------------------------------------------
In-band HA Management Interface
---------------------------------------------------------------------------------------------------
# config system interface
# edit <port name>
# set management-ip <IP address and subnet mask>
---------------------------------------------------------------------------------------------------
Firmware Update
---------------------------------------------------------------------------------------------------
===================================================================================================
===================================================================================================
[Web Proxy]
===================================================================================================
===================================================================================================
Transparetn Proxy
===================================================================================================
#config firewall policy
	edit <firewall_policy_number>
	set inspection_mode proxy
	set http-policy-redirect enable (affecs only web (HTTP/HTTPS traffic)
---------------------------------------------------------------------------------------------------
Enable Web Cache
---------------------------------------------------------------------------------------------------
#config firewall proxy-policy
	edit <proxy_policy_number>
		set webcache enable
---------------------------------------------------------------------------------------------------
Monitor Proxy Users
#diagnose wad user list
===================================================================================================
===================================================================================================
[Diagnostics]
===================================================================================================
===================================================================================================
System Information
===================================================================================================
# get system status
	Model
	Serial number
	Firmware version
	Host name
	FortiGuard license status
	Systemtime
	Version of the FortiGuard antivirus, IPS, and IPreputationdatabases, and others
# get hardware nic <interface_name>
---------------------------------------------------------------------------------------------------
ARP Table
---------------------------------------------------------------------------------------------------
# get system arp
===================================================================================================
Debug Flow
===================================================================================================
# diagnose debug flow filter <filter>
# diagnose debug enable
# diagnose debug flow trace start <number_of_packets>
# diagnose debug flow trace stop
---------------------------------------------------------------------------------------------------
The packet arriving to the FortiGate, indicating the source and destination IP addresses, port numbers, and incoming interface
	- The FortiGate creating a session, indicating the session ID
	- The route to the destination, indicatingthenext-hop IP address and outgoing interface
	- The ID of the policy that matches and allows this traffic
	- How the source NAT is applied
===================================================================================================
CPU Usage
===================================================================================================
# get system perfomance status
# diagnose system top 1
# diagnose system top-summary
===================================================================================================
Memory conserve mode
===================================================================================================
# config system global
# memory-use-threshold-green <percentage>
# memory-use-threshold-red <percentage>
# memory-use-threshold-extreme <percentage>
===================================================================================================
During conserve mode
===================================================================================================
# config ips global
# set fail-open [ enable | disable ]
	enable - Packets pass through without any IPS-engine inspection_mode
	disable - Packets are dropped
---------------------------------------------------------------------------------------------------
# config system global
# set av-failopen [ off | pass | one-shot ]
	off - All new sessions with content scanning enabled are not passed
	pass - All new sessions pass without inspection_mode
	one-shot - Similar to pass in that traffic is not inspected.
		However, it will kepp bypassing the AV proxy even after leaving conserve mode.
		Administrators must either change this setting, or restart the unit, to restart the AV scanning
---------------------------------------------------------------------------------------------------
#diagnose hardware sysinfo conserve
---------------------------------------------------------------------------------------------------
Fail-Open Session Setting
---------------------------------------------------------------------------------------------------
# config system global
# set av-failopen-session [ enable | disable ]
	enable - use behaivor from av-failopen setting
	disable(default) - Block all new sessions that require proxy-based inspection
---------------------------------------------------------------------------------------------------
Hardware test
---------------------------------------------------------------------------------------------------
# diagnose hardware test suite all
===================================================================================================
Crash Log
===================================================================================================
# diagnosedebug crashlog history

===================================================================================================
===================================================================================================
===================================================================================================
SECUIRTY
===================================================================================================
===================================================================================================
Initial Configuration
===================================================================================================
===================================================================================================
Factory Default Setting
===================================================================================================
IP: 192.168.1.99/24
Access: PING, HTTP, HTTPS, SSH (port1 or MGMT1)
Username: admin
Password: <blank>
===================================================================================================
FrotiGuard Subscription Services
===================================================================================================
Package update: FortiGuard Antivirus and IPs:
	* update.fortiguard.net
	* TCP port 443 (SSL)
---------------------------------------------------------------------------------------------------
Live queries: FortiGuard Web Filterm DNS Filtering, and Antispam
	* service.fortiguard.net - for proprietary protocol on UDP port 53 or 8888
	* securewf.fortiguard.net - for HTTPS over port 53 or 8888
===================================================================================================
Reseting a Lost Password
===================================================================================================
Username: maintainer
Password: bcpb<serial-number>(Upper Case)
Only 60 seconds after boot
===================================================================================================
Administrative Access - Protocols
===================================================================================================
FortiTelemetry - FortiClient, Security Fabric
FMGAccess - FortiManager
CAPWAP - FortiAP, FortiSwitch, FortiExtender
===================================================================================================
Interface IPs
===================================================================================================
One-Arm Sniffer - Connect to SPAN port
===================================================================================================
FortiGate as a DNS Server
===================================================================================================
Resolution methods:
	* Forward: Relay request to the next server(in DNS setting)
	* Non-Recursive: Use FortiGate DNS database only to try to resolve queries
	* Recursive: Use FortiGate DNS database first and relay unresolvable queries to next server (in DNS settings)
===================================================================================================
===================================================================================================

