The main feature of this fork of dgamelaunch is install-dgl-nh500, which automates installation of NetHack inside
a dgamelaunch chroot.

# install-dgl-nh500 documentation
```bash
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

# --menudir <dir> [/etc/menu]         Location for menu files. Really they can
#                                     be anywhere, but this instructs the
#                                     script where to put the default ones.

# --hackdir <dir> [/opt/nh500]        NetHack is built wth this as its prefix.

# --playground <dir> [/var/nh500]     NetHack is built with this as its
#                                     'var playground.'

# --dbfile <sqlite3 db> [/var/dgamelaunch.db]   The location for the database.

# --nano [FLAG]                       If you set the nano flag, nano will be
#                                     installed in the chroot, for use as an
#                                     editor. The default configuration
#                                     assumes it is "built" with nano.

# -q -s --quiet --silent [FLAG]       Causes autogen and make to run quietly.


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
# ./install-dgl-nh500 --prefix /opt/nethack/nethack.alt.org --var /dgldir --etc /. --menudir /. --hackdir /nh500 --playground /nh500/var
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

If that fails, it might be a missing library. Check `/opt/dgamelaunch/lib/` and `.../lib64`, and
compare the contents with the results of `ldd /opt/dgamelaunch/bin/nh500`.
Then copy over libraries as necessary.


If it's working, then you may wish to let people connect to the server just like other servers you see online.

To do this, you will need to do something pretty dangerous -- let anyone run dgamelaunch, even though it has
root privileges.

```bash
sudo chmod a+s /opt/dgamelaunch/bin/dgamelaunch.<date>
```

Replace <date> with the appropriate string of numbers, obviously. From here, any user can run dgamelaunch on
your system, which might potentially create a vulnerability.

Next, set up a new user. On Debian:

```bash
sudo adduser nethack
```
And follow the prompts. Name the user whatever you like, and give bogus info for everything other than password.
Now edit `/etc/passwd`. The new user's line will look like this:

```bash
nethack:x:<uid>:<gid>:nethack,1,1,1,1:/home/nethack:/bin/bash
```

You need to modify the new user's line like so:

```bash
nethack:x:<uid>:<gid>:nethack,1,1,1,1:/home/nethack:/opt/dgamelaunch/bin/dgamelaunch.<date>
```

What we are doing there is setting the user nethack's login shell to dgamelaunch. This way, when someone uses
SSH to connect to your server, and logs in as nethack, they will be presented immediately with dgamelaunch, and
will not see a shell when it exits.

Congrats on your new server...


# Dungeon Crawl Stone Soup
First, though, here's how to install Dungeon Crawl Stone Soup:
1) obtain the source, navigate to that directory
2) `sudo make install prefix=/ DATADIR=/etc/dcss0341 SAVEDIR=/var/dcss0341 NOWIZARD=1`
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

That's it!

# Resources
More resources for troubleshooting vis NetHack:
https://nethackwiki.com/wiki/User:Paxed/HowTo_setup_dgamelaunch
Vis dgamelaunch and crawl:
https://github.com/tarballqc/dcss-server-install/