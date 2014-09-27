# i3-dstatus

The ultimate DIY statusline generator for [i3wm](http://i3wm.org).

## About

*i3-dstatus is in the early phases of development and will need some adjustments to get right. If you use i3-dstatus, send me feedback!*

i3-dstatus is a statusline generator for i3 that you can use to display system information you may be interested in. i3 comes with `i3status` which has many limitations. It has no plugin interface. It has no support for events and relies on polling for all its information, which makes it surprisingly heavy on resources. It has a weird config file format that makes it difficult to configure.

Other projects have come along to make up for these weaknesses and many of them do a great job. i3-dstatus is for users who want a more flexible statusline that can be achieved from editing options in a configuration file but without having to learn a complicated plugin api to create custom statusline entries.

This is accomplished by allowing users to update the statusline through interprocess communication using [DBUS](http://www.freedesktop.org/wiki/Software/dbus/). i3-dstatus exposes a DBUS service that you can use to update the statusline simply in pretty much any programming language and from any process (maybe even in a cron!).

* No configuration file is required
* Update the statusline from multiple processes
* Update the statusline from any language (even from the command line!)
* No complicated plugin api to learn

## Usage

Use i3-dstatus as your status command in your bar block like so:

```
bar {
    status_command /path/to/i3-dstatus
}
```

And run any scripts you use to update the statusline in `exec` or `exec_always` directives in your config:

```
exec_always /path/to/bar-update-script
```

### Updating the Statusline

The dbus service exposes the method `show_block` to update the statusline. This method takes a dict of variants. Pass an object that conforms to the [i3bar input protocol](http://i3wm.org/docs/i3bar-protocol.html) to show a block.

Blocks will be shown in alphabetical order based on their "name" member. The easiest way to control order is to prefix the "name" member with a number (such as "1:clock").

You can clear a block by omitting the "full_text" member or setting it to the empty string.

You can update the statusline from a python script. Just use a method like this one:

```python3
import dbus

bus = dbus.SessionBus()
def show_block(block):
    service = bus.get_object('com.dubstepdish.i3dstatus', '/com/dubstepdish/i3dstatus')
    service_method = service.get_dbus_method('show_block', 'com.dubstepdish.i3dstatus')
    service_method(block)
```

You can update the statusline from any language with dbus bindings (which is pretty much all of them). You can even update the statusline from the command line!

```
dbus-send --session \
    --dest=com.dubstepdish.i3dstatus \
    --type=method_call \
    /com/dubstepdish/i3dstatus \
    com.dubstepdish.i3dstatus.show_block \
    dict:string:string:name,test,full_text,'hello world'
```

## Contributing

Please report bugs, request feature, write documentation, and add examples to the `examples` directory. i3-dstatus is a community project so feedback is welcome!

## License

This work is available under a FreeBSD License (see LICENSE).

Copyright © 2014, Tony Crisci

All rights reserved.