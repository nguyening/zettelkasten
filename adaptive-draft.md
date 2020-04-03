---
tags: [@computer-networking, @flow-control, @qos]
title: Admission control is a requirement for guaranteeing QoS
---

You cannot guarantee Quality-of-Service without checking if adding new data flows will violate existing flows in a network.

## References

T.-Y. Tan, et al. "Adaptive resource negotiation based control for real time applications".

---
tags: [@computer-networking, @qos]
title: Algorithmic approaches to QoS admission control
---

- To admit flows into a network, there are two approaches to setting up parameters for your algorithm:
    - (a) *after* understanding traffic patterns, a priori.
      It is difficult to be responsive when traffic deviates.
    - (b) *while* measuring traffic patterns.
- Real traffic measurements from (b) enable the algorithm to accurately reserve network resources, leading to better utilization.
    - With the caveat that applications can handle the network rejecting flows, i.e. some loss of data.

---
tags: [@computer-networking, @qos]
title: Token Bucket Filter on a packet network
---

The *Token Bucket Filter* models a network where participants can describe the traffic that they expect to generate/support.

RFC 2215 specifies several parameters as TOKEN_BUCKET_TSPEC to describe the model:
    - b, token bucket size (units)
    - r, token generation rate (units/time)
    - P, peak rate (units/time)
    - M, max discrete workload size (units)
    - m, min size, i.e. round up to this size for discrete workloads (units)

Consider a network trafficking packets between nodes. Each node can be described as a token bucket on parameters *{b, r}*, where b is in bits and r is in bits-per-second.

- The token bucket cannot hold more than b tokens.
- Assume that packets have random sizes and that a node cannot transmit partial packets.
  This implies that a node cannot transmit a packet if there are not enough bucket tokens to account for the packet bit size. Such packets will be held in a first-in-first-out (FIFO) queue until the number of tokens is greater-than-or-equal-to the packet bit size.
- For packets the time spent waiting in the FIFO queue is called the *queuing delay*.
    - Take *Q* to be the bit capacity of the FIFO queue.
      The worst-case queuing delay is the time for the last packet in the queue to be transmitted, i.e. *Q/r*.
- A node's FIFO queue can be bounded in size. When the queue is full, packets are dropped.
  We can characterize this with a measure called the *cut-off rate*, the percentage of packets dropped due to lack of tokens and insufficient capacity in the FIFO queue.

---
tags: []
title: Optimizing Token Bucket parameters
---

Ideally we would like to minimize packet (a) cut-off rate and (b) queueing delay, i.e. an optimal token bucket always has enough tokens to transmit the packets that arrive.

## Without queueing
Consider an optimally configured token bucket *without a queue*. This bucket has infinite capacity but has a token generation rate that varies with time.
Over a discrete time interval we consider every packet that arrives to exhaust the token bucket concurrently.

**Optimal token bucket size**
- The size, or the total tokens in the bucket, over a discrete time interval is the sum of (a) the number of tokens in the bucket at the start of the time interval, and (b) the token generation rate multiplied by the length of the time interval.
- The size of an optimal bucket >= the total bits of all packets that arrive in an interval.
- Network nodes only transmit whole packets, so an optimal bucket always has *at least* as many tokens as the largest arriving packet.
  If the bucket size is smaller than this largest packet, such packet will be dropped.
- An optimal bucket size is *at most* equal to the number of accumulated tokens between packet arrivals.

**Optimal token generation rate**
- An optimal bucket generates tokens when its size becomes less than the size of packets that arrive in a time interval.
  An infinitely high generation rate accumulates more tokens than necessary.
- For an empty bucket, the optimal generation rate is *at least* equal to the average bit transmission rate.
- The generation rate peaks in the scenario where the bucket is empty and the largest packet in the time interval arrives.
  Thus an optimal rate is *at most* equal to the maximum bit transmission rate.

## With queueing
Consider an optimal token bucket with finite capacity and a FIFO queue of finite capacity.

- A non-zero capacity FIFO queue relaxes the optimal size or generation rate by introducing a time delay when token requirements cannot be met.
  Network resources are better utilized because fewer tokens are required for immediate consumption in worst-case packet scenarios. Bursty traffic is simply queued.


---
tags: []
title: Measurement-based QoS admission control
---


