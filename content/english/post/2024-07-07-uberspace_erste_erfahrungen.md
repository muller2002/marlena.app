---
author: "Marlena Müller"
title: "First impressions of Uberspace"
date: 2024-07-07T00:00:00+02:00
description: ""
tags: [
    "Uberspace", "Webhosting", "Erfahrungen", "Mailserver"
]
summary: "My first experiences with Uberspace and setting up a mail server and static websites."
---

I've had an account with [Uberspace](https://uberspace.de/) for a few days now. Uberspace is a hosting provider with an innovative concept. Uberspace has a "pay what you want" model. This means that you can pay any amount from 5€ per month (theoretically you can also pay from 1€, but this is not recommended). Uberspace recommends paying €10 per month to cover the costs and ensure that Uberspace can continue to exist. There is a "solidarity model" so that Uberspace can also accept the small amounts. This means that those who pay more support those who pay less.

To try out Uberspace, the first month is free. I'm currently in the trial month and haven't paid yet. If my test month goes well, I will continue to use Uberspace and also pay :)

Uberspace offers many functions. I am mainly interested in the mail server and static web hosting.

## Mailserver

I currently use [PurelyMail](https://purelymail.com/) as my mail provider. PurelyMail is a very good mail provider, which is also relatively cheap (10$/year). I am very satisfied with PurelyMail. However, PurelyMail is based in the USA and I would prefer to have my emails in Germany, especially to have better GDPR compliance. In addition, US companies can be forced to hand over data to the US government. I want to avoid that. (Even though I don't think I'm being targeted by the US government :D)

That's why I decided to set up a mail server with Uberspace. Uberspace offers a mail server that is very easy to set up. The setup process is very well [documented](https://manual.uberspace.de/mail-access/). I will therefore not go into the setup process here.

The only thing to note is that the mail commands take a little longer to set up (5-10 seconds). This is not really a problem, but it did irritate me a little at first.
Setting up mail domains and accounts is very easy, but this has to be done via the command line. You don't have to be a pro with the command line, the biggest hurdle is the SSH connection. But this is also not difficult and very well [documented](https://manual.uberspace.de/basics-ssh/).

Since I previously had all my mails at PurelyMail, I had to move all my mails from PurelyMail to Uberspace.
For this I used the [Guide from Uberspace](https://lab.uberspace.de/howto_migrate-mails/) as a guide.

### Migration from PurelyMail to Uberspace

The tool `imapsync` is recommended for the move. `imapsync` is already installed on the "Asteroids" of Uberspace.

I used the following command to move from PurelyMail to Uberspace:

:warning: **Attention**: I assume that Uberspace version 7 is used here. When Uberspace version 8 is released, the commands may change. Please inform yourself beforehand.

```bash
imapsync \
    --logdir ~/logs/imapsync \
    --host1 imap.purelymail.com \
    --user1 <old mail adress> \
    --ssl1 \
    --host2 localhost \
    --user2 <new mail adress> \
    --ssl2
```

At first I had problems with authentication. After a bit of trial and error, I came up with the idea of using a password that doesn't contain any special characters. That worked. Then I simply changed the password again.

It took `imapsync` about 10 minutes to migrate ~2000 mails.

### Catch-All-Adress

Uberspace also offers a catch-all address. This means that all mails that are not sent to a specific address are forwarded to one address.
I use this feature to have a separate email address for each service. This way I can see which service has forwarded my email address or simply block services that spam me.

You have to set up catch-all addresses via the command line. This is also described in the [documentation](https://manual.uberspace.de/mail-mailboxes/#catch-all-mailbox).

### Restrictions

Mail at Uberspace has some limitations. If you want to set up several domains, the individual accounts are distributed to all domains. For example, if you have `example1.com` and `example2.com` and set up the account `info`, then this account receives mails for `info@example1.com` and `info@example2.com`.
This is not a problem for my use case, as I send all mails directly to one mailbox and do not split them up.

What was a problem for me was that only the `+` character is allowed for subaddressing. Previously I used `_`. The problem can easily be solved with a Sieve rule, but it is a bit more complicated.

## Static web hosting

Uberspace also offers managed web hosting to host static websites. 

To use static web hosting, simply copy the website files into the directory `/var/www/virtual/$USER/html`. `$USER` is the user name. The website can then be accessed at `https://$USER.uber.space`.

If you want to add your own domains, you only have to add the domain in the Uberspace console and adjust the DNS entries (see [Documentation](https://manual.uberspace.de/web-domains/)). The website is then accessible under `https://$DOMAIN`.
If you only want the content to be available via one domain, create the folder `/var/www/virtual/$USER/$DOMAIN/` and copy the files there instead of `/var/www/virtual/$USER/html`.In this way you can also host several static websites on one asteroid.
Uberspace advises against this for security reasons. If the websites/web applications are not separated from each other, an attacker on one website can also access the other websites if there is a security gap in the underlying software. Since I only host static websites, this is not a problem for me. With static websites, the only attack vector is the web server itself, which is provided by Uberspace and is the same for all asteroids. So only an attacker can access all websites if he finds a vulnerability in the web server, but he can also do this if the websites are on different asteroids.

## Fancy Sticker

You can also order stickers from Uberspace. I like stickers, one has already made it onto my laptop :D

![Various stickers with the Uberspace logo on a letter. The letter says in german: "Nice to have you with us! - N"](/images/uberspace/brief.jpeg)

![Purple shiny Uberspace sticker on a laptop, around it some stickers in german language: FCK AFD, Technically questionable, 5 cats lying on top of each other in the colors of the trans flag, covered sticker on which it says ...colorful Paderborn, "Possible leakage of smoke from the device is normal and harmless"](/images/uberspace/laptop.jpeg)

## More

Uberspace has its own charm. The documentation is very well designed.

Furthermore, Uberspace has its own [house rules](https://uberspace.de/de/about/houserules/). In these house rules you can see that Uberspace is a very open and tolerant company. I like that very much.

You can also conclude a data processing agreement (DPA) with Uberspace. This is very important if you process personal data. That's a big plus point for Uberspace.

There are some community guides in the [Uberlab](https://lab.uberspace.de/). These guides are from the community for the community. Skimming through the instructions gives a very good impression, they are very detailed and well written.

## Conclusion

Mail works. If you don't mind the limitations, Uberspace is a good alternative to other mail providers. Time will tell whether I will use Mail there in the long term. If the service continues to be stable and runs well, I see no reason to switch to another mail provider.

The static web hosting also works very well. I am very satisfied.  The same applies here as with Mail: If the service remains stable and good, I will host more websites (e.g. this one) on Uberspace.
