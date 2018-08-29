# The CAT Project
This is the documentation for the Correlation Attacks against Tor (CAT) research project funded by the [Internet Foundation in Sweden](https://www.iis.se/english/about-iis/) from fall 2018 until March 2019. The principal investigator is [Tobias Pulls](https://www.kau.se/en/researchers/tobias-pulls) at [Karlstad University](https://www.kau.se/en/cs).

<p align="center">
  <a href="https://www.iis.se/english/about-iis/"><img src="img/iis.png" width="64"></a>
  <a href="https://www.kau.se/en/cs"><img src="img/kau.png" width="64"></a>
</p>

The ultimate goal of the project is to contribute to figuring out how to [effectively pad network traffic](https://arxiv.org/pdf/1512.00524.pdf) in [Tor](https://www.torproject.org/) to better resist [some types of traffic analysis attacks](https://blog.torproject.org/tors-open-research-topics-2018-edition).
We will contribute towards this goal by investigating _correlation attacks_ against Tor and the effects of padding.

## Traffic Analysis and Anonymity Networks
One type of attack on anonymity networks is so-called _traffic analysis attacks_ where an attacker observes network traffic and based on observed _non-content_ information---such as packet sizes, timing, communication patterns and volume---attempts to break the anonymity (or unlinkability) properties of the anonymity network.
Traffic analysis has been around for [a long time](https://en.wikipedia.org/wiki/Signals_intelligence) and in the context of anonymity networks it was a key motivation for their inception with the [advent of mixes](https://dl.acm.org/citation.cfm?id=358563).
We're going to look at different types of attacks and attackers on anonymity networks based on how strong attackers are (i.e., their capabilities), starting with two extremes and then the grey zone in-between. While some selected papers will be cited, for a more complete list of relevant literature in the area, see [Free Haven's Selected Papers in Anonymity](https://www.freehaven.net/anonbib/).

### Strong Global Attackers
The strongest possible attacker on an anonymity network (in terms of observed network traffic) is a so-called Global Passive Adversary (GPA) that can passively observe _all network traffic_.
(Note that attackers can be stronger than a GPA if we consider active attacks, compromise of network nodes, and computational power, but we exclude this for sake of brevity.)
A GPA can perform so-called intersection or (statistical) disclosure attacks: based on the assumption that users are more likely to communicate with some (e.g, their friends) than others (i.e., they have a _profile_), over time, their profile can be determined by simply intersecting the anonymity sets observed by the GPA in each communication round.
There are many many papers on these types of attacks see, e.g., [Danezis original paper on statistical disclosure attacks](https://www.freehaven.net/anonbib/cache/statistical-disclosure.pdf) or a more recent take by [Perez-Gonzalez and Troncoso](https://www.freehaven.net/anonbib/cache/leastsquares-pets12.pdf).

GPAs are very powerful as many attacks have shown, and a compelling recent result on [the Anonymity Trilemma](https://eprint.iacr.org/2017/954.pdf) by Das et al. puts it clearly: against a GPA an anonymity network can pick _two out of three_: strong anonymity, low bandwidth overhead, or low latency.
If you're not that familiar with the design of Tor this might come as a surprise: a clearly stated goal (as part of the threat model) of Tor is to offer both low latency and low bandwidth overhead at the cost of strong anonymity. Tor is designed to protect against weaker attackers than GPAs, and exactly where to draw the line on how much weaker is an ongoing topic for discussion. In a gist, in my opinion the sentiment appears towards real-world practical attacks against high-risk users of Tor, as long as the defences have little to no overhead in terms of latency or bandwidth.

### Weak Local Attackers
Significantly weak attackers in close proximity (in the geospatial sense) are squarely within the [threat model of Tor](https://svn.torproject.org/svn/projects/design-paper/tor-design.html#subsec:threat-model). Here you find attackers such as operators of WiFi hotspots and attackers on the same LAN. While weak attackers they can observe the encrypted traffic from a client to the Tor network (guard relay) or the (potentially encrypted) connection from the Tor network (exit relay) to the destination. Since the security of of the connection between the Tor network and destination depends in large on the used transport security protocol like HTTPS, focus has been put on the connection between the client to the Tor network. In terms of mitigations, most traffic is padded into 512 or 514 bytes ([depending on version](https://gitweb.torproject.org/torspec.git/tree/tor-spec.txt)), encrypted, persistent connections maintained to only few Tor relays ([guards](https://gitweb.torproject.org/torspec.git/tree/guard-spec.txt), with [extra padding](https://gitweb.torproject.org/torspec.git/tree/padding-spec.txt)), to name a few.

One relevant and debated type of attack that local attackers can perform are so-called Website Fingerprinting (WF) attacks, where an attacker analyses the encrypted traffic between client and guard relay with the goal of determining the websites visited over Tor by the client.
(We wrote another more detailed [high-level explanation of WF-attacks](https://www.cs.kau.se/pulls/hot/setting/) for a previous project if you want to read more.)
It's currently believed that in general WF-attacks are too inaccurate for most attackers in realistic settings, even for fingerprinting onion services (see, e.g., [Juarez et al.](https://nymity.ch/tor-dns/pdf/Juarez2014a.pdf), [Overdorf et al.](https://arxiv.org/pdf/1708.08475.pdf), and [Jansen et al.](http://wp.internetsociety.org/ndss/wp-content/uploads/sites/25/2018/02/ndss2018_10-2_Jansen_paper.pdf)). That said, it's far from settled and more targetted use of WF-attacks might still be a real threat to some users. There is [ongoing work](https://gitweb.torproject.org/torspec.git/tree/proposals/254-padding-negotiation.txt) to get some form of quickly tunable defence into Tor at some point.

### Attackers In-Between
If we make our attackers stronger we can start to consider internet service providers, autonomous systems (ASes), internet exchange points (IXPs), and nation state actors. Now we're still within the [threat model of Tor](https://svn.torproject.org/svn/projects/design-paper/tor-design.html#subsec:threat-model) but with many nuances and considerations to take into account, which has been a topic of much research the last decade. 
