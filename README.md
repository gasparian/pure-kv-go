![main build](https://github.com/gasparian/pure-kv-go/actions/workflows/build.yml/badge.svg?branch=main)
![main tests](https://github.com/gasparian/pure-kv-go/actions/workflows/test.yml/badge.svg?branch=main)

# pure-kv-go  
Simple and fast in-memory key-value storage with RPC interface, written in go.  

<p align="center"> <img src="https://github.com/gasparian/pure-kv-go/blob/main/pics/logo.jpg" height=300/> </p>  

Features:  
 * uses RPC interface;  
 * supports buckets so you can organize your storage better;  
 * stores byte arrays only;  
 * uses concurrency-effective maps;  
 * supports iteration over maps;  
 * persistant;  
 * doesn't depend on any third-party library;  

### Install  
```
go get github.com/gasparian/pure-kv-go
```  

### Usage  

Client:  
```go
import (
    pkv "github.com/gasparian/pure-kv-go"
)

// creates client instance providing server address and timetout in sec. 
cli, err := pkv.client.InitPureKvClient("0.0.0.0:8001", uint(30))
defer cli.Close() 
// creates the new bucket with specified key-value pair type
err = cli.Create("BucketName") 
// creates new key-value pair in the specified bucket
err = cli.Set("BucketName", "someKey", []byte{'a'}) 
// returns decoded value
val, ok := cli.Get("BucketName", "someKey") 
// makes new iterator for specified bucket
err = cli.MakeIterator("BucketName")
// get next element of bucket
k, val, err := cli.Next("BucketName") 
// async. delete value from the bucket
err = cli.Del("BucketName", "someKey") 
// async. delete the specified bucket
err = cli.Destroy("BucketName") 
```  

[Server](https://github.com/gasparian/pure-kv-go/blob/main/main.go):  
```go

import (
    pkv "github.com/gasparian/pure-kv-go"
)

func main() {
    flag.Parse()
    srv := server.InitServer(
        6666, // port
        60, // persistence timeout sec.
        32, // number of shards for concurrent map
        "/tmp/pure-kv-db", // db path
    )
    srv.Run()
}
```  

### Tests  

Unit tests:  
```
./test.sh
```  

Benchmark tests:  
```
./bench.sh ./{PACKAGE}
```  
Benchmark will generate cpu ans mem profiles, which could be examined with the `pprof` tool.  

Data race tests based on benchmark:  
```
./test_race.sh ./{PACKAGE}
```  

Tracing:  
```
./bench_trace.sh ./{PACKAGE}
```  
Benchmark will generate `trace.out`, you then need to run tool for diagnostics:  
```
go tool trace trace.out
```  
