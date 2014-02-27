# tor2tcp configuration example

## Purpose

Say for example you have a website that you feel comfortable having online
via a public domain name, but you do not feel comfortable having it hosted
on the location of its clearnet IP because \$reason (for example you are [The
Pirate Bay](http://jntlesnev5o7zysa.onion/)). Or you want to store all your
e-mail in an undisclosed location. This configuration enables the transferring
machine to know as little as possible about the actual hosting machine (because
it's really a .onion) while using very little resources (i.e. a VPS with 128MB
RAM for little \$currency/\$timeframe) on the transparent proxy machine and even
less time to set it up.

## tl;dr 

tor2tcp attempts to:

* Have arbitrary internet facing services that actually connect you to a
  hidden service
* Enable TLS handshakes with a hidden service from the regular internet,
  enabling safe transport
* Handle connections via tiny VPS (128MB RAM) proxy machine with no access to
  information at all, except for configuration files and public ssh keys

## Lightning Talk

I presented this project at 30c3, a recording of it is [available from CCC](http://media.ccc.de/browse/congress/2013/30C3_-_5564_-_en_-_saal_g_-_201312301245_-_lightning_talks_day_4_-_nickfarr.html)
(skip to 1:41:05) with a mirror on
[Youtube](https://www.youtube.com/watch?v=wlanbPyceeU#t=6064). Feel free to
take a look at
[my slides](https://poum.niij.org/lightningtalk-mzeltner-tor2tcp.pdf) as well.

## Incoming connections

See the `vps` folder for sample configuration files. They have inline
documentation. It's that simple, if you only want to run HTTP for example.

## Outgoing connections

Say we want to receive *and* send e-mail, then we need more than just incoming
connections. Since we don't want to be \*\*\*\* SPAM \*\*\*\*, we need to send
all our mail via the incoming transparent proxy machine (`vps`). We don't want
to turn our proxy into a mail relay as we want to avoid having any data on that
machine (like a mail queue or logs).

So outgoing connections are a little more complicated and depend on torsocks,
[redsocks](http://darkk.net.ru/redsocks/) as well as
[DNS SOCKS Proxy](https://github.com/jtRIPper/dns-tcp-socks-proxy) (see
[my fork](https://github.com/mzeltner/dns-tcp-socks-proxy) to avoid automatic
writing to `/etc/resolv.conf`). Also, have a password-less ssh-key available
(that enables login on the vps machine), which we need for the following:

```
tmux new-session -d 'torsocks autossh -D 1080 mzeltner@46.246.28.219'
```

autossh backgrounding was broken for me for some reason, so I use tmux (or
screen) to keep our torified SOCKS tunnel on `127.0.0.1:1080` open.

Thanks to transparent proxying via `redsocks` and `iptables`, this torified
SOCKS tunnel is used for our outgoing connections with postfix. See the 
`onion` folder for sample configuration files.

## Saftey

Keep in mind, this is an experimental setup, **don't** use this for anything
important.

## More reading

[https://poum.niij.org/](https://poum.niij.org/) is both proof of concept and 
more documentation.

## Credits

Abel Luck for inspiration, ra\_ for testing incoming connections, MacLemon for
brain bounces.
