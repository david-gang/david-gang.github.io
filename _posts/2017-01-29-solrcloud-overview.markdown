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

When reading this is was not clear to me what this means and what Zookeeper is.
In general the solr documentation is great for users with previous knowledge in solr but not as an introductory material.

I tried for several days to understand what Zookeeper is, what Solr Cloud is and how they interact.
This will explained below.

# Zookeeper

The name of Zookeeper is quite misleading. A zookeeper normally moves around and checks that all animals behave nice and get food.

This product is quite different. As [wikipedia](https://en.wikipedia.org/wiki/Apache_ZooKeeper) explains: Zookeeper is essentially a distributed hierarchical key-value store, which is used to provide a distributed configuration service, synchronization service, and naming registry for large distributed systems.

The idea behind this is very simple. One of the hardest parts of a distributed system is the coordination and the data passing between these parties.
So let's write a service which accomplishes this and let us expose its services as a library.

Zookeeper is more like a reception

![Reception]({{ site.github.url }}/assets/reception.jpg){:class="img-responsive"}

Zookeper consists of a number of nodes which can be compared to a receptionist. There is nothing special with a certain node. There is no master slave relationship where beforehand we decide who may change the data and from whom we may read.
As here with the receptionist there is no master worker.

Every person can get to every recptionist and get a certain information.

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







