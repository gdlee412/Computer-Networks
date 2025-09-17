# Application Layer: Part 2

## Overview

-   Domain Name System (DNS)
-   P2P applications
-   video streaming and content distribution networks
-   socket programming with UDP and TCP

## Domain Name System (DNS)

-   People: many identifiers:
    -   SSN (Social Security Number). name, passport #
-   Internet hosts, routers:

    -   IP address (32 bit) - used for addressing datagrams
    -   "name" - cs.umass,edu & www.skku.edu - used by humans

-   `distributed database` implemented in hierarchy of many `name servers`
-   `application-layer protocol`: hosts, name servers communicate to `resolve` names (address/name translation)
    -   note: core Internet function, `implemented as application-layer protocol`
-   complexity at network's "edge"

### DNS: Services, structure

#### Services

-   Hostname to IP address translation
-   Host aliasing
    -   Canonical, alias names
-   Mail server aliasing
-   Load distribution
    -   replicated Web servers: many IP addresses correspond to one DNS name
-   Q: Why not centralize DNS?
    -   single point of failure
    -   traffic volume
    -   distant centralized database
    -   maintenance
-   A: doesn't scale!
    -   Comcast DNS servers alone:
        -   600B (billion) DNS queries per day

### DNS: a distributed, hierarchical database

-   `Root`: Root DNS servers
    -   `Top-Level Domain (TLD)`: .com/.org/.edu DNS servers
        -   `Authoritative`: yahoo.com/pbs.org/umass.edu DNS servers
-   Client wants IP address for www.amazon.com; 1st approximation:
    -   client queries root server to find .com DNS server
    -   client queries .com DNS server to get amazon.com DNS server
    -   client queries amazon.com DNS server to get the IP address for www.amazon.com

### DNS: root name servers

-   official, contact-of-last-resort by name servers that can not resolve name
-   `incredibly important` Internet function
    -   Internet couldn't function without it!
    -   DNSSEC - provides security (authentication and message integrity)
-   ICANN (Internet Corporation for Assigned Names and Numbers) manages root DNS domain
-   13 logical root name "servers":
    -   worldwide each "server" is replicated many times (~200 servers in US, ~1000 servers over the world)

### TLD: authoritative servers

-   Top-Level Domain(TLD) servers:

    -   responsible for .com, .org, .net, .edu, .aero, .jobs, .museums, and all top-level country domains, e.g.: .cn, .uk, .fr, .ca, .ja, .kr
    -   Network Solutions: authoritative registry for .com, .net TLD
    -   Education Organizations: .edu TLD

-   Authoritative DNS servers:
    -   organization's own DNS server(s), providing authoritative hostname to IP mapping for organization's named hosts
    -   can be maintained by organization or service provider

### Local DNS name servers

-   does not strictly belong to hierarchy
-   each ISP (residential ISP, company, university) has one
    -   also called "default name server"
-   when host makes DNS query, query is sent to its local DNS server
    -   has local cache of recent name-to-address translation pairs (but may be out of date!)
    -   acts as proxy, forwards query into hierarchy

### DNS name resolution: iterated query

-   Iterated query:

    -   contacted server replies with name of server to contact
    -   "I don't know this name, but ask this server"

-   Example: host at engineering.nyu.edu wants IP address for gaia.cs.umass.edu

1. requesting host at _engineering.nyu.edu_ sends query message to local DNS server _dns.nyu.edu_
2. local DNS server _dns.nyu.edu_ contacts root DNS server
3. root DNS server responds to the request of the .edu address
4. local DNS server _dns.nye.edu_ contacts TLD DNS server to get the umass.edu address
5. TLD DNS server responds to the request for umass.edu address
6. local DNS server _dns.nye.edu_ contacts authorative DNS server _dns.cs.umass.edu_ to get the _gaia.cs.umass.edu_ address
7. gaia.cs.umass.edu IP address returned to the local DNS server
8. local DNS server returns the gaia.cs.umass.edu IP address to the host engineering.nyu.edu

### DNS name resolution: recursive query

-   Recursive query:

    -   puts burden of name resolution on contacted name server `(especially root DNS server)`
    -   heavy load at upper levels of hierarchy?

-   Example: host at engineering.nyu.edu wants IP address for gaia.cs.umass.edu

1. requesting host at _engineering.nyu.edu_ sends query message to local DNS server _dns.nyu.edu_
2. local DNS server _dns.nyu.edu_ sends query message to root DNS server
3. root DNS server directly contacts TLD DNS server to get cs.umass.edu domain
4. TLD DNS server directly contacts authoritative DNS server to get address of gaia.cs.umass.edu
5. authoritative DNS server returns the IP address of gaia.cs.umass.edu to TLD DNS server
6. TLD DNS server returns the IP address to the root DNS server
7. root DNS server returns the IP address to the local DNS server
8. local DNS server returns the IP address to the requesting host

### Caching, Updating DNS Records

-   once (any) name server learns mapping, it `caches` mapping
    -   cache entries timeout (disappear) after some time (TTL: Time-To-Live)
    -   TLD servers typically cached in local name servers
        -   thus root name servers not often visited
-   cached entries may be `out-of-date` (best-effort name-to-address translation!)
    -   if name hosts changes IP address, it may not be known Internet-wide until all TTLs expire!
-   update/notify mechanisms proposed IETF standard
    -   RFC 2136: Dynamic Updates in the Domain Name System (DNS Update)
        -   https://datatracker.ietf.org/doc/html/rfc2136

#### DNS Records

-   DNS: distributed database storing resource records (RR)

    -   RR format: (name, value, type, ttl)

-   type=A (IP address)
    -   name = `hostname`
    -   value = `IP address`
-   type=NS (Name Server)
    -   name = `domain` (e.g., foo.com)
    -   value = `hostname of authoritative name server for this domain`
-   type=CNAME (Canonical Name)
    -   name = `alias name for some "canonical" (the real) name`
    -   www.ibm.com is really servereast.backup2.ibm.com
    -   value = `canonical name`
-   type=MX (Mailserver's Name)
    -   value = `name of mailserver associated with *name*`

#### DNS protocol Messages

-   DNS query and reply messages, both have same format:
-   message header:
    -   identification: 16 bit # for query, reply to query uses same #
    -   flags:
        -   query or reply
        -   recursion desired
        -   recursion available
        -   reply is authoritative
-   name, type fields for a query
-   RRs in response to query
-   records for authoritative servers
-   additional "helpful" info that may be used

#### Inserting records into DNS

-   example: new startup "Network Utopia"
-   register name networkutopia.com at `DNS registrar` (e.g., Network Solutions)
    -   provide names, IP addresses of authoritative name server (primary and secondary)
    -   registrar inserts NS, a RRs into .com TLD server:
        -   (networkutopica.com, dns1.networkutopia.com, NS)
        -   (dns1.networkutopia.com, 212.212.212.1, A)
-   create authoritative server locally with IP address 212.212.212.1
    -   type A record for www.networkutopia.com
        (www.networkutopia.com, 212.212.212.1, A)
    -   type MX record for networkutopia.com

### DNS security

-   DDoS (Distributed Denial of Service) attacks
    -   bombard root servers with traffic
        -   not successful to date
        -   traffic filtering
        -   local DNS servers cache IPs of TLD servers, allowing root server bypass
    -   bombard TLD servers
        -   potentially more dangerous
    -   RFC 4033: DNS Security Introduction and Requirements (DNSSEC)
        -   https://datatracker.ietf.org/doc/html/rfc4033
-   Redirect attacks
    -   man-in-middle
        -   intercept DNS queries
    -   DNS poisoning
        -   send bogus replies to DNS server, which caches
-   Exploit DNS for DDoS
    -   send queries with spoofed source address: target IP
    -   requires amplification

## P2P applications

### Peer-to-peer (P2P) architecture

-   _no_ always-on server
-   arbitrary end systems directly communicate
-   peers request service from other peers, provide service in return to other peers
    -   `self scalability - new peers bring new service capacity, and new service demands`
-   peers are intermittently conntected and change IP addresses
    -   complex IP addresses
-   examples: P2P file sharing (BitTorrent), streaming (KanKan), VoIP (Skype)

### File distribution: client-server vs P2P

-   how much time to distribute file (size _F_) from one server to _N_ peers
    -   peer upload/download capacity is limited resource
-   $u_s$: server upload capacity
-   $d_i$: peer _i_ download capacity
-   $u_i$: peer _i_ upload capacity

#### File distribution time: client-server

-   `server transmission`: must sequentially send (upload) _N_ file copies:
    -   time to send one copy: $\frac{F}{u_s}$
    -   time to send _N_ copies: $\frac{NF}{u_s}$
-   client: each client must download file copy
    -   $d_{min}$ = min client download rate = min {$d_1, d_2, ..., d_N$}
    -   min client download time: $\frac{F}{d_{min}}$
-   <u>A lower bound</u> on the minimum distribution time for the `client-server architecture`

-   time to distribute _F_ to _N_ clients using client-server approach

$$D_{c-s} \geq max\{\frac{NF}{u_s}, \frac{F}{d_{min}}\} $$

-   increases linearly in N

#### File distribution time: P2P

-   `server transmission`: must upload at least one copy:
    -   time to send one copy: $\frac{F}{u_s}$
-   `client`: each client must download file copy:
    -   min client download time: $\frac{F}{d_{min}}$
-   `clients`: as aggregate, the system of server and _N_ clients upload _NF_ bits:

    -   max upload rate (limiting max download rate) is $u_s + \sum{u_i}$

-   <u>A lower bound</u> on the minimum distribution time for the `P2P architecture`

-   time to distribute _F_ to _N_ clients using P2P approach

$$D_{P2P} > max\{\frac{F}{u_s}, \frac{F}{d_{min}}, \frac{NF}{(u_s + \sum{u_i})}\}$$

-   increases linearly in _N_
-   but so does $\sum{u_i}$, as each peer brings service capacity

#### Client server vs. P2P: example

-   client upload rate = u
-   $\frac{F}{u}$ = 1 hour
-   $u_s$ = 10u
-   $d_{min} \geq u_s$
-   Note: Peer download rates (i.e., $d_{min}$) are set large enough so as not to have an effect on minimum distribution time in P2P architecture

-   P2P: logarithmic
-   Client server: linear
-   P2P wins

### P2P file distribution: BitTorrent

-   BitTorrent is a popular P2P protocol for file distribution
-   file is divided into 256Kb chunks
-   peers in torrent send/receive file chunks

-   `tracker`: tracks peers participating in torrent
-   `torrent`: group of peers exchanging chunks of a file

-   Example:
-   Alice arrives... obtains list of peers from tracker
-   begins exchanging file chunks with peers in torrent

-   peer joining torrent:
    -   has no chunks, but will accumulate them over time rom other peers
    -   registers with tracker to get list of peers, connects to subset of peers ("neighbors")
-   while downloading, peer uploads chunks to other peers
-   peer may change peers with whom it exchanges chunks
-   `churn`: peers may come and go
-   once peer has entire file, it may (selfishly) leave or (altruistically) remain in torrent

#### BitTorrent: requesting, sending file chunks

-   Requesting chunks: equalizing the number of chunks in torrent
    -   at any given time, different peers have different subsets of file chunks
    -   periodically, Alice asks each peer for list of chunks that they have
    -   Alice requests missing chunks from peers, "rarest first"
    -   "Rarest first" is to request the rarest chunks first from her neighbors for quick redistribution of the rare chunks
-   Sending chunks: tit-for-tat (trading incentive mechanism)
    -   Alice sends chunks to those four peers currently sending her chunks at _highest rate_ (as clever trading algorithm)
        -   other peers are choked by Alice (do not receive chunks from her)
        -   re-evaluate top 4 every 10 secs
    -   every 30 secs: randomly select another peer as a _new trading partner_, starts sending it chunks
        -   "optimistically unchoke" this peer
        -   newly chosen peer may join top 4

##### BitTorrent: tit-for-tat

1. Alice "optimistically unchokes" Bob
2. Alice becomes one of Bob's top-four providers; Bob reciprocaes
3. Bob becomes one of Alice's top-four providers

-   `higher upload rate`: find better trading partners, get file faster!

## Video streaming and content distribution networks (CDN)

### Context

-   Stream video traffic: major consumer of Internet bandwidth
    -   Netflix, YouTube, Amazon Prime: 80% of residential ISP traffic (2020)
-   challenge: scale - how to reach ~1B users?
    -   single mega-video server won't work (why?)
-   challenge: heterogeneity
    -   different users have different capabilities (e.g., wired versus mobile; bandwidth rich versus bandwidth poor)
-   `solution: distributed, application-level infrastructure`

### Multimedia: video

-   video: sequence of images displayed at constant rate
    -   e.g., 24 images/sec
-   digital image: array of pixels
    -   each pixel represented by bits
-   coding: use redundancy _within_ and _between_ images to decrease # bits used to encode image
    -   spatial (within image)
        -   instead of sending _N_ values of same color (all purple), send only two values: color value (purple) and number of repeated values (N)
    -   temporal (from one image to next)
        -   instead of sending complete frame at i+1, send only differences from frame i
-   `CBR (constant bit rate)`: video encoding rate fixed
-   `VBR (variable bit rate)`: video encoding rate changes as amount of spatial, temporal coding changes
-   examples:
    -   MPEG 1 (CD-ROM) 1.5Mbps
    -   MPEG 2 (DVD) 3-6Mbps
    -   MPEG 4 (often used in Internet, 64Kbps - 12Mbps)

### Streaming stored video

-   simple scenario:
    -   video server (stored video) -> Internet -> client
-   Main challenges:
    -   server-to-client bandwidth will _vary_ over time, with changing network congestion levels (in house, in access network, in network core, at video server)
    -   packet loss and delay due to congestion will delay playout, or result in poor video quality

1. video recorded (e.g., 30 frames/sec)
2. video sent
3. video received, played out at client (30fps)

-   `streaming`: client plays out early part of video, while server still sending later part of video

#### Challenges

-   `continuous playout constraint`: once client playout begins, playback must match original timing
    -   ...but `network delays are variable` (jitter), so will need `client-side buffer` to match playout requirements
-   client interactivity: pause, fast-forward, rewind, jump through video
-   video packets may be lost, retransmitted

#### Streaming stored video: playout buffering

1. constant bit rate video transmission
2. a network delay (variable) disrupts the client video reception
3. client playout delay (buffer) occurs to provide a constant bit rate video playout at the client

-   `client-side buffering and playout delay`: compensate for network-added delay, delay jitter

### Streaming multimedia: DASH

-   Dynamic Adaptive Streaming over HTTP (DASH)
-   server:
    -   divides video file into multiple chunks
    -   each chunk stored, encoded at different rates
    -   `manifest file`: provides URLs for different chunks
-   client:
    -   periodically measures server-to-client bandwidth
    -   consulting manifest, requests one chunk at a time
        -   chooses maximum coding rate sustainable given current bandwidth
        -   can choose different coding rates at different points in time (depending on available bandwidth at time)
    -   Thus, DASH allows the client to freely switch among different quality levels
-   `"intelligence" at client`: client determines
    -   **when** to request chunk (so that buffer starvation, or overflow does not occur)
    -   **what encoding rate** to request (higher quality when more bandwidth available)
    -   **where** to request chunk (can request from URL server that is "close" to client or has high available bandwidth)
-   `Streaming video` = encoding + DASH + playout buffering

### Content distribution networks (CDNs)

-   challenge: how to stream content (selected from millions of videos) to hundreds of thousands of _simultaneous_ users?
-   option 1: single, large "mega-server"
    -   single point of failure
    -   point of network congestion
    -   long path to distant clients
    -   multiple copies of video sent over outgoing link
    -   ...quite simply: this solution `doesn't scale`
-   option 2: store/server multiple copies of videos at multiple geographically distributed sites (CDN)
    -   `enter deep`: push CDN servers deep into many access networks
        -   Akamai: 240,000 servers deployed in more that 120 conutries (2015)
    -   `bring home`: smaller number (10's) of larger clusters in POPs near (but not within) access networks
        -   used by Limelight

#### CDNs

-   CDN: stores copies of content at CDN nodes
    -   e.g., Netflix stores copies of MadMen
-   subscriber requests content from CDN
    -   directed to nearby copy, retrieves content
    -   may choose different copy if network path congested
-   OTT : "Over The Top"
-   Internet host-host communication as a service
-   OTT challenges: coping with a congested Internet
    -   from which CDN node to retrieve content?
    -   viewer behavior in presence of congestion?
    -   what content to place in which CDN node?

#### CDN content access: a closer look

-   Bob (client) requests video http://netcinema.com/6Y7B23V
    -   video stored in CDN at http://KingCDN.com/NetC6YB23V

1. Bob gets URL for video http://netcinema.com/6Y7B23V from netcinema.com web page
2. resolve http://netcinema.com/6Y7B23V via Bob's local
3. netcinema's DNS returns CNAME for http://KingCDN.com/NetC6YB23V
4. Bob's local DNS server requests for IP from KingCDN authoritative DNS
5. IP address delivered to Bob (client) from local DNS server
6. request video from KingCDN server, streamed via HTTP

#### Case study: Netflix

-   Netflix registration, accounting servers
-   Amazon cloud
    -   upload copies of multiple versions of video to CDN servers

1. Bob manages Netflix account
2. Bob browses Netflix video (Amazon cloud)
3. Manifest file, requested returned for specific video (Amazon cloud)
4. DASH server selected, contacted, streaming begins

## socket programming with UDP and TCP

### Socket programming

-   goal: learn how to build client/server applications that communicate using sockets
-   socket: door between application process and end-end-transport protocol
-   two socket types for two transport services:
    -   UDP: unreliable datagram
    -   TCP: reliable, byte stream-oriented
-   Application Example:
    1. client reads a line of characters (data) from its keyboard and sends data to server
    2. server receives the data and converts characters to uppercase
    3. server sends modified data to client
    4. client receives modified data and displays line on its screen

### Socket programming with UDP

-   UDP: no "connection" between client & server
-   no handshaking before sending data
-   sender explicitly attaches IP destination address and port # to each packet
-   receiver extracts sender IP address and port # from received packet
-   UDP: transmitted data may be lost or received out-of-order
-   Application viewpoint:
    -   UDP provides _unreliable_ transfer of groups of bytes ("datagrams") between client and server

#### Client/server socket interaction: UDP

1. server (running on serverIP)
    - create socket, port=x:
        - serverSocket = socket(AF_INET, SOCK_DGRAM)
2. client
    - create socket:
        - clientSocket = socket(AF_INET, SOCK_DGRAM)
    - Create datagram with server IP and port=x; send datagram via clientSocket
3. server
    - read datagram from serverSocket
    - write reply to serverSocket specifying client address, port number
4. client
    - read datagram from clientSocket
    - close Socket

#### Example app: UDP client

-   Python UDPClient

```python
#Include Python's socket library
from socket import *
serverName - 'hostname'
serverPort = 12000
#create UDP socket for server
clientSocket = socket(AF_INET, SOCK_DGRAM)
# get user keyboard input
message = raw_input('Input lowercase sentence:')
#attach server name, port# to message; send into socket
clientSocket.sendto(message.encode(), (serverName, serverPort))
#read reply characters from socket into string
modifiedMessage, serverAddress = clientSocket.recvfrom(2048)
#print out received string and close socket
print modifiedMessage.decode()
clientSocket.close()

```

-   Python UDPServer

```python
from socket import *
serverPort = 12000
# Create UDP socket
serverSocket = socket(AF_INET, SOCK_DGRAM)
# bind socket to local port number 12000
serverSocket.bind(('',serverPort))
print("The server is ready to receive")
# loop forever
while True:
    # read from UDP socket into message, getting client's address (client IP and port)
    message, clientAddress = serverSocket.recvfrom(2048)
    modified Message = message.decode().upper()
    #send uppercase string back to this client
    serverSocket.sendto(modifiedMessage.encode(), clientAddress)
```

### Socket programming with TCP

-   Client must contact server
    -   server process must first be running
    -   server must have created socket (door) that welcomes client's contact
-   Client contacts server by:
    -   creating TCP socket, specifying IP address, port number of server process
    -   **when client creates socket**: client TCP establishes connection to server TCP
-   when contacted by client, **server TCP creates new socket** for server process to communicate with that particular client
    -   allows server to talk with multiple clients
    -   source port numbers used to distinguish clients (Chapter 3)
-   Application viewpoint
    -   TCP provides reliable, in-order byte-stream transfer ("pipe") between client and server

#### Client/server socket interaction: TCP

1. server(running on hostid)
    - create socket, port=x, for incoming request:
        - serverSocket = socket()
    - wait for incoming connection request
        - connectionSocket=serverSocket.accept()
2. client
    - create socket connect to hostid, port=x
        - clientSocket = socket()
3. TCP connection setup
4. client
    - send request using clientSocket
5. server
    - read request from connectionSocket
    - write reply to connectionSocket
6. client
    - read reply from clientSocket
7. close connectionSocket & clientSocket

#### Example app: TCP client

-   Python TCPClient

```python
from socket import *
serverName = 'servername'
serverPort = 12000
#create TCP socket for server, remote port 12000
clientSocket = socket(AF_INET, SOCK_STREAM)
clientSocket.connect((serverName, serverPort))
sentence = raw_input('input lowercase sentence.')
clientSocket.send(sentence.encode())
# No need to attach server name, port
modifiedSentence = clientSocket.recv(1024)
print('From Server:', modifiedSentence.decode())
clientSocket.close()
```

-   Python TCPServer

```python
from socket import *
serverPort = 12000
# create TCP welcoming socket
serverSocket = socket(AF_INET, SOCK_STREAM)
serverSocket.bind(('', serverPort))
#server begins listening for incoming TCP requests
serverSocket.listen(1)
print('The server is ready to receive')
# loop forever
while True:
    #server waits on accept() for incoming requests, new socket created on return
    connectionSocket, addr = serverSocket.accept()
    #read bytes from socket(but not address as in UDP)
    sentence = connectionSocket.recv(1024).decode()
    capitalizedSentence = sentence.upper()
    connectionSocket.send(capitalizedSentence.encode())
    #close connection to this client(but not welcoming socket)
    connectionSocket.close()
```

## Summary
```
our study of network application layer is now complete
```
- application architectures
    - client-server
    - P2P
- application service requirements:
    - reliability, bandwidth, delay
- Internet transport service model
    - connection-oriented, reliable: TCP
    - unreliable, datagrams: UDP
- specific protocols:
    - HTTP
    - SMTP, IMAP
    - DNS
    - P2P: BitTorrent
- video streaming, CDNs
- socket programming: TCP, UDP sockets
```
Most importantly: learned about protocols!
```
- typical request/reply message exchange
    - client request info or service
    - server responds with data, status code
- message formats:
    - `headers`: fields giving info about data
    - `data`: info(payload) being communicated
- `important themes`:
    - centralized vs. decentralized
    - stateless vs. stateful
    - scalability
    - reliable vs. unreliable message transfer
    - "complexity at network edge"

## DNS Root Servers
- URL: https://www.iana.org/domains/root/servers

## BitTorrent
- URL: https://www.bittorrent.com/