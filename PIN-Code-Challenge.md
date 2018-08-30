PIN Challenge assumes the certificate requester and certificate authority can agree on a generated PIN in some out-of-band method.  This document doesn't define any specific method how this agreement can be reached.

Use case for the challenge: management of sub-namespace certificate.  For example, creating a certificate for `/zhiyi/iphone` namespace using `/zhiyi` identity's certificate as a local trust anchor / certificate authority.

The operations of PIN Challenge can be summarized as follows:

- Challenge selection
- Reaching agreement out-of-band on PIN code
- Challenge confirmation
- Success or failure

An example of the overall process is shown in the following diagram:

          Requester                                                                      CA
             |                                                                            |
             |                  _PROBE + <cert> generation (TBD)                          |
             |                                                                            |
             |                                                                            |
             |           Interest for /zhiyi/CA/_NEW/<cert>/[signature]                   |
             | -------------------------------------------------------------------------> |
             |                                                                            |
             |       Data, Content={"request-id":"111", "status": "wait-selection",       |
             |                      "challenges": ["PIN"]}, Signed by CA                  |
             | <------------------------------------------------------------------------- |
             |                                                                            |
      Validate CA signature.                                                              |
      Immediately abort if signature cannot be validated                                  |
             |                                                                            |
             |                 I N I T I A T I N G   C H A L L E N G E                    |
             |                                                                            |
             |     Interest for /zhiyi/CA/_SELECT/{"request-id":"111"}/PIN//[signature]   |
             | -------------------------------------------------------------------------> |
             |                                                                            |
             |                                                        Validate signature using corresponding certificate
             |                                                        Immediately abort if signature cannot be validated
             |                                                                            |
             |                                                                   Generate PIN and record
             |                                                                      it internally
             |                                                                            |
             |         Data, Content={"request-id":"111", "status": "wait-code"},         |
             |               Signed by CA                                                 |
             | <------------------------------------------------------------------------- |
             |                                                                            |
      Validate CA signature.                                                              |
      Immediately abort if signature cannot be validated                                  |
             |                                                                            |
             ~                                                                            |
      Discover PIN                                                                        |
      (out-of-band, mechanism currently undefined)                                        |
             ~                                                                            |
             |                                                                            |
             |                  F I N I S H I N G   C H A L L E N G E                     |
             |                                                                            |
             |  /zhiyi/CA/_VALIDATE/{"request-id":"111"}/PIN/{"code":"123"}/[signature]   |
             | -------------------------------------------------------------------------> |
             |                                                                            |
             |                                                        Validate signature using corresponding certificate
             |                                                        Immediately abort if signature cannot be validated
             |                                                                            |
             |      Data, Content={"request-id":"111", "status": "pending"}               |
             |            Signed by CA                                                    |
             | <------------------------------------------------------------------------- |
             |                                                                            |
      Validate CA signature.                                                              |
      Immediately abort if signature cannot be validated                                  |
             |                                                                            |
             ~                                                                            ~
             ~                                                                            ~
             |      Interest for /zhiyi/CA/_STATUS/{"request-id":"111"}/[signature]       |
             | -------------------------------------------------------------------------> |
             |                                                                            |
             |                                                        Validate signature using corresponding certificate
             |                                                        Immediately abort if signature cannot be validated
             |                                                                            |
             |      Data, Content={"request-id":"111", "status": "issued",                |
             |                     "cert": "/zhiyi/CA/_DOWNLOAD/{"request-id":"111"}"     |
             |            Signed by CA                                                    |
             | <------------------------------------------------------------------------- |
             |                                                                            |
      Validate CA signature.                                                              |
      Immediately abort if signature cannot be validated                                  |
             |                                                                            |
             ~                                                                            ~
             ~                                                                            ~
             |                R E T R I E V I N G   C E R T I F I C A T E                 |
             |                                                                            |
             |           Interest for /zhiyi/CA/_DOWNLOAD/{"request-id":"111"}            |
             | -------------------------------------------------------------------------> |
             |                                                                            |
             |       Data, Content=Certificate(/zhiyi/subnamespace/KEY/%01/my/%FD01)      |
             | <------------------------------------------------------------------------- |

1. (Pre-knowledge) The requester build up the trust to CA (installed the CA's certificate).

2. (Pre-knowledge) The requester determines from which CA certificate should be requested.

        /zhiyi/CA

3. (Pre-knowledge) The requester determines which type of CA (= how namespace is managed).

4. If CA type requires/provides `_PROBE` capabilities, determine the available/allowed namespace for the sub-identity, e.g., `/zhiyi/iphone`.

5. Generate a key-pair and the certificate request.

        /zhiyi/iphone/KEY/%01/cert-request/%00

6. Send `_NEW` command to the CA.

        /zhiyi/CA/_NEW/<cert>/[signature]

7. Validate the signature of reply data. Get the request ID and available challenge list from data.

8. Send `_SELECT` command, selecting `PIN` as a challenge.

        /zhiyi/CA/_SELECT/{"request-id":"111"}/PIN/[signature]

9. Validate the signature of reply data.

10. (Out-of-band) Reach agreement on PIN.

11. Send `_VALIDATE` command, confirming the PIN.

        /zhiyi/CA/_VALIDATE/{"request-id":"111"}/PIN/{"code":"123"}/[signature]

12. Validate the signature of reply data. If the response to `_VALIDATE` is an issued certificate ("status": "success"), download it using the provided link to encapsulated cert (or a key bundle).

If response is ("status": "pending"), periodically send `_STATUS` requests.

        /zhiyi/CA/_STATUS/{"request-id":"111"}/[signature]
