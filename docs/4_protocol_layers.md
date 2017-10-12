# Protocol Layers

## The Protocol Stack

An example protocol stack and the corresponding layers:

Protocol            | Layer
------------------- | -----------
HTPP, SSH, FTP, NTP | Application
TCP, UDP            | Transport
IP                  | Internet
Ethernet, WiFi      | Hardware

It is good to understand the protocol stack as lower layers can affect how your application works.

Protocol | concepts                             | where the code is            | failures
-------- | ------------------------------------ | ---------------------------- | ----------------------------
HTTP     | resources, URLs, verbs, cookies, ... | Flask, Apache, browsers, ... | error codes, slow responses
TCP      | ports, sessions, stream sockets      | OS kernal, system libraries  | broken connections, timeouts
IP       | IP address, packets                  | OS kernal, routers           | various
WiFi     | access points, WPA passwords         | device drivers               | network unavailable

## Ping And DNS In Tcpdump

`tcpdump` is a tool you can use to display details about network traffic between hosts and networks. Tcpdump can look at any network traffic.

```
// Ping google.com
$ ping -c3 google.com
PING google.com (172.217.11.46) 56(84) bytes of data.
64 bytes from lga25s61-in-f14.1e100.net (172.217.11.46): icmp_seq=1 ttl=63 time=13.0 ms
64 bytes from lga25s61-in-f14.1e100.net (172.217.11.46): icmp_seq=2 ttl=63 time=12.9 ms
64 bytes from lga25s61-in-f14.1e100.net (172.217.11.46): icmp_seq=3 ttl=63 time=10.3 ms

--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 10.366/12.114/13.075/1.241 ms

// Set tcpdump to listen to DNS port 53 on my machine with results after ping
$ sudo tcpdump -n port 53
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
18:54:46.601973 IP 10.0.2.15.44584 > 10.0.2.3.53: 3190+ A? google.com. (28)
18:54:46.605877 IP 10.0.2.3.53 > 10.0.2.15.44584: 3190 1/0/0 A 172.217.11.46 (44)
18:54:46.619547 IP 10.0.2.15.52710 > 10.0.2.3.53: 61195+ PTR? 46.11.217.172.in-addr.arpa. (44)
18:54:46.622459 IP 10.0.2.3.53 > 10.0.2.15.52710: 61195 1/0/0 PTR lga25s61-in-f14.1e100.net. (83)
```

## Analyzing tcpdump Data

```
// Made a HEAD request to example.net
$ printf 'HEAD / HTTP/1.1\r\nHost: example.net\r\n\r\n' | nc example.net 80
HTTP/1.1 200 OK
Accept-Ranges: bytes
Cache-Control: max-age=604800
Content-Type: text/html
Date: Thu, 12 Oct 2017 19:00:54 GMT
Etag: "359670651"
Expires: Thu, 19 Oct 2017 19:00:54 GMT
Last-Modified: Fri, 09 Aug 2013 23:54:35 GMT
Server: ECS (lga/1378)
X-Cache: HIT
Content-Length: 1270

// Set VM to listen on port 80
$ sudo tcpdump -n port 80
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
19:00:54.682248 IP 10.0.2.15.41150 > 93.184.216.34.80: Flags [S], seq 2064459953, win 29200, options [mss 1460,sackOK,TS val 9703520 ecr 0,nop,wscale 6], length 0
19:00:54.689432 IP 93.184.216.34.80 > 10.0.2.15.41150: Flags [S.], seq 642632705, ack 2064459954, win 65535, options [mss 1460], length 0
19:00:54.689463 IP 10.0.2.15.41150 > 93.184.216.34.80: Flags [.], ack 1, win 29200, length 0
19:00:54.690178 IP 10.0.2.15.41150 > 93.184.216.34.80: Flags [P.], seq 1:39, ack 1, win 29200, length 38: HTTP: HEAD / HTTP/1.1
19:00:54.690363 IP 93.184.216.34.80 > 10.0.2.15.41150: Flags [.], ack 39, win 65535, length 0
19:00:54.690520 IP 10.0.2.15.41150 > 93.184.216.34.80: Flags [F.], seq 39, ack 1, win 29200, length 0
19:00:54.690692 IP 93.184.216.34.80 > 10.0.2.15.41150: Flags [.], ack 40, win 65535, length 0
19:00:54.696936 IP 93.184.216.34.80 > 10.0.2.15.41150: Flags [P.], seq 1:300, ack 40, win 65535, length 299: HTTP: HTTP/1.1 200 OK
19:00:54.696979 IP 10.0.2.15.41150 > 93.184.216.34.80: Flags [.], ack 300, win 30016, length 0
19:00:54.697096 IP 93.184.216.34.80 > 10.0.2.15.41150: Flags [F.], seq 300, ack 40, win 65535, length 0
19:00:54.697115 IP 10.0.2.15.41150 > 93.184.216.34.80: Flags [.], ack 301, win 30016, length 0
```

- My IP address (10.0.2.15).
- The IP address of example.net (93.184.216.34).
- The `length` field at the end of each line is the payload or actual HTTP data. Most will have a value of 0\. This is because before the client and server get to exchange any data, there is some setup to bring up the connection and tear it down.

  - The `length 38` are the packets sent from the client to the server which is the HTTP HEAD.
  - The `length 299` are the packet sent from the server to the client as the HTTP response of 200 OK.

## Sequence Diagrams

The two vertical lines on the left and the right are time lines with time advancing from top to bottom. The line of the left is usually the clients view and the line on the right is the server view. The arrows between represent messages between the client and server. Sometimes they are drawn on an angle to account for latency.

![sequence-diagrams](/images/sequence-diagrams.png)

## Connection Establishment

### Transmission Control Protocol

TCP (Transmission Control Protocol) is an agreed upon system by which most internet data is transmitted and received. In the Internet Protocol Stack, it sits between HTTP (which describes the data) and IP (which describes the connection).

### What's actually happening when a TCP connections opens?

Data sent across a TCP connection is not sent in one large file, but is broken into smaller length packets. These packets are numbered into a sequence and sent using the Internet Protocol to the recipient. Upon receipt of each package, the recipient sends an ACK (or acknowledgement packet) with the sequence number to 'acknowledge' that they received it. This is necessary because packets are not guaranteed successful delivery and the sender will continue to send the packets until they know for certain they have been received.

For example, in a typical GET request the initiator sends the GET request packet to the responder. The responder ACKs the request, then sends the data packet(s), each of which has a sequence number. Each data packet is ACKed by the initiator as it comes in. This tells the responder that the packet is received and they can cease sending it.

![tcp-sequence](/images/tcp-sequence.png)

## Buffering

### Why does TCP put sequence numbers on packets?

First, the network can fail to deliver a packet. Second, the network can deliver packets out of order.

### How does putting the packets back together work?

Each end point in the operating system (not in the web browser, web server, or other applications) keeps a space in memory that fits the incoming data. It uses the sequence numbers as positions (similar to an array index).

## TCP Flags

Each TCP packet record that you look at in tcpdump has a section called _Flags_ that appears right after the address and port information. It has one or more letters or dots inside square brackets:

```
19:00:54.689432 IP 93.184.216.34.80 > 10.0.2.15.41150: Flags [S.], seq 642632705, ack 2064459954, win 65535, options [mss 1460], length 0
```

The _Flags_ field in tcpdump tells you which flags, or control bits, are set on each TCP packet.

- SYN (synchronize) `[S]` -- This packet is opening a new TCP session and contains a new initial sequence number.
- FIN (finish) `[F]` -- This packet is used to close a TCP session normally. The sender is saying that they are finished sending, but they can still receive data from the other endpoint.
- PSH (push) `[P]` -- This packet is the end of a chunk of application data, such as an HTTP request.
- RST (reset) `[R]` -- This packet is a TCP error message; the sender has a problem and wants to reset (abandon) the session.
- ACK (acknowledge) `[.]` -- This packet acknowledges that its sender has received data from the other endpoint. _Almost every packet except the first SYN will have the ACK flag set._
- URG (urgent) `[U]` -- This packet contains data that needs to be delivered to the application out-of-order. _Not used in HTTP or most other current applications._

### 3-way handshake

The 3-way handshake is how a connection is initialized between a client and server. The client sends an initial packet with the SYN flag set and an initial sequence number. Then the server responds with a packet having a SYN flag, the ACK to the initial sequence number and setting its own initial sequence number. The client then responds with it's next sequence number, ACKing the server's response.

This sets a baseline for the sequence numbers and both client and server acknowledge that they are connected and will send and receive data to each other.

### 4-way teardown

When one side has finished sending data it will send a FIN flagged packet, telling the other it is done. The other will acknowledge, but continue to send data until it also finishes. At this point it will also send a FIN flagged packet and the side that finished first will acknowledge. At this point both have finished and they will break their connection.

### ICMP and UDP don't have TCP flags

If you look at tcpdump data for pings or basic DNS lookups, you will not see flags. This is because ping uses ICMP, and basic DNS lookups use UDP. These protocols do not have TCP flags or sequence numbers.

## TCP Congestion Control

Frequently, fast networks will be sending packets to one another through a slower network. This can easily cause bottlenecks or packet loss. To prevent this TCP slows down the packet send rate each time it does not receive an ACK response. This can be seen by attempting to connect to a closed port.

After enough sends and no ACK responses, the connection will timeout.

Running the below will have `tcpdump` show several packets sent to that host, but it gets slower and slower. Then after a long delay, `nc` exits.

```
// 1st console
$ sudo tcpdump -n port 12345

// 2nd console
$ nc www.udacity.com 12345
```
