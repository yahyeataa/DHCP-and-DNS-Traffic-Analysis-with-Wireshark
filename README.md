# DHCP and DNS Traffic Analysis with Wireshark

This project shows how a computer gets an IP address and how a computer looks up a website name, by explaining two small lab network captures step by step.

## At a Glance

| Item | Details |
| --- | --- |
| Focus | Reading network traffic and recording what normal looks like, so unusual traffic is easier to spot later |
| Tools | Wireshark (shown in all screenshots); capture files named `dhcp.pcapng` and `dns_port.pcap` in the screenshots |

## What This Project Is About

A computer needs an IP address to talk on a network. An IP address is a number that identifies a device on that network. When a device starts up without an address, it asks a server for one. That server is called a DHCP server. DHCP stands for Dynamic Host Configuration Protocol. It is the system a network uses to hand out IP addresses automatically.

This project looks at a short lab capture of that process. The capture has four packets. A packet is a small piece of data sent across a network. The four packets follow a pattern called DORA. DORA means Discover, Offer, Request, and ACK. The device broadcasts a Discover message to ask for an address. The server replies with an Offer. The device broadcasts a Request to accept the offer. The server replies with an ACK to confirm the address. ACK means acknowledgment. It means the server confirms the assignment.

The second part of the project looks at DNS. DNS stands for Domain Name System. DNS translates a name a person can read, such as `us.pool.ntp.org`, into an IP address a computer can use. The capture has two packets. One is a query. A query is a request for information. The other is a response. A response is the answer to that request. The project records how long the answer took and what the answer contained.

The main idea in both parts is a baseline. A baseline is a record of normal behavior. You record normal behavior first. Later, you can compare new traffic against that record. If something looks different, you can investigate it. This project does not claim to be a production system. It is a lab and learning project based on the files provided. See Important Notice below.

## Why It Matters

- It helps a team tell normal traffic from unusual traffic. A missing step, a new server address, or a long delay can point to a problem.
- It makes troubleshooting faster. A clear baseline shows what should happen, so staff can check what did happen.
- It supports safer changes. A team can compare traffic before and after a change and see if behavior changed.

## Skills Demonstrated

- **Reading packet captures in Wireshark:** Opening a capture file and reading the packet list, packet details, and raw bytes for each packet.
- **Building a baseline for DHCP:** Recording the four DORA messages, the addresses involved, and the timing between messages.
- **Building a baseline for DNS:** Recording one query and one response, the name asked for, the answer received, and the response time.
- **Matching related packets:** Using a transaction ID to link a request with its reply. A transaction ID is a number that labels one exchange so related packets can be grouped together.
- **Checking protocol composition:** Using the protocol hierarchy view to see how many packets and bytes each layer used. A layer is one part of the communication stack, such as Ethernet, IP, or UDP.
- **Checking endpoints and conversations:** Using the endpoints and conversations views to see which addresses talked to each other and how much data each exchange used.
- **Writing evidence-based notes:** Recording times, addresses, ports, and message types from the capture, and linking each claim to a screenshot.

## What's Inside

folder structure, as shown in the editor:

    network-traffic-analysis/
    ├── analysis/
    │   ├── DHCP_Analysis.md
    │   └── DNS_Analysis.md
    ├── Screenshots/
    │   ├── DHCP_Screenshots/
    │   └── DNS_Screenshots/
    └── README.md

| Folder or file                                                      | Purpose                                                                                                                                                    |
| ------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `analysis/DHCP_Analysis.md`                                         | Full write-up of the DHCP capture: timeline, addresses, message-by-message evidence, and baseline conclusion                                               |
| `analysis/DNS_Analysis.md`                                          | Full write-up of the DNS capture: timeline, query and response details, statistics, and baseline conclusion                                                |
| `Screenshots/DHCP_Screenshots/`                                     | Wireshark screenshots for DHCP: protocol hierarchy, endpoints, conversations, packet list, and one screenshot per DORA message                             |
| `Screenshots/DNS_Screenshots/`                                      | Wireshark screenshots for DNS: protocol hierarchy, endpoints, statistics, query details, and response details                                              |
| `README.md`                                                         | This file. Project overview for non-technical and technical readers                                                                                        |
| Capture files named in screenshots (`dhcp.pcapng`, `dns_port.pcap`) | Original packet capture files opened in Wireshark. Not listed in the current folder view. |

No detection rules, playbooks, or automated test files were included in the files provided for this README. See Technical Details below.

## Highlights

These are observed values from the two lab captures only. They are not production results.

| Area | Observed result in lab capture |
| --- | --- |
| DHCP exchange | Complete DORA exchange with 4 packets, from 0.000000 s to 0.070345 s |
| DHCP timing | Discover to Offer: 0.295 ms. Request to ACK: 0.314 ms. Start to final ACK: 70.345 ms |
| DHCP addresses | Server `192.168.0.1` offered and confirmed `192.168.0.10` to the client. Client started from `0.0.0.0` and broadcast to `255.255.255.255` |
| DHCP client hardware address | `00:0b:82:01:fc:42`, shown as `GrandstreamN_01:fc:42` in Wireshark |
| DNS exchange | 1 query and 1 matching response for `us.pool.ntp.org`, type A, class IN. Response time: 6.946 ms |
| DNS addresses | Requester `192.168.50.50` asked resolver `192.168.0.1`. Response code: No error |
| DNS response size | Query: 75 bytes. Response: 540 bytes, with 15 answer records, 6 authority records, and 2 additional records |
| DNS note | Traffic used UDP port 65333 on the resolver side, not the conventional port 53. The analysis notes this as a lab configuration point to document, not as proof of a problem |

## Quick Links

- 📄 [DHCP analysis](DHCP_Analysis.md)
- 📄 [DNS analysis](DNS_Analysis.md)
- 🖼️ [DHCP screenshots](Screenshots/DHCP_Screenshots/)
- 🖼️ [DNS screenshots](Screenshots/DNS_Screenshots/)


### Capture summaries for reviewers

DHCP (from `DHCP_Analysis.md`):

- Packet 1: `0.0.0.0:68` to `255.255.255.255:67`, Discover, transaction ID `0x3d1d`, 314 bytes.
- Packet 2: `192.168.0.1:67` to `192.168.0.10:68`, Offer, transaction ID `0x3d1d`, 342 bytes.
- Packet 3: `0.0.0.0:68` to `255.255.255.255:67`, Request, transaction ID `0x3d1e`, 314 bytes.
- Packet 4: `192.168.0.1:67` to `192.168.0.10:68`, ACK, transaction ID `0x3d1e`, 342 bytes.
- Protocol hierarchy for the filtered packets: Frame 4 packets / 1,312 bytes, Ethernet 4 / 56, IPv4 4 / 80, UDP 4 / 32, DHCP 4 / 1,144.

DNS (from `DNS_Analysis.md`):

- Packet 1: `192.168.50.50:65282` to `192.168.0.1:65333`, standard query for A `us.pool.ntp.org`, transaction ID `0x002b`, 75 bytes.
- Packet 2: `192.168.0.1:65333` to `192.168.50.50:65282`, standard query response, No error, transaction ID `0x002b`, 540 bytes.
- Protocol hierarchy for the two packets: Frame 2 packets / 615 bytes, Ethernet 2 / 28, IPv4 2 / 40, UDP 2 / 16, DNS 2 / 531.
- Statistics view: 2 total DNS packets, 1 query, 1 response, average request-response time 6.95 ms, 0 retransmissions, 0 unsolicited responses.



## Glossary

| Term | Meaning in plain English |
| --- | --- |
| A record | A DNS answer that gives the IP address for a name |
| ACK | Acknowledgment. A message that confirms something was received or assigned |
| Address | An IP address or hardware address that identifies a device or a destination |
| Authority records | DNS records in a response that list the servers in charge of a name |
| Baseline | A record of normal behavior used for later comparison |
| BOOTP | An older protocol that DHCP is based on. Wireshark shows DHCP message types as Boot Request or Boot Reply |
| Broadcast | A message sent to all devices on the network, instead of to one device |
| Client | A device that asks for a service or an address |
| Conversation | A pair of addresses that exchanged packets, as grouped by Wireshark |
| DORA | Discover, Offer, Request, ACK. The four steps of a normal DHCP address assignment |
| Endpoint | One address that sent or received packets, as grouped by Wireshark |
| False positive | An alert or finding that looks like a problem but is actually normal or not a real problem |
| Flags | Small fields in a packet that set options, such as whether a DNS message is a query or a response |
| IP address | A number that identifies a device on a network, such as `192.168.0.10` |
| MAC address | A hardware address built into a network device, such as `00:0b:82:01:fc:42` |
| Packet | A small piece of data sent across a network |
| Packet capture | A saved file of packets observed on a network, often with a `.pcap` or `.pcapng` ending |
| Playbook | A step-by-step guide for how to respond to a type of alert or incident |
| Port | A number that identifies a specific service or conversation on a device. DHCP uses ports 67 and 68. DNS conventionally uses port 53 |
| Protocol | A set of rules for how devices format and send data |
| Protocol hierarchy | A Wireshark view that shows how many packets and bytes each protocol layer used |
| Query | A request for information, such as a DNS request for an IP address |
| RCODE | Response code in DNS. `0` means No error |
| Resolver | A server that answers DNS queries on behalf of a client |
| Response | An answer to a query or request |
| Retransmission | Sending the same packet again because the first copy may not have arrived |
| Server | A device that provides a service, such as handing out IP addresses or answering DNS queries |
| Transaction ID | A number that labels one exchange so a request and its reply can be matched |
| UDP | User Datagram Protocol. A simple transport protocol used by DHCP and DNS in these captures |
| Wireshark | A free tool for viewing and analyzing packet captures |

## Important Notice

This project uses only lab capture data and simulated or sanitized examples. It contains no real customer information and no personal information. IP addresses in the captures are private lab addresses, such as `192.168.0.1`, `192.168.0.10`, and `192.168.50.50`, plus the special addresses `0.0.0.0` and `255.255.255.255`. Do not use this project as evidence of production performance.

