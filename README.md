# remove-microsoft-autoupdate

**English** | [Português (Brasil)](README.pt-BR.md) | [Русский](README.ru.md)

A small shell script that removes Microsoft AutoUpdate (MAU) from macOS.

## Why

Install Word, Excel, Teams, Edge or any other Microsoft app on a Mac and
you get Microsoft AutoUpdate along with it, whether you asked for it or not.
It then:

- starts at login and keeps running in the background;
- pops up update windows at the worst possible moments;
- installs a helper tool that runs as root (`com.microsoft.autoupdate.helper`),
  which has had privilege escalation bugs in the past;
- downloads hundreds of megabytes of updates on its own schedule;
- comes back after you turn off automatic updates in its settings.

If you'd rather decide yourself when Office gets updated, or you just don't
want another background service from Microsoft, there is no uninstaller for
MAU. You have to find its files and delete them by hand. This script does
that for you.

## What it does

1. Lists every MAU file it knows about, with its size, so you can see what's
   there before anything is touched.
2. Asks for confirmation.
3. Unloads the launch daemon and agent and kills any running MAU process.
4. Deletes the app, the helper tool, the launchd plists, caches and
   preferences, both system-wide and for the current user.
5. Removes the MAU installer receipt (`pkgutil --forget`).

Paths it removes:

```
/Library/Application Support/Microsoft/MAU2.0
/Library/LaunchAgents/com.microsoft.update.agent.plist
/Library/LaunchDaemons/com.microsoft.autoupdate.helper.plist
/Library/PrivilegedHelperTools/com.microsoft.autoupdate.helper
/Library/Preferences/com.microsoft.autoupdate2.plist
/Library/Caches/com.microsoft.autoupdate.helper
~/Library/Preferences/com.microsoft.autoupdate2.plist
~/Library/Preferences/com.microsoft.autoupdate.fba.plist
~/Library/Caches/com.microsoft.autoupdate2
~/Library/Caches/com.microsoft.autoupdate.fba
~/Library/HTTPStorages/com.microsoft.autoupdate2
~/Library/Application Support/Microsoft AU Daemon
```

Office itself is not touched. Word, Excel, PowerPoint, Outlook and the rest
keep working as before; they just stop updating themselves.

## Usage

```sh
git clone https://github.com/wintermute93/remove-microsoft-autoupdate.git
cd remove-microsoft-autoupdate

./remove-microsoft-autoupdate --dry-run   # see what would be removed
sudo ./remove-microsoft-autoupdate
```

Options:

```
-n, --dry-run   list what would be removed, change nothing
-y, --yes       don't ask for confirmation
-h, --help      show this help
-V, --version   show version
```

To keep it around:

```sh
sudo install -m 755 remove-microsoft-autoupdate /usr/local/bin/
```

## Notes

- Installing or updating an Office app with Microsoft's `.pkg` installer
  brings MAU back. Run the script again afterwards.
- Without MAU, Office won't update itself. Get updates from the
  [Office for Mac update history](https://learn.microsoft.com/officeupdates/update-history-office-for-mac)
  page, which also links the standalone MAU installer if you ever want it back.
- Office installed from the Mac App Store is updated by the App Store and
  doesn't depend on MAU.

## License

MIT
