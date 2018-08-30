Two simple steps and Get your NDN Identity and Become a namespace owner
=======================================================================

Prerequisite
------------
* Installed the NDNCERT library and Command line tools
* Installed the NDN Forwarding Daemon (NFD)


Step 1: Get device bootstrapped
------------------------------
In your terminal, run
```
ndncert-client
```
Then the command will show all the available namespaces that you can obtain a certificate.
If this is the first time you run the tool, there supposed to be one item listed there:
```
Index      CA Namespace 	 CA Intro            Allow sub-namespace redirection
  0	       /ndn/CA	       NDN Testbed CA      Yes
```
Then type in the index number, which is `0` here.

Then you will get a prompt like:
```
You want a namespace with prefix (A) /ndn or (B) a sub-namespace of /ndn?
```
If you are not an authorized organization, unfortunately you cannot directly get a namespace like `/ndn/example`.
Therefore you should type `B`, and get a list of available sub-namespaces.
```
Index      CA Namespace 	     CA Intro            Allow sub-namespace redirection
  0	       /ndn/CA	           NDN Testbed CA       Yes
  1        /ndn/edu/ucla/CA     ...                 No
  2        /ndn/edu/memphis/CA  ...                 No
  3        /ndn/edu/arizona/CA  ...                 No
```

Assume you are from UCLA (i.e. you have a email whose address ended with ucla.edu), you then select index `1`.
Then you got a prompt.
```
Please input your ucla email address to get an unauthorized name.
```
Then you type in `example@cs.ucla.edu`.
You may got a further prompt.
```
You got an unauthorized name /ndn/edu/ucla/cs/example, to authorize your identity, please select one challenge from the list.

    Email   PIN
```
Given it's complicated to contact the UCLA ndn operator and get the PIN code, you'd better choose Email challenge.
Then you are required to input your email address and then supposed to receive an email.
The last step is to input the code copied from the email and finish the process.

After that, follow the instruction on the output and you can get a certificate issued from NDN testbed CA.

Step 2: Become a CA on your own namespace
-----------------------------------------
Want to become a CA for the namespace you just obtained? 
You just need to edit the NDNCERT configuration file.


Suppose you just got a certificate for identity `/ndn/edu/ucla/zhiyi`, use the following text to replace the sample configuration in your configuration file `ca.conf`.
```
{
  "ca-list":
  [
    {
        "ca-prefix": "/ndn/edu/ucla/zhiyi",
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
Then simply by running the following command line tool, you become a CA for the namespace `/ndn/edu/ucla/zhiyi`
```
ndncert-ca-server
```

