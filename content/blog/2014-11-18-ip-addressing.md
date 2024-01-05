+++
title = "IP Addressing (and a segue into CJDNS)"
+++

In this post, I'll be lightly elaborating on a 5-minute talk I've yet to give to my classmates at [Hack Reactor](http://www.hackreactor.com/). We'll start with IPv4 addresses, their format and what's known as [address exhaustion](http://en.wikipedia.org/wiki/IP_address#IPv4_address_exhaustion) before going over the advantages to the new IPv6 address format and finishing with an overview into [CJDNS](https://github.com/cjdelisle/cjdns).

# IPv4

![ipv4 byte scheme](http://upload.wikimedia.org/wikipedia/commons/thumb/7/74/Ipv4_address.svg/500px-Ipv4_address.svg.png)

IPv4 addresses are composed of 4 sections of 8-bit numbers, giving us an address space of (2^8)^4 addresses, or for those who don't speak in exponentials, a maximum of 4,294,967,296 unique addresses. However, the amount actually addressable to individual devices is far less since many of these are (by convention) inaccessible or reserved for other uses.

For example, some addresses were allocated to some big companies of the 90's, such as Xerox, Apple and Halliburton. Other addresses and address spaces, were allocated for future private use. Two such addresses are http://127.0.0.0 and http://127.0.0.1 and are known as the loopback addresses, only accessible locally on your computer or server (hence the more common URL, http://localhost).

There are some ranges reserved for "private networks," some of which you might recognize as your router's IP address (192.168.0.1, 10.0.0.1 or 172.16.0.0). By giving routers a unique global address (and giving their connected devices a derivative address from one of the three private network address spaces listed above), we've been able to mitigate this [address space exhaustion](http://en.wikipedia.org/wiki/IP_address#IPv4_address_exhaustion) issue and avoid its implications. But considering there may be as many 16 billion internet-connected devices by the end of 2014, even this scheme will have to come to an end.

# IPv6

![ipv6 byte scheme](http://upload.wikimedia.org/wikipedia/commons/thumb/1/15/Ipv6_address.svg/500px-Ipv6_address.svg.png)

The next iteration of IP addressing is known as IPv6 and makes quite a large change to their format. They are composed of 8 groups of 4 hexadecimal characters, giving us an address space of (16^4)^8 == 2^128, or 3.402 e38 addresses. Interestingly enough, this scheme was named in 1995, meaning that this problem was not only forseen almost 20 years ago, but it hasn't reached significant adoption. But this brings me to my next point and what I really want to talk about...

# CJDNS

[CJDNS](https://github.com/cjdelisle/cjdns) is an open-source, p2p encrypted meshnetting software and protocol whose developers aim make it the backbone of a new, NSA-proof internet (a goal that, while very, very lofty, might actually be accomplished at some point in the future).

Although it's use constitutes a separate, otherwise inaccessible-to-the-rest-of-the-world internet, I included it here in this post because it does something very interesting: it assigns unique IPv6 addresses to all of it's nodes from the get-go.

![OSI Model](https://3.bp.blogspot.com/-5swPB9xcxfg/VIiAnVbe4NI/AAAAAAAANRs/lpGBkKiNeRE/s1600/1-tutorial-osi-7-layer-model.gif)

On what we know as the World Wide Web, traffic can be encrypted between a browser and server on the presentation layer of the OSI Model using the SSL protocol, which is now used almost everywhere. However, there are some weaknesses with SSL (excluding [bugs](http://heartbleed.com/) in some of its various implementations), namely the required existence of trusted third-parties.

Rather than resort to this layer for encrypting traffic, CJDNS operates and encrypts on the IP layer, just above the two layers responsible for actually carrying and delivering internet traffic. In effect, CJDNS completely hijacks and rewrites the book on how IP-layer traffic and routing work and the responsibilities they should inherit.

What's even more interesting is the fact that the addresses themselves are the hash of the public keys used to encrypt the packets sent between any two nodes. This means that you can always be certain that the address you are communicating with is responsible for the encryption of the traffic you receive from them and that the traffic has not been man-in-the-middled; who that user is and whether or not you can trust them is unfortunately a completely different question.

I'm going to end this post here, but will continue this line of thought in another post in the near future, and will hopefully be connected to its most popular intranet known as Hyperboria; if so, I'll be sure to leave a tutorial for getting started with this super-cool software.
