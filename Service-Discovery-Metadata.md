IoT Package Service Discovery Metadata
=================

Metadata in this query response have two formats
- Data packets with names directly TLV encoded in content 
- Prefixes with placeholders (for variables) in it, and a placeholder description block following to demonstrate valid value types and ranges 

Directly Encoded Subprefixes
-------------
Subprefix is the name start from the component after the service name.

Example:
- `/ucla/cs/397/sensor-bedroom/temperatue/read/celsius` is a served prefix by identity `/sensor-bedroom`, under service name `/temperatue`. The encoded part would only include `/read/celsius`.
- A directly prefixes packet when interest filter hear `/sensor-bedroom/temperature/query` could contain following subprefixes.
```
                 -----------------------------------------------------------
                | Name:   /ucla/cs/397/sensor-bedroom/temperature/query/v3  |
                |-----------------------------------------------------------|
                | Content:                                                  |
                |           /read/celsius                                   |
                |           /read/fahrenheit                                |
                |           /root/reboot                                    |
                |           /root/mode/sleep                                |
                |-----------------------------------------------------------|
                | Signature:                                                |
                |           by CKpri                                        |
                 -----------------------------------------------------------
```
Placeholder & Description
-------------
Description blocks are ordered by corresponding placeholders. Placeholer may have their code in IoT Package.

Example:
- `/ucla/cs/397/light-bedroom/set` is a served service prefix by identity `/light-bedroom`, under service name `/set`. but specific brightness must included in interest.  

Query Response Packets
```
                 -----------------------------------------------------------
                | Name:   /ucla/cs/397/light-bedroom/set/query/v3           |
                |-----------------------------------------------------------|
                | Content:                                                  |
                |           /dim/_PLACEHOLDER                               |
                |           /bright/_PLACEHOLDER                            |
                |           /root/mode/sleep/time/_PLACEHOLDER              |
                |           Description Block 1                             |
                |           Description Block 2                             |
                |           Description Block 3                             |
                |-----------------------------------------------------------|
                | Signature:                                                |
                |           by CKpri                                        |
                 -----------------------------------------------------------
```

Description TLV Block Format
-------------
Description block supports four variable types description 
- uint8_t
- uint16_t
- uint32_t 
- uint64_t

```
                 -------------------------------------------------------------------
                |  VARIABLE TYPE |  VARIABLE LOWER LIMIT  |  VARIALBLE UPPER LIMIT  |
                 -------------------------------------------------------------------
```
Variable lower limit and upper limit are encoded in the of variable type