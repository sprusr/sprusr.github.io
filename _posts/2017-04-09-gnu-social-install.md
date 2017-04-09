---
layout: post
title: "Getting started with GNU social"
image: /img/gnu-social-icon.png
comments: true
---

I've seen a lot of people talking about [Mastodon](https://github.com/tootsuite/mastodon) recently as a possible location for the mass exodus from Twitter. I'm not entirely sure why it's seen a big spike of popularity over the past week or so (it's been around for a while), but folks seem to have started caring somewhat about [who's in control](https://ar.al/notes/we-didnt-lose-control-it-was-stolen/) of their data, which is a good thing!

Mastodon is what's known as a [federated social network](https://en.wikipedia.org/wiki/Distributed_social_network), meaning that there are many different sites which all communicate with each other. Anybody is free to host their own version, which will be able to interoperate with any other instance on the network. Pretty cool, right?

The most popular Mastodon instance as of writing is [mastodon.social](https://mastodon.social/about), and many people I know have been signing up to it (so much so, that they've had to close registrations temporarily 😅). But by signing up to somebody else's instance, you're defeating the whole point of it being federated! The only proper way of joining the network is to roll your own instance!

What people may not know about Mastodon is that it's based on [GNU social](https://www.gnu.io/social/), and as a result plays friendly with it. Somebody running a GNU social instance is able to read posts made by somebody with a Mastodon instance and vice versa.

![GNU social logo](/img/gnu-social-logo.png)
*GNU's Not Unix! And I guess GNU social's not Mastodon? Or something like that...*

I'm not trying to make the case for GNU social being any better than Mastodon, but I like the idea of running "the original". Also it's written in Ruby and my server doesn't have Ruby installed (nor Docker if I wanted to go that route). I'm super lazy, so I opted for the simpler option and installed GNU social (it's in PHP 😽). If you prefer the TweetDeck style of Mastodon, I recommend you check out their [installation instructions](https://github.com/tootsuite/mastodon#running-with-docker-and-docker-compose), or do a quick search for one of the numerous guides that are out there. Otherwise, please stick around!

### What you need

You're going to need **a server** somewhere. I recommend [DigitalOcean](https://m.do.co/c/215284685fd9) (referral link 💕), especially if you're a student because GitHub offer free DO credit as part of their [Student Developer Pack](https://education.github.com/pack)! Ubuntu is my preferred server distro, but you can choose whichever you like - I used Ubuntu 16.04 for this post. Shared hosting should also work, as long as you have access to a database (but where's the fun in somebody else setting everything up for you?!).

On said server you will need an installation of **PHP** (5.5+, works with 7), a **web server** (pick your own favourite, but mine's Apache 😁), and some kind of **database** (either MariaDB of MySQL - I went for MySQL because I already had it installed, but ran into some trouble later which I'll talk about).

In addition to the base PHP package on Ubuntu, I also needed to install php-gd, which was as simple as `apt-get install php-gd` on Ubuntu with PHP 7. The [install docs](https://git.gnu.io/gnu/gnu-social/blob/master/INSTALL) mentioned a whole load of other PHP extensions, but my installation came with everything else I needed.

### Do the thing!

Now you're ready to go! Log into your web server and `cd` to your htdocs (something like `/var/www`, you know the drill). GNU social does provide releases I think, but they recommend just cloning their repo, so `git clone https://git.gnu.io/gnu/gnu-social.git social`, where `social` is the name of the directory you want it in. You then need to make the directory writeable by your web server user. For me this was as easy as:

```
chgrp www-data /var/www/gnusocial/
chmod g+w /var/www/gnusocial/
```

Next you're going to want to set up a domain to point towards your site. I'm using Apache, so I set up a virtual host. Virtual hosts aren't really in the scope of this post, but there are some really great guides around (like [this one](https://www.digitalocean.com/community/tutorials/how-to-set-up-apache-virtual-hosts-on-ubuntu-14-04-lts)) which you should check out if you need a hand.

HTTPS is probably a good idea, so I got myself a certificate from [Let's Encrypt](https://letsencrypt.org/) (*free!*) using their [command-line tool](https://certbot.eff.org/). It handles everything for you, so there's no faff trying to work out certificate chains or whatever (if you've never had to do this, you should try it some time 😉).

The final step before beginning the setup script is to create a database with a user for GNU social. From a `mysql` shell you can do...

```
CREATE DATABASE social;
GRANT ALL on social.* TO 'social'@'localhost' IDENTIFIED BY 'password123';
```

...changing `password123` to be your chosen password (please don't use that).

Now you can navigate to `https://your.domain.tld/install.php` to start the setup. It's going to ask you a few questions: firstly your site name (what you want as the title), whether you want fancy URLs (/index.php?p=username vs /username; fancy requires [ability to have .htaccess files](https://help.ubuntu.com/community/EnablingUseOfApacheHtaccessFiles) - set `AllowOverride None` for directory in Apache site config), and whether you want SSL enabled (yes).

Then you have to tell the setup how to connect to your database. Provide the details that you set up before (something like `localhost`, `social`, `social`, `notpassword123`).

![GNU social install part 1](/img/gnu-social-install1.png)

Next you'll be asked for your admin login and site profile. The site profile is to do with whether you want to allow others to sign up - I opted for single user, meaning that I'm the only user (duh 🙄). If you go for this option, your admin login becomes your account, so choose the username you want to be public facing 🙂. Then press submit!

![GNU social install part 2](/img/gnu-social-install2.png)

I had some problems with *unknown SQL errors* which stalled me for a while. It ended up being to do with the version of MySQL I was running, combined with the default config on Ubuntu. After trawling through the system log, and a bit of searching, I found a saviour in the form of this [StackOverflow](https://stackoverflow.com/questions/9192027/invalid-default-value-for-create-date-timestamp-field) thread. Basically I needed to allow zero-dates (0000-00-00 00:00:00), which GNU social uses as a default value in the database (ew). Hopefully you don't have this problem!

![GNU social feed](/img/gnu-social-feed.png)
*The height of cool: your own GNU social instance*

If everything else goes according to plan, you'll end up with your very own GNU social instance! It might look a bit bare to begin with, but once you start following people on other instances, it'll soon fill up - remember you can follow anybody, regardless of which instance they're on or whether they're using Mastodon or GNU social or whichever compatible software!

### What now?

I'm not sure we'll all quite ready to delete our Twitter accounts quite yet, mainly because of where the community is. So... Do a bit of preaching! I hope that the reason you're making the move to GNU social is because you care about who owns you online. Make others take note as well!

In the meantime you've got a head start on setting up your config 😁. There are a few different themes to choose from other than the default. Even better would be to contribute! I'd really like to see the selection of themes widen, and might have a go at making one myself.

There are a whole load of [issues](https://git.gnu.io/gnu/gnu-social/issues) on the GNU social Gitlab project. And of course, contributing doesn't have to be code - the docs have plenty of room for improvement. Adding screenshots alongside a list of themes would be *amazing* (I found it very annoying to have to change the config to see what each one was like).

To ease the transition of content from Twitter to GNU social, I'd love to be able to have links to posts on GNU social auto tweeted. Hackathon project maybe?

---

I hope this helped you get to grips with GNU social. If you've managed to get set up, please let us know your new handle!

If you want to chat GNU social, decentralisation or anything else, give me a shout! I'm scott@soc.spru.sr 👋 or if you're still feeling a bit too attached, you can reach me on Twitter at [@sprusr](https://twitter.com/sprusr) 😬
