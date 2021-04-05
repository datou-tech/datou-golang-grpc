## datou-golang-grpc
---

#### Overview
Refresh Grpc (w/GoLang) in 5 min. Learn in 60 min.

#### Pre-requisite
- Install GoLang - `brew install golang`
- Install Protocol Buffer Compiler - `brew install protobuf`
- Install GoLang Protobuf Plugin - <br/>
    ```
        $ export GO111MODULE=on  # Enable module mode
        $ go get google.golang.org/protobuf/cmd/protoc-gen-go \
         google.golang.org/grpc/cmd/protoc-gen-go-grpc
    ```

#### Parts of a Protobuf
- service - with rpc methods inside of 4 possible types
    ```
        service DatouService {
            rpc SimpleFeature(SomeA) returns (SomeB) {}
            rpc ServerStreamingFeature(SomeA) returns (stream SomeB)
            rpc ClientStreamingFeature(stream SomeA) returns (SomeB)
            rpc BiDirectionalStreamingFeature(stream SomeA) returns (stream SomeB)
        }
    ```
- message - the transport object definitions
- proto primitive types<br/>
| protobuf | GoLang |
| --- | --- |
| double | float64 |
| float | float32 |
| int32 | int32 |
| int64 | int64 |
| bool | bool | 
| string | string |
| bytes | []byte |


#### Tracing the Server/Client
- Protocol Buffer is defined in a `.proto` file
- To compile the proto file (codegens serialization `*pb.go` and server/client code `_grpc.pb.go`)
    ```
        $ protoc --go_out=. --go_opt=paths=source_relative \
        --go-grpc_out=. --go-grpc_opt=paths=source_relative \
        helloworld/helloworld.proto
    ```
- To start server - open tcp, create new server and register the protobuf
    ```
        lis, err := net.Listen("tcp", port)
        if err != nil {
            log.Fatalf("failed to listen: %v", err)
        }
        s := grpc.NewServer()
        pb.RegisterGreeterServer(s, &server{})
        if err := s.Serve(lis); err != nil {
            log.Fatalf("failed to serve: %v", err)
        }
    ```
- NOTE: server/client code is an unimplemented stub and the code written leverages receiver functions to override (thus implement) the behavior
- To generate client - dial the address of the server and create a new client
    ```
        conn, err := grpc.Dial(address, grpc.WithInsecure(), grpc.WithBlock())
        if err != nil {
            log.Fatalf("did not connect: %v", err)
        }
        defer conn.Close()
        c := pb.NewGreeterClient(conn)
    ```

#### References
- A more indepth starter guide can be found [here](https://grpc.io/docs/languages/go/basics/)
