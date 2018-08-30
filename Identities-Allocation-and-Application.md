IoT Package Identities Allocation and Application
=================

This page discusses the identity distribution and application issue in bootstrapping. Many ideas mentioned in this page are not yet supported in our current implementation. Since the design of bootstrapping itself is keep evolving, This page is created for listing future design directions.

Contents:
* Current Design
* Identity Allocation Design
* Identity Application Design

Current Design 
-------------
Currently we pick identity from hard code list for devices bootstrap with controller. Users or developers can build their own identity list in controller program to fit in different scenarios. However, Randomly picking or deterministic picking identities by bootstrapping order is not the final solution. So we present two extra modes of identities obatinment for future development.  

Identity Allocation
---------------------
Controller and devices follow a set of command code, each indicating a type of device in home network. A type can map into serveral identities. Adding specific command code in interest parameter can help controller allocate proper identities, distributed in certificates.
Detailed design will come later.

Identity Application
---------------------
There're some scenarios where devices need dynamically change their identities, one-time allocation is not enough. A reverse identity application procedure is needed.
Detailed design will come later.