---
layout: post
title:  "Overview of SolrCloud"
date:   2017-01-29 13:51:59 +0200
categories: solr
---

In the following post we will discuss the general architecture of SolrCloud.

# Introduction 

The  [solr documentation](https://cwiki.apache.org/confluence/display/solr/SolrCloud) describes SolrCloud the following way:

Apache Solr includes the ability to set up a cluster of Solr servers that combines fault tolerance and high availability. 

Called SolrCloud, these capabilities provide distributed indexing and search capabilities, supporting the following features:

* Central configuration for the entire cluster
* Automatic load balancing and fail-over for queries
* ZooKeeper integration for cluster coordination and configuration.

In order to understand this description let's understand what the motivation of SolrCloud is:

## High availability

In every distributed system, a single point of failure should be avoided. 
This means that even after an outage of a server we should be able to:
* Update data
* Read data

## Data consistency
The data should be eventually consistent across all replicas of a certain data. Also data race conditions should be avoided.
This can happen when two nodes are updated with different data for a certain key.

## Data distribution
Typically the data indexed by cloud is bigger than one node can handle. This requires that the data can be splitted in a reasonable way across nodes.
This also means that the clients querying the index should get answers from all data splits.

## Load balancing

If the data is replicated across nodes, the clients should ideally spread the queries across all nodes, in order to avoid bottlenecks. 


## Cluster coordination and configuration
All solr nodes should have the same configuration. Additionaly there should be an easy way for clients to know  

# How SolrCloud tackles these challenges

In Solr cloud indices are called collections. 
Ever collection is split into shards, which contains parts of the data.
Every shard is stored on a certain Solr node. Every shard can be replicated on multiple nodes.
  
##   High availability
The replication factor ensures, that even when a part of the servers fail, the system still is functioning. The number of servers which can fail depends on the replication factor.
For a replication factor of n we can ensure that n-1 failures won't change the results.
Additionally there is no predefined master node, which makes the data updates, so in theory every node which contains the replica can perform write operations.

## Data consistency
Even if in theory every node can update the index data of a certain shard, practically at every certain timepoint there is a leader node which makes the data updates.
The leader node is elected after the system goes up and every time the previous leader goes down.
When a client sends an update request to a node which is not the leader, the request is redirected to the leader node.
This prevents race conditions as just one node can update the data at a certain time point.

## Data distribution
The split into shards fixes the data distribution problem. One point which is not so nice in SolrCloud is that there is no out the box rebalancing mechanism.
Either a certain shard can be split into two or the whole data can be loaded into a new collection with a different number of shards.
There is no mechanism which can be done in place.

## Load balancing

The Solr4j client knows how to distribute the queries around all replicas of a certain shard. It uses Zookeeper for it.

# Zookeeper

The name of Zookeeper is quite misleading. A zookeeper normally moves around and checks that all animals behave nice and get food.

This product is quite different. As [wikipedia](https://en.wikipedia.org/wiki/Apache_ZooKeeper) explains: Zookeeper is essentially a distributed hierarchical key-value store, which is used to provide a distributed configuration service, synchronization service, and naming registry for large distributed systems.

The idea behind this is very simple. One of the hardest parts of a distributed system is the coordination and the data passing between these parties.
So let's write a service which accomplishes this and let us expose its services as a library.

Zookeeper is more like a reception

![Reception]({{ site.github.url }}/assets/reception.jpg){:class="img-responsive"}

Zookeper consists of a number of nodes which can be compared to secretaries. There is nothing special with a certain node. There is no master slave relationship where beforehand we decide who may change the data and from whom we may read.
As here with the secretaries there is no master worker.

Every person can get to every secretary and get a certain information. Additionally every person can ask the secretary to get notified when a certain event occurs.

The difference between Zookeeper and a reception is that only one node at a certain time can change the data.
This node is called the leader node. The leader node is elected dynamically. The election process is not explained here.
When a party wants to update data and gets to a node which is not the leader, it is redirected automatically to the leader.

At the bottom point Zookeeper provides a distributed high available key value store, which allows distributed applications to distribute metadata and coordinate actions across all parties.

# How is zookeeper used in SolrCloud

SolrCloud uses Zookeeper in the following ways:

* Solr instances coordination - The list of solr instances for a certain collection are stored at Zookeeper.
* Leader election - Like Zookeeper, Solrcloud also selects leaders for each shard. Just this leader can update the index and then sends the data to all other replicas. This prevents race conditions
* Load balancing - The Solr4j comes with a smart client which is aware of zookeeper. It uses this fact to spread the requests across all solr replicas evenly
   
## Zookeeper instance list

As a consequence of this design, the following parties needs to know all the instances of zookeepers

* Zookeeper instances
* Solr instances
* Solr4j Clients

This makes the manual addition of zookeeper instances very tedious. The situation will improve with zookeeper 3.5 as explained [here](http://mail-archives.apache.org/mod_mbox/lucene-solr-user/201701.mbox/%3Ca768c4f2-a284-9179-e505-919014b29818%40elyograg.org%3E) but as it is still in alpha this does not help too much.
The solution is either to 

* Administrate the configuration through puppet as explained [here](http://mail-archives.apache.org/mod_mbox/lucene-solr-user/201701.mbox/%3Czarafa.5889fbcc.69a6.5e96630819e09ea9%40mail1.ams.nl.openindex.io%3E)
* Start with a reasonable number of zookeeper instances so the list does not have to be changed. It is recommended to start with 5 zookeeper instances as explained in the [SO post](http://stackoverflow.com/questions/41874718/solr-cloud-what-is-the-recommended-number-of-zookeepers-per-solr-instances/41931285#41931285). Then the zookeeper instances can be distributed as hard coded.







