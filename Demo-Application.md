Instructions to deploy email challenge
======================================

* Configure the ndncert email configuration
* Configure the ndncert CA configuration
* Try ndncert email challenge!


Configure the ndncert email configuration
-----------------------------------------
You need to edit the ndncert-mail.conf to make email challenge work.
To create ndncert-mail.conf, just run
```
mv /usr/local/etc/ndncert/ndncert-mail.conf.sample /usr/local/etc/ndncert/ndncert-mail.conf
```
You then need to edit the conf by run
```
vim /usr/local/etc/ndncert/ndncert-mail.conf
```
As an example, here is my ndncert-conf
```
[ndncert_smtp_settings]
SMTP_SERVER = smtp.gmail.com
SMTP_PORT = 587
ENCRYPT_MODE = tls
SMTP_USER = zhangzhiyi1919@gmail.com
SMTP_PASSWORD = **that's my secret**

[ndncert_email_settings]
MAIL_FROM = zhangzhiyi1919@gmail.com
SUBJECT = Email Challenge Triggered by NDNCERT
TEXT_TEMPLATE = Your PIN code: {0} Please keep it secret and type in to your application to finish the certificiate issuance process. If you do not know what is going on, please ignore the message
HTML_TEMPLATE = <html><head></head><body><p><b>Your PIN code: {0}</b></p><p>Please keep it secret and type in to your application to finish the certificiate issuance process. If you do not know what is going on, please ignore the message.</p><p>Sincerely,<br/>NDN Testbed Certificate robot</p>
```

Configure the ndncert CA configuration
--------------------------------------
You need to add `Email` as an available challenge from your CA configuration
Here is my CA configuration:
```
{
  "ca-list":
  [
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

Try ndncert email challenge!
----------------------------
Run ndncert ca:
```
ndncert-ca-server
```

The ndncert client side command line input and output
```
Index 	 CA Namespace 	 CA Introduction
0	/zhiyi/CA	/zhiyi/KEY/X%E9%A7ik%02%14%AA
Step0: Please type in the CA namespace index that you want to apply
0
Step1: Please type in the identity name
test003
Step2: Please select one challenge from following types.
	PIN
	Email
Email
Step3: Please satisfy following instruction(s)
	Please input your email address:
zhiyi@cs.ucla.edu
Step4: Please satisfy following instruction(s)
	Please input your verification code:
408529
DONE! Certificate has already been issued
DONE! Certificate has already been installed to local keychain
```

The email sent to `zhiyi@cs.ucla.edu` from `zhangzhiyi1919@gmail.com`:
```
Content-Type: multipart/alternative; boundary="===============5394734142355072363=="
MIME-Version: 1.0
From: zhangzhiyi1919@gmail.com
To: zhiyi@cs.ucla.edu
Subject: Email Challenge Triggered by NDNCERT

--===============5394734142355072363==
Content-Type: text/plain; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit

Your PIN code: 408529 Please keep it secret and type in to your application to finish the certificiate issuance process. If you do not know what is going on, please ignore the message
--===============5394734142355072363==
Content-Type: text/html; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit

<html><head></head><body><p><b>Your PIN code: 408529</b></p><p>Please keep it secret and type in to your application to finish the certificiate issuance process. If you do not know what is going on, please ignore the message.</p><p>Sincerely,<br/>NDN Testbed Certificate robot</p>
--===============5394734142355072363==--
```