+++
date = '2026-08-02T02:13:01+02:00'
title = 'A shitbox as a step towards digital sovereignty (Nextcloud setup)'
+++

Trying to selfhost basic services: attempt 953284753.

Once again did the reality dawn on me. To check my upcoming appointments, I open... _Google Calendar_. To open one of my prehistoric files I had on a computer I put away in 2017, I open... _Google Drive_. To listen to music on my phone, I use... _YouTube Music_ (ReVanced, true, but my point still stands). Preposterous!

However, all past attempts at trying to create a home server went close to this:

1. "I wanna try to create a home server."
1. Find an old computer, plug it in, install Ubuntu Server.
1. Realize I don't know anything about servers or networking; spend 15 hours watching YouTube tutorials and reading blogs of people who have successfully accomplished this.
1. Get completely lost, give up.

Most of the time, the problem was making my shitbox computer available to "the public" (or rather, to me when I'm outside of the reach of my network). There are so many possibilities -- Tailscale, Headscale, Cloudflare Tunnel, reverse proxies... also I'm a little stupid so that's that. This time, I decided to approach it differently: I rented a VPS and am going to document my attempts at making stuff work here, not having to worry about having to circumvent my ISP's limitation about not giving out public IPv4s.[^untrue] An expected bonus of making this blog is that when something explodes, I'll be able to go back and read how I made it work the last time.

A non-exhaustive list of reasons why I'm going to put myself through this:

1. I get to use Big Tech products less.
1. It's a step towards decentralization of the internet, which is cool.
1. About three years ago I promised my mum I'd sort through our family photos (which are dispersed over countless drives all around the house, with the photos dating back to maybe 2006?) and put them all in one place, which would be the home server. As you might have guessed, that hasn't happened yet.

## VPS Provider

After careful consideration (i.e. approximately 5 AI prompts), I decided to go for [Hetzner](https://hetzner.com/). They offer reasonably priced, based-in-Europe VPS with good reviews. Since I'm just in the "testing" phase for now, I decided to opt for the cheapest possible option: "cost optimized server" with 2 vCPUs, 4 GB RAM and 4 GB ROM at 6.64 EUR per month. For irrelevant reasons, I opted for the name "kudlus" (any resemblance to my surname is purely coincidental). Out of Nürnberg, Falkenstein and Helsinki, Hetzner's European data centers, I opted for Helsinki -- not because it is the closest, the fastest or the best, but because it was the only one available. (It would have been the best for me to choose Nürnberg -- according to [cloudping.info](https://cloudping.info), the latency is just 4 ms, compared to Helsinki's 47 ms and Falkenstein's 69 ms in Prague, Czech Republic).

## Initial Setup

The Hetzner setup wizard made it easy to set up the most important of things -- OS installation (I went for Ubuntu Server 26.04), ability to get a public IPv4 address (costing 0.00097 EUR / h), setting up SSH keys, add storage volumes (I did not do this yet), firewalls, backups, etc.

Then, I followed a YouTube tutorial I don't have the link to anymore (sorry) setting up the SSH properly: disabling password authentication, the inability for `root` to log in and enabling authentication using SSH keys: (editing `/etc/ssh/sshd_config`)

```
PasswordAuthentication no
PermitRootLogin no
PubkeyAuthentication yes
```

Then, I decided to look up how to actually get my server to host some apps for me. At first, due to my laziness, I considered using [Coolify](https://coolify.io/); this plan failed rather quickly because my budget server didn't even meet the minimum system requirements (notably the CPU amount -- Coolify itself needs at least 2 CPUs, which would leave nothing for the actual hosted apps). Abort mission, retreat. (Besides, damn that's heavy considering it's just supposed to install some apps)

Browsing through a [Reddit thread called "Idiot's guide to self-hosting"](https://www.reddit.com/r/selfhosted/comments/121bkz8/idiots_guide_to_selfhosting/) (perfect for me), I found [Yunohost](https://yunohost.org/) (pronounced "why you no host") promising to remove most of the headaches from selfhosting. All you need to do is to run a .sh file, wait until it finishes installing and voilà, all that's left is to install apps. But that's boring! I don't want my homelab to _actually work_, I want to suffer as it refuses to do what I want it to do! (translation: I might come back to this.)

I found [this YouTube tutorial](https://www.youtube.com/watch?v=GarMdDTAZJo) which lead me to realize I first need to run the apps locally using Docker. Meaning I first have to learn Docker. Meaning I first have to install Docker, which turned to be an [unreasonably complex task](https://docs.docker.com/engine/install/ubuntu/) since the only package available through `apt`, `docker.io`, is not the one you're actually supposed to use.

## Nextcloud & NPM

After getting the "Hello from Docker!" message from running hello-world, I followed the tutorial for installing [Nextcloud (AIO)](https://github.com/nextcloud/all-in-one). This tutorial assumes there is no existing web server or reverse proxy, which is true as of now. Surprisingly enough, I didn't run into any issues and am therefore able to report success! Visiting the server's public IPv4 address with the port 8080 appended to it resulted in me seeing the "Nextcloud All-in-One setup".

I have been greeted with a splash screen informing me that I'm using the official Nextcloud installation method. I also typed in the domain that will be used for Nextcloud -- nextcloud.kudlus.space -- which left me with the error message "DNS config is not set for this domain or the domain is not a valid domain!". It hasn't been configured indeed, and since the completion of this installation process seems to rely on that, I gave up my pride and followed a [TechHut YouTube tutorial](https://www.youtube.com/watch?v=DFUmfHqQWyg).

I got through the Nextcloud installation and I set up my domain correctly (finally). Then, as I tried to use the Nginx Proxy Manager -- which I accessed just fine on port 81 -- I tried to set up a proxy host: http://proxy.kudlus.space/ that was to point to 62.238.63.244:81 (that is, where I just had NPM open), but that just didn't work. It was very strange, using `curl -v http://proxy.kudlus.space/` gave me a response of 200 OK but trying to access it through Firefox resulted in a 404 Not Found error. I have no idea what could have caused this.

After I pulled out a sufficient amount of hair (genetics has it that I only have a few years left of this luxury anyway) and a random visit of http://kudlus.space (which gave me the "Congratulations! You've successfully started the Nginx Proxy Manager. If you're seeing this site then you're trying to access a host that isn't set up yet." message), I tried visiting http://proxy.kudlus.space one more time, out of desperation more than anything. The software has decided to show mercy, recognizing that I suffered enough and without me having to change anything, showed me the desired site. Must be a miracle (admittedly, I would have preferred the miracle to do its magic right away instead of after 30 minutes of debugging, but I'm thankful anyway).

The next step was generating an SSL certificate. To do this in NPM, you need an API key to edit zone DNS. This is where I came across a huge limitation of Namecheap -- to use their API, one needs to have at least 20 domains, have at least 50 USD on their account or needs to have spent at least 50 USD in the past two years. I quickly dealt with this steaming pile of... regulations I don't necessarily agree with by quickly switching to Cloudflare (which, btw, is where this very website is also hosted). In a matter of tens of seconds, I had the desired API key in my posession without having paid a single cent more to Namecheap. 

Just like that, I was able to set up another proxy, this time https://nc-aio.kudlus.space (notice the https) to point to -- you guessed it -- the AIO interface of Nextcloud. Before I go back to where I was 4 paragraphs ago, I will create another proxy (this time https://nc.kudlus.space) leading to port 11000 and having the following under "Advanced":

```
client_body_buffer_size 512k;
proxy_read_timeout 86400s;
client_max_body_size 0;
```

This will allow me to access the actual Nextcloud Hub. And as such, the Nextcloud actually works as it should.

Thank you for reading this article. Please note that more articles about this topic are to come.

## Links

I'm linking blog articles I read and liked, and which possibly helped me solve a problem.

[Intro to self-hosting. How to get started hosting your applications](https://boringtech.net/blog/intro-to-self-hosting-how-to-get-started-hosting-your-applications/)  
[How to start your self-hosting adventure: a high-level overview](https://ounapuu.ee/posts/2021/03/17/how-to-start-your-self-hosting-adventure/)

Some time ago, I also attended [Ondřej Pacovský](https://ondrejpacovsky.cz/)'s talk on InstallFest 2026 which I liked (in Czech): [Slides](https://ondrejpacovsky.cz/talks/if26.pdf) / [Video](https://www.youtube.com/watch?v=e64q7W_-Pl8)

[^untrue]: Yes, that makes the title of the article untrue, but I thought it was too good to change it. Sue me.
