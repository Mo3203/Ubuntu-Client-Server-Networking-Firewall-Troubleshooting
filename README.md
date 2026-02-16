# 🛡 Ubuntu Client–Server Networking & Firewall Troubleshooting Lab
# Overview
This repository documents a hands-on networking lab conducted in a controlled Ubuntu client–server environment.
The objective was to move beyond theory and analyze end-to-end network communication at the packet, protocol, and system levels, while introducing controlled failures to strengthen troubleshooting capability.
The lab covers:
DNS resolution
TCP 3-way handshake
HTTP request/response cycle
Packet capture and analysis
Firewall misconfiguration scenario
Root cause identification and remediation

<img width="1269" height="1741" alt="Ubuntu Client–Server Networking   Firewall Troubleshooting Lab" src="https://github.com/user-attachments/assets/2f198d9b-3b56-47b6-b85f-9228c866c7c1" />

# Architecture
Environment:
Ubuntu Client VM
Ubuntu Server VM
Python HTTP Server (python3 -m http.server 8000)
UFW Firewall
Tools: curl, tcpdump, Wireshark
High-level components:
Client → DNS → Firewall (UFW) → Server (Python HTTP)

# Objectives
Understand layered communication (Application → Transport → Network).
Observe TCP handshake behavior in real packet captures.
Analyze HTTP GET and 200 OK responses.
Simulate firewall-induced connectivity failure.
Perform structured troubleshooting using logs and packet inspection.
Document findings with visual architecture and sequence diagrams.



# Phase 1: DNS Resolution

Goal: Verify the Client can resolve the Server's name to 192.xxx.xxx.x.

On Ubuntu Client:

# 1. Test external DNS (checks internet)
dig google.com

# 2. Test internal "DNS" (checks /etc/hosts mapping)
ping server.techexapmle.com -c 4

# Phase 2: TCP Handshake

# Goal: Establish a connection on Port 8000.

On Rocky Server (Setup):
Rocky Linux has a strict firewall by default. We must allow port 8000.

# Allow port 8000
sudo firewall-cmd --add-port=8000/tcp

# Start the Python Web Server
python3 -m http.server 8000

On Ubuntu Client (Execution):
You will need two terminal windows open on the Ubuntu machine.

Terminal 1 (The Sniffer):

# Listen for traffic specifically between client and the server
sudo tcpdump -i any host 192.168.xxx.x and port 8000

Terminal 2 (The Actor):
curl http://server.techexample.com:8000

[S] = SYN (Client asking to connect)

[S.] = SYN-ACK (Server saying yes)

[.] = ACK (Client confirming)

Phase 3: HTTP Request / Response

Goal: See the actual data (HTML) moving across the wire.

On Ubuntu Client:

Bash
# Using the verbose flag (-v) to see the HTTP headers clearly
curl -v http://server.techgroup.com:8000
On Rocky Server:
Watching a log entry appear:

<img width="738" height="513" alt="results1" src="https://github.com/user-attachments/assets/889629f0-5949-460a-b92f-1a4b97dcd728" />




# View security logs (ssh logins, sudo usage)
sudo tail -f /var/log/secure
Drill (Firewall Test on Rocky):

# 1. On Rocky Server, block the Client IP
sudo firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.200.80" reject'

# 2. On Ubuntu Client, try to connect
curl http://server.techgroup.com:8000
# (It will hang or say "Connection refused")

<img width="1144" height="101" alt="Connection refused" src="https://github.com/user-attachments/assets/a3790e4d-9f12-42f6-af85-9fcded23ac9f" />

# 3. On Rocky Server, remove the block to fix it
sudo firewall-cmd --reload
