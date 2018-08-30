IoT Package Service Discovery Protocol Design
=============================================

The service discovery module helps applications find available services in the local IoT network and also to advertise their own services to other nodes. Running over NDN, the service discovery is implemented by prefix discovery and prefix registration. Unlike the service discovery solutions in TCP/IP networks which are based on a (distributed) database query, IoT package utilizes NDN's naming convention and the use of application names at the \emph{network layer} to facilitate the discovery/advertisement process.
To start service discovery, the device must be bootstrapped first, to obtain assigned identities and communication certificate inside the network. This dependency is guaranteed by system design. two pre-processing steps are required to begin service discoery. 
* device obtains identities and certificate from holder thread (e.g., lightweight NFD),
* user provides served prefixes in this network.

After pre-processing, prefixes are aggregated to several service names, device will periodically broadcast service names along with corresponding identities names, in convention /{home prefix}/servicediscovery/{identity}/servicelist/{service names}. Time interval between two broadcast can be defined users, or set by default. To reduce communication overhead in constrained devices network, a longer interval is preferred, for most nodes in home network scenario being comparatively stationary and stable in their functionalities and positions.  
Other than periodically broadcast interest to notify neighbours the available identities and service names, discovery process also need devices to listen other broadcast interests under the namescope /{home prefix}/servicediscovery, collecting available identities and service names around.

```
               bootstrapped device 1                                      bootstrapped device 2
                         |                                                             |
                         |   /{home prefix}/servicediscovery/{identity 1}/servicelist  |
                         |               /{service name 1}/{service name 2}            |
                         | ----------------------------------------------------------->|
                         |   /{home prefix}/servicediscovery/{identity 2}/servicelist  |
                         |               /{service name 1}/{service name 2}            |
                         | ----------------------------------------------------------->|
                         |                                                             |
                         |                           ......                            |
                         | ----------------------------------------------------------->|
                         |                                                             |
                         |                                                             | 
                         |                                                learn available identities,
                         |                                                        and services
                         |                                                             |
                         |                                                             |
                         |   /{home prefix}/servicediscovery/{identity 1}/servicelist  |
                         |               /{service name 1}/{service name 2}            |
                         | <---------------------------------------------------------- |
                         |   /{home prefix}/servicediscovery/{identity 2}/servicelist  |
                         |               /{service name 1}/{service name 2}            |
                         | <---------------------------------------------------------- |
                         |                                                             |
                         |                           ......                            |
                         | <---------------------------------------------------------- |
                         |                                                             |
                         |                                                             |
             learn available identities,                                               |
                   and services                                                        |
                         |                                                             |
                         |                                                             |
                         |                                                             |
                         |        /{home prefix}/{identity}/{service name}/query       |
                         | ----------------------------------------------------------->|
                         |                                                             |
                         |                                                             |
                         |                                              check service and identity table 
                         |                                                             |
                         |  Data: {Naming Convention, Parameters Needed}, {sig by CK}  |
                         | <---------------------------------------------------------- |
                         |                                                             |
                         |                                                             |
                         |                     Normal Interest                         |                         
                         | ----------------------------------------------------------->|
                         |                                                             |
                         |                           Data                              |
                         | <---------------------------------------------------------- |
                         |                                                             |
```

### Query Response
--------------------------
Currently we don't define any specific metadata format in query response, user should assign proper formats for themselves in this module.  

