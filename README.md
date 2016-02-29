This is a bridge to run Windows VST plugins (both 32 and 64 bits) with
Linux VST hosts.

## Dependencies
* gcc-multilib
* wine >= 1.6

## Build instructions

```console
$ cd vst-bridge
$ ./configure
$ make
$ make install
```

## How to use it?

Let's suppose that you have installed vst-bridge in ~/local/ and
you have a windows VST installed in ~/.wine/drive_c/VST/Synth1/Synth1.dll

First you have to create a directory for your bridges:
 $ mkdir ~/.vst-bridges

Then you can create your first bridge:
 $ ~/local/bin/vst-bridge-maker ~/.wine/drive_c/VST/Synth1/Synth1.dll ~/.vst-bridges/Synth1.so

Optionally, you can add a third argument — a path to a WINEPREFIX you want the plugin to use.

Now edit ~/.bashrc and add $HOME/.vst-bridges/ to VST_PATH. Mine looks like:
export VST_PATH=/usr/lib/vst/:$HOME/.vst-bridges/

Then you can start your favorite DAW, ask him to scan plugins again and enjoy!

## Architecture

A typical installation looks like:
/home/abique/local/
/home/abique/local/bin
/home/abique/local/bin/vst-bridge-maker
/home/abique/local/lib
/home/abique/local/lib/vst-bridge
/home/abique/local/lib/vst-bridge/vst-bridge-plugin-tpl.so
/home/abique/local/lib/vst-bridge/vst-bridge-host-32.exe
/home/abique/local/lib/vst-bridge/vst-bridge-host-32.exe.so
/home/abique/local/lib/vst-bridge/vst-bridge-host-64.exe
/home/abique/local/lib/vst-bridge/vst-bridge-host-64.exe.so

vst-bridge.so is a Linux VST plugin which has space reserved for the windows
plugin path.

vst-bridge-maker creates a dedicated <plugin>.so for a single Windows VST
plugin by copying vst-bridge-plugin-tpl.so and updating the path to the
Windows VST and the path to the corresponding host (32 bits or 64 bits).

vst-bridge-host-(32|64).exe hosts a Windows VST and communicates with <plugin>.so.

<plugin>.so spawns a new wine process vst-bridge-host-(32|64).exe and
passes the path to the Windows VST plugin.

## Protocol

The communication is done through socket(AF_UNIX, SOCK_SEQPACKET, 0).

 - request : tag, cmd, data
 - tag: 4 bytes
 - cmd: 4 bytes
 - data: n bytes

## Roadmap

 - optimize I/O (reduce the number of bytes transfered)
 - make a scanner

## Extra info

http://www.steinberg.net/en/company/developer.html
http://reaper.fm/sdk/vst/vst_ext.php
http://www.asseca.org/vst-24-specs/efVendorSpecific.html
