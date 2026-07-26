# Module 0 — Cloud Networking Fundamentals


> **Goal**
>
> By the end of this module, you should understand **how data travels from one computer to another**, because Azure networking is simply Microsoft's implementation of these same networking principles.

---

# 0.1 How Computers Communicate

Imagine you're sitting at home and you want to order pizza.

You know:

* Your home address
* Pizza shop's address
* Roads connecting both
* Delivery person
* Phone number

Computer networking works almost exactly the same way.

Instead of pizzas, computers exchange **data**.

Instead of roads, they use **networks**.

Instead of addresses, they use **IP addresses**.

---

## Big Picture

Suppose your laptop wants to open:

```
https://google.com
```

A lot happens behind the scenes.

```
Laptop
   │
Home WiFi
   │
Internet
   │
Google Server
```

Your laptop sends a request.

Google sends back the webpage.

Thousands of tiny pieces of data travel back and forth every second.

---

## Why Do We Need Rules?

Imagine if every country had different road rules.

Driving would be chaos.

Networking also needs rules.

These rules are called **Protocols**.

Examples:

* HTTP
* HTTPS
* TCP
* UDP
* IP
* DNS

Together these protocols allow computers made by different manufacturers to communicate.

---

# OSI Model (High Level)

Networking is complicated.

To make it easier, engineers divided communication into layers.

```
Application
Presentation
Session
Transport
Network
Data Link
Physical
```

Don't try to memorize them.

Instead remember:

> Every layer has one responsibility.

---

### Physical Layer

Moves electrical signals.

Examples:

* Ethernet cable
* Fiber cable
* WiFi radio signals

Think:

"The road."

---

### Data Link Layer

Moves data inside the same local network.

Uses:

* MAC Address

Think:

"Which apartment inside the building?"

---

### Network Layer

Moves data between different networks.

Uses:

* IP Address

Think:

"Which city?"

---

### Transport Layer

Makes sure data reaches the correct application.

Uses:

* TCP
* UDP
* Ports

Think:

"Which room inside the house?"

---

### Application Layer

Actual applications.

Examples:

* Browser
* Email
* WhatsApp
* SSH

Think:

"The person receiving the package."

---

## Simple Memory

```
Application → User

Transport → Which application?

Network → Which computer?

Data Link → Which device?

Physical → How data travels
```

That's enough for cloud engineers.

---

# TCP/IP Model

Azure, AWS, Linux and the Internet don't actually use the OSI model.

They use the TCP/IP model.

```
Application

Transport

Internet

Network Access
```

It's basically a simplified OSI model.

Whenever someone says:

"TCP/IP Networking"

They're talking about the networking model used by the Internet.

---

# Packets

Suppose you want to send a 2 GB movie.

It doesn't travel as one giant file.

Instead it becomes thousands of small pieces.

```
Movie

↓

Packet 1

Packet 2

Packet 3

Packet 4
```

Each packet travels independently.

The destination reassembles them.

Why?

Because smaller pieces are easier to transmit and recover if one is lost.

---

# Frames

Packets travel between networks.

Frames travel inside one local network.

Example:

```
Laptop

↓

WiFi Router

↓

Packet becomes Frame

↓

Router
```

A frame is simply a packet wrapped with local network information.

Think:

```
Frame

┌──────────────┐

MAC Address

Packet

Checksum

└──────────────┘
```

---

# Ports

Imagine an apartment building.

```
Building

↓

Apartment 101

Apartment 102

Apartment 103
```

The building is the computer.

The apartment number is the port.

Multiple applications can run on one machine.

How does the computer know where data should go?

Ports.

Examples:

```
80 → HTTP

443 → HTTPS

22 → SSH

3306 → MySQL

5432 → PostgreSQL

6379 → Redis

5672 → RabbitMQ
```

Azure NSGs often allow or block traffic based on ports.

---

# IP Addresses

Every computer needs an address.

Example:

```
192.168.1.25
```

Without an address,

Nobody knows where to send data.

Just like a postal address.

---

# MAC Address

Every network card has a unique hardware address.

Looks like:

```
00:1A:2B:3C:4D:5E
```

MAC Address

↓

Used only inside the local network.

IP Address

↓

Used across the Internet.

Think:

```
IP

↓

House Address

MAC

↓

Person inside the house
```

---

# Summary

| Concept     | Think Of                       |
| ----------- | ------------------------------ |
| Packet      | Parcel                         |
| Frame       | Parcel inside a delivery truck |
| IP Address  | Home address                   |
| MAC Address | Person receiving parcel        |
| Port        | Apartment number               |
| Protocol    | Delivery rules                 |

---

# 0.2 IP Addressing

---

## IPv4

An IPv4 address contains four numbers.

Example:

```
192.168.1.10
```

Each number ranges from:

```
0–255
```

Every device connected to a network needs one.

---

## Public IP

Reachable from the Internet.

Example:

```
Laptop

↓

Internet

↓

Azure VM (Public IP)
```

Anyone can connect if firewall rules allow it.

---

## Private IP

Used only inside private networks.

Examples:

```
10.x.x.x

172.16.x.x

192.168.x.x
```

Private IPs are not directly reachable from the Internet.

Azure VMs inside a VNet typically use private IPs to communicate with each other.

---

## Subnet Mask

A subnet mask tells us:

> Which part of an IP address identifies the network, and which part identifies the specific device.

Example:

```
IP

192.168.1.25

Subnet Mask

255.255.255.0
```

This means:

```
192.168.1

↓

Network

25

↓

Host
```

Cloud platforms often express this using CIDR notation instead of subnet masks.

---

## CIDR Notation

Instead of writing:

```
255.255.255.0
```

We write:

```
/24
```

Examples:

```
10.0.0.0/24

10.0.1.0/24

10.0.2.0/24
```

A `/24` network has 256 IP addresses (with some reserved in Azure).

You'll see CIDR notation everywhere in Azure when creating VNets and subnets.

---

## Default Gateway

Imagine your neighborhood.

If you want to visit a house in the same neighborhood, you drive directly there.

If you want to visit another city, you first drive onto the highway.

The **default gateway** is like that highway entrance.

```
Laptop
   │
Default Gateway (Router)
   │
Internet
```

Whenever your computer doesn't know where a destination is, it sends the traffic to the default gateway, which decides the next hop.

---

# 0.3 DNS

---

## What DNS Does

Humans remember names:

```
google.com
```

Computers use IP addresses:

```
142.250.x.x
```

DNS (Domain Name System) translates names into IP addresses.

Think of DNS as the **Internet's phone book**.

---

## Recursive Lookup

When you type:

```
google.com
```

Your computer usually asks a DNS resolver:

> "Can you find the IP address for google.com?"

If the resolver doesn't already know the answer, it queries other DNS servers until it finds the correct IP, then returns it to your computer. This process is called **recursive lookup**.

---

## A Record

An **A record** maps a hostname to an IPv4 address.

Example:

```
app.company.com

↓

10.0.0.5
```

---

## CNAME

A **CNAME (Canonical Name)** points one name to another name.

Example:

```
www.company.com

↓

company.com
```

The DNS system then resolves `company.com` to its IP address.

---

## Private DNS

Sometimes services should only be reachable inside a private network.

Example:

```
database.internal

↓

10.0.1.10
```

Only resources inside your Azure Virtual Network can resolve and access this name.

---

# 0.4 Routing

Routers decide where packets should go next.

---

## Static Routing

Routes are entered manually.

Example:

```
Destination: 10.1.0.0/16

Next Hop: Router A
```

Simple and predictable, but requires manual updates.

---

## Dynamic Routing

Routers automatically exchange routing information using routing protocols (such as BGP).

If a better path appears, routers can update their routing tables automatically.

Cloud providers use dynamic routing extensively behind the scenes.

---

## Longest Prefix Match

Sometimes multiple routes could match the same destination.

Routers choose the **most specific** route.

Example:

```
10.0.0.0/16

10.0.1.0/24
```

A packet for:

```
10.0.1.50
```

Matches both routes, but `/24` is more specific than `/16`, so the `/24` route is chosen.

This rule is fundamental to how routing works in Azure and many other networks.

---

# 0.5 NAT (Network Address Translation)

NAT allows private IP addresses to communicate with public networks.

---

## SNAT (Source NAT)

Changes the **source IP address**.

Example:

```
VM (10.0.0.4)

↓

Internet

↓

Appears as

52.100.x.x
```

This is how private Azure VMs access the Internet without exposing their private IPs.

---

## DNAT (Destination NAT)

Changes the **destination IP address**.

Example:

```
Internet

↓

Public IP

↓

Private VM
```

Azure uses this concept when incoming traffic to a public IP is forwarded to a private resource.

---

## PAT (Port Address Translation)

PAT is a type of NAT where many private devices share a single public IP by using different source ports.

Example:

```
Laptop A  → Public IP:203.0.113.10:50001
Laptop B  → Public IP:203.0.113.10:50002
Laptop C  → Public IP:203.0.113.10:50003
```

The public IP stays the same, but different port numbers keep the connections separate.

---

# 0.6 Firewalls

A firewall controls which network traffic is allowed or blocked.

Think of it as the security guard at the entrance to a building.

---

## Stateful Firewall

A stateful firewall remembers active connections.

If your computer starts an outbound connection, the corresponding response traffic is automatically allowed back in.

This is how Azure Network Security Groups (NSGs) behave.

---

## Stateless Firewall

A stateless firewall does not remember previous traffic.

Every packet is evaluated independently, so you must explicitly allow both directions if needed.

---

## Allow and Deny Rules

Firewalls use rules to decide what happens to traffic.

Example:

| Priority | Source   | Port | Action |
| -------- | -------- | ---- | ------ |
| 100      | Internet | 22   | Allow  |
| 200      | Internet | 80   | Allow  |
| 300      | Internet | 443  | Allow  |
| 4096     | Any      | Any  | Deny   |

Rules are evaluated in order of priority. The first matching rule is applied.

---

# Module 0 Recap

By completing this module, you should be comfortable answering questions like:

* How does a browser reach a website?
* What is the difference between an IP address and a MAC address?
* Why do we need ports?
* What is the difference between a public IP and a private IP?
* Why do VNets and subnets use CIDR notation?
* How does DNS translate names into IP addresses?
* How do routers decide where packets should go?
* Why can private IP addresses access the Internet using NAT?
* How do firewalls control network traffic?

With these fundamentals in place, you'll be well prepared to start **Module 1: Azure Networking Basics**, where you'll see how Azure implements these concepts using Virtual Networks, Subnets, Network Interfaces, Public IPs, and Network Security Groups.
