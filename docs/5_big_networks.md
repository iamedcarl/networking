# Big Networks

## Hops

The internet is held together by routers that forward your packets from the source to the destination. Each forward from one machine to another is called a hop.

### Traceroute

You can see all the hops between you to a distant server by using the `traceroute` tool. It shows all the IP addresses of the routers it took traffic to get there.

```
$ traceroute www.google.com
traceroute to www.google.com (172.217.11.36), 30 hops max, 60 byte packets
 1  10.0.2.2 (10.0.2.2)  0.075 ms  0.075 ms  0.079 ms
 2  FIOS_Quantum_Gateway.fios-router.home (192.168.1.1)  2.201 ms  2.526 ms  2.523 ms
 3  lo0-100.NWRKNJ-VFTTP-313.verizon-gni.net (108.5.233.1)  5.315 ms  4.055 ms  5.173 ms
 4  B3313.NWRKNJ-LCR-22.verizon-gni.net (130.81.216.230)  7.747 ms  7.670 ms  7.772 ms
 5  * * *
 6  0.et-9-0-2.GW11.EWR6.ALTER.NET (140.222.0.235)  9.237 ms  5.777 ms 0.et-10-1-2.GW11.EWR6.ALTER.NET (140.222.0.241)  5.862 ms
 7  74.125.147.14 (74.125.147.14)  8.288 ms  4.904 ms  6.554 ms
 8  * * *
 9  72.14.234.65 (72.14.234.65)  7.559 ms  7.324 ms  7.263 ms
10  lga25s61-in-f4.1e100.net (172.217.11.36)  7.063 ms  9.330 ms  11.303 ms
```

#### The story of Traceroute

An engineer named, Van Jacobson discovered a safety feature made to prevent infinite loops made it possible to do traceroute.

Here's the safety feature: Every packet has a TTL field which starts at a large number and is reduced by 1 each time the packet hits a router. As it move through the network, the packet is getting reduced by 1 all the way to its destination. So if there is a misconfigured router where packets flow in an infinite loop, eventually the TTL will reach 0 for the packets and expire. The router that last received it will send a small error message back to the sender. The error message contains the address of the last router where the packet expired.

So with traceroute, it starts with TTL 1 and expires. The error message will come back with the last router address. Then the TTL increments to 2 to capture the next router. The TTL keeps incrementing until it reaches the destination.

### MTR

Another more advanced tool is `mtr` which will repeatedly trace and sometimes show different routes the traffic may take.

```
$ mtr www.google.com
                                  My traceroute  [v0.85]
vagrant-ubuntu-trusty-64 (0.0.0.0)                             Thu Oct 12 20:25:23 2017
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                              Packets               Pings
Host                                        Loss%   Snt   Last   Avg  Best  Wrst StDev
1\. 10.0.2.2                                  0.0%    14    0.3   0.3   0.2   0.5   0.0
2\. FIOS_Quantum_Gateway.fios-router.home     0.0%    14    2.4   3.1   1.5   7.3   1.2
3\. lo0-100.NWRKNJ-VFTTP-313.verizon-gni.net  0.0%    14    6.9  10.6   2.7  48.9  11.7
4\. 130.81.216.228                           14.3%    14    6.8   8.0   4.8  10.9   1.9
5\. ???
6\. 140.222.0.237                             0.0%    13    6.5   7.8   5.8  16.2   2.6
7\. 74.125.147.14                             0.0%    13    4.2   9.3   4.2  26.6   5.5
8\. ???
9\. 216.239.62.149                            0.0%    13    6.5   6.7   4.1  19.2   3.9
10\.lga34s12-in-f4.1e100.net                  0.0%    13    5.4   6.0   4.4   7.6   1.0
```

## Bandwidth across Networks

Bandwidth is the amount of data that can be transmitted on a network. It can be thought of as the width of the internet tube that is transmitting data. In this case a two people living in the same building make a request to amazon.com, but one has DSL and the other has gigabit internet, the gigabit connection will receive a largish response quicker. This is because they can be sent more packets per second. ![bandwidth](/images/bandwidth.png)

## Latency

Latency is the time it takes data to travel. This is non-dependent on size, but generally dependent upon the number of hops and the distance of those hops. So generally, a New Yorker getting a response from a server in Nanjing would take longer than a server located in Florida.

## Bandwidth Delay-Product

Calculate the amount of data between the Earth and Moon.

- bandwidth: 1 Gbit/sec
- distance: 384,000 km
- speed of light: 300,000 km/sec

Solution:

```
 384000 km       1 sec       1 Gbit       384000
----------- x ----------- x --------- = ---------- = 1.28 GBit
    1          300000 km      1 sec       300000
```

## Middleboxes

**Firewalls** are devices that network operators can use to filter traffic that's coming into or leaving their network. A firewall is one example of a class of network devices called middleboxes -- devices that inspect, modify, or filter network traffic. Other examples of middleboxes include intrusion detection systems and load balancers. Technically, it's only a middle-box if it's a separate device from the client or server.

With **NAT**, several devices can access Internet resources through a single public IP address, with the NAT device using port numbers to match up connections on the inside and outside. Your web site can see requests from the same IP address that actually come from different users on different computers.

Another way that can happen is through the use of **web proxies**. Whereas a NAT works at the IP level, rewriting packets, a web proxy works at the HTTP level, taking queries from browsers and sending them out to web servers.

_Having the user log in or using a session cookie (or both!) will definitely let you tell apart different users behind the same IP address._
