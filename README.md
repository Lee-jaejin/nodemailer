Nodemailer
==========

**Nodemailer** is an easy to use module to send e-mails with Node.JS. 

You can use two ways to send an e-mail message: the *EmailMessage* constructor or the shortcut function *send_mail()*. 
The *send_mail()* function takes all the fields of the e-mail message as a function parameter and sends the e-mail immediately. 
*EmailMessage* allows to compose the message object first and send it later with its method *send()*.

**Nodemailer** is Unicode friendly ✔. You can use any characters you like.

Installation
------------

NPM support is future feature.

Usage
-----

Using *send_mail()*

    var nodemailer = require("nodemailer");
    
    nodemailer.send_mail({sender: "me@example.com", 
                          to:"you@example.com",
                          subject:"Hello!",
                          body:"Hi, how are you doing?"},
                          function(error, success){
                              console.log("Message "+(success?"sent":"failed"));
                          });
                          
Using *EmailMessage*

    var nodemailer = require("nodemailer");
    
    var mail = nodemailer.EmailMessage({
                          sender: "me@example.com", 
                          to:"you@example.com"
                          });
    mail.subject = "Hello!";
    mail.body = "Hi, how are you doing?";
    
    mail.send(function(error, success){
                  console.log("Message "+(success?"sent":"failed"));
              });

The callback function gets two parameters - *error* and *success*. If there's an error, then sending failed and you should check where's the problem.
If there's no error value but *success* is not *true* then the server wasn't able to process the message correctly. Probably there was timeout while processing
the message etc - in this case you should re-schedule sending this e-mail. If *success* is *true* then the message was sent successfully. 

### NB!

Before sending e-mails you need to set up SMTP server parameters.

    nodemailer.SMTP = {
        host: "smtp.example.com",
        port: 25,
        hostname: "myhost.com",
        use_authentication: false,
        user: "",
        pass: ""
    }

See [examples/example.js](/andris9/Nodemailer/blob/master/examples/example.js) for a complete example.

Email Message Fields
--------------------

The following are the possible fields of an e-mail message:

  - **sender** - The e-mail address of the sender. All e-mail addresses can be as `sender@server.com` or formatted `Sender Name <sender@server.com>`
  - **to** - Comma separated list of recipients e-mail addresses that will appear on the `To:` field
  - **cc** - Comma separated list of recipients e-mail addresses that will appear on the `Cc:` field
  - **bcc** - Comma separated list of recipients e-mail addresses that will appear on the `Bcc:` field
  - **reply_to** - An e-mail address that will appear on the `Reply-To:` field
  - **subject** - The subject of the e-mail
  - **body** - The plaintext version of the message
  - **html** - The HTML version of the message
  - **attachments** - An array of attachment objects. Attachment object consists of two properties - `filename` and `contents`. Property `contents` can either be a String or a Buffer (for binary data). `filename` is the name of the attachment.

There's an optional extra field **headers** which holds custom header values in the form of `{key: value}`. These values will not overwrite any existing header but will be appended to the list. 

    mail_data = {
        sender:"me@example.com",
        ....
        headers: {
            'X-My-Custom-Header-Value': 'Visit www.example.com for more info!'
        }
    }


Using Embedded Images
--------------------

Attachments can be used as embedded images in the HTML body. To use this feature, you need to set additional property
of the attachment - `cid` (unique identifier of the file) which is a reference to the attachment file.
The same `cid` value must be used as the image URL in HTML (using `cid:` as the URL protocol, see example below).

    var cid_value = "unique-image-cid";
    var html = 'Embedded image: <img src="cid:'+cid_value+'" />';
    var attachments = [{
        filename: "image.png",
        contents: IMAGE_CONTENTS,
        cid: cid_value
    }];

Issues
------

### TLS

Node.JS v0.3.x doesn't support changing to a secure channel in the middle of a connection (STARTTLS). So when a server requires authentication and this must be done over TLS it's a problem.

### Charsets

Currently the only allowed charset is UTF-8.

### Attachments

Do not use large attachments as the attachment contents are read into memory and the final message body is combined into a single string.

License
-------

**Nodemailer** is licensed under [MIT compatible license](/andris9/Nodemailer/blob/master/LICENSE). Basically you can do whatever you want to with it.