See far below for step by step usage!

This repository is a fork of Crawl's version of dgamelaunch[^dgl-crawl], which
was forked from Paxed's dgamlaunch after January 29th, 2011[^dgl-paxed-2011].
Work by Paxed continued[^dgl-paxed] after Crawl's fork; this fork has been
patched to merge the two versions.

For history's sake, and in case you're running into trouble and need more
threads to pull, check out also the original READMEs from each other
repository: [paxed's readme](README-paxed) and [crawl's readme](README-crawl)

Other than that, the primary feature here is `install-dgl-nh500`, which is a
drop-in replacement script for `install-dgl-chroot`.

`install-dgl-nh500` It automates installation of NetHack inside a dgamelaunch
chroot.

[^dgl-crawl]: https://github.com/crawl/dgamelaunch/commit/455308b96fa7522c8c9873653401b4e0cf8f71e4
[^dgl-paxed-2011]: https://github.com/paxed/dgamelaunch/commit/3d0812cc3c78f713ec244f64d67c922d66dd46f3
[^dgl-paxed]: https://github.com/paxed/dgamelaunch/commit/55bd7dce01db17356b05da966595ff1ae6097e60

# TODO
* Fixing the makefiles so that we don't have to use eldritch bash scripts to
    install stuff
* reading through Paxed's TODOs, importing ones from crawl, incorporating
    issues below

## Issues
(from Paxed's repository; some may have been addressed by crawl team already)
### Security
* [Blank password bypasses certain passwords #20](https://github.com/paxed/dgamelaunch/issues/20)
* [dgamelaunch vulnerable to pass-the-hash attack #10](https://github.com/paxed/dgamelaunch/issues/10)
    This one was most likely addressed by crawl's team -- I avoided clobbering
    any changes they made to password infrastructure... if not, a pull request
    exists in paxed's repository which proposes to resolve the issue.
* [dgamelaunch binary should not be in chroot](https://github.com/paxed/dgamelaunch/issues/1)
    I mean yeah probably
### Compiling
* [Does not build with newer Versions of flex #4](https://github.com/paxed/dgamelaunch/issues/4)
* [yywrap undefined #19](https://github.com/paxed/dgamelaunch/issues/19)
    Duplicate of #4, probably
### Suggestions
* [suggestion - record terminal size info in ttyrec #17](https://github.com/paxed/dgamelaunch/issues/17)
* [Allow importing NETHACKOPTIONS environment variable? #16](https://github.com/paxed/dgamelaunch/issues/16)

# dgamelaunch documentation
From paxed's excellent README
```
COMMANDLINE PARAMETERS
======================

 -a		Ignored.
 -c		Shows error message and exits. (login shell command)
 -e		Ignored.
 -h		Ignored.
 -i user	Autologin and run the register -command hook for "user"
    		(with password "user").
 -p		Ignored.
 -q		Be quiet, suppress errors.
 -s		Show players currently playing.
 -W user:msg	Send message "msg" to all players. The message seems
    		to come from "user".
 -S 		Free the shared memory block.
 -D		Show contents of the shared memory block.


ENVIRONMENT VARIABLES
=====================

 Linux telnetd allows importing the USER environment variables via telnet,
 while FreeBSD does not.  FreeBSD, on the other hand, does allow the LOGNAME
 environment variable.  Dgamelaunch will first check USER, then LOGNAME,
 for "username:password", and tries autologin if either exists.
 Dgamelaunch-specific DGLAUTH is checked before either of those.


ERROR CODES
===========

   1	Could not terminate stale processes
   2	Cannot chroot()
   3	Cannot chdir()
   4	Cannot setgroups()
   5	Cannot setgid()
   6	Cannot setuid()
   7	Caught HUP
   8	Cannot run as root: Config file has shed_user = "root"
   9	Cannot run as root: Config file has shed_user set to UID 0 user.
  10	Could not setup player
  11	Cannot run as root: Config file has shed_uid = 0
  12	Config file has an unrecognized token
  13	Config file: Negative value not accepted
  15	dgamelaunch called with -c (login shell command); exited
  20	No menu defined, or no banner found for menu
  60	Cannot create a new terminal, or no termcap files.
  61	Cannot openpty()
  62	Cannot open /dev/ptmx
  65	Cannot open master ptsname
  68	Cannot fcntl inprogress-lock
  70	Cannot write to inprogress-lock
  71	ftok() error for shm_key, no "dgamelaunch" file found?
  72	ftok() error for shm_sem_key, no "dgamelaunch" file found?
  73	shmget() error, cannot connect to shared memory
  74	smat() error, cannot attach to shared memory
  75	Nothing in shared memory?
  76	sem_init() error, could not initialize shared memory
  77	sem_wait() error
  78	sem_post() error
  95	Cannot fnctl lockfile
  96	sqlite3_open() failed when checking user existence: could not open login database
  97	sqlite3_open() failed in writefile()
  98	sqlite3_exec() failed in writefile()
  99	Could not open password file
 100	Username field too long in login file
 101	Email field too long in login file
 102	password field too long in login file
 103	env field too long in login file
 104	Cannot read config file
 105	Cannot read default config file
 106	Cannot open lockfile or password file
 107	fcntl failed on login database in writefile()
 108	sqlite3_exec() failed when checking user existence.
 109	Too many registered users. (see maxusers setting in config file)
 110	Login failed
 111	Two users trying to register at the same time
 112	Error changing password: cannot have ':' in password
 113	Error parsing configuration file
 114	exec-command fork failed
 115	could not read lock file in writefile()
 116	Too many registered users. (see maxusers setting in config file)
 117	wall error: no message to send
 118	wall error: no players
 119	User has a retarded terminal
 120	wall error: message too long
 121	wall error: "from" username is too short
 122	Error changing password: struct "me" does not exist
 123	chdir() failed in dgl commands.
 140	populate_games(): Cannot open inprogress-dir
 145	populate_games(): Inprogress-filename does not have ':' in it
 146	populate_games(): Inprogress-filename does not have ': in it (pt. 2)
 200	purge_stale_locks(): could not open inprogress-dir
 201	purge_stale_locks(): inprogress-file did not have ':' in it
 202	purge_stale_locks(): could not read inprogress-file
 203	purge_stale_locks(): could not get inprogress-file contents
```

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


# Dungeon Crawl Stone Soup
First, though, here's how to install Dungeon Crawl Stone Soup:
1) obtain the source, navigate to that directory
2) modify `<src>/crawl-ref/source/AppHdr.h`, and set `DGAMELAUNCH` (and tweak
    other settings which interest you)
3) `sudo make install prefix=/ DATADIR=/etc/dcss0341 SAVEDIR=/var/dcss0341 USE_DGAMELAUNCH=1`
4) navigate to the directory with these scripts (dgamelaunch source dir)
5) ...
```bash
./cpbin -b /bin /bin/crawl /opt/dgamelaunch
sudo cp /etc/dcss0341 /opt/dgamelaunch/etc/
sudo cp /var/dcss0341 /opt/dgamelaunch/var/
```
6) (clean up in `/bin/crawl`, `/etc/dcss0341`, `/var/dcss0341` if you want to; don't need those files anymore)
7) Modify `dgamelaunch.conf` to uncomment relevant lines
8) ...
```bash
sudo mkdir /opt/dgamelaunch/var/inprogress-dcss0341
sudo chown -R games:games /opt/dgamelaunch/var
```
9) Modify the main menu (it might help to `dcss-menu.patch` to it)

That's it!

# Resources
More resources for troubleshooting vis NetHack:
https://nethackwiki.com/wiki/User:Paxed/HowTo_setup_dgamelaunch
Vis dgamelaunch and crawl:
https://github.com/tarballqc/dcss-server-install/