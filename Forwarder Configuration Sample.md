NDNCERT Renewal Challenge
=========================

Protocol Overview
=================

Certificate renewal is to renewal a certificate ID without changing the namespace of the certificate.
NDNCERT treat renewal as a special security challenge with challenge the requester:
  * proof of control the old certificate
  * proof of the new certificate
  * proof of the identity through some out-of-band way, e.g. PIN code, email, etc.

To renewal a certificate, the requester send a NEW command interest to the CA.
Among all the challenge choices provided by CA, the requester should select challenge **Renewal**.
By selecting the **Renewal** challenge, the CA will require the requester to perform the renewal challenge(s) based on CA's configuration.
Based on CA's configuration (more detail in next section), requester may or may not redo the out-of-band challenge.

```
certificate requester                                             CA
 |                                                                 |
 |         _SELECT (select renewal challenge + old cert)           |
 |             double signed by new key and old key                |
 | --------------------------------------------------------------> |
 |                                                                 |
 |                          Verify requester's possession of old cert
 |                                                                 |
 |         Check the configuration whether need out-of-band challenge
 |                    If need, send back the available challenge list
 |                                    If not, send back the new state
```

If need to redo out-of-band challenge, goes like:
```
 |          Data (available challenges without renewal)            |
 | <-------------------------------------------------------------- |
 |                        Normal _SELECT                           |
 | --------------------------------------------------------------> |
```

If don't need to redo out-of-band challenge, goes like:
```
 |             Data (status: challenge is done)                    |
 | <-------------------------------------------------------------- |
 |       Normal _VALIDATE or _DOWNLOAD depending on status         |
 | --------------------------------------------------------------> |
```

Renewal Related Extension of Certificate
========================================

### Issuance Records
This extension helps to remember all historic renewals of the certificate, including the timestamp and whether the out-of-band challenge is redone or not.
Renewal Record:
  * renewal date: time point
  * redo challenge: true/false

Renewal Related Configuration
=============================

CA's config:
```
renewal-latest-challenge-freshness-time: n
```
The number of the field is of hour granularity.
0 implies that one out-of-band challenge should be redone for every renewal.
-1 implies that requester never needs to redo challenge.

Challenge Detail
================

The _SELECT interest:
```
/CA-prefix/CA/_SELECT/{"request-id":""}/renewal/{"old-cert":""}/<tp>/<randomness>/<Sig1>/<Sig2>
```

