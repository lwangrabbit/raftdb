raftdb
======

raftdb is a simple distributed key value store based on the Raft consensus protocol. It can be run on Linux, OSX, and Windows.

## Running raftdb
*Building raftdb requires Go 1.9 or later. [gvm](https://github.com/moovweb/gvm) is a great tool for installing and managing your versions of Go.*

Starting and running a raftdb cluster is easy. Download raftdb like so:
```bash
go get github.com/hanj4096/raftdb
```

Build raftdb like so:
```bash
cd $GOPATH/src/github.com/hanj4096/raftdb
go install
```

### Bring up a cluster
Run your first raftdb node like so:
```bash
$GOPATH/bin/raftdb -id node01  -haddr raft-cluster-host01:8091 -raddr raft-cluster-host01:8089 ~/.raftdb
```

Let's bring up 2 more nodes, so we have a 3-node cluster. That way we can tolerate the failure of 1 node:
```bash
$GOPATH/bin/raftdb -id node02 -haddr raft-cluster-host02:8091 -raddr raft-cluster-host02:8089 -join raft-cluster-host01:8091 ~/.raftdb

$GOPATH/bin/raftdb -id node03 -haddr raft-cluster-host03:8091 -raddr raft-cluster-host03:8089 -join raft-cluster-host01:8091 ~/.raftdb
```


run in localhost:
```
./raftdb -id node1 -haddr localhost:9091 -raddr localhost:8091 ./node1

./raftdb -id node2 -haddr localhost:9092 -raddr localhost:8092 -join localhost:9091 ./node2
./raftdb -id node3 -haddr localhost:9093 -raddr localhost:8093 -join localhost:9091 ./node3
```

show cluster status:
```
# curl http://localhost:9091/status
{"leader":"localhost:8091","peers":["127.0.0.1:8091","localhost:8092","localhost:8093"]}
```


## Reading and writing keys
You can now set a key and read its value back:
```bash
curl -X POST raft-cluster-host01:8091/key -d '{"foo": "bar"}' -L
curl -X GET raft-cluster-host01:8091/key/foo -L
```

You can now delete a key and its value:
```bash
curl -X DELETE raft-cluster-host02:8091/key/foo -L
```

### Three read consistency level
You can now read the key's value by different read consistency level:
```bash
curl -X GET raft-cluster-host02:8091/key/foo?level=stale
curl -X GET raft-cluster-host02:8091/key/foo?level=default  -L
curl -X GET raft-cluster-host02:8091/key/foo?level=consistent  -L
```


