## How User Kernel Space Starts

User Space starts roughly in this order

1. init
2. Essential low-level services, such udevd and syslogd
3. Network Configuration
4. Mid and High Level services (cron, printing, and so on)
5. Login Prompts, GUI, high level-applications such as web servers.

#### Introduction to Init

*init* is a user space program in linux like any other program. It can be found in */sbin*, with other system binaries.
Its main purpose is to start and stop essential service processes on system.

The *init* implementation used right now and which we'll be looking into is *systemd*.

#### Systemd

*systemd* handles regular boot proccess and services. Its goal oriented, ie. At top level you can define a goal, called
*unit* for some task and *systemd* will start/achieve that goal. A *unit* can contain some instructions, scripts or 
even other dependent *units*. *systemd* will start the dependent *units* firsts and then the main *unit*.

Below are most significant types of *units* listed:
* Service Units - Control the services daemons found on a Unix System
* Target Units - Control other units, by grouping them
* Socket Units - Represent incoming network connection request locations
* Mount Units - Represent the attachment of filesystems to the system

When you boot a system, the first unit which is called is *default.target* which then groups other dependencies, services
and mount points. Unit/Target Units usually form a graph of dependencies. You can view that dependency graph by using
`systemd-analyze dot` command. 

The *systemd* configuration files are spread across different paths, *system unit* directory - `/usr/lib/systemd/system`
and *system configuration* directory - `/etc/systemd/system`. We usually avoid making changes to system unit directory,
because the system maintains this, instead use the local config which is `/etc/`. You can check the current system
config path using command
```bash
systemctl -p UnitPath show
```
```
UnitPath=/etc/systemd/system.control /run/systemd/system.control /run/systemd/
transient /etc/systemd/system /run/systemd/system /run/systemd/generator /lib/
systemd/system /run/systemd/generator.late
```

The format for *unit* files is taken from *.ini* files used in earlier Microsoft systems or *.desktop* files. Here is an
example of *unit* file for *dbus-daemon.service*, desktop bus daemon
```
[Unit]
Description=D-Bus System Message Bus
Documentation=man:dbus-daemon(1)
Requires=dbus.socket
RefuseManualStart=yes
[Service]
ExecStart=/usr/bin/dbus-daemon --system --address=systemd: --nofork --nopidfile
--systemd-activation --syslog-only
ExecReload=/usr/bin/dbus-send --print-reply --system --type=method_call --dest=
org.freedesktop.DBus / org.freedesktop.DBus.ReloadConfig
```

There are two sections [Unit] and [Service]. [Unit] service contains something called **Requires**, which is `dbus.socket`.
What this means is that this unit requires `dbus.socket` unit to be activated first, is a dependency. In the [Service]
section it will include how to start, reload and execute the service.

You will also find variables inside [Service] section or the unit file.
```
[Service]
EnvironmentFile=/etc/sysconfig/sshd
ExecStartPre=/usr/sbin/sshd-keygen
ExecStart=/usr/sbin/sshd -D $OPTIONS $CRYPTO_POLICY
ExecReload=/bin/kill -HUP $MAINPID
```
The variable always starts with a *$*. They might all be variables but their origins can be different. *$OPTIONS* and
*$CRYPTO_POLICY* are used to pass variables when loading ssd.service. They are defined in *EnvironmentFile* path.
On the other hand *$MAINPID* is used to store the ID of tracked process. When the service gets called to reload it
uses this variabled to perform a hangup(HUP) call.


