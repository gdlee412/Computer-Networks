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
    - Network Solutions: authoritative registry for .com, .net TLD
    - Education Organizations: .edu TLD

- Authoritative DNS servers:
    - organization's own DNS server(s), providing authoritative hostname to IP mapping for organization's named hosts
    - can be maintained by organization or service provider

### Local DNS name servers
- does not strictly belong to hierarchy
- each ISP (residential ISP, company, university) has one
    - also called "default name server"
- when host makes DNS query, query is sent to its local DNS server
    - has local cache of recent name-to-address translation pairs (but may be out of date!)
    - acts as proxy, forwards query into hierarchy 

### DNS name resolution: iterated query
- Example: host at engineering.nyu.edu wants IP address for gaia.cs.umass.edu
- Iterated query:
    - contacted server replies with name of server to contact
    - "I don't know this name, but ask this server" 

1. requesting host at *engineering.nyu.edu* from local DNS server *dns.nyu.edu* recursively
2. local DNS server *dns.nyu.edu* contacts root DNS server
3. root DNS server responds to the requests .edu
4. local DNS server *dns.nye.edu* contacts TLD DNS server to get umass.edu
5. TLD DNS server responds to the request for umass.edu