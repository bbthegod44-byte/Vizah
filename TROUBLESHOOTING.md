# 🆘 Troubleshooting

Don't worry, nearly every Vizah problem is one of the ones below. Find your symptom, try the fix, and you're back to the music.

**First, two reflexes that solve a surprising amount:**

```bash
bash ~/vizah/stop.sh && bash ~/vizah/launch.sh     # turn it off and on again
tail -n 40 ~/vizah/server.log                       # what did the server say?
```

Jump to: [Install](#-install--startup) · [App & server](#-the-app-and-the-server) · [Last.fm & visualizer](#-lastfm--visualizer) · [Playback & adb](#-playback--adb) · [YouTube Music](#-youtube-music) · [Spotify](#-spotify) · [Lyrics](#-lyrics) · [Accounts & friends](#-accounts--friends) · [Funkin'](#-friday-night-funkin)

---

## 📦 Install & startup

**`git: command not found`**
Run `pkg install git`, then try the clone again.

**`pkg` is slow, or says it can't reach a mirror**
Run `termux-change-repo`, pick a mirror close to you, then `pkg update`.

**Setup says `python3 is required`**
Run `pkg install python`, then `bash ~/vizah/setup_pwa.sh` again.

**`pip install ytmusicapi` failed**
Vizah still works. Only YouTube Music playlists are affected. Try again later with:
```bash
pip install --upgrade ytmusicapi || pip install --upgrade --break-system-packages ytmusicapi
```

**Scripts say "Permission denied"**
```bash
chmod +x ~/vizah/*.sh
```

**I cloned into `/sdcard` or `Downloads` and things act weird**
Android's shared storage doesn't support private files or script permissions. Move it home:
```bash
mv /sdcard/vizah ~/vizah     # or re-clone into ~/vizah
bash ~/vizah/setup_pwa.sh
```

**Termux from the Play Store is broken / out of date**
Uninstall it and install **Termux** (and **Termux:API**, plus **Termux:Widget** / **Termux:Boot** if you want them) from **F-Droid** or the official GitHub releases. Mixing sources causes problems, so install them all from the same place.

---

## 🖥️ The app and the server

**"Vizah server isn't running"**
That friendly page means Chrome can't reach the server. In Termux: `bash ~/vizah/launch.sh`.

**`launch.sh` says "Server didn't come up"**
It prints the last log lines. Common causes:
- **Port already in use.** Pick another: `PORT=8001 bash ~/vizah/launch.sh`. (If you do, use `:8001` in every address, and update the Spotify redirect URI.)
- **A Python error.** Read `~/vizah/server.log` and look at the last lines.

**It works, then the server dies when I lock the phone**
Android is putting Termux to sleep.
1. Make sure **Termux:API** is installed (it provides `termux-wake-lock`, which `launch.sh` uses).
2. **Settings → Apps → Termux → Battery** and set it to **Unrestricted** (or "Don't optimize").
3. Pull down Termux's notification and make sure the **wake lock** is held.

**Chrome doesn't offer "Install app"**
- Open Vizah at exactly `http://127.0.0.1:8000/` (not `localhost`, not your Wi-Fi IP). That address counts as secure, which installing requires.
- Use **Chrome** (or Brave/Edge). Some browsers don't support installing.
- Already installed? Then Chrome hides the button. Check your home screen and app drawer.
- Chrome menu **⋮ → Add to Home screen → Install** also works.

**The installed app shows an old version after an update**
The app caches only its icons and the offline page, never your data. A hard refresh fixes it: open the app, pull to refresh, or clear Chrome's site data for `127.0.0.1`.

**Nothing opens automatically**
`launch.sh` prints the address when it can't open a browser. Open **`http://127.0.0.1:8000/`** yourself.

---

## 🌈 Last.fm & visualizer

**"Waiting for your music..." forever**
Vizah can only see what Last.fm sees. Check that:
1. You entered the right **username** (not your email) and a valid **API key**.
2. Your player is **scrobbling**. Play a song, then open your Last.fm profile in a browser. It should say *Scrobbling now*.
3. **YouTube Music doesn't scrobble by itself.** Use a scrobbler app (the official Last.fm app or a third-party one) and allow it to read notifications.
4. Your Last.fm privacy settings aren't hiding your recent tracks.

**Login says my key is wrong**
Re-copy it from <https://www.last.fm/api/accounts>. Make sure there's no trailing space.

**The song info shows up a few seconds late**
Last.fm only reveals a track a few seconds after it starts. Vizah compensates in the lyrics, but the title itself arrives when Last.fm announces it.

**No cover art**
Some tracks have none on Last.fm, so Vizah shows a placeholder instead. That's normal.

---

## ⏯️ Playback & adb

**The play/pause/next buttons do nothing**
Almost always adb. In the Vizah phone step, the status should be green. If not:
1. Is **Wireless debugging** still switched on? (It switches itself off when Wi-Fi drops.)
2. Did the **port change**? It does after toggling Wireless debugging or rebooting. Read the new one under the switch and connect again.
3. Check from Termux: `adb devices`. You want to see a line ending in `device`.

| `adb devices` says | Meaning | Fix |
|---|---|---|
| *(empty list)* | Nothing connected | Connect again with the current address |
| `offline` | Stale connection | `adb disconnect`, then connect again |
| `unauthorized` | You haven't tapped Allow | Look for the *Allow wireless debugging?* popup on the phone |
| `device` | 🎉 | You're good |

**Pairing keeps failing**
- The pairing popup closes when you leave Settings. Use **split-screen** or a floating window.
- Pairing **address and port** (in the popup) are *different* from the connection address on the main Wireless debugging screen. Don't mix them up.
- The 6-digit code **expires** with the popup. Get a fresh one.
- You must be on **Wi-Fi**. Mobile data won't do.

**`adb: command not found`**
```bash
pkg install android-tools
```

**It controls the wrong app**
Media keys go to whichever app most recently played audio. Start something in YouTube Music or Spotify first, then press the buttons.

---

## 📚 YouTube Music

**"Connect YouTube Music" doesn't finish after clicking the helper**
- You must be **signed in to Vizah in that same browser**, and signed in to **music.youtube.com**.
- You must be able to reach the phone from the computer (same Wi-Fi, correct `IP:8000`). The helper was baked with the address you downloaded it from. If you moved the server, download the helper again.
- Firefox temporary add-ons vanish on restart. Load it again.

**Playlists list is empty or says login expired**
YouTube cookies expire or get revoked (for example when you change your Google password or sign out everywhere). Connect again with the helper. Disconnect first if needed.

**Wrong Google account's playlists**
You may have several Google accounts in one browser. Use the manual route with `--authuser`:
```bash
python3 ~/vizah/ytm_setup.py cookie.txt --authuser 1
```
(`0` is the first account in the browser, `1` the second, and so on.)

**Playlists won't load at all**
Make sure `ytmusicapi` is installed: `pip install --upgrade ytmusicapi`.

**Pasting my cookie into the terminal gets cut off**
Terminals truncate very long lines. Save it to a file and run `python3 ytm_setup.py <file>`.

---

## 🟢 Spotify

**Spotify says `INVALID_CLIENT: Invalid redirect URI`**
The Redirect URI in your Spotify app must match the one Vizah shows **exactly**: same `http`, same `127.0.0.1`, same port, same `/spotify/callback`, no trailing slash differences. Save the Spotify app settings after adding it.

**It won't let me connect from my Wi-Fi address**
Spotify only accepts `https` or the loopback address. Open Vizah at `http://127.0.0.1:8000/` **on the phone** and connect from there.

**Login works, but the play button doesn't**
- Playback control needs **Spotify Premium**.
- Open the Spotify app and **start any song once** so it's an active device.
- Make sure the **Playback** card has *Spotify* selected, if you also have YouTube Music connected.

**"User not registered in the Developer Dashboard"**
Your Spotify app is in development mode. On the dashboard open your app → **User Management** and add the Spotify account you're logging in with.

**Spotify playlists are missing songs / empty**
Some Spotify-made playlists aren't available to developer apps. Your own and followed playlists, plus Liked Songs, work.

---

## 📝 Lyrics

**No lyrics / wrong lyrics**
Turn on **Use more sources** in the lyrics settings. It also searches NetEase, QQ Music and KuGou (no keys needed) and finds time-synced lyrics for many more songs. Vizah drops results whose length doesn't match the song, so you won't get lyrics from the wrong version.

**Lyrics are out of time**
If a song has no synced lyrics anywhere, Vizah spreads the lines evenly across the song. That's a best guess, so some songs will drift.

---

## 👤 Accounts & friends

**Password rejected when signing up**
Passwords need **at least 8 characters**.

**"Too many attempts - wait a few minutes"**
That's a brute-force guard. Wait a few minutes and try again.

**New people can't sign up**
The server may have been started with `VIZAH_SIGNUP=0`. Restart without it (or with `VIZAH_SIGNUP=1`).

**A friend can't open my Vizah**
They need to be on the **same Wi-Fi** and use `http://<your-phone-ip>:8000`. Some guest or hotspot networks isolate devices from each other, and then it can't work.

**I forgot my password**
There's no email reset, since everything is local. If you control the server, you can wipe everything with `python3 ~/vizah/clear_users.py` (stop the server first) and sign up fresh. It makes `.bak` copies first.

---

## 🕹️ Friday Night Funkin'

**The game is stuck loading**
Files are missing. A complete install has **2037** files. Free up space and run `bash ~/vizah/install_fnf.sh` again.

**"git is missing"**
`pkg install git`, then run the script again.

**Not enough space**
You need about **2 GB** free during the download (about 850 MB remains afterwards).

---

## Still stuck?

[Open an issue](../../issues) and include:

- What you were doing and what you expected
- The last ~40 lines of `~/vizah/server.log` (`tail -n 40 ~/vizah/server.log`)
- Your Android version and Termux version

⚠️ **Before pasting anything, delete cookies, API keys, tokens and passwords.** Never post `creds.txt`.
