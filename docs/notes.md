# In Class Notes

[Google Drive Resources Folder](https://drive.google.com/drive/u/0/folders/1ros2yDhEZhPIqQgwCroSsive4c4r0Ocz?ogsrc=32)

[GitHub Security+ Study Guide](https://github.com/alyssaknight/security-plus-study-guide/blob/master/security-plus-master-guide.md)

---
## Week 4
#### **Day 13 - 11.6.18**

* [HBGary Hack](https://arstechnica.com/tech-policy/2011/02/anonymous-speaks-the-inside-story-of-the-hbgary-hack/)
* Famous Insiders
    * Robert Hansen
    * Bradley Manning
    * Edward Snowden
    * Mittesh Das
        * What did he do? - placed logic bomb in US Army servers
        * Who did it affect? - Army reservists and taxpayers
        * How much did it cost? - $2.6m
        * Was the cost reasonable? - yes
        * What happened to him? - sentenced to 2 years imprisonment followed by 3 years of supervised release and ordred to pay $1.5m in restitution
* Hacktivists
    * Goals
        * social
        * political
        * reason of choice
* Financial Crime
* Incident Response 
    * Disciplines/Components
        * Malware Analysis
        * Digital Forensics
        * Memory Analysis
* Lifecycle of an Intrusion
    * Recon
    * Scanning
    * Gaining Access
    * Maintaining Access
    * Covering Tracks

---
* Nmap, Zenmap, and Hping3 Lab
    * used for recon
    * open kali and ubuntu VM
        * kali terminal
            * `nmap 10.0.2.6 -p 53`
            * `nmap -f 10.0.2.6`
            * `nmap -mtu 8 10.0.2.6` - same as -f flag
            * `nmap -mtu 24 10.0.2.6`
            * `nmap -sT -Pn --spoof-mac <mac address/prefix/vendor name> 10.0.2.6`
            * `nmap -e eth0 -sU -S 10.0.2.4 10.0.2.6` - 10.0.2.4 is the allowed ip address set in firewall lab and 10.0.2.6 is the dns server ip address
            * `nmap -e eth0 -S 10.0.2.4 -sU 10.0.2.0/24`
            * `nmap -sS 10.0.2.6`
            * `nmap -sA 10.0.2.6`
            * `nmap -sT 10.0.2.6`
            * `nmap -sT --top-ports 10 -D Rnd:10 10.0.2.6` - scan for top 10 ports
            * `nmap -sT -D Rnd:10 10.0.2.6` - scan for top 1000 ports
            * `nmap -sT -ff 10.0.2.6`
            * `nmap -sU -ff 10.0.2.6`
            * `nmap -sS --spoof-mac Cisco 10.0.2.6`
            * `nmap --scan-delay 10mx -sT 10.0.2.6`
            * launch zenmap - GUI for nmap
            * `Target: 10.0.2.0/24` and `Profile: Intense scan, all TCP ports`
            * change Profile to `Slow comprehensive scan`
            * `hping3 -S 10.0.2.6 -p 80`

---
#### **Day 12 - 11.5.18**

* [IT Security Cookbook - Firewalls: Securing External Network Connections](http://www.boran.com/security/it12-firewall.html#Heading53)
* Firewalls
    * protective control
    * hardware or software that filters what traffic is allowed into a network or network interface
    * a properly configured firewall should block ALL traffic by default and whitelist what kind of traffic is allowed explicitly
* Types of Firewalls
    * Stateless/packet filtering firewall (iptables)
        * OSI layer 2
        * filters fiels in the packet headers
        * easily spoofed by minipulating packet headers
    * Stateful firewall
        * more intelligent, checks for things like 3 way handshake
        * OSI layer 3/4 
    * Application layer firewall
        * OSI layer 5 and above
        * analyzes payload contents for things accepted by the application
    * Next-gen firewall
        * all of the above with additional features like IPS and WAF
* Nmap port status definitions
    * open
    * filtered
    * closed
    * unfiltered
* Firewall Lab
    * ubuntu VM
        * `sudo iptables -L`
            * Chain INPUT (policy ACCEPT) - whitelisting
                * also known as INGRES traffic - incoming traffic
            * Chain FORWARD (policy ACCEPT) - authenticate with a host like ssh
            * Chain OUTPUT (policy ACCEPT) - response to a request or a server sending out GET requests for example
                * also known as EGRES traffic - outgoing traffic
        * `sudo apt-update` - this is a GET request
        * `sudo iptables -F` - flush rules
        * `sudo ufw disable` - disable firewall (switch to kali after this step)
        * `sudo iptables -P INPUT ACCEPT`
        * `sudo iptables -P INPUT DROP`
        * `sudo iptables -L`
        * `sudo iptables -P FORWARD DROP`
        * `sudo iptables -P OUTPUT DROP` - all drop commands setup drop or deny by default firewall policy
        * `sudo iptables -A INPUT -i lo -j ACCEPT` - allow pings from own computer
        * `sudo iptables -A OUTPUT -o lo -j ACCEPT`
        * `sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT` - this is a stateful rule for setting up a stateful firewall
        * `sudo iptables -A OUTPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT`
        * `sudo iptables -L` - verify new rules were setup correctly
        * `sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT`
        * `sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT`
        * `sudo iptables -A INPUT -p udp --dport 53 -j ACCEPT`
        * `sudo iptables -L --line-numbers`
        * `sudo iptables -D INPUT 5`
        * `sudo iptables -I INPUT 2 -p udp -s <source ip address, i.e. 10.0.2.4> --source-port 1024:65535 -d 10.0.2.6 --destination-port 53 -j ACCEPT`
        * `sudo iptables -P OUTPUT ACCEPT`
        * `sudo apt-get install iptables-persistent`
        * `sudo netfilter-persistent save`
        * `sudo iptables -P OUTPUT DROP`
        * `sudo iptables -N LOGGING`
        * `sudo iptables -A INPUT -j LOGGING`
        * `sudo iptables -S`
        * `sudo iptables -A OUTPUT -j LOGGING`
        * `sudo iptables -A LOGGING -m limit --limit 2/min -j LOG --log-prefix "IPTables-Dropped: " --log-level 4`
        * `sudo iptables -A LOGGING -j DROP`
        * `sudo vim /etc/rsyslog.d/my_iptables.conf`
            * paste this in `:msg,contains,"IPTables-Dropped: " /var/log/iptables.log`
        * `sudo service rsyslog restart`
        * `sudo tail -F /var/log/iptables.log`

    * kali VM
        * `nmap -sT <ubuntu ip address>` (switch back to ubuntu)

---
## Week 3
#### **Day 10 - 10.30.18**

* [Seinfeld CTF Possible Answers](https://pastebin.com/NS4ng79h)
* [Generate a Self Signed Certificate for Nginx](https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-nginx-in-ubuntu-16-04)
* setup ssl/https for our proxy server
    * open ubuntu
        * `cd /etc/nginx/sites-enabled`
        * `sudo mv internalportal.com ../sites-available`
        * `sudo ln -s /etc/nginx/sites-available/internalportal.com internalportal.com`
        * `sudo vim internalportal.com`
            `listen 443 default_server;` - add this line under `listen 80 default_server`
        * `sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048`
        * `sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/internalportal-selfsigned.key -out /etc/ssl/certs/internalportal-selfsigned.crt` - generate self signed ssl certificate
        * `sudo vim internalportal.com` - replace with [this gist](https://gist.github.com/jocelynsaysrawr/d283c46c3ce58746adc9d917714b6ea5)
        * `sudo systemctl restart nginx.service`
        * `sudo nginx -t` - check to make sure status is 'ok'

---
#### **Day 9 - 10.29.18**

* [Customizing Wireshark](https://researchcenter.paloaltonetworks.com/2018/08/unit42-customizing-wireshark-changing-column-display/)
* HTTPS and HTTP Lab
    * open kali, ubuntu, and windows VMs
        * kali
            * open network settings
            * change ipv4 tab
                * no automatic dns
                * set to ubuntu ip address
        * ubuntu
            * we're setting up our ubuntu VM to be a web server by installing nginx
            * `sudo rm /var/www/html/index.html` - delete fake website from last lab
            * `cd /etc/nginx`
            * `sudo vim nginx.conf` - edit confirguration file
                * `sudo vim sites-enabled/default` - default server configuration
            * `sudo vim /var/www/html/login.html`
                * copy and paste [this code](https://gist.github.com/jaywon/524b8536a917a310ef894e92d00fb23a)
            * `cd /etc/nginx/sites-enabled`
            * `sudo rm default`
            * `sudo service nginx restart`
            * `sudo vim internalportal.com` 
                * copy and paste [this file](https://gist.github.com/jaywon/60f088f20eba649e8e10e5b77084dbf9)
            * `cd /var/www/html`
            * `sudo mkdir internalportal` - this is the new web root path
            * `sudo mv login.html internalportal` - move the login page to the new root path folder
            * `cd /etc/bind`
            * `sudo vim named.conf.local`
                * ```
                    //
                    // Do any local configuration here
                    //
                    zone "devleague.com" {
                        type master;
                        file "db.devleague.com";
                    };

                    zone "internalportal.com" {
                        type master;
                        file "db.internalportal.com";
                    };

                    // Consider adding the 1918 zones here, if they are not used in your
                    // organization
                    //include "/etc/bind/zones.rfc1918";
                ```
            * `cd /var/cache/bind`
            * `sudo cp db.devleague.com db.internalportal.com`
            * `sudo vim db.internalportal.com` - change devleague.com values to internalportal.com
            * `sudo systemctl restart bind9.service` - restart service after changing config files
            * `sudo systemctl restart nginx.service` - restart service after changing config files
        * windows
            * change network settings
                * configure ipv4 to ubuntu ip address
    * open wireshark on host computer

---
## Week 2
#### **Day 8 - 10.27.18**

* [DNS Rebinding Attack Medium Article](https://medium.com/@brannondorsey/attacking-private-networks-from-the-internet-with-dns-rebinding-ea7098a2d325)
* DNS Lab
    * port 53
    * sometimes you are assigned a DNS server
        * can be a security/privacy risk
    * `dig -x <ip address>` - reverse lookup
    * top level domain - website.ending
    * subdomain - stuff.website.ending
    * non-authoritative answer - the dns server is not the owner of the record or the (ns) nameserver
    * kali dns tools
        * `ping sudokrew.com` - get ip address
        * `dnsrecon -t rvl -r 138.197.206.0/24` - reverse lookup a range of ip addresses on same subnet
        * `whois <domain.name>`
    * in ubuntu VM
        * `sudo vim /etc/hosts`
        * `127.0.0.1    devleague.com`
        * `sudo apt update`
        * `sudo apt install bind9` - create dns server
        * `named -v` - check if install worked
        * `sudo systemctl status bind9.service` - check if service is active (running)
        * `cat /etc/bind/named.conf.options`
        * `cd /ect/bind/var/cache/bind/`
        * `sudo vim db.devleague.com`
            * copy and paste [this file](https://gist.github.com/jaywon/92b6d50a186b7d6f8208ebd53db38ac7)
            * use your own ip address ^^
        * `named-checkzone devleague.com db.devleague.com`
        * `sudo vim /etc/bind/named.conf.local`
            * ```
                //
                // Do any local configuration here
                //
                zone "devleague.com" {
                    type master;
                    file "db.devleague.com";
                };

                // Consider adding the 1918 zones here, if they are not used in your
                // organization
                //include "/etc/bind/zones.rfc1918";
            ```
        * `sudo systemctl restart bind9.service`
        * `sudo service bind9 status`
        * `sudo vim /etc/hosts`
            * delete devleague.com line `dd`
        * `dig devleague.com`
        * `dig @<your ip address> devleague.com ANY`
        * freeze your ip address for this machine
            * settings -> network -> manually configure ipv4
        * `nslookup`
            * `devleague.com`
            * `server <your ip address>`
            * `devleague.com`
            * `exit`
        * `sudo vim /etc/systemd/resolved.conf`
            * `DNS=10.0.2.6`
            * `FallbackDNS=8.8.8.8`
        * `sudo systemctl restart systemd-resolved.service`
        * `dig devleague.com`
        * `sudo apt install nginx`
            * `sudo service nginx status`
            * `sudo systemctl status nginx.service` - check if nginx installed and running - ^^ does the same thing
        * `sudo su`
        * `echo "This is a fake DevLeague site" > /var/www/html/index.html`
        * `exit`
        * navigate to devleague.com in web browser to see your new fake site

---
#### **Day 7 - 10.23.18**

* ICMP has no ports
* First three hex values in a MAC address are specific to each device manufacturer
* ARP - Address Resolution Protocol
    * Every device on a standard Ethernet network must have an OSI layer 2 and layer 3 address
    * an ARP request asks who is at a given IP address
    * an ARP reply is a response by whatever computer has been assigned that IP address and contains that computer's MAC address
    * each computer caches what physical addresses map to each IP address
* ARP Lab - man in the middle attack arp spoofing/arp poisoning
    * configure kali, windows, and ubuntu VMs
        * settings -> Network
        * Adapter 1
            * ~~Attached to: Host-only Adapter~~ **Edit: Attached to: Nat Network**
            * ~~Name: vboxnet0~~ **Edit: Name: NatNetwork** - instructions below are for how to generate vboxnet0
                * Main window -> Global Tools -> Host Network Manager
                * Create - automatically generates vboxnet0
            * Advanced Options
                * Promiscuous mode: Allow All
    * launch terminals
        * kali commands
            * `ifconfig`
            * `ping <windows IP address>`
            * `arp -a` - view cached addresses
            * `arp -d <IP address>` - clear out arp cache
            * `echo 1 > /proc/sys/net/ipv4/ip_forward` - enables IP forwarding
                * we turn this on so that the target computer will send us packets and then we will forward them on 
            * `ip route` - find IP address of router
            * `arpspoof -i eth0 < <- eth0 is what interface you are using> -t 10.0.2.6 < <- target IP> -r 10.0.2.1 < <- router IP>`
                * this will make the target think that the router and the attacking computer have the same Physical Address
        * ubuntu commands
            * `ifconfig`
        * windows commands
            * `ipconfig /all`
            * `arp -a`
    * launch wireshark
        * capture from vboxnet0
        * capture filter - `arp`
* DNS - Domain Name System
    * [Whats My DNS](https://www.whatsmydns.net)
    * mapping domain names to IP addresses
    * the phonebook of the web
    * 5 main types of DNS records most commonly used
        * A - map a domain name to an IP address
        * CNAME - map a domain name to another domain name, aka an alias
        * MX - mail records, used for handling domain specific mail records since mail servers are often separate from web servers even though they share the same domain name
        * TXT - not used for routing but used as a means to add metadata to a domain
        * NS - shows where the official name servers are that host the original/official DNS record configurations
---
#### **Day 6 - 10.22.18**

* Wireshark
    * [wireshark capture filters](https://wiki.wireshark.org/CaptureFilters)
    * [wireshark filters man pages](https://www.wireshark.org/docs/man-pages/wireshark-filter.html)
    * open wireshark on host computer
    * capture filter - specify filter before starting capture
    * display filter - specify filter after capture starts
    * configure name resolution
        * Wireshark -> Preferences -> Name Resolution -> check all boxes above `Maximum concurrent requests`
* [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)
    * `mkdir devleagueCyber/captures`
    * `mkdir devleagueCyber/captures/geoIP`
    * `cd devleagueCyber/captures/geoIP`
    * download all files in MaxMind DB binary, gzipped row from GeoLite website above
    * `mv ~/Downloads/Geo*.tar.gz .`
    * `gunzip Geo*.tar.gz`
    * `tar -xvf Geo<rest-of-file>.tar` - do this for each file
    * Wireshark -> Preferences -> Name Resolution -> `MaxMind database directories` -> add file paths to parent folders of .mmdb files
* PING Lab
    * when you hear `ping` think `ICMP`
    * change kali VM and windows VM settings
        * Network Tab -> Adapter 1 -> Attached to: Bridged Adapter
        * Advanced -> Promiscuous Mode: Allow All
    * open kali VM and windows VM
    * open terminals in each VM
        * windows commands
            * `ipconfig /all`
            * turn off Windows firewall for both public and private networks
        * kali commands
            * `ifconfig`
            * `ping <Windows Machine IPv4 Address>` - if no response you may be blocked by a firewall in Windows VM
            * `fping -g <starting of IP address range> <ending of IP address range>` or `fping -g <network address/subnet>` - ping sweep
            * `nmap -sn 192.168.200.0/24` where 192.168.200.0/24 is whatever your network address is/subnet - nmap ping sweep
    * open wireshark
        * capture interfaces
            * check `Enable promiscuous mode on all interfaces`
            * capture filter - `icmp or icmp6`

---
## Week 1
#### **Day 5 - 10.20.18**

* [Packet Tracer](https://www.netacad.com/courses/packet-tracer-download/?p_auth=h6jOI2lZ&p_p_auth=iJXrHgDA&p_p_id=resendscreenname_WAR)
* [In Class Lab](https://docs.google.com/document/d/1ymzaY0K1MPwvqW6ttmGzmwqI6fipapwY5AW6AbgAavw/edit)
---
* Packet Tracer
    * Configure Router
        * CLI tab
            * press enter
            * `enable`
            * `configure terminal` or `config t`
            * `int Fa0/0`
            * `ip address 192.168.1.2 255.255.255.0`
            * `no shut`
            * `exit`
            * `exit`
            * `show run` - show all configurations
            * `copy run start` - save configurations
            * `config t`
            * `line console 0`
            * `password <password>`
            * `login`
            * `line vty 0 4`
            * `password <password>`
            * `login`
            * `enable password <password>`
            * `enable secret <password>`
            * `service password-encryption`
            * `no service password-encryption` - turn off password encryption
            * `copy run start`
---

#### **Day 4 - 10.16.18**

* Networking Notes:
    * Subnetting
        * reduces network traffic
        * divides up a network into smaller parts for faster/easier communication
        * easier management - easier to isolate issues in small networks than one gigantic one and it also enables an administrator to implement security policies such as which subnets are allowed or not allowed to communicate together.
        * How to create subnets
            * take bits from host portion of the IP address and reserve them to define the subnet address
            * fewer bits for the host
            * subnetting steps
                * determine number of required network IDs
                * determine the number of required host IDs per subnet
            * subnetting 5 Qs
                * how many subnets does the chosen subnet mask produce?
                    * 2^x (x = 1)
                    * 2^x = number of subnets
                    * x = the number of masked bits or the 1s
                    * for class C networks check the last byte
                    * Example: 11000000 we would get 2^2 => so there would be 4 possible subnets
                * how many valid hosts per subnet are available?
                    * 2^y - 2 (y = 0) = number of hosts per subnet
                    * y = the number of unmasked bits or 0s
                    * Example: 11000000 = 2^6 - 2 = 62 possible hosts
                    * we subtract 2 for the network address and the broadcast address
                * what are the valid subnets?
                    * 256 - subnet mask = block size
                    * Example: 256 - 192 = 64
                    * each subnet is going to have 64 addresses
                    * so the subnet groups would be 0 - 63, 64 - 127, 128 - 191, 192 - 255
                * what is the broadcast address of each subnet?
                    * the number right before the next subnet
                    * for the above groups the broadcast addresses would be 63, 127, 191, and 255
                * what are the valid hosts in each subnet?
                    * the numbers between the network address and the broadcast address
                    * 1 - 62, 65 - 126, 129 - 190, 193 - 254
    * OSI Model
        * 7) Application Layer (Data) 
            * provides interface between users and machines
            * Protocols - HTTP, HTTPS, FTP
        * 6) Presentation Layer (Data)
            * handles processing of encryption/decryption to the application layer
            *   Protocols - JPEG, BMP, GIF, TIF, PNG, MP3, MIDI, ASCII, ANSI
        * 5) Session Layer (Data)
            * provides virtual agreement between two end communication devices
            * functions of this layer: etablishment, management, and termination
            * Protocols - SIP, NFS, SQL, ASP
        * 4) Transport Layer (Segment)
            * functions of this layer: handshaking, acknowledgement, and sequencing
            * Protocols -
        * 3) Networking Layer (Packet)
            * functions of this layer: sorting, filtering, and distribution
            * Protocols - Routed Protocol: IP, IPX, Apple talk
            * Routing protocols - IGP, EGP, BGP, EBGP, IBGP, RIP
        * 2) Data Link (Frame)
            * physical transmission of the data and handles error notification, network topology, and flow control
            * functions of this layer: error detection and control of data
            * Protocols - 
        * 1) Physical Layer (Binary)
            * involves media, move bits between devices
            * consists of the physical components in a LAN such as hardware or networking cables
    * Routers
        * by default won't forward any broadcast packets. Without the IP address, a router will not forward any packets
        * routers use the IP address in a network-layer header to deterine the next hop router to forward packets to
        * use routing tables to keep track of routes to neighboring routers so they don't have to ask again and again (kinda like a browser cache)
    * Ports & Protocols
        * Ports
            * theoretical or virtual location where information is sent
            * 65,535 TCP & UPD ports
            * 0 - 1024 are well known or common ports
            * greater than 1024 used by upper layers to set up sessions
            * Common Ports
                * SSH - port 22
                * telnet - port 23
                * DNS - port 53
                * HTTP - port 80
                * HTTPS - port 443 or 8080
                * NetBIOS - port 137-139
                * Nessus - port 1241
        * Networking Protocols
            * provide the rules for computers to communicate with each other on a network
            * 4 categories of protocols
                * connectivity - connecting devices
                * encryption - data in transit
                * application - operate in application layer of OSI (display information)
                * email - SMTP, POP3
            * HTTP/HTTPS
                * hypertext transfer protocol
                * html common language
                * HTTPS encrypts web trafic
            * IP
                * internet protocol
                * connectionless protocol for use on packet switched networks
                * best effort delivery
            * TCP/IP
                * suite of protocols created by the DoD to ensure and preserve data integrity as well as maintain communication in the event of a catastrophic war
                * breaks large blocks of information into smaller segments
                * numbers and orders segments
                * ensures delivery
                * 3 way handshake
                    * SYN
                    * SYN-ACK
                    * ACK
            * UDP
                * user datagram protocol
                * connectionless sessions (no 3 way handshake)
                * ICMP (like audio and video)
                * many DoS attacks use UDP
                * send large number of UDP packets to random prots on a remote host
            * ICMP
                * internet control missage protocol
                * used for testing basic network conectivity
                * ping, pathping, traceroute
                * many DoS attacks use ping
                * block ICMP at firewalls and routers which disables a ping response
                * blocking ICMP prevents attackers from discovering devices in a network
            * ARP
                * address resolution protocol
                * resolve IPv4 addresses to media access control MAC addresses
                * MAC addresses - physical device addresses
            * TELNET
                * terminal emulation (what we will use to connect to the network devices)
                * allows users on a remote machine (client) to access resources of another machine
                * all traffic is in plaintext
                * used for initial connection to a router
                * makes it look like a client is a terminal directly connected to the local network
            * SSH (encryption)
                * secure shell
                * encrypts a wide variety of traffic
                * TCP port 22
                * remote system administration
            * DNS
                * domain name service
                * resolves hostnames to corresponding IP address
            * Other Application Protocols
                * FTP - file transfer protocol
                * SNMP
                * NetBIOS
                * Kerberos
                * RDP

---
#### **Day 3 - 10.15.18**

* [2018 Hacker Report](https://www.hackerone.com/sites/default/files/2018-01/2018_Hacker_Report.pdf)
* [RFC IPv4](https://tools.ietf.org/html/rfc791)
* OSINT - Open Source INTelligence 
---
* Networking Notes:
    * IPv4
        * connectionless protocol or best effort - does not guarantee delivery but uses protocols like TCP to guarantee delivery
        * 32 bit value that uniquely identifies the system on the network
        * i.e. 192.168.1.0
        * uses binary numbers translated into decimal numbering
        * Public addresses are used by the internet and Private are used by internal networks
    * Private IP Addresses:
        * 10.0.0.0 /8 or 10.0.0.0 to 10.255.255.255
        * 172.16.0.0 /12 or 172.16.0.0 to 172.31.255.255
        * 192.168.0.0 /16 or 192.168.0.0 to 192.168.255.255
    * Bits and Bytes
        * Base -        2   2   2   2   2   2   2   2
        * Exponent -    7   6   5   4   3   2   1   0
        * Position -    128 64  32  16  8   4   2   1
        * Bit -         1   1   0   0   0   0   0   0
        * If all bits are on they total 255
    * To communicate on the internet your system must have 1) a unique IP address 2)
    * IPv6
        * 128 bit address
        * 340 undecillion addresses
    * Router - routes packets based on their IP address ( police officer )
    * Switch - gets packets to their very specific locations using MAC addresses ( mail man )
    * Firewall - network security device that monitors incoming and outgoing network traffic and decides whether to allow or block specific traffic based on a defined set of security rules
    * IDS/IPS - Intrusion Detection/Prevention Systems - Logs, detects, prevents, and monitors traffic.
    * End Devices - computer, server, laptop, smartphone, etc.
    * Cabling
        * 3 main types
            * coaxial cable
            * twisted pair
                * most common
                * 4 pairs of wires twisted to reduce interference
                * unshielded twisted pair (UTP) cables have an rj-45 adapter at the end (ethernet port adapter)
                * Cat5 is a grad or category of UTP and is based on speed
            * fiber optic
                * uses light to transmit
            * console
                * go from router to 'computer'
                * used to configure routers or switches
        * Crossover Cable - brown, white, green, white, blue, white, orange, white
        * Straight-Through Cable - brown, white, orange, white, blue, white, green, white
    * OSI Model - Open System Interconnection model defines a networking framework to implement protocols in seven layers
        * layered model that describes the steps to be used to transfer data over a transmission medium from one networked device to another
        * 7 layers
            * Application (7) - services that are used with end user applications
                * SMTP
            * Presentation (6) - formats the data so that it can be viewed by the user - encrypt and decrypt
                * JPG, GIF, HTTPS, SSL ,TLS
            * Session (5) - establishes/ends connections between two hosts
                * NetBIOS, PPTP
            * Transport (4) - responsible for the transport protocol and error handling
                * TCP, UDP
            * Network (3) - reads the ip address from the packet
                * Routers, Layer 3, Switches
            * Data Link (2) - reads the MAC address from the data packet
                * Switches
            * Physical (1) - send data on to the physical wire
                * Hubs, NICS, Cable
            * All People Seem To Need Data Processing
            * Please Do Not Touch Steve's Pet Alligator
    * Terminology:
        * bit - one digit either 1 or 0
        * byte - 8 bits
        * octet - 8 bit binary number 
        * network address
            * The network address represents all the devices on the same network
            * always the first IP address in the range
                * `192.168.0.0`
            * every machine on the network shares the network address as part of its individual IP address
        * broadcast address
            * always the last address in the range
                * `192.168.255.255`
        * host id - uniquely assigned and identifies a specific device 
        * network id - refers to the network the IP is part of 
            * class A allows 3 bytes for the host ID (networkID.hostID.hostID.hostID) (0.0.0.0/8 to 127.0.0.0/8)
            * class B allows 2 bytes for the host ID (networkID.networkID.hostID.hostID) (128.0.0.0/16 to 191.255.0.0/16)
            * class C allows 1 byte for the host ID (networkID.networkID.networkID.hostID) (192.0.0.0/24 to 223.255.255.0/24)
            * an IP will check if computer is on the same network before sending packets directly or to a router
        * default gateway - IP address of the router that can send data from your network
        * subnet mask - used to deterine what bits identify the network ID and what portion identifies the host ID
        * ANDing - how to figure out network address
            * 1 and 1 = 1
            * 0 and 1 = 0
            * 0 and 0 = 0
            * 1 and 0 = 0
            * Step 1: Convert IP address and subnet mask to binary
            * Step 2: AND the binary addresses together
            * Step 3: Convert network address from binary to decimal
        * DNS - domain name service
        * DHCP - leases out IP addresses for a period of time and returns it when no longer in use
        * PAN
            * personal area network
            * i.e. Bluetooth
            * inside small office or residence
        * HAN
            * home area network
            * single wired internet connection connected to a modem
            * i.e. sending a document to a computer from laptop
        * LAN
            * local area network
            * communicating over ethernet
        * WLAN
            * wireless local area network
        * WAN
            * wide area network
            * used to connect computer that are not located near each other
            * connect many LANs together
        * CAN
            * campus area network
        * MAN
            * metropolitan area network
            * covers a large geographical area and may serve as an ISP
            * usually a city and controlled by one company
        * SAN
            * storage area network
            * separate network for storage devices
        * VPN
            * virtual private network
            * 2 types
                * remote access
                * site-to-site
            * used to provide direct access to a corporate network to a remote user who is not in the geographical coverage of the network
* Commands Linux:
    * `traceroute <webaddress>` - see where packets are getting routed

* Commands Windows:
    * `tracecert <webaddress>` - see where packets are getting routed
---
## Week 0
#### **Day 2 - 10.13.18**

* [Linux Command Line](https://github.com/devleague/linux-all-the-things)
* Install Vim Gnome - `sudo apt install vim-gnome`
* [Vim Cheat Sheet](https://devhints.io/vim)
* [Install Vimium](https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb?hl=en) [GitHub](https://github.com/philc/vimium/blob/master/README.md)
* [TL;DR Command Line Setup](https://github.com/raylee/tldr)
* Learn default settings for users, etc.
---
* [Vim Adventures](https://vim-adventures.com/)
* `vimtutor` Terminal Practice
* Vim Practice
    * `vim first-doc` start new document called first-doc
    * `i` insert editor mode
    * `esc` exit insert mode
    * `:wq` save and quit
    * `h` left
    * `j` down
    * `k` up
    * `l` right
    * `:q!` exit without saving changes
    * `w` skip forward to next word
    * `b` skip back to previous word
---
* Linux Command Line Practice
    * `man hier` see man pages for filesystem hierarchy
    * `vim ~/.bashrc` view/set alias commands
    * `vim ~/.bash_aliases` store alias commands so you don't mess with .bashrc file
        * `export PATH=~/bin:$PATH` add to alias file so that `tldr` works everytime
    * `chmod` users:group:world (example: `chmod -r 777 filename`)
---
#### **Day 1 - 10.9.18**

* Install Toby Chrome extension
    * Better than bookmarks
* Install Wappalyzer Chrome extension
* [Daniel Miessler](https://danielmiessler.com/)
* [How to Build a Successful Career in Cybersecurity](https://danielmiessler.com/blog/build-successful-infosec-career/)
* [Hacker One Newsletter](https://www.hackerone.com/zerodaily)
* [Professor Messer](https://www.youtube.com/user/professormesser)
    * [Security+](https://www.professormesser.com/security-plus/sy0-501/sy0-501-training-course/)
* [Cybrary](https://www.cybrary.it/)
* [VulnHub](vulnhub.com)
* Snapshots
    * allow us to roll back to previous moments in time
* Virtual Box - Global Tools
    * Virtual Media Manager - Hard disks
        * check how much disk space is being used and by what
    * Virtual Media Manager - Optical disks
        * .iso files are like the disks that used to come with computers to install operating systems
        * once installed you can release and delete
        * ! means it is 'problematic'
* Virtual Box - Guest Additions
    * use for labs not in real world
    * Devices
    * Insert Guest Additions CD image...
    * Cancel VBox_GAs
    * open terminal
        * ls /media/cdrom (list what is in directory)
        * pwd (print working directory)
        * cd /media/cdrom (change directories)
        * cp VBoxLinuxAdditions.run ~/Desktop/
        * cd ~/Desktop/
        * ls -l (check permissions)
        * ./VBoxLinuxAdditions.run (run the file)
        * cat /etc/apt/sources.list
        * deb https://http.kali.org/kali kali-rolling main non-free contrib - must be in /etc/apt/sources.list
        * apt-get update (checks ^^ for any updates)
        * apt-get upgrade (installs everything found during apt-get update)
        * Y
        * apt update
        * apt upgrade
        * Y
        * q
        * No
        * No
        * N
        * reboot
        * uname -r (check version of kernel)
        * apt install linux-headers-4.18.0-kali1-all-amd64
        * Y
        * ./VBoxLinuxAdditions.run (run the file)
        * reboot
        * enable bidirectional shared clipboard and drag'n'drop in virtualbox settings

* Productivity Tips:
    * click left cmd key to search
---
#### **Day 0 - 10.8.18**

* Setup virtual machines 
    * [my in class notes](https://gist.github.com/jocelynsaysrawr/14208581977d64193c4be8bcbeeda8af)
    * [jaywon notes](https://github.com/devleague/virtual-box-setup-and-overview/blob/master/VirtualBox%20Setup%20%26%20Overview.ipynb)
