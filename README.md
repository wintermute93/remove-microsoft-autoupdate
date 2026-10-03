# remove-microsoft-autoupdate

Removes Microsoft AutoUpdate (MAU) from macOS.

MAU gets installed with Office, Teams, Edge and friends, runs in the
background, and keeps nagging you to update. Disabling it in the settings
doesn't always stick. This script gets rid of it.

## What it does

1. Lists every MAU file it knows about, with size, so you can see what's there.
2. Asks for confirmation.
3. Unloads the launch daemon/agent and kills any running MAU process.
4. Deletes the app, helper tool, launchd plists, caches and preferences
   (system-wide and for the current user).
5. Forgets the MAU installer receipt (`pkgutil --forget`).

Paths removed:

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

Office itself is not touched.

## Usage

```sh
git clone https://github.com/isaiasoliveira/remove-microsoft-autoupdate.git
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

To have it around permanently:

```sh
sudo install -m 755 remove-microsoft-autoupdate /usr/local/bin/
```

## Notes

- Installing or updating an Office app with Microsoft's `.pkg` installer
  brings MAU back. Just run the script again afterwards.
- Without MAU, Office won't update itself. Grab updates manually from the
  [Office for Mac update history](https://learn.microsoft.com/officeupdates/update-history-office-for-mac)
  page, which also links the standalone MAU installer if you want it back.
- Apps installed from the Mac App Store are updated by the App Store and
  aren't affected.

## License

MIT
