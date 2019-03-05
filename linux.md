## Kali root vs Chromium or "cannot open display"
Kali Linux is often run as root. Offsec images don't include any other users and that's how it stays on a lot of machines.

However, Chromium can't be run as root because it runs in its own sandbox. The first thing I found on Google was to disable sandbox altogether, however it is a pretty bad idea. Sandboxes offer some protection right?)

The proper solution is to create a separate user for chromium and always run it with `sudo -u chromiumUser`, but when I tried it I got this:
```
No protocol specified
(chromium:2187): Gtk-WARNING **: 03:38:59.242: cannot open display: :1
```

xhost protects display from everyone who's not on its white list. And common solution found on the internet is to disable that protection altogether with `xhost +`. Another layer of defense wasted to run a single browser.

### Solution
To add one specific user to xhost's whitelist run 

`xhost +SI:localuser:chromiumUser`

where `chromiumUser` is the name of the user you created to run chromium. That will last until reboot. To make this change permanent there are multiple ways, but on my Kali VM I added `xhost +si:localuser:chromiumUser` to `/etc/X11/Xsession.d/35x11-common_xhost-local` so it looks like this now (the rest was there before):
```bash
if type xhost >/dev/null 2>&1; then
  xhost +si:localuser:$(id -un) || :
  xhost +si:localuser:chromiumUser
fi
```

To run chromium from dock favorites, prepend chromium binary location in `exec` parameter of `chromium.desktop` file with `sudo -u chromiumUser`. In my installation, the file is `/usr/share/applications/chromium.desktop` and the line looks like this:

`Exec=sudo -u zelr0x /usr/bin/chromium %U`
