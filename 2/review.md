# review

> The comment is organized by two parts:
>  
1. Brief summary of the paper. 
2. My personal opinions about this paper.


## Summary
This paper is aim at migrating OSes running services with liveness constraints in data center and cluster environments. Live OS migration provides a significant opportunity to separate the hardware considerations with software. In order to explore the design of migration, they comprehensively consider the downtime and total migration time. The former is the period that services are entirely unavailable and the latter is the whole migration process. 

The design of the live VM migration they proposed is followed. The memory migrating approach proposed in this paper is pre-copy migration, which combines a bounded iterative push phase with a typically very short stop-and-copy phase. Besides, the transfer of local resources, principally network resources and local storage, are an innegligible issue. For network resources, the solution is to generate an unsolicited ARP reply from the migrated host, advertising that the IP has moved to a new location. And for local storage, due to the advantage of NAS, it is no need to consider its migration of disk. Furthermore, the design overview can be generalized into six phases: Pre-Migration, Reservation, Iterative Pre-Copy, Stop-and-Copy, Commitment as well as Activation.

WWS (writable working set) refers to a set of pages that are modified frequently. After conducting various experiments with different workload, they conclude that performing a migration will give different results depending on the workload. Besides, some hottest pages must be transferred in the final stop-and-copy phase because of the frequent write.

In order to explore mechanisms for initiating and managing state transfer, two methods are proposed, namely managed migration and self migration. The former is performed by migration daemons while the latter is implemented within the OS being migrated. Both of them track dirtied pages by maintaining a dirty bitmap. More importantly, in order to balance the tradeoff between the effectiveness of migration and the performance of running services, a dynamic rate-limiting algorithm is proposed. The bandwidth of each round is chosen according to the previous round’s dirtying rate. As for the rapid dirty pages, only those pages dirtied in the previous round but have not been dirtied again will be transferred so as to reduce the overhead of network transmission.

For the purpose of exploring the migration of several workloads, they conduct a set of experiments with different workloads including simple web server, complex web workload SPECweb99, multiplayer on-line game server Quake 3 and a diabolical workload MMuncher. The experimental results indicate that the migration can not only control impact on live services, but also gain a short downtime, which validates its applicability and 
practicability.

## Opinions
Advantages:

1.	This paper proposes a complete solution for live migration of VM and validates its applicability through various experiments with different workloads.
2.	Comprehensively considering the downtime and total migration time, as well as the impact on live services, and balancing the trade-off of these three aspects.

Disadvantages:

1.	From users’ perspective, they only care about the downtime (services are entirely unavailable) and the impact on live services, they are indifferent to the total migration time as long as the impact is acceptable.
2.	The dynamic rate-limiting algorithm requires the administrator to select the minimum and maximum bandwidth, which is hard to decide under different circumstances.
3.	According to the dynamic rate-limiting algorithm, the first round has minimum bandwidth. However, maximum page transmission occur in this round, which leads to longest transfer time.
4.	The pre-copy mechanism cannot play a good role when dealing with the pages modified frequently.
