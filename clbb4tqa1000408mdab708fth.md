# Networking Protocols

Protocols are a set of well-defined rules that are followed by nodes on the network for a variety of reasons. Each protocol is strictly defined and used for the use cases it is designed for. They provide a standardised way for data transfer and communication. Most Common protocols on the web include HTTP, TCP, IP etc. Let's learn about them in detail.

#### BGP

*   Abbr: Border Gateway Protocol
    
*   This protocol is used by routers to find the most optimal path to forward traffic.
    
*   This routing protocol controls how packets pass through routers in an autonomous system (AS) -- one or multiple networks run by a single organization or provider -- and connect to different networks.
    
*   BGP can connect endpoints on a LAN to one another, and it can connect endpoints in different LANs to one another over the internet.
    
*   Routers share data among themselves and keep themselves informed about the traffic congestion.
    
*   External BGP directs network traffic from various ASes to the internet and vice versa. Additionally, internal BGP directs network traffic between endpoints within a single AS.
    

#### ARP

*   Abbr: Address Resolution Protocol
    
*   Discovers the hardware address of a node with a given IP address.
    
*   ARP translates IP addresses to Media Access Control (MAC) addresses and vice versa so LAN endpoints can communicate with one another.
    
*   ARP is necessary because IP and MAC addresses are different lengths: IP version 4 (IPv4) addresses are 32 bits long, IPv6 addresses are 128 bits and MAC addresses -- a device's physical hardware number -- are 12 hexadecimal digits split into six pairs. Translations must occur for proper device communication.
    
*   ARP isn't required every time devices attempt to communicate because the LAN's host stores the translated addresses in its ARP cache, so this process is mainly used when new devices join the network.
    

#### IGPs

*   Abbr: Interior Gateway Protocols
    
*   These are of two types: "Link State" and "Distance Vector" routing protocols
    
*   Used by routers to share information within a single autonomous system. (under the control of a single network operator)
    
*   Distance vector protocols are old standards. A router with this protocol sends the list of hops needed for the destination to reach each neighbouring router directly connected to it
    
*   Doesn't allow routers to have much information about the state of the world outside of their direct neighbours.
    
*   Routers may slow down to react to changes in the network
    
*   Link state protocols allow routers to advertise the state of the link of each of the interfaces (direct connection to the network or connections to each router) making the connection about each router available to each other router in the autonomous system. with detailed large information about all other routers.
    
*   The sender router applies an algorithm against it to find the shortest path and update its routing tables.
    
*   This requires more memory and processing power.
    

#### EGPs

*   Abbr: Exterior Gateway Protocols
    
*   These are used for communication between routers representing edges of an autonomous system to share information across different autonomous systems/organisations.
    
*   ASNs are numbers assigned to individual autonomous systems. These are 32-bit numbers normally referred to as single decimal numbers because ASN never needs to change to prevent more networks/hosts
    
*   The core internet routing tables need to be updated to know what ASN represents. It isn't looked at by humans more often. So, this doesn't need to be in readable blocks.
    

#### TCP

*   Abbr: Transmission Control Protocol
    
*   It is connection-oriented
    
*   It establishes a connection and ensures all data is properly transmitted
    
*   By ACK flags, both ends confirm the sent and received packets/information.
    
*   Pure congestion may cause the router to drop your traffic for more important ones, or cut a cable connecting ISPs. This problem is solved using the ACK (acknowledgement) flags. This is helpful in data recovery or if the data is discarded in transit.
    
*   This is used where reliable delivery is needed. For ex- Email, Websites, File Sharing, FTP servers, etc.
    

#### UDP

*   Abbr: User Datagram Protocol
    
*   Its work is the same as TCP but it doesn't ensure the reliable delivery of packets.
    
*   It also doesn't rely on connections (connection-less protocol) or ACK flags (no acknowledgements sent/received).
    
*   This removes a lot of extra overhead and is helpful in a variety of use cases.
    
*   This shortens the header sent/received to a very large extent and thus the data to be transferred is minimized. This makes the content load faster and provides a good end-user-experience.
    
*   It is simpler than TCP, just the destination port is set and the data/packet is sent.
    
*   This is used in places for ex- Video/Audio streaming services (like youtube, Vimeo, etc, as it doesn't matter if certain frames of the video are not sent/received, the end-user won't experience this in playing video until the loss is very large).
    

#### IP

*   Abbr: Internet Protocol
    
*   IP functions similarly to a postal service.
    
*   When users send and receive data from their device, the data gets spliced into packets, which are like letters with two IP addresses: one for the sender and one for the recipient.
    
*   After the packet leaves the sender, it goes to a gateway, like a post office, that directs it in the proper direction. Packets continue to travel through gateways until they reach their destinations.
    
*   IP is commonly paired with TCP to form TCP/IP, the overall internet protocol suite.  
    Together, IP sends packets to their destinations, and TCP arranges the packets in the correct order, as IP sometimes sends packets out of order to ensure the packets travel the fastest ways.
    

#### HTTP

*   Abbr: HyperText Transfer Protocol
    
*   By default, it used PORT 80 (for unencrypted traffic)
    
*   HTTP is a file-sharing protocol that runs over TCP/IP, although HTTP primarily works over web browsers and is commonly recognizable for most users.  
    When a user enters a website domain and aims to access it, HTTP provides the access.
    
*   HTTP connects to the domain's server and requests the site's HTML, which is the code that structures and displays the page's design.
    
*   Another form of HTTP is HTTPS, which stands for HTTP over Secure Sockets Layer or HTTP Secure.
    
*   HTTPS can encrypt a user's HTTP requests and web pages. This provides more security to users and can prevent common cybersecurity threats, such as man-in-the-middle attacks.
    

#### SMTP

*   Abbr: Simple Mail transfer protocol
    
*   SMTP is the most popular email protocol, is part of the TCP/IP suite and controls how email clients send users' email messages.
    
*   Email servers use SMTP to send email messages from the client to the email server to the receiving email server.
    
*   However, SMTP doesn't control how email clients receive messages -- just how clients send messages.
    
*   SMTP requires other protocols to ensure email messages are sent and received properly.
    
*   SMTP can work with Post Office Protocol 3 or Internet Message Access Protocol, which controls how an email server receives email messages.
    

#### FTP

*   Abbr: File Transfer Protocol
    
*   FTP is a client-server protocol, with which a client requests a file and the server supplies it. FTP runs over TCP/IP -- a suite of communications protocols -- and requires a command channel and a data channel to communicate and exchange files, respectively.
    
*   Clients request files through the command channel and receive access to download, edit and copy the file, among other actions, through the data channel.
    
*   FTP has grown less popular as most systems began to use HTTP for file sharing. However, FTP is a common network protocol for more private file sharing, such as in banking.
    

#### Telnet

*   Telnet is designed for remote connectivity, and it establishes connections between a remote endpoint and a host machine to enable a remote session.
    
*   Telnet prompts the user at the remote endpoint to log on and, once authenticated, gives the endpoint access to network resources and data at the host computer.
    
*   Telnet has existed since the 1960s and was arguably the first draft of the modern internet. However, Telnet lacks the sophisticated security protections required for modern communications and technology, so it isn't commonly used anymore.
    

#### OSPF

*   Abbr: Open Shortest Path First
    
*   OSPF works with IP in sending packets to their destinations.
    
*   IP aims to send packets on the quickest route possible, which OSPF is designed to accomplish.
    
*   OSPF opens the shortest, or quickest, path first for packets.  
    It also updates routing tables -- a set of rules that control where packets travel -- and alerts routers of changes to the routing table or network when a change occurs.
    
*   OSPF is similar to and supports Routing Information Protocol -- which directs traffic based on the number of hops it must take along a route -- and it has also replaced RIP in many networks. OSPF was developed as a more streamlined and scalable alternative to RIP.
    
*   For example, RIP sends updated routing tables out every 30 seconds, while OSPF sends updates only when necessary and makes updates to the particular part of the table where the change occurred.
    

#### DHCP

*   Abbr: Dynamic Host Configuration Protocol  
    It is an application layer protocol that automates the configuration process of hosts on a network
    
*   Used in assigning/providing an IP address to any new host/node connected to the network.
    
*   May be configured to use one of the three techniques: "Dynamic Allocation", "Automatic Allocation" or "Fixed Allocation".
    
*   When a device connects to a network, a DHCP handshake takes place, where the device and DHCP server communicate. The device establishes a connection; the server receives it and provides available IP addresses; the device requests an IP address, and the server confirms it to complete the process.
    
*   By default, it used PORT 67
    

#### NTP

*   Abbr: Network Time Protocol
    
*   Used to keep all computers/nodes on a network synced in time.
    

#### ICMP

*   Abbr: Internet control message protocol
    
*   This protocol is used to communicate issues.
    
*   Used by router/remote hosts for error detection
    
*   The data/payload in an ICMP request contains the entire IP header and the first 8-bytes of the data payload section of the offending packet
    
*   ICMP PING lets send special ICMP echo requests. If the destination is fine and able to communicate, it sends back an ICMP echo reply.
    
*   you can try this in your terminal also. The syntax is `ping <destination> <IP/FQDN>`. For example: `ping www.google.com`. In windows, it sends 4 echo requests by default while in Linux/Unix-based OSs, it sends requests continuously until it is stopped/terminated.