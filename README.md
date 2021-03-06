# Hubot XMPP

Connects Hubot to your XMPP network

[![Build Status](https://secure.travis-ci.org/xmppjs/hubot-xmpp.png?branch=master)](http://travis-ci.org/xmppjs/hubot-xmpp)

## Setup

Install dependencies with `npm`

	npm install

## Configuration

There are a few configuration values you can use when getting hubot-xmpp
running. The XMPP adapter requires only 3 (5 if you need specify server
and port) environment variables to be set to able to use it.

* `HUBOT_XMPP_USERNAME`
* `HUBOT_XMPP_PASSWORD`
* `HUBOT_XMPP_ROOMS`

Optional:

* `HUBOT_XMPP_HOST` The host name you want to connect to if its different than
  what is in the username jid.
* `HUBOT_XMPP_PORT` The port to connect to on the jabber server.
* `HUBOT_XMPP_LEGACYSSL` Set to 1 to enable legacy SSL port.  This requires
  the host to be defined.
* `HUBOT_XMPP_PREFERRED_SASL_MECHANISM` Used to change the encoding used for SASL.
* `HUBOT_XMPP_DISALLOW_TLS` Prevent upgrading the connection to a secure one via TLS.
* `HUBOT_XMPP_PM_ADD_PREFIX` Make commands work in PMs to hubot without robot name/alias.
* `HUBOT_XMPP_KEEPALIVE_INTERVAL` Keep-alive interval in ms.
* `HUBOT_XMPP_RECONNECT_TRY` the number of reconnect retry in case of disconnection, default is 5.
* `HUBOT_XMPP_RECONNECT_WAIT` the time in ms to wait before reconnecting, default is 5000.

`HUBOT_XMPP_ROOMS` can be a comma separated list of rooms to join.  If
your rooms require passwords you should use the `jid:password` syntax.
Room passwords cannot contain `,`. Room names must be the full jid of the
room for example `dev@conference.jabber.example.org`.

`HUBOT_XMPP_PM_ADD_PREFIX` works by prefixing the private message with hubot name, so a
side effect is that the bot ignores commands of type `/^command/i`.

## Installation

Create a standalone hubot with xmpp adapter:

```sh
mkdir -p ./my-bot
cd ./my-bot
npx -p yo -p generator-hubot -c 'yo hubot --adapter=xmpp'
```

See also https://hubot.github.com/docs/ .

## Group chat vs private JID

The jabber protocol does not broadcast real user JID in groupchat presence
stanzas unless the server/chat room is configured to do so.

If you need to send private chat in response to a groupchat message, use
hubot's send method with the groupchat jid and `envelope.user.type = 'direct'`.
hubot-xmpp will then automatically resolve the JID to a private
chat JID, and private message the sender.

If you need to get the private chat JID, you can use
`msg.envelope.user.privateChatJid` where `msg` is the parameter of hubot's
`route` callback.

Example:

```js
robot.respond(/talk to me$/i, msg => {
  // Simply reply
  msg.reply(`Hello ${msg.envelope.user.name}. Your private JID is ${msg.envelope.user.privateChatJID}`);
});

robot.respond(/talk to me in private$/i, msg => {
  msg.envelope.user.type = 'direct';
  msg.send(`Hey ${msg.envelope.user.name}! You told me in room ${msg.envelope.user.room} to talk to you.`);
});
```
