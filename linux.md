## Kali root vs Chromium or "cannot open display"
Kali Linux is often run as root. Offsec images don't include any other users and that's how it stays on a lot of machines.

However, Chromium can't be run as root because it runs in its own sandbox. The first thing I found on Google was to disable sandbox altogether, however it is a pretty bad idea. Sandboxes offer some protection right?

### Solution
#### Step 1
The proper solution is to create a separate user for Chromium - `chromiumUser` for the sake of example:
1. `useradd -G sudo chromiumUser`
2. `passwd chromiumUser`
3. input your secure password (different from root password obviously) and confirm it. You can forget this password right away since root is not asked for credentials of any other user.

After that Chromium can be run with `sudo -u chromiumUser`. I ran it and got this:
```
No protocol specified
(chromium:2187): Gtk-WARNING **: 03:38:59.242: cannot open display: :1
```

The reason for such behaviour is that `xhost` protects display from everyone who's not on its white list. And the first thing I found on Google was `xhost +` which disables this filter. Another layer of defense wasted to run a single browser.

#### Step 2
To add one specific user to xhost's whitelist run `xhost +SI:localuser:chromiumUser`. That will last until reboot. To make this change permanent, this line needs to be added to some config file that will be ran before you run the browser (most likely on boot). On my Kali VM I added a line `xhost +si:localuser:chromiumUser` to `/etc/X11/Xsession.d/35x11-common_xhost-local` which made it look like this:
```bash
if type xhost >/dev/null 2>&1; then
  xhost +si:localuser:$(id -un) || :
  xhost +si:localuser:chromiumUser
fi
```

To run chromium from dock favorites, prepend chromium binary location in `exec` parameter of `chromium.desktop` file with `sudo -u chromiumUser`. In my installation, the file is `/usr/share/applications/chromium.desktop` and the line looks like this:
`Exec=sudo -u chromiumUser /usr/bin/chromium %U`
