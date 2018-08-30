NDNCERT CA Manual
=================

This manual shows how to setup certificate authority using NDNCERT.
We take NDN Testbed CA and its child CA, ucla CA, as an example.

Contents:
* Prerequisites
* Setup NDN Testbed CA
* Setup UCLA CA
* Publish issued certificate to repo-ng
* Add email as an out-of-band identity challenge
* Checking status of your running CAs
* Multiple CAs running on same device

Prerequisites
-------------
* NDN testbed certificate /ndn/KEY/xxxxx/xxx/xxx
* ucla certificate /ndn/edu/ucla/KEY/xxxxx/xxx/xxx (signed by NDN testbed certificate)
* If you want CA to publish certificate to local repo, have repo-ng running with TCP bulk insertion enabled
* If you want to use email as out-of-band identity challenge, have smtp service running

Setup NDN Testbed CA
---------------------
Open ndncert NDN CA config file and edit it as:
```
// ndn.ca.conf
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
          { "ca-prefix": "/ndn/edu/ucla" },
        ],

        "supported-challenges":
        [
            { "type": "PIN" }
        ]
    }
  ]
}
```

Then run ndn testbed CA by the following command in terminal:

```
ndncert-ca-server -f ndn.ca.conf
```


Setup UCLA CA
---------------------
Open ndncert UCLA CA config file and edit it as:

```
// ucla.ca.conf
{
  "ca-list":
  [
    {
        "ca-prefix": "/ndn/edu/ucla",
        "issuing-freshness": "720",
        "validity-period": "360",
        "ca-info": "UCLA CA",
        "probe": "use your edu address as probe information",
        "supported-challenges":
        [
            { "type": "PIN" }
        ]
    }
  ]
}
```

Then run NDNCERT CA:

```
ndncert-ca-server -f ucla.ca.conf
```

Publish issued certificate to repo-ng
-------------------------------------
If repo-ng is already running, you can run NDNCERT CA with options `-r`.

```
ndncert-ca-server -f ucla.ca.conf -r
```

In this way, NDNCERT CA will publish all issued certificate to repo-ng using the default TCP socket `localhost:7376`.
If the running repo-ng is not using default socket, you can specify the host and port with option `-H` and `-P`.

```
ndncert-ca-server -f ucla.ca.conf -r -H yourhost -P yourport
```

Add email as an out-of-band identity challenge
----------------------------------------------

To add email as a out-of-band challenge, edit the `supported-challenges` field in your ca conf. Take UCLA ca as an example:

```
// ucla.ca.conf
{
  "ca-list":
  [
    {
        "ca-prefix": "/ndn/edu/ucla",
        "issuing-freshness": "720",
        "validity-period": "360",
        "ca-info": "UCLA CA",
        "probe": "use your edu address as probe information",
        "supported-challenges":
        [
            { "type": "PIN" },
            { "type": "Email" }
        ]
    }
  ]
}
```

Then you need to edit `ndncert-mail.conf`.
Please see [[Deploy Email Challenge]] for more details.

Checking status of your running CAs
-----------------------------------
To check the current status of your running CA, use the command 'ndncert-ca-status'. An example is shown as follows.

```
-> ndncert-ca-status
Request ID: 7606930243737969087	Current Status: need-code
Applying CA: /ndn
Applying for key: /ndn/Arizona/KEY/%8E%28%93%DA%99%7C%F0%E1
Challenge Secret: {
    "code-timepoint": "20180202T011527.670291",
    "code": "880713",
    "attempt-times": "3"
}
```

As shown, there is a pending request applying for key `/ndn/Arizona/KEY/%8E%28%93%DA%99%7C%F0%E1`. The secret code for the request is `880713`.
CA operator should only give out the number to the requester if you trust the requester.

Multiple CAs running on same device
-----------------------------------

If you want to run multiple CAs on same device, you can just put multiple CA config into one config file and run one NDNCERT CA instance.
As an example, to run CA `/ndn` and CA `/zhiyi` as one CA instance, edit the config file like this:

```
{
  "ca-list":
  [
    {
        "ca-prefix": "/ndn",
        "issuing-freshness": "720",
        "validity-period": "360",
        "probe": "Use the university/organization name as input",
        "ca-info": "NDN Testbed CA",
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
        "ca-prefix": "/zhiyi",
        "issuing-freshness": "720",
        "validity-period": "360",
        "supported-challenges":
        [
            { "type": "PIN" },
            { "type": "Email" }
        ]
    }
  ]
}
```