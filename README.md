# SimpleDistributedFileSystem
Simple Implementation of distributed file system inspired by GoogleFileSystem

Will divide the entire design into three components:
1. Client - Interface that can be used to create, delete, open , close, read and write files
2. Metadata Server - Master Node that contain the metadata about files, chunks, file to chunk mapping & location of chunks replica.
3. ChunkServer - Node that contain the chunk of data. Client reads or write data to chunk servers

## Client
Whenever a client creates an instance of the SimpleDistributedFileSystem, following operations are performed:
1. Using the fixed chunk size , the client translates the file name and byte offset specified by the application into chunk index within the file.
2. Sends request to the metadata server a request containing the file name and chunk index.
3. The client caches the information received by the master using file name and chunk index as key
4. The client then send the request to one of the replicas

## Metadata Server
Metadata Server stores these info:
1. Files and Chunks namespaces
2. File to chunk mapping
3. Chunk replica count and location of replicas

The first two metadata are store in a persistent operation log and replicated over remote machines. The master does not store the chunk relica location. It fetches that information during master bootup  or whenever a new chunkserver joins the cluster.

For each 64MB file chunk it stores 64 bytes of metadata. It uses prefix compression it.

### Metadata Server <-> Chunkserver
metadata serever sends regular heartbeat messages to chunk server. This heartbeat message can be used for:
1. Chunkserver health
2. Chunks replica placement
3. Lease primary chunk server

### Operation Log
The operation log contains the hostorical record of critical metadata changes.
1. The operation logs are critical and hence are replicated remotely.
2. Chnages are not made visible to the clients until the metadata changes has been made persistent both locally and remotely.

#### Checkpointing and B-Tree


## Chunkserver

