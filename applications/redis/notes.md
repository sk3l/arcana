# Redis Facts and Observations

1. High-availability vs Key Partitioning
   - Redis Sentinel ==> solution for HA, failover detection/remediation
   - Redis Clustering ==> solution for sharding/key partitioning (includes failover?)

1. Data Replication ([official doc](https://redis.io/topics/replication))
   - Redis supports *master-slave* replication
      - writes performed to _master_ node
      - slave nodes are configured read-only **by default** (configurable but best not changed)
   - Replication is performed _asynchronously_
   - Replication can be based on entire data set or delta/incremental ("give me what changed")
   
1. Sentinel ([official doc](https://redis.io/topics/sentinel))
   - monitors set of master-slave instances to detect failure and initiate fail-over
   - Sentintels must reach threshold ("quorum" number) to identify a master failure has actually occurred
   - To _perform_ the failover, Sentinel leader election occurs
      - **must** get majority of Sentinels to agree to execute the failover
      - *cannot perform failover unless **majority** of Sentinel nodes are reachable*
   - clients require support for Sentintel when interacting with Sentintel-managed Redis servers
   - best practices:
      - need, at minimum, **3** Sentinel instances for 'robust deployment'
      - should maintain pool containing **odd number** of sentinel nodes to _ensure consensus_
      - best to arrange Sentinel instances to fail in 'independant way'
         * shouldn't comingle Sentintels with Redis "pod" (masters/slaves)
         * keep Sentintels in different hosts, better yet different DC's
