# DeLorean for XMPP
A proof-of-concept of a modernizing MITM XMPP proxy.

Basically, DeLorean taps in-between your rusty, old XMPP client (in my case, Maemo's Telepathy-Gabble) and your shiny, modern, sexy XMPP server, translating stuff to improve overall user experience.

It's dirty, hacky, smelly and full of weird shit thanks to newest engineering techniques such as *Copy'n'Paste Driven Development* and a carefully curated set of the finest, most sensible requirements, such as "*I want it to run on Maemo but I'm too lazy to cross compile anything; oh crap everything here is so old let's just ducttape it*" or "*I really should be doing something else right now*". But maybe it will become a beautiful swan someday, who knows.

## Usage
```
./delorean local-port remote-host remote-port
```

Point DeLorean to your XMPP server's TLS port (usually 5223), then point your client to localhost and the `local-port` of your choice. Remember to disable encryption in your client - DeLorean will take care about it. Well, not really, it won't validate the certificate, but there's commented out code for it, find it and uncomment. You could also add TLS to the client side as well if you wanted.

You should also rewrite `delorean-suppressor` and `delorean-inactivity` to match your platform, unless you want to run it on Maemo. There's also my Telepathy account and automatic status change hardcoded, you might probably want to adjust it. Oh, and make sure to run `mkfifo /tmp/delorean-suppression` and `mkfifo /tmp/delorean-inactivity` before.

You might also need to change the first line of `delorean` file to not depend on `python3.4`. And if you run it on Maemo, remember to install `python3.4`, `python-gobject` and `python-dbus` (in case you're now wondering, yes, those last two for Python 2.5, really).

## Modernized stuff:
- XEP-0280: Message Carbons
  - Received carbons are unwrapped, so you can finally get incoming messages sent to your other resources.
  - Sent carbons are sadly still ignored, but when they arrive, you can suppress earlier notifications.
    - This means you still get notifications for incoming messages on your DeLoreaned mobile while you're happily chatting on a PC, but at least the notification is cleared when you do anything with the conversation on PC, including marking it as read. Good enough for me!
- XEP-0352: Client State Indication
  - You can signalize whether your device is unused - for instance, phone being locked with its screen turned off - so the server won't send you useless presences eating the battery up until you turn on the screen back.
- XEP-0184: Message Delivery Receipts
  - So your interlocutors can have a little tickmark near the message they sent that means it got delivered to at least one of your clients.
- `disco#items` removal
  - Telepathy on Maemo doesn't use it in any meaningful way, but still talks to every service listed there to receive information and capabilities. Highly use-case specific, you might want to remove it.
- Caps version override
  - Telepathy on Maemo seems to be needlessly chatty about its presences, breaking caps cache for some clients, again causing unnecessary chattiness on login. As above, highly use-case specific, you might want to remove it.

## TODO:
- XEP-0280: Message Carbons
  - Find a way to inject sent messages into Telepathy, so sent carbons can be nicely displayed in the conversation history.
- XEP-0198: Stream Management
  - Initially just for the pings.
  - Later maybe also transparent reconnection on connection timeout using stream resumption.
- XEP-0237: Roster Versioning
- XEP-0363: HTTP File Upload
  - DeLorean could be used to inject HTTP uploads from some other component, like Maemo's share plugin.
- XEP-0384: OMEMO Encryption
  - Because why not.

## FAQ

### Damn this code is so horrible.

Yup! 

### It ate my child!

I didn't know it can do that, awesome!

### ...but I want to use/enhance/perform exorcisms on it anyway. Can I?

Sure, it's GPLv3.
