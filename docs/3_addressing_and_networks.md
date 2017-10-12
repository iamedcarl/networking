# Addressing and Networks

## IPv4

An IPv4 address is actually a 32-bit numeric value. We usually write it as a dotted quad -- four decimal numbers, separated by dots, like `206.190.36.45` or `180.149.132.47`. Each of the four numbers written down like this represents one octet or 8-bit value.

Note: There are 2^32 - 1 = 4,294,967,295 IPv4 addresses.

![ipv4](/images/ipv4.png)

## IPv6

IPV6 Addresses are similar to IPV4, but due to the small address space of IPV4, additional addresses were needed. This means more bits per address. IPV6 uses 128-bit and is represented by an octuple of 16-bits. Because 16-bit numbers are quite large (65,536) each part of the octuple is represented using hexadecimal values (base 16).

![ipv6](/images/ipv6.png)

## Reserved IP Addresses

![reserved-ip](/images/reserved-ip.png)

- The light-green squares (0, 10, and 127) are blocks that are entirely reserved.
- The dark-green squares are blocks that are partly reserved. For instance, not all of the 192 block is reserved, but some of it is.
- The entire cyan row (starting at 224) is set aside for IP multicast.
- And the entire orange bottom row (starting at 240) was originally set aside for "future use" but was effectively lost due to being blocked as invalid. No, really. We lost 1/16th of all IPv4 addresses due to mistaken planning.

## Netblocks

Computers working on a network can designate a 'network prefix' that is some number of bits in their IP address that is shared. The bits following designate the machine on the network.

A 24 bit prefix, denoted /24:

```
Network Prefix   Machine #
[1100 0101 0001] 0001
...
[1100 0101 0001] 1111
```

## Subnet Masks

To tell computers how large the network prefix is, a subnet mask is applied. This is a dotted quad with 1s on the prefix and 0s on the machine IP.

```
255.255.255.0
OR
11111111.11111111.11111111.00000000
```

Calculating subnet masks: ![subnet](/images/subnet.png)

- The number of usable IP addresses in `/14` is 2^18 = 262,144.

## Routers and Default Gateways

A router is a device that connects two different IP networks. It acts as a gateway. While most hosts may have 1 interface with an IPv4 address, a router will have 2 or more.

The host on a local network will know about the default gateway, which is the router that's connected to the rest of the internet. Computers and devices can connect to a local network (LAN) and send packets directly to each other without going through a different network.

The default gateway may also have a default gateway as well. Eventually as you go towards the rest of the internet, you'll reach the default-less part. This is where routers on major transit ISPs know about the global routing table, a directory of all public networks.

```
// linux
$ ip route default show default
default via 10.0.2.2 dev eth0
10.0.2.0/24 dev eth0  proto kernel  scope link  src 10.0.2.15

// linux, mac osx
$ netstat -nr
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         10.0.2.2        0.0.0.0         UG        0 0          0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U         0 0          0 eth0
```

## Network Address Translation (NAT)

Whenever traffic goes between the private network behind the router and public internet, the router has the rewrite or translate the network addresses on it. The router maintains a map of which inside addresses and ports are connected to what public internet addresses and ports.

NAT is a workaround and not solution to address shortage. It makes it hard for end users to run servers that people can access. That makes it hard to write and debug network applications.

### Private Address Netblocks

These addresses are what you would usually see in a local network (homes, offices, datacenters, mobile networks). They can't be used in the public internet because they aren't unique. Each of the private addresses are behind a NAT router.

- 10.0.0.0/18
- 172.16.0.0/12
- 192.168.0.0/16

#### Links

[Test IPv6](https;//www.test-ipv6.com) - Test if your device is running on IPv6
