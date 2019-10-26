+++
title = "TCP Congestion Control: I"
date = 2019-10-26T13:38:18+05:30
categories = ["code"]
tags = ["tcp", "algorithm"]
+++
It's been a while since I picked up the proverbial pen to jot down my ramblings. The usual trappings of work and life have encumbered me. But then again, the curious mind just needs to take a liking to something for the muse to strike. So here I am; digging into TCP congestion control mechanisms & the underlying algorithms. 

Cause, heck; why not!

Networks are fascinating things; Links and network elements working together to ferry bytes from point A to point B. As with everything else, each link and network element has varied resources and performance. Ah, the constraints of the real world! 

Any real network is not infinitely scalable and has limited data carrying capacity. Given that, how does it effectively and fairly allocate resources among it's users? Remember, the resources are the bandwidth of the links, the buffers on the routers/switches etc. Age old problems! 

All packets contend at a router for the use of a link. Each such contending packet is placed in a queue on the router, where it waits for its turn to be transmitted over the link. When you have too many such packets contending for the same link, the queue fills up. At first the packets start experiencing increased end-to-end delay, and eventually the queue overflows and packets are dropped by the router. Over time, if these long queues become the norm and drops more frequent, the network is said to be congested. 

Most networks provide congestion-control mechanisms to deal with such situations. These mechanisms are typically available and implemented on both hosts and network elements like routers/switches. Let's dig into how they typically work on hosts for now.

Each host (the one that's starting the transmission) or more specifically its tcp stack needs to gauge how much capacity is available in the network. That will help it in figuring out how many packets it can safely have in transit at any given time. The network however does not advertise this information (duh) so it's up to each host to determine it on it's own. How does it do that? Well, it starts off by sending packets. When it recieves an ACK, it now knows that one of its packets has left the network. Which means, that the network has the capacity to handle one more packet now i.e. without increasing congestion. The host uses these ACKs to pace the transmission of packets. Of course, there are many other hosts using the network and at any point of time they may or may not be using the network, which means that the available network capacity changes over time. This means that any host must be able to adjust the number of packets it has in transit factoring all these in dynamically. Well, good luck doing that effectively! But as it turns out, TCP provides vaious algorithms for just this purpose.

Most congestion control algorithms are loss-based i.e. they depend on packet loss as a signal to control the packet transmission rate. Some of them are **Tahoe, Reno and CUBIC**. All of these are typically based on some common underlying concepts like:

- Congestion Window: https://en.wikipedia.org/wiki/TCP_congestion_control#Congestion_window
- AIMD (Additive Increase/Multiplicative Decrease): https://en.wikipedia.org/wiki/Additive_increase/multiplicative_decrease
- Slow Start: https://en.wikipedia.org/wiki/TCP_congestion_control#Slow_start
- Fast Retransmit: https://en.wikipedia.org/wiki/TCP_congestion_control#Fast_retransmit

Then there is **BBR** which gives relatively little weight to packet loss. Instead, it focuses on actual bandwidth of data delivered to the other end of the pipe. Whenever an ACK is received, it updates its measurement of the amount of data delivered. The cumulative sum of data delivered over a given period of time is a reasonably good indicator of the bandwidth the connection is able to provide; which it relies on. 

Now, I imagine all this is too hard to chew and as it is, its too taxing on my fingers to get this laid out in one go; so I'll call it a day and leave it to you to go look into these algorithms and the concepts on which most of these are based on. 

In subsequent posts, hopefully I'll get to some of them. Hope lives. Cheers!  


















