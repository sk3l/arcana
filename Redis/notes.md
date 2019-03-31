# Redis Facts and Observations

1. High-availability vs Key Partitioning
   - Redis Sentinel ~~> solution for HA, failover detection/remediation
   - Redis Clustering ~~> solution for sharding/key partitioning (includes failover?)

1. Data Replication ([official doc](https://redis.io/topics/replication))
   - Redis supports *master-slave* replication
     - writes performed to _master_ node *by default*
     - slave nodes are configured read-only *by default* (configurable)
   - Replication is performed _asynchronously_
   - Replication can be based on entire data set or delta/incremental ("give me what changed")
   
1. Sentinel ([official doc](https://redis.io/topics/sentinel))
   - monitors set of master-slave instances to detect failure and initiate fail-over
   - uses leader election & consensus to promote slaves to masters ("quorum")
     - should maintain pool containing *odd number* of sentinel nodes to _ensure consensus_
   - prefer to _not_ stack sentinel instances along with master/slaves instances on same nodes
     - don't wish to lose both cache instances _and_ sentintels if single host goes down
