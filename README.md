# NoSQL DB Environments

## Learning Goals

- Experience standing up local dev environments for common NoSQL Systems
- Experience constructing simple queries for each major type of NoSQL system

## Instructions

The best way to get familiar with any new Database system or technology stack is through documentation and experimentation. Unfortunately,
documentation isn't always correct, or even clear in its intent, so the best way to approach evaluation is through some real hands on demonstration. Dockerized environments
have made this a much more accessible experience for developers.
In this lab, we will be setting up several common NoSQL DB stacks, and experimenting with them to see how they are used,
and how their data models are constructed.

As all these systems can be deployed via local Docker containers, this is a practice that should be similar to how we've
been using Postgres, and can be done for many more systems.

## Redis

Redis is a very commonly used instance of a Key Value store type system.

You can dig into all the variations of how to launch a Redis Docker instance from the documentation on its [Docker Hub page](https://hub.docker.com/_/redis). The below command should get you started quickly though.

``` text
docker run --name redis-lab --network labnetwork -d redis:7.0.4-bullseye
docker exec -it redis-lab /bin/bash
root@cb2bc569195b:/data# redis-cli
127.0.0.1:6379> ping
```
``` shell
PONG
```
``` text
127.0.0.1:6379> set mykey somevalue
```
``` shell
OK
```
``` text
127.0.0.1:6379> get mykey
```
``` shell
"somevalue"
```

At this point, we have a running Redis instance ready for use and to develop locally with. Take a look at the Redis documentation and run through one of the initial 
[tutorials](https://redis.io/docs/manual/data-types/data-types-tutorial/). We'll only be diving more in depth with Cassandra, so this is probably a good primer.


## Elasticsearch

Elasticsearch is a fairly popular text indexing and search engine, and falls in the category of document based NoSQL systems.

We can setup a working instance with the following:

``` text
docker run --name elasticsearch-lab --network labnetwork -d -p 9200:9200 -p 9300:9300 \
    -e "discovery.type=single-node" \
    -e "xpack.security.enabled=false" \
    -e "xpack.security.http.ssl.enabled=false" \
    -e "xpack.security.transport.ssl.enabled=false" elasticsearch:8.3.2

curl -X POST "localhost:9200/test_index/_doc/1?pretty" -H 'Content-Type: application/json' -d '{ "mykey": "somevalue" }'
```
``` shell
{
  "_index" : "test_index",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```
``` text
curl -X GET "localhost:9200/test_index/_doc/1?pretty"
```
``` shell
{
  "_index" : "test_index",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 0,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "mykey" : "somevalue"
  }
}
```

There are some good getting started guides in the Elasticsearch documentation you can run through. Follow through the below using `curl` as a client.

- [Elasticsearch Quick Start](https://www.elastic.co/guide/en/elasticsearch/reference/7.17/getting-started.html)


## Neo4j

Graph based NoSQL systems tend to have a more niche use case, so you don't tend to see them being used to solve
general use case problems all that frequently.
One of the more popular offerings would be Neo4j, which is fairly easy to get up and running with. Try running through
one of the Neo4j getting started guides, and you should quickly see how these graph relationships would work in practice.


``` text
docker run --name neo4j-lab --network labnetwork -d neo4j:4.4.9-community
docker exec -it neo4j-lab /bin/bash
root@3ad5cf322de1:/var/lib/neo4j# cypher-shell -u neo4j -p neo4j
```
``` shell
Password change required
new password:
confirm password: 
Connected to Neo4j using Bolt protocol version 4.4 at neo4j://localhost:7687 as user neo4j.
Type :help for a list of available commands or :exit to exit the shell.
Note that Cypher queries must end with a semicolon.
neo4j@neo4j> 
```

Try running through one of the Neo4j getting started guides, and you should quickly see how these graph relationships would work in practice.

> Note: The guide assumes a different client than the built in shell from above. One semicolon is needed after every cypher block, not per line.

- [Neo4j Introduction](https://neo4j.com/docs/cypher-manual/4.4/introduction/)


## Cassandra

Cassandra can be placed in the category of Wide Column NoSQL database systems. We will be spending the next day
diving into this system in more depth, but we can still go over the same exercise here to get a quick testable lab environment
started. 

``` text
docker run --name cassandra-lab --network labnetwork -d -p 9042:9042 cassandra:4.0.4
docker exec -it cassandra-lab /bin/bash
# Might need to wait some time for startup to complete
root@96eaf3fafc82:/# cqlsh
```
``` shell
Connected to Test Cluster at 127.0.0.1:9042
[cqlsh 6.0.0 | Cassandra 4.0.4 | CQL spec 3.4.5 | Native protocol v5]
Use HELP for help.
```
``` text
cqlsh> SELECT cluster_name, listen_address FROM system.local;
```
``` shell
 cluster_name | listen_address
--------------+----------------
 Test Cluster |     172.17.0.5

(1 rows)
```

There is a fairly trivial quickstart guide you can take a look at [Cassandra Quickstart](https://cassandra.apache.org/_/quickstart.html)
There isn't much content there to follow, but it should be enough for you to see that it greatly resembles the Relational
Database structure we've been using up until now.


## Advanced Lab

We won't be putting too much focus into other NoSQL systems beyond Cassandra at this point. If you want to continue reading through
the documentation for these systems, feel free to explore and experiment with any of the ones you find interesting.
