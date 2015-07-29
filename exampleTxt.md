## tcp_v4_rcv()
- tcp_v4_rcv()(net/ipv4/tcp_ipv4.c)

   首先来看一下这个函数：
```c
int tcp_v4_rcv(struct sk_buff *skb)
{
    struct sock *sk;

}
```
首先检查（检查packet type不是PACKE_HOST后者packet大小不足tcp首部长度-最少20字节）并丢弃这个包，如果这是个有问题的包，然后做相关的初始化，首调用__inet_lookup_skb()函数，然后根据tcp状态调用__inet_lookup_established()或者__inet_lookup_listener()，如果没有找到socket，则丢弃这个包。
```c
sk = __inet_lookup_skb(&tcp_hashinfo, skb, th-
>source, th->dest);
. . .
if (!sk)
goto no_tcp_socket;

if (!sock_owned_by_user(sk)) {
. . .
{

if (!tcp_prequeue(sk, skb))
ret = tcp_v4_do_rcv(sk, skb);
}

} else if (unlikely(sk_add_backlog(sk,skb,sk->sk_rcvbuf + sk->sk_sndbuf))) {
bh_unlock_sock(sk);
NET_INC_STATS_BH(net,LINUX_MIB_TCPBACKLOGDROP);
goto discard_and_relse;
}
}
```

然后我们看一下tcpv4_do_rcv()函数。
```c
int tcp_v4_do_rcv(struct sock *sk struct sk_buff
*skb)
{

```
如果tcp的状态是TCP_ESTABLISHED.则调用tcp_rcv_establishe()函数.
```c
if (sk->sk_state == TCP_ESTABLISHED) { /* Fast
path */
. . .
if (tcp_rcv_established(sk, skb,
tcp_hdr(skb), skb->len)) {
rsk = sk;
goto reset;
}
return 0;

```
如果tcp的状态是TCP_LISTE状态，则调用tcp_v4_hnd_req()函数
```c
if (sk->sk_state == TCP_LISTEN) {
struct sock *nsk = tcp_v4_hnd_req(sk,skb);
}

```
如果不是在TCP_LISTE状态，则调用tcp_rcv_state_process()函数。
```c
if (tcp_rcv_state_process(sk, skb,
tcp_hdr(skb), skb->len)) {
rsk = sk;
goto reset;
}
return 0;
reset:
tcp_v4_send_reset(rsk, skb);
}

```

# Dillinger

Dillinger is a cloud-enabled, mobile-ready, offline-storage, AngularJS powered HTML5 Markdown editor.

  - Type some Markdown on the left
  - See HTML in the right
  - Magic

Markdown is a lightweight markup language based on the formatting conventions that people naturally use in email.  As [John Gruber] writes on the [Markdown site] [1]:

> The overriding design goal for Markdown's
> formatting syntax is to make it as readable
> as possible. The idea is that a
> Markdown-formatted document should be
> publishable as-is, as plain text, without
> looking like it's been marked up with tags
> or formatting instructions.

This text you see here is *actually* written in Markdown! To get a feel for Markdown's syntax, type some text into the left window and watch the results in the right.

### Version
3.0.2

### Tech

Dillinger uses a number of open source projects to work properly:

* [AngularJS] - HTML enhanced for web apps!
* [Ace Editor] - awesome web-based text editor
* [Marked] - a super fast port of Markdown to JavaScript
* [Twitter Bootstrap] - great UI boilerplate for modern web apps
* [node.js] - evented I/O for the backend
* [Express] - fast node.js network app framework [@tjholowaychuk]
* [Gulp] - the streaming build system
* [keymaster.js] - awesome keyboard handler lib by [@thomasfuchs]
* [jQuery] - duh

And of course Dillinger itself is open source with a [public repository](https://github.com/joemccann/dillinger) on GitHub.

### Installation

You need Gulp installed globally:

```sh
$ npm i -g gulp
```

```sh
$ git clone [git-repo-url] dillinger
$ cd dillinger
$ npm i -d
$ mkdir -p public/files/{md,html,pdf}
$ gulp build --prod
$ NODE_ENV=production node app
```

### Plugins

Dillinger is currently extended with the following plugins

* Dropbox
* Github
* Google Drive
* OneDrive

Readmes, how to use them in your own application can be found here:

* [plugins/dropbox/README.md](https://github.com/joemccann/dillinger/tree/master/plugins/dropbox/README.md)
* [plugins/github/README.md](https://github.com/joemccann/dillinger/tree/master/plugins/github/README.md)
* [plugins/googledrive/README.md](https://github.com/joemccann/dillinger/tree/master/plugins/googledrive/README.md)
* [plugins/onedrive/README.md](https://github.com/joemccann/dillinger/tree/master/plugins/onedrive/README.md)

### Development

Want to contribute? Great!

Dillinger uses Gulp + Webpack for fast developing.
Make a change in your file and instantanously see your updates!

Open your favorite Terminal and run these commands.

First Tab:
```sh
$ node app
```

Second Tab:
```sh
$ gulp watch
```

(optional) Third:
```sh
$ karma start
```

### Todos

 - Write Tests
 - Rethink Github Save
 - Add Code Comments
 - Add Night Mode

License
----

MIT


**Free Software, Hell Yeah!**

- [john gruber](http://daringfireball.net)
- [@thomasfuchs](http://twitter.com/thomasfuchs)
- [1](http://daringfireball.net/projects/markdown/)
- [marked](https://github.com/chjj/marked)
- [Ace Editor](http://ace.ajax.org)
- [node.js](http://nodejs.org)
- [Twitter Bootstrap](http://twitter.github.com/bootstrap/)
- [keymaster.js](https://github.com/madrobby/keymaster)
- [jQuery](http://jquery.com)
- [@tjholowaychuk](http://twitter.com/tjholowaychuk)
- [express](http://expressjs.com)
- [AngularJS](http://angularjs.org)
- [Gulp](http://gulpjs.com)
