[KDE/Gnome/Xfce/... does not boot up](#desktop-environment-does-not-bootup)

[GUI crash/exception/does not show up](#GUI-crash-exception-or-does-not-show-up):
* NameError: name 'unicode' is not defined
* ModuleNotFoundError: No module named 'grpc'
* Others...

[GUI not working across reboots](#GUI-not-working-across-reboots)

[The GUI doesn't change to dark style theme](#The-GUI-does-not-change-to-dark-style-theme)

[no icons on the GUI](#no-icons-on-the-GUI)

[GUI size problems on 4k monitors](#GUI-size-problems-on-4k-monitors)

[OpenSnitch icon doesn't show up on Gnome-Shell](#OpenSnitch-icon-does-not-show-up-on-gnome-shell)

[Kernel panic on >= 5.6.16 || kernel hardening incompatibilities](#kernel-panics)

[opensnitchd/daemon does not start](#opensnitchd-does-not-start):


***

### Desktop Environment does not boot up

If after installing OpenSnitch, or after changing the Default Action to `deny`, the Desktop Environment does not show up (after restart), try:

1. setting the `DefaultAction` back to `allow`
2. adding a rule to allow system apps.

In both cases the idea is to allow certain programs needed by KDE, Gnome, etc: dirmngr, xbrlapi, host, kdeinit5. [more info #402](https://github.com/evilsocket/opensnitch/issues/402):

Save it to `/etc/opensnitchd/rules/000-allow-system-cmds.json`
```
{
  "created": "2021-04-26T09:58:03.704090244+02:00",
  "updated": "2021-04-26T09:58:03.704216578+02:00",
  "name": "000-allow-system-cmds",
  "enabled": true,
  "precedence": true,
  "action": "allow",
  "duration": "always",
  "operator": {
    "type": "regexp",
    "operand": "process.path",
    "sensitive": false,
    "data": "^(/usr/bin/host|/usr/bin/xbrlapi|/usr/bin/dirmngr)",
    "list": []
  }
}
```

You can also allow all traffic to localhost (save it to `/etc/opensnitchd/rules/000-allow-localhost.json`):
```
{
  "created": "2021-04-26T09:58:03.704090244+02:00",
  "updated": "2021-04-26T09:58:03.704216578+02:00",
  "name": "000-allow-localhost",
  "enabled": true,
  "precedence": true,
  "action": "allow",
  "duration": "always",
  "operator": {
    "type": "network",
    "operand": "dest.network",
    "sensitive": false,
    "data": "127.0.0.0/8",
    "list": []
  }
}
```

***

### GUI crash/exception or does not show up

If you have installed it by double clicking on the pkgs, using a graphical installer, try to install it from command line:

> $ sudo dpkg -i `*opensnitch*deb`; sudo apt -f install

See [issue #25](https://github.com/gustavo-iniguez-goya/opensnitch/issues/25), [issue #16](https://github.com/gustavo-iniguez-goya/opensnitch/issues/16) and [issue #32](https://github.com/gustavo-iniguez-goya/opensnitch/issues/32) for additional information.


***

You have to install `unicode_slugify` and `grpcio-tools`, usually not available in many distros. You can install them using pip:

```
pip3 install unicode_slugify
pip3 install grpcio-tools
```

***

Check that you don't have a previous installation of opensnitch GUI in _/usr/lib/python3*/*/opensnitch/_ or _/usr/local/lib/python3*/*/opensnitch/_

If you have a previous installation remove it, and install the GUI again (you may have an installation of the original repo). 

If it doesn't work, report it describing the steps to reproduce it, and the exception or log. For example:
```
Traceback (most recent call last):
  File "/usr/lib/python3.8/site-packages/opensnitch/dialogs/prompt.py", line 362, in _on_apply_clicked
    self._rule.name = slugify("%s %s %s" % (self._rule.action, self._rule.operator.type, self._rule.operator.data))
  File "/usr/lib/python3.8/site-packages/slugify.py", line 24, in slugify
    unicode(
NameError: name 'unicode' is not defined
```

--

For ArchLinux/Manjaro users this worked:
> installed was from AUR python-unicode-slugify-git r43.b696c37-1

> removed it and installed python-unicode-slugify 0.1.3-1.


***

### Opensnicth GUI not working across reboots

If after installing OpenSnitch and reboot, the GUI does not show up upon login to your Desktop Environment, be sure that the following path exist in your $HOME:

`ls ~/.config/autostart/opensnitch_ui.desktop`

If it doesn't exist, create it:
```
$ mkdir -p ~/.config/autostart/
$ ln -s /usr/share/applications/opensnitch_ui.desktop ~/.config/autostart/
```

If you have installed the GUI from the repositories of a distribution, tell the maintainer of the package to create that symbolic link after installation.

see issue [#434](https://github.com/evilsocket/opensnitch/issues/434#issuecomment-859968103) for more information.

***

### The GUI does not change to dark style theme

It's usually a problem of the Desktop Environment. You can try to configure the theme by using `qt5ct`, or executing the following commands: 
```
sudo apt-get install -y qt5-style-plugins
sudo cat << EOF | sudo tee  /etc/environment
QT_QPA_PLATFORMTHEME=gtk2
EOF
```

More info: [#303](https://github.com/evilsocket/opensnitch/issues/303)


***

### No icons on the GUI

Be sure that you have properly set the icon theme of your Window Manager. [More information](https://github.com/gustavo-iniguez-goya/opensnitch/issues/53#issuecomment-671419790)


***

### GUI size problems on 4k monitors

Some users have reported issues displaying the GUI on 4k monitors. See [#43](https://github.com/gustavo-iniguez-goya/opensnitch/issues/43) for more information.

Setting these variables may help:

```
export QT_AUTO_SCREEN_SCALE_FACTOR=0
export QT_SCREEN_SCALE_FACTORS=1 (or 1.25, 1.5, 2, ...)
```

In case of multiple displays:
`export "QT_SCREEN_SCALE_FACTORS=1;1"`


***

### OpenSnitch icon does not show up on Gnome-Shell

On Gnome-Shell >= 3.16, systray icons have been removed. You have to install the extension [gnome-shell-extension-appindicator](https://extensions.gnome.org/extension/615/appindicator-support/) to get them back.

1. Download latest version - https://github.com/ubuntu/gnome-shell-extension-appindicator/releases
2. Install it with your regular user: `gnome-extensions install gnome-shell-extension-appindicator-v33.zip`

See this comment/issue for more information: [#44](https://github.com/gustavo-iniguez-goya/opensnitch/issues/44#issuecomment-654373737)


***

### opensnitchd does not start

For all these options, 

* `Error while creating queue #0: Error binding to queue: operation not permitted.`
* `Error while enabling probe descriptor for opensnitch_exec_probe: write /sys/kernel/debug/tracing/kprobe_events: no such file or directory`
* `Error while creating queue #0: Error binding to queue: operation not permitted.`
* `Error opening Queue handle: protocol not supported`
* `Could not open socket to kernel: Address family not supported by protocol (IPv6)`
* `Error while creating queue #0: Error unbinding existing q handler from AF_INET protocol` see [#323](https://github.com/evilsocket/opensnitch/issues/323) and [#204](https://github.com/evilsocket/opensnitch/issues/204#issuecomment-802932344). Issue not solved, if you can provide more information open a new issue please.

be sure that you have NFQUEUE support in the kernel (=y or =m):
```
$ grep -E "(NFT|NETLINK|NFQUEUE) /boot/config-$(uname -r)"
CONFIG_NFT_QUEUE=y
CONFIG_NETFILTER_NETLINK_QUEUE=y
CONFIG_NETFILTER_XT_TARGET_NFQUEUE=y
```
and that the needed modules are loaded:
```
$ lsmod | grep -i nfqueue
xt_NFQUEUE             16384  4
x_tables               53248  20 xt_conntrack,nft_compat,xt_LOG,xt_multiport,xt_tcpudp,xt_addrtype,xt_CHECKSUM,xt_recent,xt_nat,ip6t_rt,xt_set,ip6_tables,ipt_REJECT,ip_tables,xt_limit,xt_hl,xt_MASQUERADE,ip6t_REJECT,xt_NFQUEUE,xt_mark
```


***

### Kernel panics

Some users have reported kernel panics with kernel 5.6.16 ([#297](https://github.com/evilsocket/opensnitch/issues/297)) and other kernels([#41](https://github.com/gustavo-iniguez-goya/opensnitch/issues/41)). **deathtrip** found that the culprit in his/her case was a configuration of the Arch's [linux-hardened](https://www.archlinux.org/packages/extra/x86_64/linux-hardened/) kernel command line option. 

Removing the following options from the kernel booting parameters solved the issue:

`slab_nomerge, slub_debug=FZP and page_alloc.shuffle=1`

On Debian with kernel 5.7.0, remove `slub_debug=FZP` if you have it configured and try again.

**Note:** This was caused by [a bug in the libnetfilter_queue library](https://bugzilla.netfilter.org/show_bug.cgi?id=1440).