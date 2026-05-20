Network Packet Analysis and Traffic Inspection
Overview:
This project demonstrates a network traffic capture and analysis exercise performed to identify communication protocols, inspect packet behavior, and detect suspicious or abnormal network activity.
The investigation focused on analyzing:
TCP handshake behavior
HTTP/HTTPS communication
DNS activity
QUIC protocol traffic
Packet retransmissions
Connection failures
TCP flags and packet structures
The analysis identified failed local service connections, retransmission activity, port reuse behavior, and successful encrypted external communications using TLS and QUIC protocols.

Goal:
Capture and analyze network packets to identify protocols and detect suspicious or abnormal traffic behavior.

Requirements:
The assessment included:
Capturing HTTP, DNS, and TCP handshake traffic
Analyzing TCP flags and packet structures
Identifying retransmissions and malformed packets
Detecting failed connection attempts
Reviewing encrypted TLS and QUIC communication behavior

Traffic Capture and Analysis:
1. TCP Handshake Traffic:
Filter Used
tcp.flags.syn == 1 && tcp.flags.ack == 0
Observations:
Multiple SYN packets were observed originating from:
"127.0.0.1"
"::1"
Destination ports included:
1521 (Oracle Database)
8080 (Web Server)
3000 (Development Server)
Observed behaviors:
Some connections received no SYN-ACK responses
Some connections immediately returned RST, ACK responses

2. HTTP / HTTPS Traffic:
Ports Observed:
443 (HTTPS)
Protocols Identified:
TLSv1.2
TLSv1.3
QUIC (HTTP/3)
TLS Handshake Observations:
Successful encrypted sessions included:
Client Hello
Server Hello
Encrypted Application Data
Server Name Indication (SNI):
Detected domains:
www.google.com
fonts.gstatic.com
www.gstatic.com

3. DNS Traffic:
DNS activity was observed during browsing sessions before TLS and QUIC connections were established.
Domain resolution activity was primarily related to Google services.
Packet Structure and Flag Analysis:
TCP  Flags Observed:
Flag	        Meaning	             Observed Behavior
SYN	          Start connection	   Frequently observed
ACK           Acknowledgment	     Present in responses
RST	          Reset connection	   Frequent in local services
PSH	          Push data	           Observed in HTTPS traffic
FIN	          Close connection	   Seen in retransmissions
Key Packet Patterns:
Normal TCP Handshake
SYN → SYN-ACK → ACK
Observed during successful external communications.
Failed TCP Handshake:
SYN → RST, ACK
Observed during failed local service connections.
Retransmission Activity:
Observed indicators:
[TCP Retransmission]
[TCP Spurious Retransmission]
QUIC Packet Structure:
Observed QUIC frame types:
CRYPTO
PING
PADDING
QUIC traffic was carried over UDP.
TLS Packet Structure:
Observed TLS sequence:
Client Hello
Server Hello
Change Cipher Spec
Encrypted Application Data

Unusual and Suspicious Packet Behavior:
1. Broken TCP Handshakes:
Observations:
Continuous SYN packets
No successful handshake completion
Immediate RST, ACK responses
Analysis:
This behavior indicates that services were not running or not listening on:
Port 8080
Port 1521

2. TCP Port Reuse:
Wireshark Indicator:
[TCP Port numbers reused]
Analysis:
This may indicate:
Rapid retry attempts
Application retry loops
Poor connection handling

3. TCP Retransmissions:
Observed Types:
Standard retransmission
Spurious retransmission
Possible Causes:
Packet loss
Network congestion
Wi-Fi instability
Delayed packet delivery
Capture Warnings Observed:
Warnings:
[TCP Previous segment not captured]
[Ignored Unknown Record]
Interpretation:
These warnings indicate:
Packet capture limitations
Missing captured frames
Non-malicious recording issues
No confirmed malicious activity was associated with these warnings.

Technical Findings:
Failed Local Service Connections:
Continuous SYN packets followed by RST, ACK responses indicated:
Closed ports
Misconfigured services
Stopped applications
Affected services included:
Oracle Database (Port 1521)
Web Server (Port 8080)

Application Retry Behavior:
Rapid retry attempts and repeated port reuse suggested:
Aggressive reconnection logic
Possible infinite retry loops
Poor application error handling

Healthy External Communications:
Successful encrypted communication was observed with:
Google services
Cloudflare infrastructure
Protocols used:
TLS
QUIC
This indicates proper secure internet connectivity.

Risk Classification
Issue	                      Risk Level
Local service failure	      Medium
TCP retransmissions	        Low
External encrypted traffic  No Risk

Recommendations:
Service Availability:
Start and verify required services:
Oracle Database (Port 1521)
Web Server (Port 8080)
Network Configuration:
Verify:
Port bindings
Service configurations:
Firewall rules
Listening services
Application Improvements:
Improve retry logic
Prevent excessive connection retries
Implement connection timeout handling
Network Monitoring:
Monitor retransmission frequency
Check network stability if retransmissions increase
Review abnormal TCP behaviors periodically

Conclusion:
The packet analysis identified two primary behaviors:
Failed local TCP connections to ports 8080 and 1521
Successful encrypted external communication using TLS and QUIC protocols
The investigation showed that local services were unavailable or misconfigured, resulting in repeated failed handshake attempts and TCP port reuse behavior.
External internet communication functioned normally using secure encrypted protocols.
The assessment demonstrates how packet analysis can be used to identify:
Service failures
Connection anomalies
Retransmissions
Network instability
Protocol behavior
Secure communication patterns
