IoT Package Access Control Schemes
=================

### Identity Attribute Based Control Scheme
Control access according to identity's attributes. 

Potential Attributes are

- constrained
- stable
- low battery
- high battery
- Read/Write

Example
- `/clock` have attributes list `/constrained/stable/Read`

### Access Level Based Control Scheme
Some identities like `/camera` and `/microwave` need strict control, whereas `/light` only requiring low level management. In this scheme, access levels are determined by identity names. Upper level consumer identities can have the access of lower level producer identities. Authentication server will deny applications coming from low level consumer identities who want to apply for higher level identities' access.

Examples
- `/door` : level 5 producer identity
- `/camera` : level 4 producer identity
- `/microwave` : level 3 producer identity
- `/light-switch` : level 2 producer identity
- `/AC-bedroom` : level 1 producer identity
- `/sensor-bedroom` : level 0 producer identity (no access control needed)