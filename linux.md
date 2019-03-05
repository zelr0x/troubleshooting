## Kali root vs Chromium or "cannot open display"
Kali Linux is often run as root. Offsec images don't include any other users and that's how it stays on a lot of machines.

However, Chromium can't be run as root because it runs in its own sandbox. It can be disabled, but why leave the system unprotected from  browser? I find it strange even if you restore VM from snapshot after each shutdown.

The proper solution is to create a separate user for chromium and run it with `sudo -u chromiumUserName`. Success! We found the solution. Now we type it, hit enter and...

No protocol specified
(chromium:2187): Gtk-WARNING **: 03:38:59.242: cannot open display: :1

xhost protects display from everyone who's not on its white list. And common solution found on the internet is to disable that protection altogether with `xhost +`. Another layer of defense wasted to run a single browser.

### Solution
To add one specific user to xhost's whitelist run 

`xhost +SI:localuser:chromiumUserName`

where `chromiumUserName` is the name of the user you created to run chromium. `sudo -u chromiumUserName` is working now, finally.
