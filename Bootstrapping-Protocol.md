IoT Package Bootstrapping Protocol Design
=========================

Overview
--------
The bootstrapping module provides applications an automatic way of bootstrapping.
Based on a pre-shared secret (e.g., by scanning a QR code), the security bootstrapping enables an IoT device/application to 
* install the certificate of the controller as a trust anchor 
* obtain a certified name linked to a certificate signed by the controller.

A new device who want to join the network should have at least one pair of bootstrap key. At the beginning of bootstrap, the new coming device should broadcast a interest started with /ndn/sign-on, appended with the digest of BKpub, and a Diffie Hellman key bits.
In this part, considering the time limtation of bootstrap, we use four smaller bits size Diffie Hellman, 64bits for each part, to assembly a token to guarantee the security. Although this break down in key bits size will impair the strength of cryptographic operation, given that IoT devices, generally have lifetime shorter than ten years, this tradeoff is reasonable. 

```
           bootstrapping requester                                                     controller
                     |                /ndn/sign-on/{digest of Parameter}                    |
                     |                       Parameter: {token}                             |
                     |                          Sig by BKpri                                |
                     | -------------------------------------------------------------------> |  
                     |                                                                      |
                     |                                               search BKpub by digest list, verify signature, 
                     |                                             generate HMAC key by using Diffie Hellman on token
                     |                                                                      |
                     |                                                                      |
                     |          Data: {anchor certificate}, {token}, {sig by HMAC}          |
                     | <------------------------------------------------------------------- |
                     |                                                                      |
          generate HMAC from token,                                                         |
            verify HMAC signature                                                           |
         install anchor certificate                                                         |
    learn home prefix from anchor cert                                                      | 
                     |                                                                      |
           Select one pair of CK                                                            |
                     |                                                                      |
                     |             /{home prefix}/cert/{digest of Parameter}                |
                     |                 Parameter: {SHA256(BKpub)}, {CKpub}                  |
                     |                           sig by HMAC                                |
                     | -------------------------------------------------------------------> |
                     |                                                                      |
                     |                                                              verify signature, 
                     |                                                            allocate identities,
                     |                                                           sign Communication Cert
                     |                                                                      |
                     |                                                                      |
                     |       Data: {Communicaiton Cert signed by AKpri}, {sig by HMAC}      |                           
                     | <------------------------------------------------------------------- |
                     |                                                                      |
                     |                                                                      |
             verify signature,                                                              |
            install identities,                                                             |
        install communication cert                                                          |
                     |                                                                      |
                     |                                                                      |
```