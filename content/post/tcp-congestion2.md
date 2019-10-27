+++
title = "TCP Congestion Control II: Concepts"
date = 2019-10-27T11:20:23+05:30
categories = ["code"]
tags = ["tcp", "algorithm"]
+++
Ah, so we're back again. Let's see if we can cover some distance this time.

In the [last post](/post/tcp-congestion), we went with an overview of congestion control in networks. In this post, lets take a look at some of the concepts mentioned in the previous post and some new ones to aid our understanding of this subject.

### Modeling Networks

While modeling networks in order to figure out strategies to combat congestion, one question almost always pops up. A network has so many end hosts, links and network elements. How do we figure out congestion control in such a network? 

Well, the way to look at it is that at any time, there will be 1 slowest link or bottleneck in any given path from host A to B. This slowest link will determine the connection's maximum rate of data transfer. Eventually, all the queues for links which are upstream of this bottleneck will migrate to the bottleneck since they have faster departure rates. So, this path between A to B can be modeled as a single link with the same RTT (round-trip time) and bottleneck rate. 

### Bandwidth Delay Product (BDP)

Delay of a link is the time it takes for one packet to go from start to end of a link. Bandwidth of a link is the number of packets that can pass through the link per second. BDP is a product of the bandwidth and the delay of the link. Essentially, it's the number of bytes that can fit on the link at any given point in time.

### Congestion Window (CW)

CW is the number of packets that a sender can send without having seen an ACK yet. If the CW on the sender is 5, it means that after the sender has sent 5 packets, it must wait to get an ACK in order to send any more. CW is essentially the number of packets in-transit or in-flight at any given point of time.

Higher the CW, more the number of packets we can send in the same time period. However, raising CW too high will lead to congestion. The goal of a congestion-control algorithm, therefore is to figure out the right size of CW. Ideally, it should be equal to the BDP of the link. This would mean that the link is fully utilized. 

### Additive Increase/Multiplicative Decrease (AIMD)

AIMD is a generic feedback control algorithm. In the context of TCP congestion, this dictates additive increase of the CW till congestion is detected. On congestion detection, the algorithm mandates multiplicative decrease of CW. Effectively with AIMD, CW increase is linear to the point of congestion detection; the CW decrease thereafter is exponential.

Different loss-control algorithms use variations of this generic algorithm leading to different link-utilization behavior. For instance, Tahoe on detecting congestion resets CW to 1 Packet. Reno on the other hand, resets CW to half of the previous CW. 

### Slow Start

The linear CW increase in AIMD works well, when sender is operating close to BDP. However, when starting from scratch it's fairly slow to utilize the available link capacity. That's why TCP provides a different mechanism called Slow Start (misnomer?). Slow Start actually increases CW exponentially rather than linearly. This is how it operates:

- Sender sets CW to 1 packet and sends it out.
- When it receives the ACK, it sets CW to CW+1.
- Sender then sends 2 packets. 
- On receiving corresponding 2 ACKs, it sets CW to CW+2 (one for each ACK) 
- Sender then sends 4 packets and so on and so forth.

As you can see, this is exponential increase.

When does Slow Start run? It actually runs in two different situations.

- The first is at the start of a connection, when the sender has no idea about the network capacity and hence no known value of CW.
- The second is when the connection goes dead while waiting for a timeout to occur. In this case, sender has a value of CW, lets call it CW<sub>target</sub> which is the CW (prior to congestion detection)/2. In this case, sender using Slow Start exponentially increases CW to get to CW<sub>target</sub> and then switches to additive increase till it detects congestion again. 

### Fast Retransmit

Fast retransmit triggers resend of dropped packets sooner than regular timeouts. This is done to improve link utilization. This is how it works: 

- Every time a packet arrives out of order at a receiver, it resends the same ACK it had sent last time. This is duplicate ACKing. 
- When the sender sees a duplicate ACK, it knows that the receiver has received a packet out of order. 
- But the earlier packet could be lost or is just delayed. So, the sender waits & does not retransmit yet. 
- When it receives the duplicate ACK thrice, it retransmits the missing packet. 

This simple retransmit mechanism has generally lead to a 20% improvement in link utilization.

### Finally

Alright, we have covered the basic concepts underlying TCP congestion control mechanisms. Let's go forwards from here another time. Maybe pick up one of the algorithms? 

We'll see. Ciao! 
