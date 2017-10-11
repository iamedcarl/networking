# Names and Addresses

## Re-visit ping

You can ping a host. Each time you ping a host, it will show a different IP address.

`$ ping www.google.com`

## Intro to Domain Name System (DNS)

It stores a wide variety of records. The best known record is the A record (A for address), which maps a name like www.example.net to an IPv4 address.

If DNS goes down, users can't visit your website.

## host

`host` is a simple utility for performing DNS lookups. It is normally used to convert names to IP addresses and vice versa. When no arguments or options are given, host prints a short summary of its command line arguments and options.

```
$ host www.google.com
www.google.com has address 172.217.10.36
www.google.com has IPv6 address 2607:f8b0:4006:815::2004

// `-t` flag is the type and `a` is for A record
$ host -t a www.google.com
www.google.com has address 172.217.10.36
```

## dig

`dig` (domain information groper) is a flexible tool for interrogating DNS name servers. It performs DNS lookups and displays the answers that are returned from the name server(s) that were queried. Most DNS administrators use `dig` to troubleshoot DNS problems because of its flexibility, ease of use and clarity of output.

```
$ dig www.google.com

; <<>> DiG 9.9.5-3ubuntu0.16-Ubuntu <<>> www.google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 17336
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1280
;; QUESTION SECTION:
;www.google.com.			IN	A

;; ANSWER SECTION:
www.google.com.		32	IN	A	172.217.3.100

;; Query time: 6 msec
;; SERVER: 10.0.2.3#53(10.0.2.3)
;; WHEN: Wed Oct 11 21:30:58 UTC 2017
;; MSG SIZE  rcvd: 59
```

## DNS Record Types

- A - maps a domain name to the IP (IPv4) address of a computer hosting the domain
- CNAME - Canonical Name. Used to alias to another domain
- AAAA (quad-A) - (IPv6) domain -> IP mapping
- NS - server component of DNS, tells which DNS servers have records for that domain

## DNS is Distributed with Caching

DNS is a distributed directory. Around the world there are several root servers which direct requests to top-level domain (TLD) servers like for .com which in turn direct requests to the servers for particular domains.

Nesting example:
![nesting](/images/nesting.png)

### How does DNS work
1. The DNS server that the client resolver talks to is a nearby caching DNS server. For home user, it might be the home router or ISP. It could also be DNS services like Google Public DNS.
2. When the caching server receives a query from the client, it first consults the local cache.
3. If the cache doesn't know what the record is, the caching server recursively resolves the query by forwarding to the appropriate name servers.
4. Possibly first at the root servers.
5. Then the top-level domain servers (com servers).
6. Then at the lower-level domain until it eventually gets back an answer.
7. Then it stores the results in its cache so it doesn't run the same query over again.
8. Finally, it is able to return the result to the client.

![dns-works](/images/dns-works.png)

Caching can cause problems. Moving your site from one IP address to another and some DNS cache has your old IP address will make the site inaccessible. To resolve this, DNS records have a TTL (time to live) which tells caches how long to keep them. After the time expires, the caching servers will need to look up the record again to make sure the information is fresh.

### SSL and Cookies

When a web app sets a cookie, it sets it for a the particular domain name. Further requests will get the cookie sent back.

SSL encryption certificates are issued for particular domains. It encrypts the traffic between browser and server to prevent networks in the middle from reading private data. It also lets the user's browser verify that the site they're getting data from is actually the site they expect.

## 32 bits to 128 bits

### IPv4
`216.58.217.206`

The older and the majority of what the internet uses today. Each four numbers is one byte or octet, or 8-bits. It will contain a value from 0 to 255.

![ip](/images/ip-addr.png)

### IPv6
`2607:f8b0:4007:0804:0000:0000:0000:1006`

## Bits

With a given number of bits, you can only make a certain number of distinct values. This is usually represented by 2^(# of bits). For example if you have a network with only three bit long addresses, you can only have eight machines on the network.

## Bits in port numbers

16-bits are used to represent a port number up to 65,535.
