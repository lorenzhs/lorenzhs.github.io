---
layout: post
title: Forwarding All Incoming Email from Gmail, The Right Way™
---

A couple of months ago, I moved my email to [FastMail](https://fastmail.com). I had my own domain with a catch-all forward to Gmail for a few years before that, and it now points to FastMail. But some people still send email to my old Gmail address, mainly people I met before using my own domain. So I set up forwarding in Gmail. Problem solved?

Unfortunately not quite. Sometimes things end up in Gmail's spam folder, which isn't forwarded. Internet wisdom suggests to create a filter in Gmail for all emails that don't match some really long random string (like queijeemae6Lai6baey8Iecaephu6oor4ahpoz), choose "never send it to spam", and forward *everything* this way. That works, but as Gmail rightfully notes, it's unnecessarily hard on your spam filter: forwarded spam is harder to detect. You'll end up with more spam in your new inbox, which isn't very nice. Fortunately, I found a way around this. You'll probably need your own domain for it, though.

Here's how it works. Configure your mail server to treat all incoming messages to an address of your choice (I'll use spam@yourdoma.in for the remainder of this post) as spam. If you set up your domain with FastMail, create a new filtering rule that moves all messages to this address to the spam folder.

Next, create a new label in Gmail, e.g. `spam2` (`spam` is your spam folder and therefore a reserved name). Now create a filter that matches `label:spam` with the following actions: "Skip Inbox", "Apply label `spam2`", "Forward to spam@yourdoma.in", and "Never send it to Spam". Gmail will warn you about filters that match labels, because things you manually classify won't be matched by the filter (it runs when an email arrives, not when you do things with it)—but the Gmail spam filter will run before your new filter, so it will indeed match all email that Gmail thinks is spam, and forward it to your new spam-only address.

Lastly, to forward the rest of your email, create a filter that matches `-{in:spam2}` and forwards to your actual email address. Because filters are run in order, this will match all non-spam messages.

In the end, it should look something like this (I used späm instead of spam2 because umlauts are fun): ![screenshot of filters](/images/gmail_filters_forwarding.png)
