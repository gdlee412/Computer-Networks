# Application Layer (Part 1)

## Overview

-   Principles of network Applications
-   Web and HTTP
-   E-mail, SMTP, IMAP

## Goals

-   Conceptual and implementation aspects of application-layer protocols
    -   transport-layer service models
    -   client-server paradigm
    -   peer-to-peer paradigm
-   learn about protocols by examining popular application-layer protocols
    -   HTTP
    -   SMTP, IMAP
    -   DNS
-   programming network applications
    -   socket API

## Principles of Network Applications

### Some network apps

-   social networking
-   Web
-   text messaging
-   e-mail
-   multi-user network games
-   streaming stored video (YouTube, Hulu, Netflix)
-   P2P file sharing
-   voice over IP (e.g., Skype)
-   real-time video conferencing
-   Internet search
-   remote login
-   ...

### Creating a network app

-   write programs that
    -   run on _different_ end systems
    -   communicate over network
    -   e.g., web server software communicates with browser software
-   no need to write software for network-core devices
    -   network-core devices do not run user applications
    -   applications on end systems allows for rapid app development, propagation

### Client-server paradigm

#### Server:

-   always-on host
-   permanent IP address
-   often in data centers, for scaling

#### Clients:

-   contact, communicate with server
-   may be intermittently connected
-   may have dynamically IP addresses
-   do _not_ communicate directly with each other
-   examples: HTTP, IMAP, FTP

### Peer-Peer architecture

-   no always-on server
-   arbitrary end systems directly communicate
-   peers request service from other peers, provide service in return to other peers
    -   `self scalability`: new peers bring new service capacity, as well as new service demands
-   peers are intermittently connected and change IP address
    -   complex management
-   example P2P file sharing

### Process communicating

-   `process`: program running within a host
-   within the same host, two processes communicate using `inter-process communication`(defined by OS)
-   processes in different hosts communicate by exchanging `messages`
-   note: applications with P2P architectures have client processes & server processes

#### Client process

-   process that initiates communication

#### Server process

-   process that waits to be contacted

### Sockets

-   process sends/receives messages to/from its `socket`
-   socket analogous to door
    -   sending process shoves messages out of door
    -   sending process relies on transport infrastructure on other side of door to deliver message to socket at receiving process
    -   two sockets involved: one on each side

### Addressing Processes

-   to receive messages, process must have `identifier`
-   host device has unique 32-bit IP address

#### Q: does IP address of host on which process runs suffice for identifying the process?

-   A: no, many processes can be running on the same host
-   `identifier` includes both `IP address` and `port number` associated with process on host
    -   example port numbers:
        -   HTTP server: 80
        -   mail server: 25
    -   to sernd HTTP message to gaia.cs.umass.edu web server:
        -   IP address: 128.119.245.12
        -   port number: 80

### Application-layer protocol defines

-   types of messages exchanged
    -   e.g., request, response
-   message syntax
    -   what fields in messages & how fields are delineated
-   message semantics
    -   meaning of information in fields
-   rules for when and how processes send & respond to messages

#### open protocols

    - defined in RFCs (i.e., standards), everyone has access to protocol definition
    - allows for interoperability
    - e.g., HTTP, SMTP

#### Propreity protocols

    - e.g., Skype

### What transport service does an app need?

#### Data integrity

-   some apps (e.g., file transfer, web transactions) require 100% reliable data transfer
-   other apps (e.g., audio) can tolerate some loss

#### Timing

-   some apps (e.g., Internet telephony, interactive games) require low delay to be "effective"

#### Throughput

-   some apps (e.g., multimedia) require minimum amount of throughput to be "effective"
-   other apps ("elastic apps") make use of whatever throughput they get

#### Security

-   encryption, data integrity, ...

### Transport service requirements: common apps

| application            | data loss     | throughput                             | time sensitive? |
| ---------------------- | ------------- | -------------------------------------- | --------------- |
| file transfer/download | no loss       | elastic                                | no              |
| e-mail                 | no loss       | elastic                                | no              |
| Web documents          | no loss       | elastic                                | no              |
| real-time audio/video  | loss-tolerant | audio:5Kbps-1Mbps; video: 10Kbps-5Mbps | yes, 10's msecs |
| streaming audio/video  | loss-tolerant | same as above                          | yes, few secs   |
| interactive games      | loss-tolerant | Kbps+                                  | yes, 10's msecs |
| text messaging         | no loss       | elastic                                | yes and no      |

### Internet transport protocols services

#### TCP service

-   `reliable transport` between sending and receiving process
-   `flow control`: sender won't overwhelm receiver
-   `congestion control`: throttle sender when network overloaded
-   `does not provide`: timing, minimum throughput guarantee, security
-   `connection-oriented`: setup required between client and server processes

#### UDP service

-   `unreliable data transfer` between sending and receiving process
-   `does not provide`: reliability, flow control, congestion control, timing, throughput guarantee, security, or connection setup

| application            | application layer protocol                     | transport protocol |
| ---------------------- | ---------------------------------------------- | ------------------ |
| file transfer/download | FTP[RFC 959]                                   | TCP                |
| e-mail                 | SMTP [RFC 5321]                                | TCP                |
| Web documents          | HTTP 1.1 [RFC 7320]                            | TCP                |
| Internet telephony     | SIP [RFC 3261], RTP [RFC 3550], or proprietary | TCP or UDP         |
| streaming audio/video  | HTTP [RFC 7320], DASH                          | TCP                |
| interactive games      | WOW, FPS (proprietary)                         | UDP or TCP         |

### Securing TCP

#### Vanilla TCP & UDP sockets

-   no encryption
-   cleartext passwords sent into socket traverse Internet in cleartext(!)

#### Transport Layer Security (TLS)

-   provides encrpyted TCP connections
-   data integrity
-   end-point authentication

#### TLS implemented in application layer

-   apps use TSL libraries, that use TCP in turn

#### TLS socket API

-   cleartext sent into socket traverse Internet _encrypted_

## Web and HTTP

### Quick review

-   web page consists of objects, each of which can be stored on
    different Web servers
-   object can be HTML file, JPEG image, Java applet, audio file, ...
-   web page consists of base HTML-file which includes several
    referenced objects, each addressable by a URL
-   example

```
www.someschool.edu/someDept/pic.gif
hostname/pathname
```

### HTTP Overview

-   `HTTP: Hypertext Transfer Protocol`
-   Web's application layer protocol
-   client/server model:
    -   client: browser that requests, receives, (using HTTP protocol) and "displays" Web objects
    -   server: Web server sends (using HTTP protocol) objects in response to requests
-   `HTTP uses TCP`
    -   client initiates TCP connection (creates socket) to server, port 80
    -   server accepts TCP connection from client
    -   HTTP messages (application-layer protocol messages) exchanged between browser (HTTP client) and Web server (HTTP server)
    -   TCP connection closed
-   `HTTP is "stateless"`
    -   server maintains _no_ information about past client requests

```
protocols that mainain "state" are complex!
- past history (state) must be maintained
- if server/client crashes, their views of "state" may be inconsistent, must be reconciled
```

### HTTP connections: two types

#### Non-persistent HTTP

1. TCP connection opened
2. at most one object sent over TCP connection
3. TCP connection closed

-   downloading multiple objects requires multiple connections

-   Example: www.someschool.edu/someDept/home.index (references to 10 jpeg images)

```
1a. HTTP client initiates TCP connection to HTTP server (process) at www.someschool.edu on port 80

1b. HTTP server at host (www.someschool.edu) waiting for TCP connection at port 80 "accepts" connection, notifying client

2. HTTP client sends HTTP request message (containing URL) into TCP connection socket.
    - Messages indicates that client wants objects (someDept/home.index)

3. HTTP server receives request message, forms response message containing requested object, and sends message into its socket

4. HTTP server closes TCP connection

5. HTTP client receives response message containing html file, displays html. Parsing html file, finds 10 referenced jpeg objects
6. Steps 1a- 5 repeated for each of 10 jpeg objects
```

##### Response Time

-   Round Trip Time (RTT): time for a small packet to travel from client to server and back
-   HTTP response time (per object)
    -   one RTT to initiate TCP connection
    -   one RTT for HTTP request and first few bytes of HTTP response to return
    -   object/fule transmission time

```
Non-persistent HTTP response time = 2RTT + file transmission time
```

#### Persistent HTTP

1. TCP connection opened to a server
2. multiple objects can be sent over _single_ TCP connection between client, and that server
3. TCP connection closed

##### Non-persistent HTTP issues

-   requires 2 RTTs per object
-   OS overhead for _each_ TCP connection
-   browsers often open multiple parallel TCP connections to fetch referenced objects in parallel

##### Persistent HTTP (HTTP1.1)

-   server leaves connection open after sending response
-   subsequent HTTP messages between same client/server sent over open connection
-   client sends requests as soon as it encounters a referenced object
-   as little as one RTT for all the referenced objects (cutting response time in half)

### HTTP request message

-   two types of HTTP messages: `request`, `response`
-   HTTP request message:
    -   ASCII (human-readable format)

```
Request line (GET, POST, HEAD commands)
    GET /index.html HTTP/1.1\r\n
header lines
    Host: www-net.cs.umass.edu\r\n
    User-Agent: Firefox/3.6.10\r\n
    Accept: text/html,application/xhtml+xml\r\n
    Accept-Language: en-us,en;q=0.5\r\n
    Accept-Encoding: gzip,deflate\r\n
    Accept-Charset: ISO-8859-1,utf-8;q=0.7\r\n
    Keep-Alive: 115\r\n
    Connection: keep-alive\r\n
Carriage return, line feed at start of line indicates end of hear lines
    \r\n
```

-   `\r` = carriage return character
-   `\n` = line-feed character

#### Carriage Return vs. Line Feed

-   `Carriage return`: The cursor (as carriage) moves to the 1st column of the line
-   `Line feed`: the cursor moves to the next line
-   `Combination of carriage return and line feed`: it lets the cursor move to the start of the next line

#### General Format

```
request line
| method | sp | URL | sp | version | cr | lf |
header lines
| header field name | | value | cr | lf |
~
~
| header field name | | value | cr | lf |
| cr | lf |
body
| entire body |
```

#### Other HTTP request messages

-   `POST method`

    -   web page often includes form input
    -   user input sent from client to server in entity body of HTTP POST request message

-   `GET method` (for sending data to server)

    -   include user data in URL field of HTTP GET request message (following a '?')
    -   e.g., www.somesite.com/animalsearch?monkeys&banana

-   `HEAD method`

    -   requests headers (only) that would be returned _if_ specified URL were requested with an HTTP GET method

-   `PUT method`
    -   uploads new file (object) to server
    -   completely replaces file that exists at specified URL with content in entity body of POST HTTP request message

### HTTP response message

```
status line (protocol status code status phrase)
    HTTP/1.1 200 OK\r\n
header lines
    Date: Sun, 26 Sep 2010 20:09:20 GMT\r\n
    Server: Apache/2.0.52 (CentOS)\r\n
    Last-Modified: Tue, 30 Oct 2007 17:00:02
    GMT\r\n
    ETag: "17dc6-a5c-bf716880"\r\n
    Accept-Ranges: bytes\r\n
    Content-Length: 2652\r\n
    Keep-Alive: timeout=10, max=100\r\n
    Connection: Keep-Alive\r\n
    Content-Type: text/html; charset=ISO-8859-
    1\r\n
\r\n
data, e.g., requested HTML file
    data data data data data ...
```

#### HTTP response status codes

-   status code appears in 1st line in server-to-client response message
-   sample codes
    -   200 OK
        -   request succeeded, requested object later in this message
    -   301 Moved Permanently
        -   request object moved, new location specified later in this message (in Location: field)
    -   400 Bad Request
        -   request msg not understood by server
    -   404 Not Found
        -   requested document not found on this server
    -   505 HTTP Version Not Supported

#### Trying out HTTP (client side) for yourself

1. Telnet to your favorite Web server

```
telnet gaia.cs.umass.edu 80
```

-   opens TCP connection to port 80 (default HTTP server pport) at gaia.cs.umass.edu
-   anything typed in will be sent to port 80 at gaia.cs.umass.edu

2. type in a GET HTTP request

```
GET /kurose_ross/interactive/index.php HTTP/1.1
Host:gaia.cs.umass.edu
```

-   by typing this in (hit carriage return twice), you send this minimal (but complete) GET request to HTTP server

3. look at response message sent by HTTP server! (or use Wireshark to look at captured HTTP request/response)

### Cookies: Maintaining user/server state

-   Recall: HTTP GET/response interaction is `stateless`
-   no notion of multi-step exchanges of HTTP messages to complete a Web "transaction"

    -   no need for client/server to track "state" of multi-step exchange
    -   all HTTP requests are independent of each other
    -   no need for client/server to "recover" from a partially-completed-but-never-completely-completed transcation

-   `stateful protocol`: client makes two changes to X, or none at all

```
Server Data: X
Client: Lock data record X
Server: OK
Server Data: X (locked)
Client: update X <- X'
Server: OK
Server Data: X' (locked)
t'
Client: update X <- X''
Server: OK
Server Data: X'' (locked)
Client: unlock X
Server: OK
Server Data: X''
```

-   if server crashes at t', anything afterwards will not be updated

-   Web sites and client browser use `cookies` to maintain some state between transactions

#### Four components

1. cookie header line of HTTP _response_ message
2. cookie header line in next HTTP _request_ message
3. cookie file kept on user's host, managed by user's browser
4. back-end database at Web site

Example:

-   Susan uses browser on laptop, visits specific e-commerce site for first time
-   when initial HTTP requests arrives at site, site creats:
    -   unique ID (cookie)
    -   entry in backend database for ID
-   subsequent HTTP requests from Susan to this site will contain cookie ID value, allowing site to "identify" Susan

```
Client: eBay 8734
Client -> usual HTTP request msg -> Amazon server
Amazon server -> creates ID 1678 for user -> create entry -> backend database

Server -> usual HTTP response set-cookie: 1678 -> client

Client: ebay 8734; amazon 1678

Client -> usual HTTP request msg cookie: 1678 -> cookie specific action

Amazon server <- access -> backend database

Amazon server -> usual HTTP response msg
```

-   one week later

```
Client -> usual HTTP request msg cookie: 1678 -> cookie specific action

Amazon server <- access -> backend database

Amazon server -> usual HTTP response msg
```

#### Comments

-   What cookies can be used for:

    -   authorization
    -   shopping carts
    -   recommendations
    -   user session state (Web e-mail)

-   Challenge: How to keep state

    -   protocol endpoint: maintain state at sender/receiver over multiple transactions
    -   cookies: HTTP messages carry state

-   cookies and privacy
    -   cookies permit sites to learn a lot about you on their site
    -   third party persistent cookies (tracking cookies) allow common identity (cookie value) to be tracked across multiple web sites

### Web Caches (proxy servers)

Goal: satisfy client request without involving origin server

-   user configures browser to point to a `Web cache`
-   browser sends all HTTP requests to cache

    -   if object in cache: cache returns object to client
    -   else cache requests object from origin server, caches received object, then returns object to client

-   Web cache acts as both client and server
    -   server for original requesting client
    -   client to origin server
-   typically cache is installed by ISP (university, company, residential ISP)

#### Why?

-   reduce response time for client request
    -   cache is closer to client
-   reduce traffic on an institution's access link
-   Internet is dense with caches
    -   enables "poor" content providers to more effectively deliver content

#### Caching Example

Scenario

-   access link rate: 1.54 Mbps
-   RTT from institutional router to server: server
-   Web object size: 100K bits
-   Average request rate from browsers to origin servers: 15/sec
    -   average data rate = web object size _ average request rate = 100,000 _ 15 = 1,500,000 bits/s = 1.5 Mbps
    -   average data rate to browsers: 1.50 Mbps
-   LAN: 1Gbps

Performance

-   LAN utilization = average data rate / LAN speed = 1.50 Mbps / 1 Gbps = 0.0015
-   Access link utilization = avg data rate / access link rate = 1.50 / 1.54 = `0.97` (problem: large delays at high utilization)
-   end-end delay = Internet delay + access link delay + LAN delay = 2sec+ minutes + usecs

##### Caching Example part 2 (buy a faster access link)

Scenario

-   access link rate: `154` Mbps
-   RTT from institutional router to server: server
-   Web object size: 100K bits
-   Average request rate from browsers to origin servers: 15/sec
    -   average data rate = web object size _ average request rate = 100,000 _ 15 = 1,500,000 bits/s = 1.5 Mbps
    -   average data rate to browsers: 1.50 Mbps
-   LAN: 1Gbps

Performance

-   LAN utilization = average data rate / LAN speed = 1.50 Mbps / 1 Gbps = 0.0015
-   Access link utilization = avg data rate / access link rate = 1.50/154 = `0.0097`
-   end-end delay = Internet delay + access link delay + LAN delay = 2sec+ `msecs` + usecs
-   COST: faster access link (expensive)

##### Caching Example part 3 (install a web cache)

Scenario

-   access link rate: 1.54 Mbps
-   RTT from institutional router to server: server
-   Web object size: 100K bits
-   Average request rate from browsers to origin servers: 15/sec
    -   average data rate = web object size _ average request rate = 100,000 _ 15 = 1,500,000 bits/s = 1.5 Mbps
    -   average data rate to browsers: 1.50 Mbps
-   LAN: 1Gbps
-   web cache: cheap

Calculating access link utilization, end-end delay with cache:

-   suppose cache hit rate is 0.4: 40% requests satisfied at cache, 60% requests satisfied at origin
-   access link: 60% of requests use access link
-   data rate to browsers over access link = 0.6 \* 1.50 Mbps = 0.9 Mbps
-   utilization = 0.9/1.54 = 0.58
-   average end-end delay = 0.6 _ (delay from origin servers) + 0.4 _ (delay when satisfied at cache) = 0.6 (2.01) + 0.4(~msecs) = ~1.2 secs
-   lower average end-end delay than with 154 Mbps link (and cheaper too)

#### Conditional GET

-   Goal: don't send object if cache has up-to-date cached vesion
    -   no object transmission delay
    -   lower link utilization
-   `cache`: specify date of cached copy in HTTP request
    -   if-modified-since: <date>
-   `server`: response contains no object if cached copy is up-to-date:
    -   HTTP/1.0 304 Not Modified

```
Not modified

Client: HTTP request msg if-modified-since: <date>

Server: HTTP response HTTP/1.0 304 Not Modified
```

```
Modified

Client: HTTP request msg if-modified-since: <date>

Server: HTTP response HTTP/1.0 200 OK <data>
```

### HTTP/2

-   Key goal: decreased delay in multi-object HTTP requests

#### HTTP1.1

-   introduced `multiple, pipelined GETs` over single TCP connection
-   server respons _in-order_ (FCFS: first-come-first-served scheduling) to GET requests
-   with FCFS, small object may have to wait for transmission (head-of-line (HOL) blocking) behind large object(s)
-   loss recovery (retransmitting lost TCP segments) stalls object transmission

#### HTTP/2 [RFC 7540, 2015]

-   increased flexibility at _server_ in sending objects to client:
-   methods, status codes, most header fields unchanged from HTTP 1.1
-   transmission order of requested objects based on client-specified object prority (not necessarily FCFS)
-   _push_ unrequested objects to client
-   divide objects into frames, schedule frames to mitigate HOL blocking

#### mitigating HOL blocking

HTTP 1.1: client requests 1 large object (e.g., video file, and 3 smaller objects)

```
O1 = video file
client: GET O1
client: GET O2
client: GET O3
client: GET O4
server: O1
server: O2
server: O3
server: O4

objects delivered in order requested: O2,O3,O4 wait behind O1
```

HTTP/2: objects divided into frames, frame transmission interleaved

```
client: GET O1
client: GET O2
client: GET O3
client: GET O4
server: O2
server: O4
server: O3
server: O1

O2, O3, O4 delivered quickly, O1 slightly delayed
```

### HTTP/2 to HTTP/3

-   key goal: decreased delay in multi-object HTTP requests

#### HTTP/2 over single TCP connection means

-   recovery from packet loss still stalls all object transmissions
    -   as in HTTP 1.1, browsers have incentive to open multiple parallel TCP connections to reduce stalling, increase overall throughput
-   no security over vanilla TCP connection

#### HTTP/3

-   adds security, per object error- and congestion- control (more pipelining) over UDP (i.e., QUIC over UDP)

    -   QUIC: A UDP-Based Multiplexed and Secure Transport [RFC 9000]
        -   new reliable, secure transport protocol suitable for a protocol like HTTP
        -   URL: https://datatracker.ietf.org/doc/html/rfc9000

-   Protocol Stacks for HTTP/2 and HTTP/3

| HTTP/2   | HTTP/3                                                      |
| -------- | ----------------------------------------------------------- |
| TLS 1.2+ | Quic ; TLS 1.3 ; TCP-like congestion control, loss recovery |
| TCP      | UDP                                                         |
| IP       | IP                                                          |

## E-mail, SMTP, IMAP

### E-mail

#### Three major components

-   User agents
-   Mail servers
-   Simple Mail Transfer Protocol (SMTP)

#### User Agent

-   AKA "mail reader"
-   composing, editing, reading mail messages
-   e.g., Outlook, iPhone mail client
-   outgoing, incoming messages stored on server

### Mail Servers

-   `mailbox` contains incoming messages for user
-   `message queue` of outgoing (to be sent) mail messages
-   `SMTP protocol` between mail servers to send email messages
    -   client: sending mail server
    -   "server": receiving mail server

### RFC (5321)

-   uses TCP to reliably transfer email message from client (mail server initiating connection) to server, port 25
-   direct transfer: sending server (acting like client) to receiving server
-   three phases of transfer
    -   handshaking(greeting)
    -   transfer of messages
    -   closure
-   command/response interaction (like HTTP)
    -   `commands`: ASCII text
    -   `response`: status code and phrase
-   messages must be in 7-bit ASCII

#### Scenario: Alice sends e-mail to Bob

1. Alice uses UA to compose e-mail message "to" bob@someschool.edu
2. Alice's UA sends messages to her mail server; message placed in message queue
3. client side of SMTP opens TCP connection with Bob's mail server
4. SMTP client sends Alice's message over the TCP connection
5. Bob's mail server places the message in Bob's mailbox
6. Bob invokes his user agent to read message

```
Alice -> Bob

Alice's User agent -> Message -> Alice's mail server
Alice's mail server -> TCP connection -> Bob's mail server
Alice's mail server -> Message -> Bob's mail server -> Bob's mailbox
Bob's User agent -> message -> Bob
```

### Sample SMTP interaction
```
S: 220 hamburger.edu
C: HELO crepes.fr
S: 250 Hello crepes.fr, pleased to meet you
C: MAIL FROM: <alice@crepes.fr>
S: 250 alice@crepes.fr... Sender ok
C: RCPT TO: <bob@hamburger.edu>
S: 250 bob@hamburger.edu ... Recipient ok
C: DATA
S: 354 Enter mail, end with "." on a line by itself
C: Do you like ketchup?
C: How about pickles?
C: .
S: 250 Message accepted for delivery
C: QUIT
S: 221 hamburger.edu closing connection
```

### Try SMTP interaction for yourself:
- telnet <servername> 25
    - see 220 reply from server
    - enter HELO, MAIL FROM:, RCPT TO:, DATA, QUIT commands above lets you send email without using e-mail client (reader)
    - Only works if <servername> allows telnet connections to port 25 (this is becoming increasingly rare because of security concerns)

### SMTP: Closing observations
#### Comparison with HTTP
- HTTP: pull
- SMTP: push
- both have ASCII command/response interaction, status codes
- HTTP: each object encapsulated in its own response message
- SMTP: multiple objects sent in multipart message
- SMTP uses persistent connections
- SMTP requires message (header & body) to be in 7-bit ASCII
- SMTP server uses CRLF.CRLF (Carriage Return Line Feed) to determine end of message

### Mail message format
- SMTP: protocol for exchanging e-mail messages, defined in RFC 531 (like HTTP)
- RFC 822 defines *syntax* for e-mail message itself (like HTML)
    - header lines, e.g.,
        - To:
        - From:
        - Subject:
        - these lines, within the body of the email message area different from SMTP MAIL FROM:, RCPT TO: commands
    - Body: the "message", ASCII characters only

### Mail access protocols
```
User Agent -> SMTP -> Sender's email server -> SMTP -> Receiver's email server -> email access protocol (e.g., IMAP, HTTP) -> User Agent
```
- ``SMTP``: delivery/storage of e-mail messages to receiver's server
- mail access protocol: retrieval from server
    - ``IMAP``: Internet Mail Access Protocol[RFC 3501]: messages stored on server, IMAP provides, retrieval, deletion, folders of stored messages on server
- ``HTTP``: gmail, Hotmail, Yahoo!Mail, etc. provides web-based interface on top of SMTP (to send), IMAP (or POP) to retrieve e-mail messages