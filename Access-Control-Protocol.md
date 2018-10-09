IoT Package Access Control Protocol Design
=====================

IoT devices like smart home cameras and door locks require high data confidentiality and strict access control, which are supported by NDNoT's access control module. Instead of storing the access keys in remote cloud servers, IoT package provides localized access control, allowing the local user (e.g., home owner) to have complete control of the data produced and consumed by the IoT system. Considering most RIOT OS devices are very constrained, which means one identity per device is reasonable, so we simplified the original NAC protocol, avoiding unnecessary energy consumption on computation. 

### Requirement of Access Controller
Access Controller must be capable of generating ECDSA key pairs on their own, to guarantee the effectiveness of key updating, since some devices may always use one ECDSA public key to conduct Diffie Hellman.

### Key Management
Each time Producer apply for a new Content Key, After the DH exchange, Access Controller will increase the `<CK-ID>` and hold the Content Key in plaintext, until consumer identities apply for access. Then Access Controller encrypt the Content Key in using consumer's Diffie Hellman result, and publish the data.

### Producer - Access Controller
In this method, a producer first communicate with Access Controller, using classic Diffie Hellman key exchange algorithm based on ECDSA keys (ECDH), to generate a seed for content encryption. The generated key is noted with Content Key. Its access control request interest include identity name and Diffie Hellman key bits and a ECDSA signature by its communication key. 
```
             producer identity                                                       Access Controller
                     |           /<home prefix>/accesscontrol/<identity>/KEY/<KEY-ID>       |
                     |                         /{digest of Parameters}                      |
                     |        Parameters: {Diffie Hellman public key bits}                  |
                     |                          Sig by CKpri                                |
                     | -------------------------------------------------------------------> |  
                     |                                                                      |
                     |                                                              verify signature, 
                     |                                                           key management process 
                     |                                                 generate HMAC key by using Diffie Hellman
                     |                                                      register corresponding KDK prefix
                     |                                                                      |
                     |                                                                      |
                     |            Data: {Diffie Hellman bits}, {sig by AKpri}               |
                     | <------------------------------------------------------------------- |
                     |                                                                      |                            
            verify HMAC signature,                                                          |
  generate HMAC from Diffie Hellman bits,                                                   |
          key management process                                                            | 
                     |                                                                      |
                     |                                                                      |
```

### Consumer - Access Controller
Consumer express an interest to fetch data from access controlled producer identity. After a normal interest-data round, consumer obtains data name `/<original name>/ENCRYPTED-BY/<producer identity prefix>/CK/<CK-ID>` and automatically construct an interest name `/<home prefix>/accesscontrol/<producer identity>/KDK/<Key-ID>/ENCRYPTED-BY/<consumer identity prefix>` to query the producer's encrypted CK from Access Controller.

```
             consumer identity                                                       Access Controller
                     |           /<home prefix>/accesscontrol/<producer identity>           |
                     |        /KDK/<Key-ID>/ENCRYPTED-BY/<consumer identity prefix>         |   
                     |                      /{digest of Parameters}                         |
                     |        Parameters: {Diffie Hellman public key bits}                  |
                     |                          Sig by CKpri                                |
                     | -------------------------------------------------------------------> |  
                     |                                                                      |
                     |                                                              verify signature, 
                     |                                                           key management process 
                     |                                                 generate HMAC key by using Diffie Hellman
                     |                                                                      |
                     |                                                                      |
                     |            Data: {Diffie Hellman bits}, {sig by AKpri}               |
                     | <------------------------------------------------------------------- |
                     |                                                                      |                            
            verify HMAC signature,                                                          |
  generate HMAC from Diffie Hellman bits,                                                   |
          key management process                                                            | 
                     |                                                                      |
                     |                                                                      |
```