---
layout: post
title: "Introducing Crebain"
categories: [security, rf, c2]
published: true
date: 2026-01-13
---

For me, 2025 began with a newfound interest in various LoRa-based mesh networking "systems". This led me down a pretty standard path of experimenting with Meshtastic, then into more esoteric, but similar technologies like Meshcore and Reticulum. After tinkering with the basics, and doing some pretty basic designs of cyber-security related applications using these systems, I took it a step further. Alongside my colleague Mike Seay, I began developing a command and control system designed around secure out-of-band implant communication using LoRa mesh networking systems. This was introduced during BSides Nashville and BSides Tampa 2025, but was mostly a proof-of-concept at the time (more details can be found here). Mike Seay and myself hinted at a full release in 2025 to some folks, but we fell a little short of that goal for a myriad of reasons. But, here we are, to show the handful of folks from BSides and in the industry what we've been working on, and to introduce Crebain.

## Crebain 1.0

Crebain is a command and control system that uses the Reticulum Network Stack for transferring data between nodes. The tool is intended to be used by benign actors as a covert, physically deployed device that enables ingressing and egressing a target network via a cryptographically secure communications channel over LoRa (using RNodes and RNS). The idea is that, using Crebain, operators can totally avoid traditional C2 over web, TCP, UDP, or really any standard protocol. It uses a client-server architcture, Python, SQLite, Flask, and RNS to acheive a pretty standard set of basic C2 functionalities (we're mostly modeling our base use cases after CobaltStrike, Empire, and Metasploit, because we're stuck in 2017). The core C2 functionalities include:

- **Periodic client check-ins:** clients send back detailed health checks based on a configured interval/jitter value.
- **Command execution:** operators can run command on the linux clients in an asynchronous manner.
- **File transfer:** operators can upload and download files in an asynchronous manner.
- **Web proxying:** clients can enable a web proxy, allowing other implants to proxy their web traffic through the Crebain client/server connection (LoRa).
- **Interactive UI:** operators interact with Crebain via a web UI, which includes a graphical display of geographic location data, authentication, and a few other features.
- **Logging:** commands, file transfers, checkin information, and proxy status are all logged and accessible via the UI.

Most of these features are built atop the Reticulum Network Stack (RNS), using a client-server model in which a central Crebain server interacts with multiple Crebain clients (nodes) using RNS Links over LoRa. Here's how this looks at a high level.

```
    ┌─────────────────────────┐                    ┌─────────────────────────┐
    │     CREBAIN SERVER      │                    │     CREBAIN CLIENT      │
    │    (Operator Side)      │                    │    (Target Network)     │
    ├─────────────────────────┤                    ├─────────────────────────┤
    │                         │                    │                         │
    │  ┌───────────────────┐  │                    │  ┌───────────────────┐  │
    │  │    Flask Web UI   │  │                    │  │   Node Monitor    │  │
    │  │   (app.py:8080)   │  │                    │  │    (client.py)    │  │
    │  │                   │  │                    │  │                   │  │
    │  │ • Dashboard       │  │                    │  │ • Health Data     │  │
    │  │ • Command Queue   │  │                    │  │ • GPS/Services    │  │
    │  │ • File Transfers  │  │                    │  │ • Network Stats   │  │
    │  │ • GPS Map View    │  │                    │  │ • System Metrics  │  │
    │  └────────┬──────────┘  │                    │  └────────┬──────────┘  │
    │           │             │                    │           │             │
    │           ▼             │                    │           ▼             │
    │  ┌───────────────────┐  │                    │  ┌───────────────────┐  │
    │  │  SQLite Database  │  │                    │  │ Command Executor  │  │
    │  │                   │  │                    │  │                   │  │
    │  │ • Node Status     │  │                    │  │ • Shell Commands  │  │
    │  │ • Command Queue   │  │                    │  │ • File I/O        │  │
    │  │ • File Chunks     │  │                    │  │ • Response Queue  │  │
    │  │ • Logs            │  │                    │  └────────┬──────────┘  │
    │  └────────┬──────────┘  │                    │           │             │
    │           │             │                    │           │             │
    │           ▼             │                    │           ▼             │
    │  ┌───────────────────┐  │                    │  ┌───────────────────┐  │
    │  │  Monitoring       │◄─┼────────────────────┼─►│  RNS Client       │  │
    │  │  Server           │  │   Monitoring Link  │  │                   │  │
    │  │  (server.py)      │  │  (Health/Commands) │  │  • Link Mgmt      │  │
    │  │                   │  │                    │  │  • Path Discovery │  │
    │  │ • Dual RNS Dest   │◄─┼────────────────────┼─►│  • Msg Queue      │  │
    │  │ • Job Polling     │  │     Proxy Link     │  └────────┬──────────┘  │
    │  │ • HTTP Relay      │  │  (Web Proxy Data)  │           │             │
    │  └────────┬──────────┘  │                    │           │             │
    │           │             │                    │           ▼             │
    │           │             │                    │  ┌───────────────────┐  │
    │           │             │                    │  │  HTTP Proxy       │  │
    │           │             │                    │  │  (127.0.0.1:8080) │  │
    │           │             │                    │  │                   │  │
    │           │             │                    │  │  Local apps can   │  │
    │           │             │                    │  │  proxy web traffic│  │
    │           │             │                    │  │  through LoRa     │  │
    │           │             │                    │  └───────────────────┘  │
    └───────────┼─────────────┘                    └─────────────────────────┘
                │                                              │
                │                                              │
                ▼                                              ▼
    ┌───────────────────────┐                    ┌───────────────────────┐
    │      RNode/LoRa       │~~~~~~~~~~~~~~~~~~~~│      RNode/LoRa       │
    │     Transceiver       │   LoRa Radio Link  │     Transceiver       │
    │    (915/868 MHz)      │   (Up to 15+ km)   │    (915/868 MHz)      │
    └───────────────────────┘                    └───────────────────────┘
```

## Use Cases

Crebain is intended to be a covert command and control system and rogue device (see: "dropbox", "leave-behind", "implant") for use in red team engagements. Specifically, it's aimed at enabling an operator to interact with a target network securely, over long distances, and in a way that bypasses network protocols (HTTP, TCP, UDP) for C2 egress. You provision at least one server and one client, connect the client device to a target network, and begin interacting with that client over LoRa to perform your operation. So how can this be applied in reality?

### Rogue Device Campaigns

Red Teamers can simulate a sophisticated, covert rogue device using Crebain. An operator can provision a client/server node combination, place the rogue device into a network via a physical connection to a switch or router, and control the device from a theoretically unlimited distance without ever using HTTP or TCP to egress the target network. This is the primary use case, and what I initially envisioned using Crebain for.

### Network Penetration Testing

Crebain can also function as a platform for performing standard network penetration tests. Connect a client node, and begin sending typical shell commands to the client. For those who want to be fancy, lazy, or some combination of both by performing their typical duties at a target datacenter from the comfort of their vehicle 10km away (without cellular or pre-provisioned target network access)!

### Augmenting Existing C2 Implants

If you have a C2 implant within a target network already, you can use Crebain's native web proxy server to tunnel your communications outbound over LoRa. Operators can use this to make their communications even more covert, as there's no need to traverse target egress firewalls, DLP, or other pesky security controls. As long as your C2 implant has the ability to configure a web proxy (metasploit, CobaltStrike, Empire all do), you can tunnel your traffic through Crebain. Here's how this looks at a basic level:


```
TARGET NETWORK                                        OPERATOR SIDE
+-----------------+     +----------------+                +----------------+     +-------------+
|     Beacon      |---->| Crebain Client |~~~~~ LoRa ~~~~>| Crebain Server |---->| Team Server |
| (HTTP to proxy) |     |  (HTTP Proxy)  |                |  (HTTP Relay)  |     |  (Internet) |
+-----------------+     +----------------+                +----------------+     +-------------+
                               |
                        Egress controls
                           bypassed
```

## An Example Deployment and Interaction

Crebain was designed to be easily deployed, and the software itself certainly achieves that. However, there are a few hardware components and their respective firmware to be configured before the software deployment and initial operation. This example Crebain deployment and target interaction will consist of the following components:

>**Crebain Server**: 1x Raspberry pi 2b, 1x Heltec v3
>
>**Crebain Client**: 1x Raspberry pi nano 2w, 1x Heltec v3, 1x Generic USB GPS transmitter
>
>**Operator Laptop**: 1x Thinkpad T480
>
>**Target Network**: 1x Asus Nighthawk router

The idea is that, for this demonstration, the attacker (myself) will provision a typical Crebain client/server (raspberry Pis with Heltec LoRa boards), physically implant the client device onto the target network (my home network) via an ethernet connection to a target networking device (my router), then performing reconnaissance and tunnel C2 traffic from an existing implant (on my laptop) via Crebain.

### Setting Up Devices

Configuring Crebain devices is straightforward. Here's how I do it.

1. To begin, provision the raspberry pi devices with a compatible Linux image of your choosing using your favorite imager. I used the raspberry pi imaging softare to flash the Raspberry pi OS to both devices. Plug in your USB peripherals (LoRa board, GPS transmitter in my case).

2. Authenticate to the devices and provision a passwordless sudo user for the Crebain client/server.

3. Clone the Crebain Github repo.

4. Setup Crebain with the setup script on both the client and server.

6. Navigate to the Crebain web interface on your operator laptop. Authenticate using the default credentials, and change your password (if you'd like).

### Confirming Clients



1. 

### Attacking the Network

### Tunneling C2 Web Traffic

## Hardware

## Part 2

Secure configurations and advanced usage
Range Testing

## Part 3

Detections
Improvements