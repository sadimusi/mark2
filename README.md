# mark2

mark2 is a minecraft server wrapper, written in python and twisted

**NOTE**: this readme reflects behaviour expected when mark2 is complete. Currently
it is a work-in-progress.

## enhancements

* server runs in background
* attach and switch between servers with the function keys
* navigate your input with the left/right arrow keys, home, end, etc
* command scrollback using the up/down arrow keys
* tab-complete player names

## plugins

### alert

The 'alert' plugin periodically calls the 'say' command with one of a few predefined messages.

This can be useful for alerting your players to news or new commands.

### hang

This plugin guards against server lock-ups and out-of-memories. If one is detected, it will
generate a failure log and perform a hard restart of the server (basically kills it and brings it back up)

### irc

A basic bot to relay in-game chat to an IRC channel. Could be easily extended to work in
reverse (perhaps with a CommandHelper alias)

### save

Save the map ('save-all') on an interval

### script

Run server commands, mark2 commands, or shell commands on at
[set times or server events](https://github.com/edk141/mark2/blob/master/resources/scripts.sample.txt).

### shutdown

4 different ways of shutting down and then starting back up again:

* soft restart: kicks all users, runs 'save-all' and 'stop'
* hard restart: kills the server
* warn restart: issues a warning ("[SERVER] server will restart in 60 seconds") and performs a 'soft restart' after a delay
* restart: does a 'soft restart', and if the server fails to come down, performs a 'hard restart' after a delay

Also provided the same functionality for 'stop', the only difference being that the server isn't brought back up afterward

This plugin also allows you to run a 'warn restart' on an interval, for servers where performance degrades substantially over time.

### trigger

Allows a user to run "!teamspeak", for example, and for the server to /msg or /say back a factoid.

## requirements

* unix-like system (linux, bsd, os x)
* python 2
* twisted
* blessings
* clize

Assuming a relatively sane system it should be trivial to install the latter three using `pip`.

## installation

	ln -s /path/to/mark2 /usr/bin/mark2

## usage

### configure

All servers load configuration from `resources/mark2.default.properties`, which defines the default memory and plugins.
This file should not be edited as it will break your ability to cleanly update your mark2 installation from Git. Instead,
copy it to `mark2.properties` and edit it there.

To configure per-server, create a file called `mark2.properties` in the server's directory. In it you can define any
subset of the default config - as much or as little as you want (see `resources/mark2.sample.properties` for an example).
Feel free to also edit the default config.

The plugins 'script' and 'trigger' require their own configuration files in the server directory, named `scripts.txt` and
`triggers.txt`. If either is not present its respective plugin will do nothing. Examples for both files can be found in
`resources/`.

### wrap

To start a minecraft server:

    $ mark2 start /path/to/server

If you're already in the right directory, you can omit the last parameter.

At this point if `resources/mark2.properties` doesn't exist, mark2 will create it for you. You are **required** to edit
this file in order to run and will be prompted to do so now. When you quit the editor, the server will start.

If mark2 doesn't notice your server jarfile because it's got an unrecognized name,

    $ mark2 start /path/to/server/an_oddly_named_server.jar

Note that when using this form, mark2 still calls the server by the directory it is in, i.e. `server` in
this example.

### attach

To attach to a wrapped server:

    $ mark2 attach name

Where 'name' is the name of your server. If your server directory is /home/you/mcservers/pvp, the name would be 'pvp'

If you omit the 'name' parameter, you'll just attach to the first server alphabetically. If you only run one server,
you may as well omit this param.

If you want to stop a server without attaching, you can:

	$ mark2 kill /path/to/server

Note that at the present time, the server will not be given an opportunity to save when shut down in this way.

### the display

The server you're attached to is displayed in the top left. 

Users are displayed in the top right corner.

* light grey: logged in (via ssh or otherwise) but not attached
* dark grey: attached to another server
* black: attached to the same server as you

### input

* left, right, home and end: navigate text
* up and down: command scrollback
* tab: complete player name (requires query to be enabled in server.properties). If no text is entered, yields "say "

Prefixing something with "#" makes it a comment that isn't run, but is added to the console output. This is useful
for chatting to other attached admins.

You can also run mark2 plugin commands by prefixing "~". See `~commands` for a list of available commands.

If you run more than one server, you can switch between them with Control and the left and right arrow keys.

### tips

If your servers all reside in one directory, you may want to add a start helper to your path:

    #!/bin/bash
    mark2 start /home/you/mcservers/$1

And run it like
    
    $ mcstart pvp

Likewise if "mark2 attach" becomes a little too much, you could always `alias at='mark2 attach'`
