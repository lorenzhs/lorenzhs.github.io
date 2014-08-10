---
layout: post
title: Setting up a trusted secure WeeChat relay with StartSSL
---

If you're a user of [Glowing Bear](/2014/04/10/glowing-bear) or another [WeeChat](http://weechat.org/) relay client, you might find that setting up a secure, trusted relay is a bit more work than it should be. Of course you can use a self-signed certificate, but that can cause all kinds of problems. For example, in Google Chrome, you need to re-accept the certificate every time you restart the browser. So ideally, your relay's certificate should be accepted by all major browsers. This guide will show you, step by step, how you can get such a certificate for free from StartCom. I'm going to assume that you're running WeeChat on a Linux machine, but it shouldn't be too hard to apply these instructions to other systems.

First, you will need to collect some information. Namely, under what name can you reach the machine running WeeChat? I'm going to use `4z2.de` as an example for the rest of this post, but you'll have to figure out what you need to replace it with for your setup. An IP address won't cut it, as StartCom only issues certificates for domain holders.

When you've got that, it's time to log onto your machine as the user that's running WeeChat, and generate some keys. First of all, you will need to create the directory to hold the keys and certificate. Then, it's time to go there, generate some keys, and create a certificate request:

	mkdir -p ~/.weechat/ssl
    cd ~/.weechat/ssl
    openssl genpkey -out key.pem -algorithm RSA -pkeyopt rsa_keygen_bits:2048
    openssl req -new -key key.pem -out csr.pem -subj /CN=4z2.de/
    cat csr.pem

Don't forget to replace `4z2.de` with your own domain name, which you figured out in the previous step! The last command will output a bunch of gibberish to your command line, starting with `-----BEGIN CERTIFICATE REQUEST-----`. You'll need that in a few minutes.

Next, you will need to register with StartCom. Head over to [their website](https://www.startssl.com/?app=12) and hit the "Express Lane" button. Fill in all the stuff, finish the registration process, and come back here when you're logged into the control panel.

Now, hit the "Validations Wizard" button and select "Domain Name Validation" before hitting "Continue". Type in your domain name, and select one of the verification emails proposed. As you own the domain, that shouldn't be an issue. Wait for a bit until you receive a verification code via email. Paste it into the field to finish validation of your domain. Congratulations, you can now issue certificates!

To finally get your certificate, click "Certificates Wizard" in the top bar, and select "Web Server SSL/TLS Certificate". **On the next screen, select "Skip"**, and paste the gibberish certificate request that got dumped on your command line in the input field. Continue and select the domain you just verified. Now you will need to choose a subdomain to add to the certificate. If your WeeChat is listening to a subdomain of your domain, enter that. Otherwise, enter whatever you want -- e.g. "weechat". Verify that all the information is correct, and click continue one last time. On the next screen, you will see your certificate! (In some cases, StartCom will hold the certificate back and mark it for approval by personnel. In that case, you will have to wait for a bit until you can download it from the "Tool Box" tab, under "retrieve certificate").

Copy the certificate, go back to the command line, and save it into a file called `relay.pem` in the `~/.weechat/ssl` folder. Combine it with your private key to finish the setup: `cat key.pem >> relay.pem` (note the double `>` sign to append instead of overwrite!).

To finish things off, you need to enable SSL for the relay in WeeChat. Open up your WeeChat, and in the core buffer, enter `/relay sslcertkey` to load the certificate. Now, all you need to do is create an SSL-secured relay: `/relay add ssl.weechat 9001`. If you want to use a port other than 9001, just replace the number with whatever port you want. That's it! Head over to the [Glowing Bear](http://www.glowing-bear.org/) and try it out!
