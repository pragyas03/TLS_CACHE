## TLS Cache
------------------------

The directory structure is as follows:
```
certificates/	// Contains CA and server certificates.
scripts/	// Helper scripts.
src/		// Client and Server code. Add your code here.
cmake/		// CMake find script. 
extern/		// Required third party tools and libraries- LibreSSL & CMake.
licenses/	// Open source licenses for code used.
```


### Steps
-------------------------
1. Download and extract the code.
2. Run the following commands:
```
$ cd TLSCache
$ source scripts/setup.sh

Generate the server and client certificates
$ cd certificates
$ make
```
3. The plaintext server and client can be used as follows:
```
$ cd TLSCache

Run the server:
$ ./build/src/server 9999

Run the client (in another terminal):
$ ./build/src/client 127.0.0.1 9999
```

### How to build and run code
--------------------------
1. Add your code in `src/client` or `src/server`. 
2. Go to `build/`
3. Run `make`


### Scripts included
--------------------------
1. `setup.sh` should be run exactly once after you have downloaded code, and never again. It extracts and builds the dependencies in extern/, and builds and links the code in src/ with LibreSSL.
2. `reset.sh` reverts the directory to its initial state. It does not touch `src/` or `certificates/`. Run `make clean` in `certificates/` to delete the generated certificates.

### At a high level the system interacts in a simple manner.

At every interaction between entities, there is a TLS handshake in order to ensure that the connection is secured with TLS. Information is then sent over a secure TLS connection. The client will only interact with the proxy, we can see this as a security feature as to not allow direct interaction with the server from a random client.

The proxy will only ping the server for information if the file is not blacklisted and not found in the cache, otherwise the proxy will immediately send the file content back to the client if it is found within the cache or a message saying that the file is blacklisted if the file is found within the bloom filter. Files contents returned from the server are stored within a cache inside the proxies for quick retrieval the next time the file is requested.
