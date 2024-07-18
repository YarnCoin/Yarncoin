Sample init scripts and service configuration for yarncoind
==========================================================

Sample scripts and configuration files for systemd, Upstart and OpenRC
can be found in the contrib/init folder.

    contrib/init/yarncoind.service:    systemd service unit configuration
    contrib/init/yarncoind.openrc:     OpenRC compatible SysV style init script
    contrib/init/yarncoind.openrcconf: OpenRC conf.d file
    contrib/init/yarncoind.conf:       Upstart service configuration file
    contrib/init/yarncoind.init:       CentOS compatible SysV style init script

Service User
---------------------------------

All three Linux startup configurations assume the existence of a "yarncoin" user
and group.  They must be created before attempting to use these scripts.
The macOS configuration assumes yarncoind will be set up for the current user.

Configuration
---------------------------------

At a bare minimum, yarncoind requires that the rpcpassword setting be set
when running as a daemon.  If the configuration file does not exist or this
setting is not set, yarncoind will shutdown promptly after startup.

This password does not have to be remembered or typed as it is mostly used
as a fixed token that yarncoind and client programs read from the configuration
file, however it is recommended that a strong and secure password be used
as this password is security critical to securing the wallet should the
wallet be enabled.

If yarncoind is run with the "-server" flag (set by default), and no rpcpassword is set,
it will use a special cookie file for authentication. The cookie is generated with random
content when the daemon starts, and deleted when it exits. Read access to this file
controls who can access it through RPC.

By default the cookie is stored in the data directory, but it's location can be overridden
with the option '-rpccookiefile'.

This allows for running yarncoind without having to do any manual configuration.

`conf`, `pid`, and `wallet` accept relative paths which are interpreted as
relative to the data directory. `wallet` *only* supports relative paths.

For an example configuration file that describes the configuration settings,
see contrib/debian/examples/yarncoin.conf.

Paths
---------------------------------

### Linux

All three configurations assume several paths that might need to be adjusted.

Binary:              /usr/bin/yarncoind
Configuration file:  /etc/yarncoin/yarncoin.conf
Data directory:      /var/lib/yarncoind
PID file:            `/var/run/yarncoind/yarncoind.pid` (OpenRC and Upstart) or `/run/yarncoind/yarncoind.pid` (systemd)
Lock file:           `/var/lock/subsys/yarncoind` (CentOS)

The configuration file, PID directory (if applicable) and data directory
should all be owned by the yarncoin user and group.  It is advised for security
reasons to make the configuration file and data directory only readable by the
yarncoin user and group.  Access to yarncoin-cli and other yarncoind rpc clients
can then be controlled by group membership.

NOTE: When using the systemd .service file, the creation of the aforementioned
directories and the setting of their permissions is automatically handled by
systemd. Directories are given a permission of 710, giving the yarncoin group
access to files under it _if_ the files themselves give permission to the
yarncoin group to do so (e.g. when `-sysperms` is specified). This does not allow
for the listing of files under the directory.

NOTE: It is not currently possible to override `datadir` in
`/etc/yarncoin/yarncoin.conf` with the current systemd, OpenRC, and Upstart init
files out-of-the-box. This is because the command line options specified in the
init files take precedence over the configurations in
`/etc/yarncoin/yarncoin.conf`. However, some init systems have their own
configuration mechanisms that would allow for overriding the command line
options specified in the init files (e.g. setting `BITCOIND_DATADIR` for
OpenRC).

### macOS

Binary:              `/usr/local/bin/yarncoind`
Configuration file:  `~/Library/Application Support/YarnCoin/yarncoin.conf`
Data directory:      `~/Library/Application Support/YarnCoin`
Lock file:           `~/Library/Application Support/YarnCoin/.lock`

Installing Service Configuration
-----------------------------------

### systemd

Installing this .service file consists of just copying it to
/usr/lib/systemd/system directory, followed by the command
`systemctl daemon-reload` in order to update running systemd configuration.

To test, run `systemctl start yarncoind` and to enable for system startup run
`systemctl enable yarncoind`

NOTE: When installing for systemd in Debian/Ubuntu the .service file needs to be copied to the /lib/systemd/system directory instead.

### OpenRC

Rename yarncoind.openrc to yarncoind and drop it in /etc/init.d.  Double
check ownership and permissions and make it executable.  Test it with
`/etc/init.d/yarncoind start` and configure it to run on startup with
`rc-update add yarncoind`

### Upstart (for Debian/Ubuntu based distributions)

Upstart is the default init system for Debian/Ubuntu versions older than 15.04. If you are using version 15.04 or newer and haven't manually configured upstart you should follow the systemd instructions instead.

Drop yarncoind.conf in /etc/init.  Test by running `service yarncoind start`
it will automatically start on reboot.

NOTE: This script is incompatible with CentOS 5 and Amazon Linux 2014 as they
use old versions of Upstart and do not supply the start-stop-daemon utility.

### CentOS

Copy yarncoind.init to /etc/init.d/yarncoind. Test by running `service yarncoind start`.

Using this script, you can adjust the path and flags to the yarncoind program by
setting the YarnCoinD and FLAGS environment variables in the file
/etc/sysconfig/yarncoind. You can also use the DAEMONOPTS environment variable here.

### macOS

Copy org.yarncoin.yarncoind.plist into ~/Library/LaunchAgents. Load the launch agent by
running `launchctl load ~/Library/LaunchAgents/org.yarncoin.yarncoind.plist`.

This Launch Agent will cause yarncoind to start whenever the user logs in.

NOTE: This approach is intended for those wanting to run yarncoind as the current user.
You will need to modify org.yarncoin.yarncoind.plist if you intend to use it as a
Launch Daemon with a dedicated yarncoin user.

Auto-respawn
-----------------------------------

Auto respawning is currently only configured for Upstart and systemd.
Reasonable defaults have been chosen but YMMV.
