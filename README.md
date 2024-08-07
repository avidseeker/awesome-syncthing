<p align="center">A curated list of offline-first apps</p>
<p align="center"><a href="https://awesome.re" target="_blank"><img src="https://awesome.re/badge-flat.svg"></a></p>

# Note taking
- [Joplin](https://f-droid.org/en/packages/net.cozic.joplin/)
- [Logseq](https://github.com/logseq/logseq)
- [MOrg](https://github.com/brvier/MOrg) - A future proof opinionated software
- [Markor](https://f-droid.org/en/packages/com.appmindlab.nano)
- [Material Files editor](https://f-droid.org/en/packages/me.zhanghai.android.files/)
- [MobileOrg](https://f-droid.org/packages/com.matburt.mobileorg/)
- [Obsidian](https://obsidian.md/) - proprietary
- [Orgzly](https://f-droid.org/packages/com.orgzly/)
- [neutriNote](https://f-droid.org/en/packages/com.appmindlab.nano)

# Task Management
- [Lift](https://f-droid.org/en/packages/ca.momi.lift)
- [Personal Log](https://f-droid.org/en/packages/com.tiwa.pl)
- [Shopping list](https://f-droid.org/en/packages/com.woefe.shoppinglist/)
- [Simpletask](https://f-droid.org/packages/nl.mpcjanssen.simpletask/): manages todo.txt file format.
- [Tasks](https://f-droid.org/en/packages/org.tasks/)

## [DecSync](https://github.com/39aldo39/DecSync)
- [DecSync CC](https://f-droid.org/en/packages/org.decsync.cc): contacts, calendar and tasks. Doesn't support two-way syncing in `.ics` and `.vcf` formats.
- [Flym DecSync](https://f-droid.org/en/packages/org.decsync.flym): RSS, atom feed syncing
- [spaRSS DecSync](https://f-droid.org/en/packages/org.decsync.sparss.floss): RSS, atom feed syncing

# Export-based integrations
These require manual export/import to be synced.
- [Antennapod](https://f-droid.org/en/packages/de.danoeh.antennapod) - `.opml` podcast feed
- [Feeder](https://f-droid.org/en/packages/com.nononsenseapps.feeder) - `.opml` RSS feed
- [ListMyApps](https://f-droid.org/en/packages/de.onyxbits.listmyapps) - `.txt` app list
- [Loop Habit Tracker](https://f-droid.org/en/packages/org.isoron.uhabits) - `.db` database file
- [Neo Backup](https://f-droid.org/en/packages/com.machiav3lli.backup/) - syncs app data. Requires root.

# Desktop integrations
- [KeePassDX](https://github.com/Kunzisoft/KeePassDX) - `.kdbx` password manager. Enable by
selecting a synced database from "Open existing database". Read-only mode can be enabled to
avoid sync conflicts from Settings > App settings > Write-protected.

## [AnkiDroid](https://f-droid.org/en/packages/com.ichi2.anki)
Syncs `.apkg` flash cards. [Known limitation](https://forums.ankiweb.net/t/30356)
for v2.15: Database needs to be downgraded on exit if viewed from PC. Current
workaround is to install the [Beta version](https://github.com/ankidroid/Anki-Android/releases)
and check "Use the new backend" from Settings > Advanced > Experimental. Note:
Backup your data before attempting this. If Syncthing is running while Anki is
open, you risk database corruption.

Setup:
1. Locate AnkiDroid path. Defaults to `/storage/emulated/0/AnkiDroid`.
Can be changed from Settings > Advanced > AnkiDroid directory.
2. Sync with PC folder: `~/AnkiDecks` using Syncthing.
3. Locate Anki settings directory on desktop. Defaults to `~/.local/share/Anki2`.
4. Create a profile symlink to `~/AnkiDecks` inside the settings directory:
```bash
ln -s ~/AnkiDecks ~/.local/share/Anki2/main
```
5. Select the profile from `Ctrl-Shift-P`
6. If you're using an older version of AnkiDroid, you have to downgrade every
time you exit Anki from desktop, by `Ctrl-Shift-P` then "Downgrade and exit".

## [ICSx5](https://f-droid.org/packages/at.bitfire.icsdroid/)
Syncs `.ics` calendar format. Can be used in one-way sync with [calcurse](https://github.com/lfos/calcurse) by
adding the following post-save hook in `~/.config/calcurse/hooks/post-save`:
```bash
#!/bin/sh
cd "$SYNC_CAL_DIR" || exit 1
calcurse -c ./apts -x > main.ics
```
and running `calcurse` with `-c "$SYNC_CAL_DIR/apts"`. Where `$SYNC_CAL_DIR` is the path
of Syncthing directory.

From Android, you can either manually refresh the calendar or set up autosyncing interval.
ICSx5 can be then used with a calendar app like [Etar](https://f-droid.org/packages/ws.xsoh.etar/).

# Email
In your `~/.profile`
```sh
export \
    MAILRC="$XDG_CONFIG_HOME"/mail/mailrc \
    MAIL="$HOME"/Sync/mail/inbox.mbox
```

In your `~/.config/.mailrc`:
```conf
set folder=mail
set expandaddr
set stealthmua
set from=mail@example.com
set folder=~/Sync/mail/ inbox=+inbox.mbox
```

## Usage
Simply edit `~/Sync/mail/inbox.mbox` in your editor. Here's a snippet you can
add if you're using UltiSnips in Vim:

```sh
snippet main "Mail header"
From M `date "+%a %b %d %H:%M:%S %Y"`
Status: U
$0
endsnippet
```
This format is necessary for correct parsing of `mail` command, which we will
use for notifications.

And add this to `vimrc` to associate files ending with .mbox with mbox filetype:
```vimrc
autocmd BufRead,BufNewFile *.mbox setfiletype mbox
```

## Notifications
As simple as:
```sh
mail -H | grep -q '^ U' && notify-send "New mail"
```
Just put it in a script and add it as a cronjob.
