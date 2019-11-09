+++
title = "TCP Congestion Control III: BBR"
date = 2019-11-09T17:01:32+05:30
categories = ["code"]
tags = ["tcp", "algorithm"]
+++
In the [first post in this series](/post/tcp-congestion), we went with an overview of congestion control in networks (primarily to whet your appetite). Then, in the [second post](/post/tcp-congestion2), we explored some of the basic concepts underpinning most of the loss-based congestion control algorithms. 

Now though instead of going over Reno, Tahoe or CUBIC which frankly are covered quite a bit elsewhere; in this post, lets take a look at BBR. It's the new unruly kid on the block; who dare I say, plays a bit rough in the yard! :smirk:

### Why?
Loss based TCP congestion control algorithms (CCA) balance the need between transmitting data quickly and fairness i.e. sharing the link for multiple clients, with a lot of emphasis on being fair. Hence, most of these algorithms use a type of back off which leads to severe under-utlization of the link bandwidth, almost half of what's available. 

In order to use the available link bandwidth effectively, senders need to find the **optimum operating point**, which is essentially:

- sending at a rate matching the available bandwidth for the connection
- maintaining a volume of data in-flight in the network matching the BDP

This will ideally lead to maximum throughput, but minimum delay. In other words, **the link should be saturated but not congested**. Finding this optimum operating point however has been quite difficult, as it's impossible to measure bandwidth and RTT simultaneously.

### BBR
BBR by the way, stands for **'Bottleneck Bandwidth and RTT'**. It is designed to respond to actual link congestion, rather than packet loss. It finds the optimum operating point by sequentially probing bandwidth and RTT, and using those measurements to maintain an explicit model estimating the bandwidth and RTT of the link.

### Adoption
Service providers have deployed BBR rapidly as there is no need for client support or intermediate network devices. Google already deployed BBR in GCP for instance. BBR is also the default congestion control algorithm for Youtube.

### BBR On Linux
BBR was added for Linux TCP in Linux Kernel v4.9. If you are interested, the code is [here](https://git.kernel.org/pub/scm/linux/kernel/git/davem/net-next.git/tree/net/ipv4/tcp_bbr.c). 

In order to find if BBR is supported on your tcp stack or not, you can run:
```
cat /proc/sys/net/ipv4/tcp_available_congestion_control
```

In order to find which tcp congestion control algorithm is configured for your Linux machine, you can run:
```
sysctl net.ipv4.tcp_congestion_control
```

One notable aspect of BBR is that clients are not required to implement BBR. so, just switch to BBR on server-side and you are good to go. You can do that on a 4.10+ linux kernel as shown below:
```
sysctl -w net.ipv4.tcp_congestion_control=bbr
```

### Phases of BBR
BBR has four different phases: **Startup, Drain, Probe Bandwidth, Probe RTT**

##### Startup phase
The first phase is modeled similarly to the exponential startup behavior in CUBIC. In this, BBR doubles the sending rate with each round-trip in an attempt to measure the available bandwidth. BBR then watches the bandwidth measurement specifically by looking at the actual delivered bandwidth for the last three RTTs to see if it changes. Once the bandwidth stops increasing, BBR assumes that it has reached the bottleneck bandwidth.

##### Drain phase
Note that in measuring the bandwidth, BBR has probably transmitted packets at a high rate for a while; and this would have have led to some queues being filled. In order to drain those packets from the buffers, BBR now goes into a 'drain' state by reducing the pacing gain, when it actually transmits below the measured bandwidth till it has made up for the excess packets sent before. 

##### Probe Bandwidth phase
At the end of the drain phase, BBR is in a sort of steady-state, where it is transmitting at the calculated optimum bandwidth. However, the characteristics of a network connection change over time. So, BBR has to continuously monitor the actual delivered bandwidth. This can only be done by occasionally trying to transmit at a higher rate than current. This is when BBR enters the Probe Bandwidth phase. It scales up the transmit rate by 25% about 1/8 of the time. If the increased transmission does not lead to increase in data delivery rate, then it means that the optimum bandwidth is still into play.

##### Probe RTT phase
After probing bandwidth, BBR follows it up by a drain period to even out any excessive packet transmission. This is called the Probe RTT phase. During this phase, the bandwidth is reduced to 4 packets to drain any possible queues and get a real estimation of the RTT.

### Is it Fair?
That's the million dollar question. Isn't it? 

BBR’s fairness to other connections has been a point of discussion for a while. Most empirical measurements have shown it to be unfair when competing with traditional loss-based CCAs. Experiments have led to the following 2 key observations:

- In shallow-buffered networks, BBR’s bandwidth probing phase causes buffer overflows and bursty loss for competing flows. These bursts can lead to Cubic and Reno flows being starved for bandwidth. This will get fixed in [BBRv2](https://github.com/google/bbr/blob/v2alpha/net/ipv4/tcp_bbr2.c).
- In residential capacity links (e.g. 10-100Mbps) with deep buffers, conflicting observations are reported on how BBR shares bandwidth with competing Cubic and Reno flows. For example, one of the experiments observed a single BBR flow consume almost 40% of link capacity when competing with as many as 16 Cubic flows. However early claims were made that BBR was generous to competing Cubic flows. This is quite confusing.

Very recently, a new paper has come up with a mathematical model to explain BBR's behavior in competition with loss-based CCAs. One of the key findings from this paper is that **while BBR is a rate-based algorithm when running alone, it degrades to window-based transmission when it competes with other flows** :boom:. Read the full paper [here](http://www.justinesherry.com/papers/ware-imc2019.pdf).

In fact, there is now a new **harm-based approach** being proposed in lieu of the **fairness index** to measure the deployment threshold for inter-CCA competition primarily driven by the unfair behavior of some of the new CCAs. The proposal is [here](https://drive.google.com/file/d/1S8cZLq17PbVGuUXVVuYmaPlV5YAFupf4/view). 

While it will take some time for the community to arrive at a consensus on finalizing this new proposed approach and eventually rolling this out; it's a good first step in moving beyond the fairness index to address the problems of CCA deployments in today's networks.

Keep an eye on this exciting space. It's :fire:!





