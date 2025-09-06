# Introduction to Computer Networks

## Chapter Goal
- Get "feel," "big picture," introduction to terminology
    - more depth, detail *later* in course
- Approach:
    - Use internet as example

## Overview / Roadmap
- What *is* the Internet?
- What *is* a protocol?
- ``Network edge``: hosts, access network, physical media
- ``Network core``: packet / circuit switching, internet structure
- ``Performance``: loss, delay, throughput
- Security
- Protocol layers, service models
- History

## The Internet: a "nuts and bolts" view
- Billions of connected computing *``devices``*:
    - *``hosts`` = end systems*
    - running *network ``apps``* at Internet's "edge"
- ``Packet switches``: forward packets (chunks of data)
    = *routers, switches*
- ``Communication links``
    - fiber, copper, radio, satellite
    - transmission rate: *bandwidth*
- ``Networks``
    - collections of devices, routers, links: managed by an organization 
- ``Internet: "network of networks"``
    - Interconnected ISPs (Internet Service Providers)
- ``protocols`` are *everywhere*
    - control sending and receiving of messages
    - e.g., HTTP (Web), streaming video, Skype, TCP, IP, Wifi, 4G / 5G, Ethernet
- ``Internet standards``
    - RFC: Request for Comments
    - IETF: Internet Engineering Task Force
    - IESG: Internet Engineering Steering Group
## The Internet: a "service" view
- ``Infrastructure`` that provides services to applications:
    - Web, streaming video, multimedia teleconferencing, email, games, e-commerce, social media, interconnected appliances, etc.
- provides ``programming interface`` to distributed applications:
    - "hooks" allowing sending / receiving apps to "connect" to, use Internet transport service
    - provides service options, analogous to postal service

## What's a protocol?

| ``Human Protocols`` | ``Network Protocols`` |
| --------------- | ----------------- |
| "What's the time?" | computers (devices) rather than humans |
| "I have a question" | all communication activity in Internet governed by protocols |
| introductions | |
- specific messages sent
- specific actions taken when message received, or other events

```
``Protocols`` define the ``format``, ``order`` of ``messages sent and received`` among network entities, and ``actions taken`` on msg transmission, receipt
```

## A closer look at Internet structure
- ``Network edge``
    - hosts: clients and servers
    - servers often in data centers
- ``Access networks, physical media``
    - wired, wireless communication links
- ``Network core``
    - interconnected routers
    - network of networks

## Access networks and physical media
### How to connect end systems to edge router?
- residential access nets
- institutional access networks (school, company)
- mobile access networks (WiFi, 4G/5G)

### What to look for:
- transmission rate (bits per second) of access network?
- shared or dedicated access among users?

### Access networks: cable-based access
- ``frequency division multiplexing (FDM)``: different channels transmitted in different frequency bands
- ``HFC: hybrid fiber coax``
    - asymmetric: up to 40 Mbps - 1.2 Gbps downstream transmission rate, 30 - 100 Mbps upstream transmission rate
    - ``network`` of cable, fiber attaches homes to ISP router
        - homes *``share access network``* to cable headend

### Access networks: digital subscriber line (DSL)
- use *``existing``* telephone line to central office DSLAM
    - data over DSL phone line goes to Internet
    - voice over DSL phone line goes to telephone net
- 24 - 52 Mbps dedicated downstream transmission rate
- 3.5 - 16 Mbps dedicated upstream transmission rate

### Wireless access networks
- Shared *wireless* access network connects end system to router
    - via base station aka "access point (AP)"
- ``Wireless local area networks (WLANs)``
    - typically within or around building (~100ft)
    - 802.llb/g/n (WiFi): 11, 54, 450 Mbps transmission rate
- ``Wide-area cellular access networks``
    - provided by mobile, cellular network operator (10's km)
    - 4G-LTE cellular networks: 10's Mbps
        - LTE: Long Term Evolution
    - 5G- cellular networks: 100's Mbps

### Access networks: enterprise networks
- companies, universities, etc.
- mix of wired, wireless link technologies, connecting a mix of switches and routers
    - Ethernet: wired access at 100 Mbps, 1Gbps, 10Gbps
    - Wifi: wireless access points at 11, 54, 450 Mbps

### Host: sends *packets* of data
- host sending function
    - takes application message
    - breaks into smaller chunks, known as *``packets``*, of length *L* bits
    - transmits packet into access network at *``transmission rate R``*
        - link transmission rate, aka link *``capacity``*, *``aka link bandwidth``*

```
packet transmission delay = time needed to transmit L-bit packet into links = L (bits) / R (bits / sec)
```

### Links: physical media
- ``bit``: propagates between transmitter / receiver pairs
- ``physical link``: what lies betweeen transmitter & receiver
- ``guided media``:
    - signals propagate in solid media: copper, fiber, coax
- ``unguided media``:
    - signals propagate freely, e.g., radio
- ``Twisted pair (TP)``
- two insulated copper wires
    - Category 5: 100Mpbs, 1Gbps Ethernet
    - Category 6L 10Gbps Ethernet
- ``Coaxical cable``
    - two concentric copper conductors
    - bidirectional
    - broadband
        - multiple frequency channels on cable
        - 100's Mbps per channel
- ``Fiber optic cable``
    - glass fiber carrying light pulses, each pulse a bit
    - high-speed operation:
        - high-speed point-to-point transmission (10’s-100’s Gbps)
    - low error rate: 
        - repeaters spaced far apart 
        - immune to electromagnetic noise
- ``Wireless radio``
    - signal carried in electromagnetic spectrum
    - no physical “wire”
    - broadcast and “half-duplex” (sender to receiver)
    - propagation environment effects:
        - reflection 
        - obstruction by objects
        - interference
-   ``Radio link types``
    - terrestrial  microwave
        - up to 45 Mbps channels
    - Wireless LAN (WiFi)
        - Up to 100’s Mbps
    - wide-area (e.g., cellular)
        - 4G cellular: ~ 10’s Mbps
        - 5G cellular: ~ 100’s Mbps
    - satellite
        - up to 45 Mbps per channel
        - 270 msec end-end delay
        - geosynchronous versus low-earth-orbit 

## The network core
- mesh of interconnected routers
- ``packet-switching``
    - hosts break application-layer messages into *``packets``*
    - forward packets from one router to the next, across links on path from source to destination
    - each packet transmitted at full link capacity

### Packet-switching: store-and-forward
- ``Transmission delay``
    - takes L/R secs to transmit (push out) L-bit packet into link at R bps
- ``Store and forward``
    - *entire* packet must arrive at router before it can be transmitted on next link
- ``End-to-end delay``
    - 2 L/R, assuming zero propagation delay

- Example:
```
L = 10Kbits
R = 100Mbps
one-hop transmission delay = 0.1ms
```
### Packet-switching: queueing delay, loss
- if arrival rate(in bps) to link exceeds transmission rate(in bps) of link for a period of time
    - packets willl queue, waiting to be transmitted on output link
    - packets can be dropped(lost) if memory(buffer) in router fills up

### Two key network-core functions
- *``Forwarding``*
    - ``local`` action
        - move arriving packets from router's input link to appropriate router output link
- *``Routing``*
    - ``global`` action
        - determine souce-destination paths taken by packets
        - routing algorithms

### Alternative to packet switching: circuit switching
- ``end-to-end resources allocated to, reserved for "call" between source and destination``

### Circuit switching: FDM and TDM
- ``Frequency Division Multiplexing (FDM)``
    - optical, electromagnetic frequencies divided into (narrow) frequency bands
    - each call allocated its own band, can transmit at max rate of that narrow band
- ``Time Division Multiplexing (TDM)``
    - time divided into slots
    - each call allocated periodic slot(s), can transmit at maximum rate of (wider) frequency band, but only during its time slot(s)

### Packet switching vs. circuit switching
- Example:
    - 1GB/s link
    - each user:
        - 100 Mb/s when "active"
        - active 10% of the time
        
    - ``circuit-switching``: 10 users
    - ``packet switching``: with 35 users, probability that > 10 active at same time is less than 0.0004
- Is packet switching a "slan dunk winner"?
    - great for "bursty" data - sometimes has data to send, but at other times not
        - resource sharing
        - simpler, no call setup
    - ``excessive congestion possible``
        - packet delay and loss due to buffer overflow
        - protocols needed for reliable data transfer, congestion control
    - How to provide circuit-like behavior?
        - bandwidth guarantees traditionally used for audio / video applications

- ``Comparison between Packet Switches and Circuit Switching``

| Item | Packet Switching | Circuit Switching |
| -- | -- | -- |
| Dedicated “copper” path | X | O |
| Bandwidth available | Dynamic | Fixed |
| Potentially wasted bandwidth | X | O |
| Store-and-forward transmission | O | X |
| Each packet follows the same route | X | O |
| Call Setup | Not Needed | Required |
| When can congestion occur | On every packet | At setup time |
| Effect of congestion | Queueing delay | Call blocking |

### Internet structure: a "network of networks"
- Hosts connect to Internet via access Internet Service Providers (ISPs)
    - residential, enterprise (company, university, commercial) ISPs
- Access ISPs in turn must be interconnected
    - so that any two hosts can send packets to each other
- Resulting network of networks is very complex
    - evolution was driven by economics and national policies
- ``Question``: given millions of access ISPs, how to connect them together
    - connecting each access ISP to each other directly *``doesn't scale``*:
        - O(n<sup>2</sup>) connections
- ``Option``: connect each access ISP to one global transit ISP?
- ``Customer`` and ``provider`` ISPs have economic agreement
- But if one global ISP is viable business, there will be competitors...
    - ... who will want to be connected
    - ... and regional networks may arise to connect access nets to ISPs
    - ... and content provider networks (e.g., Google, Microsoft, Akamai) may run their own network, to bring services, content close to end users
- At "center": small # of well-connected large networks
    - ``"tier-1" commercial ISPs`` (e.g., Level 3, Sprint, AT&T, NTT), national & international coverage
    - ``content provider networks`` (e.g., Google, Facebook): private network that connects its data centers to Internet, often bypassing tier-1, regional ISPs

## Performance
### How do packet loss and delay occur?
- packets *queue* in router buffers
    - packets queue, wait for turn
    - arrival rate to link (temporarily) exceeds output link capacity: packet loss
    - ``transmission delay``: packet being transmitted
    - ``queueing delay``: packets in buffers
    - ``free (available) buffers``: arriving packets dropped (loss) if no free buffers
### Packet delay: four sources

d<sub>nodal</sub> = d<sub>proc</sub> + d<sub>queue</sub> + d<sub>trans</sub> + d<sub>prop</sub>
- d<sub>proc</sub>: ``nodal processing``
    - check bit errors
    - determine output link
    - typically < ms (millisecs)
- d<sub>queue</sub>: ``queueing delay``
    - time waiting at output link for transmission
    - depends on congestion level of router
- d<sub>trans</sub>: ``transmission delay``
    - L: packet length(bits)
    - R: link transmission rate (bps)
    - d<sub>trans</sub> = L / R
- d<sub>prop</sub>: ``propagation delay``
    - d: length of physical link
    - s: propagation speed(~2x10<sup>8</sup> m/sec)
    - d<sub>prop</sub> = d / s

### Caravan analogy
- cars “propagate” at  100 km/hr
- toll booth takes 12 sec to service car (bit transmission time)
- car ~ bit; caravan ~ packet
- Q: How long until caravan is lined up before 2nd toll booth?
- time to “push” entire caravan through toll booth onto highway
    - 12*10 = 120 sec
- time for last car to propagate from 1st to 2nd toll both: 
    - 100km/(100km/hr) = 1 hr
- A: 62 minutes

#### Part 2
- suppose cars now “propagate” at 1000 km/hr
- and suppose toll booth now takes one min to service a car
- Q: Will cars arrive to 2nd booth before all cars serviced at first booth?
- A: Yes!  after 7 min, first car arrives at second booth; three cars still at first booth
    - The first car passes through the first booth for 1 min
    -  it runs through the highway toward the second booth for 100km/(1000km/hr) = 0.1 hr = 0.1 * 60 min = 6 min 
    - The total time is 7 min
    - so 7 cars have departed from the first booth
        - 3 cars still stay at the first booth
    
### Packet queueing delay (revisited)
- R: link bandwidth (bps)
- L: packet length (bits)
- a: average packet arrival rate
- La / R ~ 0: avg. queueing delay small
- La / R -> 1: avg. queueing large
- La / R > 1: more "work" arriving is more than can be serviced 
    - average delay infinite!

### "Real" Internet delays and routes
- ``traceroute`` program: provides delay measurement from source to router along end-to-end Internet path towards destination.
- For all i:
    - sends three packets that will reach router i on path towards destination (with time-to-live (TTL) field value of i)
    - router i will return packets to sender
    - sender measures time interval between transmission and reply

### Packet Loss
- queue (aka buffer) preceding link in buffer has finite capacity
- packet arriving to full queue dropped (aka lost)
- lost packet may be retransmitted by previous node, by source end system, or not at all

### Throughput
- rate (bits/time unit) at which bits are being sent from sender to receiver
    - ``instantaneous``
        - rate at given point in time
    - ``average``
        - rate over longer period of time


- R<sub>s</sub>, R<sub>c</sub>: pipes running rates
    - in practice, R<sub>s</sub> and R<sub>c</sub> are often bottleneck
- R<sub>s</sub> < R<sub>c</sub>, R<sub>s</sub> > R<sub>c</sub> = ``bottleneck link``
    - link on end-to-end path that constrains end-to-end throughput

#### Network Scenario
- 10 connections (fairly) share backbone bottleneck link R bits / sec
- per-connection end-to-end throughput: 
    - *min* (R<sub>c</sub>, R<sub>s</sub>, R / 10)
## Network Security
- ``Field of network security``
    - how bad guys can attack computer networks
    - how we can defend networks against attacks
    - how to design architectures that are immune to attacks
- Internet not originally designed with (much) security in mind
    - original vision: “a group of mutually trusting users attached to a transparent network” 
    - Internet protocol designers playing “catch-up”
    - security considerations in all layers!
    
### Malware
- malware can get in host from:
    - ``virus``: self-replicating infection by receiving / executing object (e.g., e-mail attachment)
    - ``worm``: self-replicating infection by passively receiving object that gets itself executed
        - can self-replicate and spread to other computers, but a virus cannot spread by itself
        - virus should be sent from a computer to another by a user or via software (e.g., email)
- ``spyware malware`` can record keystrokes, websites visited, upload info to collection site
- infected host can be enrolled in ``botnet``, used for spam or distributed denial of service (DDoS) attacks

### Denial of service (DoS)
- attackers make resources (server, bandwidth) unavailable to legitimate traffic by overwhelming resource with bogus traffic
    1. select target
    2. break into hosts around the network (see botnet)

### Packet interception
- ``packet "sniffing"``
    - broadcast media (shared Ethernet, wireless)
    - promiscuous network interface reads / records all packets (e.g., including passwords!) passing by
        - Wireshark software is a (free) packet sniffer

### Fake identity
- ``IP spoofing``
    - send packet with false source address

## Protocol Layers and reference models
- Networks are complex, with many "pieces"
    - hosts
    - routers
    - links of various media
    - applications
    - protocols
    - hardware, software
- Is there any hope of *organizing* structure of network?
    - ... or at least our *discussion* of networks?


### Example: organization of air travel:
1. ticket (purchase)
2. baggage (check)
3. gates (load)
4. runway takeoff
5. airplane routing
6. runway landing
7. gates (unload)
8. baggage (claim)
9. ticket (complain)

- Airline travel: a series of steps, involving many services

| Layer | Service | Layer |
| -- | -- | -- |
| ticket (purchase) | ticketing service | ticket (complain) |
| baggage (check) | baggage service | baggage (claim) |
| gates (load) | gate service | gates (unload) |
| runway takeoff | runway service | runway landing |
| airplane routing | routing service | airplane routing |

- ``layers``: each layer implements a service
    - via its own internal-layer actions
    - relying on services provided by layer below

### Why layering?
- dealing with complex systems
    - explicit structure allows identification, relationship of complex system's pieces
        - layered *``reference model``* for discussion
    - Modularization eases maintenance, updating of system
        - change in layer's service *implementation*: transparent to rest of system
        - e.g., change in gate procedure doesn't affect rest of system
    - layering considered harmful?
    - layering in other complex systems?

### Internet protocol stack
- ``application``
    - supporting network applications
        - IMAP, SMTP, HTTP
- ``transport``
    - process-process data transfer
        - TCP, UDP
- ``network``
    - routing of datagrams from source to destination
        - IP, routing protocols
- ``link``
    - data transfer between neighboring network elements
        - Ethernet, 802.11 (WiFi), PPP (Point-to-Point Protocol)
- ``physical``
    - bits "on the wire"

### Encapsulation
1. Source Application : Message [M]
2. Source Transport : Segment [H<sub>t</sub>][M]
3. Source Network : Datagram [H<sub>n</sub>][H<sub>t</sub>][M]
4. Source Link : Frame [H<sub>l</sub>][H<sub>n</sub>][H<sub>t</sub>][M]
5. Source Physical
6. Switch Physical
7. Switch Link
8. Switch Link
9. Switch Physical
10. Router Physical
11. Router Link : [H<sub>n</sub>][H<sub>t</sub>][M]
12. Router Network
13. Router Network : [H<sub>n</sub>][H<sub>t</sub>][M]
14. Router Link : [H<sub>l</sub>][H<sub>n</sub>][H<sub>t</sub>][M]
15. Router Physical
16. Destination Physical
17. Destination Link : Frame [H<sub>l</sub>][H<sub>n</sub>][H<sub>t</sub>]
18. Destination Network : Datagram [H<sub>n</sub>][H<sub>t</sub>][M]
19. Destination Transport : Segment [H<sub>t</sub>][M]
20. Destination Application : Message [M]

## Internet History
### 1961 - 1972: Early packet-switching principle
- 1961: Kleinrock
    - queueing theory shows effectiveness of packet-switching
- 1964: Baran
    - packet-switching in military nets
- 1967: ARPAnet
    - conceived by Advanced Research Projects Agency
- 1969: first ARPAnet node operational
- 1972:
    - ARPAnet public demo
    - NCP (Network Control Protocol)
        - first host-host protocol
    - first e-mail program
    - ARPAnet has 15 nodes

### 1972 - 1980: Internetworking, new and proprietary nets
- 1970: ALOHAnet satellite network in Hawaii
- 1974: Cerf and Kahn
    - architecture for interconnecting networks
- 1976: Ethernet at Xerox PARC
- late 70's: proprietary architectures
    - DECnet, SNA, XNA
- late 70's: switching fixed length packets (ATM precursor)
- 1979: ARPAnet has 200 nodes

```
Cerf and Kahn's internetworking principles:
- minimalism, autonomy
    - no internal changes required to interconnect networks
    - best-effort service model
    - stateless routing
    - decentralized control
- define today's Internet architecture
```

### 1980 - 1990: new protocols, a proliferation of networks
- 1983: deployment of TCP / IP
- 1982: smtp e-mail protocol defined
- 1983: DNS defined for name-to-IP address translation
- 1985: ftp protocol defined
- 1988: TCP congestion control
- new national networks: CSnet, BITnet, NSFnet, Minitel
- 100,000 hosts connected to confederation of networks

### 1990, 2000s: commercialization, the Web, new applications
- early 1990s: ARPAnet decommissioned
- 1991: NSF lifts restrictions on commercial use of NSFnet (decommissioned, 1995)
- early 1990s: Web
    - hypertext [Bush 1945, Nelson 1960's]
    - HTML, HTTP: Berners-Lee
- 1994: Mosaic, later Netscape
- late 1990s: commercialization of the Web
- late 1990s - 2000s:
    - more killer apps: instant messaging, P2P file sharing
    - network security to forefront
    - est. 50 million host, 100 million+ users
    - backbone links running at Gbps

### 2005 - Present: more new applications, Internet is "everywhere"
- ~18 B devices attached to Internet (2017)
    - Rise of smartphones (iPhone: 2007)
- aggressive deployment of broadband access
- increasing ubiquity of high-speed wireless access: 4G/5G, WiFi
- emergence of online social networks:
    - Facebook (Meta in 2021): ~ 2.5 B users
- service providers (Google, Meta, Microsoft) create their own networks
    - bypass commercial Internet to connect "close" to end user, providing "instantaneous" access to search, video content, ...
- enterprises run their services in "cloud" (e.g., Amazon Web Services, Microsoft Azure)

## ISO / OSI (Open Systems Interconnection) reference model
### Two layers not found in Internet protocol stack
- ``presentation``
    - allow applications to interpret meaning of data
        - e.g., encryption, compression, machine-specific conventions
-  ``session``
    - synchronization, checkpointing, recovery of data exchange
- Internet stack "missing" these layters!
    - these services, if needed, must be implemented in application
    - needed?

```
1. Application
2. Presentation
3. Session
4. Transport
5. Network
6. Link
7. Physical
```
## Wireshark: Network Packet Analyzer
https://www.wireshark.org/