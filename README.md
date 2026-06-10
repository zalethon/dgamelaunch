This fork is a branch of version 1.6.0-hdf; this fork is currently in version 1.6.1-roc.

Several forks have been merged in; some features prior to my own modifications:
* noisytoot's linux namespace support
* crawl's character-stripping for watchers with extremely-outdated hardware
* paxed's handful of changes (mostly fixing warnings)

Here are some other features of this fork:
* install-dgl-nh500 script, replacement for dgl-create-chroot
* rubber-ducky branch, which has currently a single function drawbanner()
  explained line-by-line -- planning to do this for more of the codebase,
  as the personal need arises.
* Some upgrades to menu/bannerfile handling -- see Changelog

I'm pretty interested in the history of this program -- so much so that I went through pains trying to make
sure I was forking a version of the repository that contained accurate commit hashes -- crawl's fork has
somehow mangled them such that its history appears entirely-unrelated to git.

Anyway, if you know anything of the history, reach out.

# install-dgl-nh500 documentation
```bash
#!/bin/bash

# install-dgl-nh500
#
# Script for setting up Debian DGL/NetHack 5.0.0 environment
# (hopefully to also set up DCSS 0.34.1 too)
#
# You should run this as root.
#
# Should be run in the directory it was shipped in.
#
# Usage:
# ./install-dgl-nh500 [options]
#
# OPTION [default]                    DESCRIPTION
# --prefix <dir> [/opt/dgamelaunch]   DGL's chroot. All other locations are
#                                     relative to this directory! And
#                                     everything DGL or NetHack need to run
#                                     is in this directory.

# --var <dir> [/var]                  Location for mutable data. Organized a
#                                     little strangely in that each game known
#                                     to dgamelaunch has its own subdirectory,
#                                     and dgamelaunch's data is just in here.

# --etc <dir> [/etc]                  Location for immutable and config data.
#                                     Ditto, each game has its own subdirectry.
#                                     note that NetHack doesn't have one; all
#                                     of its immutable data lives in hackdir.

# --menudir <dir> [<etc>/menu]         Location for menu files. Really they can
#                                     be anywhere, but this instructs the
#                                     script where to put the default ones.

# --hackdir <dir> [/opt/nh500]        NetHack is built wth this as its prefix.

# --playground <dir> [/var/nh500]     NetHack is built with this as its
#                                     'var playground.'

# --dbfile <sqlite3 db> [<var>/dgamelaunch.db]   The location for the database.

# --without-nano [FLAG]               If you do not set this flag, nano will be
#                                     installed in the chroot, for use as an
#                                     editor. The default configuration
#                                     assumes it is "built" with nano.

# --without-nh500 [FLAG]              If you set without-nh500, the script will
#                                     not install NetHack. Know what you're
#                                     doing!

# -q -s --quiet --silent [FLAG]       Causes autogen and make to run quietly.

# --clean                [FLAG]       Skips everything except the clean step.
#                                     Useful if the process gets interrupted...


# Here are some settings which would cause nethack and dgl to be installed
# in a way that reflects the setup instructions provided by Paxed...
# for posterity and testing.
#
# CHROOT="/opt/nethack/nethack.alt.org"
# DGLVAR="/dgldir"
# DGLETC="/."
# MENUDIR="$DGLETC"
# HACKDIR="/nh500"
# PLAYGROUND="/nh500/var"
#
# ./install-dgl-nh500 --prefix /opt/nethack/nethack.alt.org --var /dgldir \
#   --etc /. --menudir /. --hackdir /nh500 --playground /nh500/var --quiet
```

# step-by-step usage
To install and use NetHack and dgamelaunch, simply (tested as working on
Debian 6.12.88-1, with prerequisites already installed):

After downloading this version of dgamelaunch:

```bash
cd dgamelaunch-nh500
sudo ./install-dgl-nh500
```
dgamelaunch will be installed at `/opt/dgamelaunch`, and NetHack 5.0.0 will be
installed in `/opt/dgamelaunch/opt/nh500`.

To test the system:

```bash
sudo /opt/dgamelaunch/bin/dgamelaunch
```

Try to register a new user, login, and play the game.
If it works, brilliant! If it doesn't work, try the NetHack binary:

```bash
sudo chroot /opt/dgamelaunch nh500
```

If that fails, it might be a missing library. Check `/opt/dgamelaunch/lib/`
and `.../lib64`, and compare the contents with the results of
`ldd /opt/dgamelaunch/bin/nh500`. Then copy over libraries as necessary.


If it's working, then you may wish to let people connect to the server just
like other servers you see online.

To do this, you will need to do something pretty dangerous -- let anyone run
dgamelaunch, even though it has root privileges.

```bash
sudo chmod a+s /opt/dgamelaunch/bin/dgamelaunch.<date>
```

Replace <date> with the appropriate string of numbers, obviously. From here,
any user can run dgamelaunch on your system, which might potentially create
a vulnerability.

Next, set up a new user. On Debian:

```bash
sudo adduser nethack
```
And follow the prompts. Name the user whatever you like, and give bogus info
for everything other than password. Now edit `/etc/passwd`. The new user's
line will look like this:

```bash
nethack:x:<uid>:<gid>:nethack,1,1,1,1:/home/nethack:/bin/bash
```

You need to modify the new user's line like so:

```bash
nethack:x:<uid>:<gid>:nethack,1,1,1,1:/home/nethack:/opt/dgamelaunch/bin/dgamelaunch.<date>
```

What we are doing there is setting the user nethack's login shell to
dgamelaunch. This way, when someone uses SSH to connect to your server, and
logs in as nethack, they will be presented immediately with dgamelaunch, and
will not see a shell when it exits.

Congrats on your new server...


# Hints
## Dungeon Crawl Stone Soup
1) obtain the source, navigate to that directory
2) `sudo make install prefix=/ DATADIR=/etc/dcss0341 SAVEDIR=/var/dcss0341 USE_DGAMELAUNCH=1`
Note: 2) instead of setting USE_DGAMELAUNCH, you can also edi
`<src>/crawl-ref/source/AppHdr.h`, and `#define DGAMELAUNCH=1` there. (and tweak
    other settings which interest you)
3) navigate to the directory with these scripts (dgamelaunch source dir)
4) ...
```bash
./cpbin -b /bin /bin/crawl /opt/dgamelaunch
sudo cp /etc/dcss0341 /opt/dgamelaunch/etc/
sudo cp /var/dcss0341 /opt/dgamelaunch/var/
```
5) (clean up in `/bin/crawl`, `/etc/dcss0341`, `/var/dcss0341` if you want to; don't need those files anymore)
6) Modify `dgamelaunch.conf` to uncomment relevant lines
7) ...
```bash
sudo mkdir /opt/dgamelaunch/var/inprogress-dcss0341
sudo chown -R games:games /opt/dgamelaunch/var
```
8) Modify the main menu (it might help to `dcss-menu.patch` to it)

## Angband
You should probably add more config options like `--disable-borg`, and might want to go through the source to disable debug and wizard commands.

1) obtain source, navigate to the directory
2) `./configure --prefix=/. --bindir=/bin --datarootdir=/var --with-gamedata-in-lib`
3) make DESTDIR=/opt/dgamelaunch install
4) Try out cpbin, with --libs-only, to see if you can just automagically transfer libraries
4) otherwise do it by hand, check out the `ldd` command and other parts of this readme for more.
5) `mkdir -p <dgamelaunch chroot>/usr/lib/locale`
5) `cd <dgamelaunch chroot>/usr/lib/locale`
5) `cp /usr/lib/locale/locale-archive .`
6) Uncomment relevant lines in dgamelaunch.conf, and make/chown the inprogress dir

# Resources
More resources for troubleshooting vis NetHack:
https://nethackwiki.com/wiki/User:Paxed/HowTo_setup_dgamelaunch
Vis dgamelaunch and crawl:
https://github.com/tarballqc/dcss-server-install/
