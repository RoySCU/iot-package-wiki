IoT Package Access Control Protocol Design
=====================

IoT devices like smart home cameras and door locks require high data confidentiality and strict access control, which are supported by NDNoT's access control module. Instead of storing the access keys in remote cloud servers, IoT package provides localized access control, allowing the local user (e.g., home owner) to have complete control of the data produced and consumed by the IoT system. We provide identities based access control and prefixes based access control as two options in our implementaion. 

### Identities Based Access Control
In this method, a producer first communicate with access controller, using classic Diffie Hellman key exchange algorithm based on ECDSA keys (ECDH), to generate a seed for content encryption. Its access control request interest include identity name and Diffie Hellman key bits and a ECDSA signature by its communication key. As an optional parameter, a scheme of access condition may also be appended to the name, informing access controller what type of consumers/applicants can have the decrytion key of the packets.

```
             producer identity                                                     access controller
                     |           /{home prefix}/accesscontrol/{digest of Parameter}         |
                     |        Parameters: _PRODUCER, {identity}, {ECDSA public key bits}    |
                     |                   Optional Parameter: Control Scheme                 |
                     |                          Sig by CKpri                                |
                     | -------------------------------------------------------------------> |  
                     |                                                                      |
                     |                                                              verify signature, 
                     |                                                  update or create producer identity entry
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

On the access application side, a consumer identity expresses a signed request, which includes the identity it want to get access to. This interst also may include an optional parameter to prove the request sender has the right of getting access. Successfully verifying the signature, access controller will search the list of registered producer identities, and check access control scheme to decide whether to the applicant have the right to obtain the seed of desired producer identity. After that access controller and access applicant will also use classic Diffie Hellman algorithm on ECDSA (ECDH) to decide the encryption key for transmitting producer's access seed on channel.

```
             consumer identity                                                     access controller
                     |           /{home prefix}/accesscontrol/{digest of Parameter}         |
                     |        Parameters: _CONSUEMR, {identity}, {ECDSA public key bits},   |
                     |                         {desired identity}                           |
                     |                   Optional Parameter: Control Scheme                 |
                     |                          Sig by CKpri                                |
                     | -------------------------------------------------------------------> |  
                     |                                                                      |
                     |                                                              verify signature, 
                     |                                                  update or create consumer identity entry
                     |                                                 generate HMAC key by using Diffie Hellman
                     |                                                                      |
                     |                                                                      |
                     |        Data: {Diffie Hellman bits}, {encrypted producer seed},       | 
                     |                           {sig by AKpri}                             |
                     | <------------------------------------------------------------------- |
                     |                                                                      |                            
            verify HMAC signature,                                                          |
  generate HMAC from Diffie Hellman bits,                                                   |
            decrypt producer seed                                                           |
           key management process                                                           | 
                     |                                                                      |
                     |                                                                      |
```