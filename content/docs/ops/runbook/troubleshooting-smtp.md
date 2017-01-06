---
menu:
  docs:
    parent: runbook

title: Troubleshooting SMTP
---
### Overview
Often times a service is functioning properly, but mail will not deliver.  The 
general root cause for this is the mail service silently rejecting mail, or
sometimes a firewall change.

#### Mail Relay
All outgoing mail should be sent via [Mandrill](https://mandrillapp.com).  #infrastructure on Slack can
help with account credentials if needed.

#### Authentication
SMTP Authentication should be used to send mail.  In order to communicate,
the username and password need to be sent base64 encoded:

```shell
# Get the username in base64
perl -MMIME::Base64 -e 'print encode_base64("noreply\@cloud.gov")'

# Get the password in base64
perl -MMIME::Base64 -e 'print encode_base64("secretpassword")'
```

#### Sending a test email
"VXNlcm5hbWU6" is base64 for "Username:".  "UGFzc3dvcmQ6" is base64 for "Password:".
```shell
# Connect to the server
nc smtp.mandrillapp.com 25
220 smtp.mandrillapp.com ESMTP
AUTH LOGIN
334 VXNlcm5hbWU6
PASTE-YOUR-BASE64-USERNAME-HERE
334 UGFzc3dvcmQ6
PASTE-YOUR-BASE64-PASSWORD-HERE
235 2.7.0 Authentication successful
MAIL from: <noreply@cloud.gov>
250 2.1.0 Ok
RCPT to: <your-test-email-address@cloud.gov>
250 2.1.5 Ok
DATA
354 End data with <CR><LF>.<CR><LF>
From: noreply@cloud.gov
To: your-test-email-address@cloud.gov
Subject: Our mail really does work 

We have worked with the best scientists abroad, and found that indeed this works.
.
250 2.0.0 Ok: queued as A0F8860435
QUIT
221 2.0.0 Bye
```