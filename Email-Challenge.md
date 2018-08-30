Email-based Challenge Module (ChallengeEmail) validates claims for the namespace based on email possession verification.  The specific namespace that can be claimed based on the email possession depends on the specific CA module and can be directly inferred from the email or be on the first-claim-first-given basis.

Email-based challenge is used by certificate authorities on NDN testbed (`/ndn/edu/ucla/CA`, `/ndn/edu/arizona/CA`, and others).

The operations of Email Challenge can be summarized as follows:

- Determining namespace (e.g., using `_PROBE` command) and generating certificate request
- Email challenge selection
- Informing CA about which email will be used for verification
- Obtaining verification code from email
- Challenge confirmation
- Success or failure

An example of the overall process is shown in the following diagram:

         requester                                                                       CA
             |                                                                            |
             |                                                                            |
             |                  _PROBE + <cert> generation (TBD)                          |
             |                                                                            |
             |                                                                            |
             |                                                                            |
             |                                                                            |
             |           Interest for /ndn/edu/ucla/CA/_NEW/<cert>/[signature]            |
             | -------------------------------------------------------------------------> |
             |                                                                            |
             |       Data, Content={"request-id":"111", "status":"wait-selection",        |
             |                      "challenges": ["EMAIL"]}, Signed by CA                |
             | <------------------------------------------------------------------------- |
             |                                                                            |
      Validate CA signature.                                                              |
      Immediately abort if signature cannot be validated                                  |
             |                                                                            |
             |                 I N I T I A T I N G   C H A L L E N G E                    |
             |                                                                            |
             |        Interest for /ndn/edu/ucla/CA/_SELECT/{"request-id":"111"}          |
             |                     /EMAIL/{"email":"zhiyi@cs.ucla.edu"}/[signature]       |
             | -------------------------------------------------------------------------> |
             |                                                                            |
             |                                                        Validate signature using corresponding certificate
             |                                                        Immediately abort if signature cannot be validated
             |                                                                            |
             |                                                        Generate verification code and email to requester
             |                                                                            |
             |         Data, Content={"request-id":"111", "status": "wait-code",          |
             |                        "challenge": "EMAIL"} Signed by CA                  |
             | <------------------------------------------------------------------------- |
             |                                                                            |
      Validate CA signature.                                                              |
      Immediately abort if signature cannot be validated                                  |
             |                                                                            |
             ~                                                                            |
      Discover verification code                                                          |
      (by checking email)                                                                 |
             ~                                                                            |
             |                                                                            |
             |                  F I N I S H I N G   C H A L L E N G E                     |
             |                                                                            |
             |       Interest for /ndn/edu/ucla/CA/_VALIDATE/{"request-id":"111"}         |
             |                    /EMAIL/{"code":"123"}/[signature]                       |
             | -------------------------------------------------------------------------> |
             |                                                                            |
             |                                                        Validate signature using corresponding certificate
             |                                                        Immediately abort if signature cannot be validated
             |                                                                            |
             |      Data, Content={"request-id":"111", "status": "pending",               |
             |                     "challenge": "EMAIL"} Signed by CA                     |
             | <------------------------------------------------------------------------- |
             |                                                                            |
      Validate CA signature.                                                              |
      Immediately abort if signature cannot be validated                                  |
             |                                                                            |
             ~                                                                            ~
             ~                                                                            ~
             |                                                                            |
             |   Interest for /ndn/edu/ucla/CA/_STATUS/{"request-id":"111"}/[signature]   |
             | -------------------------------------------------------------------------> |
             |                                                                            |
             |                                                        Validate signature using corresponding certificate
             |                                                        Immediately abort if signature cannot be validated
             |                                                                            |
             |      Data, Content={"request-id":"111", "status": "issued",                |
             |              "cert": "/ndn/edu/ucla/CA/_DOWNLOAD/{"request-id":"111"}",    |
             |              "challenge":"EMAIL"} Signed by CA                             |
             | <------------------------------------------------------------------------- |
             |                                                                            |
      Validate CA signature.                                                              |
      Immediately abort if signature cannot be validated                                  |
             |                                                                            |
             ~                                                                            ~
             ~                                                                            ~
             |                R E T R I E V I N G   C E R T I F I C A T E                 |
             |                                                                            |
             |       Interest for /ndn/edu/ucla/CA/_DOWNLOAD/{"request-id":"111"}         |
             | -------------------------------------------------------------------------> |
             |                                                                            |
             |    Data, Content=Certificate(/ndn/edu/ucla/cs/zhiyi/KEY/%01/NDN/%FD01)     |
             | <------------------------------------------------------------------------- |

1. (Pre-knowledge) The requester build up the trust to CA (installed the CA's certificate).

2. (Pre-knowledge) The requester determines from which CA certificate should be requested.

        /ndn/edu/ucla/CA

3. (Pre-knowledge) The requester determines which type of CA (= how namespace is managed).

4. If CA type requires/provides `_PROBE` capabilities, determine the available/allowed namespace for the sub-identity, e.g., `/ndn/edu/ucla/cs/zhiyi`.

5. Generate a key-pair and the certificate request.

        /ndn/edu/ucla/cs/zhiyi/KEY/%01/cert-request/%00

6. Send `_NEW` command to the CA.

        /ndn/edu/ucla/CA/_NEW/<cert>/[signature]

7. Validate the signature of reply data. Get the request ID and available challenge list from data.

8. Send `_SELECT` command, selecting `EMAIL` as a challenge and providing requester's email.

        /ndn/edu/ucla/CA/_SELECT/{"request-id":"111"}/EMAIL/{"email":"zhiyi@cs.ucla.edu"}/[signature]

9. Validate the signature of reply data.

10. (Out-of-band) Obtain verification code by checking email.

11. Send `_VALIDATE` command, confirming the verification code.

        /ndn/edu/ucla/CA/_VALIDATE/{"request-id":"111"}/EMAIL/{"code":"123"}/[signature]

12. Validate the signature of reply data. If response to `_VALIDATE` is an issued certificate ("status": "success"), download it using the provided link to encapsulated cert (or a key bundle).

If response is ("status": "pending"), periodically send `_STATUS` requests.

        /ndn/edu/ucla/CA/_STATUS/{"request-id":"111"}/[signature]