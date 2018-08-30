CA Configuration Sample
=======================

CA configuration, which contains a set of Certificate Authority (CA) information, is designed for CAs to configure the behavior for each CA.
CA configuration contains a list of CA information; for each CA entry, it contains:

  * ca-prefix: (**required**) CA name prefix
  * issuing-freshness: (**required**) The freshness of newly issued certificate Data packet
  * validity-period: (**required**) The validity period of newly issued certificate Data packet
  * ca-info: (**optional**) A readable brief introduction of CA
  * probe: (**optional**) If the CA operator wants to enable the PROBE function (name assignment), the operator needs to set this value to be a readable instruction what information should be used as the _PROBE parameter.
  * target-list: (**optional**) If this CA is supposed to support LIST function and be able to make recommendation, the CA operator needs to set this value to be a readable instruction for requester to know what information should be used to get a recommendation.
  * related-ca-list: (**optional**) If this CA wants to support LIST function, this field should be a list containing all the child CA that the CA wants to help.
  * supported-challenges: (**required**) All supported challenge names.

A CA configuration sample:
```
{
  "ca-list":
  [
    {
        "ca-prefix": "/ndn",
        "issuing-freshness": "720",
        "validity-period": "360",
        "ca-info": "NDN Testbed CA",

        "probe": "Use the university/organization name as input",

        "targeted-list": "Use your email address (edu preferred) as input",
        "related-ca-list":
        [
          { "ca-prefix": "/ndn/edu/arizona" },
          { "ca-prefix": "/ndn/edu/memphis" }
        ],

        "supported-challenges":
        [
            { "type": "PIN" }
        ]
    },
    {
        "ca-prefix": "/example",
        "issuing-freshness": "720",
        "validity-period": "360",
        "supported-challenges":
        [
            { "type": "PIN" }
        ]
    }
  ]
}
```