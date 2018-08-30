Client Configuration Sample
===========================

Client configuration, which contains a set of Certificate Authority (CA) information, is designed to be used for requesters to remember trusted CAs.
Client configuration contains a list of CA information; for each CA entry, it contains:

  * ca-prefix: CA name prefix
  * ca-info: A readable name with a brief introduction of CA
  * probe: Whether CA requires a _PROBE for name assignment ahead of certificate request. If need, this field would contain a readable instruction what should be used as a _PROBE parameter.
  * target-list: A readable instruction for requester to learn how to trigger a LIST request with some additional information so that the requester will later get a recommended CA back.


A client configuration sample:
```
{
  "ca-list":
  [
    {
        "ca-prefix": "/ndn/CA",
        "ca-info": "NDN Testbed CA",
        "probe": "Use the university/organization name as input",
        "target-list": "Use your email address (edu preferred) as input",
        "certificate":  "Bv0CJAcsCANuZG4IBXNpdGUxCANLRVkICBG8IvRjFf8XCARzZWxmCAn9AAABWcgU2aUUCRgBAhkEADbugBX9AU8wggFLMIIBAwYHKoZIzj0CATCB9wIBATAsBgcqhkjOPQEBAiEA/////wAAAAEAAAAAAAAAAAAAAAD///////////////8wWwQg/////wAAAAEAAAAAAAAAAAAAAAD///////////////wEIFrGNdiqOpPns+u9VXaYhrxlHQawzFOw9jvOPD4n0mBLAxUAxJ02CIbnBJNqZnjhE50mt4GffpAEQQRrF9Hy4SxCR/i85uVjpEDydwN9gS3rM6D0oTlF2JjClk/jQuL+Gn+bjufrSnwPnhYrzjNXazFezsu2QGg3v1H1AiEA/////wAAAAD//////////7zm+q2nF56E87nKwvxjJVECAQEDQgAES9Cb9iANUNYmwt5bjwNW1mZgjzIkDJb6FTCdiYWnkMMIVxh2YDllphoWDEAPS6kqJczzCuhnGYpZCp9tTaYKGxZMGwEDHB0HGwgDbmRuCAVzaXRlMQgDS0VZCAgRvCL0YxX/F/0A/Sb9AP4PMTk3MDAxMDFUMDAwMDAw/QD/DzIwMzcwMTE3VDIxMjg0NhdIMEYCIQDXkR1hF3GiP7yLXq+0JBJfi9QC+hhAu/1Bykx+MWz6RAIhANwelBTxxZr2C5bD15mjfhWudK4I1tOb4b/9xWCHyM7F"
    }
  ]
}
```
