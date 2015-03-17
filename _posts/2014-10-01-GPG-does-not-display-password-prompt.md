---
title: Re-enable GPG password prompt in terminal
layout: post
---

## Problem
So I recently had cause to use GPG to encrypt something and on running the
command instead of popping up a `pinentry-curses` window it just hung there
with the following message.

~~~~~~~~~~~~~~
You need a passphrase to unlock the secret key for
user: "Prajjwal Bhandari <pbhandari@pbhandari.ca>"  
2048-bit RSA key, ID 0xBBCF9930A356E4E6, created 2014-09-04  
             (subkey on main key ID 0x438F5F1D67D55E74)
~~~~~~~~~~~~~~

The larger problem with this, at least on my computer, is that
`pinentry-curses` hangs waiting for access, and eating up cpu resources.

## Workarounds

### Don't feed stuff through stdin
This isn't always a solution, *but* if none of the other solutions work for
you, this will fix it. The issue, at-least for me, only happens when something
is being piped in to gpg.

### Kill it with fire
The easy solution to this, and one that I had been using is

~~~~~~~~~~~~~~
^C  
killall -9 pinentry-curses  
# Use what you would normally do to authenticate the command
~~~~~~~~~~~~~~

Now, the problem with this is that `kill -9` is ugly, but `pinentry-curses`
won't respond to the other signals.

### Use another program
<div class="message">
This is not an option for everyone but it is will solve the problem for most
people.
</div>

First we need to find which `pinentry`s we have installed:

~~~~~~~~~~~~~~
( IFS=:
for p in $PATH; do
    ls -1 $p/pinentry* 2>/dev/null
done )
~~~~~~~~~~~~~~

Then once you've found them, pick one you'd like to use, say `pinentry-gtk-2`.
Simply open `$HOME/.gnupg/gpg-agent.conf` in your favourite editor, and add
the full path to that, like so.

~~~~~~~~~~~~~~
pinentry-program /usr/bin/pinentry-gtk-2
~~~~~~~~~~~~~~

## Solution
Sometimes, this is not an option, maybe you're on an ssh connection, or on a
`vty`, or just don't want to use anything graphical. If that is the case: add
the one of the following lines, or equivalent, to your `~/.bashrc`, and you
are golden.


~~~~~~~~~~~~~~
export GPG_TTY=$TTY # or GPG_TTY=`tty`
~~~~~~~~~~~~~~

Alternatively, you can use `env GPG_TTY=$TTY cmd | gpg ...`.

