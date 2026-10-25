<div align="center">

<img src="pwa/icon-512.png" width="140" alt="Vizah icon" />

# Vizah

**Your Last.fm scrobbles, but make it a light show.**
A visualizer, synced lyrics, playlists, friends and a pocket-sized music hub that runs *on your own phone*.

`Last.fm` · `YouTube Music` · `Spotify` · `Synced lyrics` · `Friends & DMs` · `Installable app (PWA)`

</div>

---

## 🎧 What is this thing?

Vizah is a self-hosted music app. A tiny Python server runs on your Android phone (inside [Termux](https://termux.dev)), and you open it in Chrome like a website, or install it to your home screen like a real app.

It watches what you're listening to on **Last.fm** and turns it into a living, breathing visualizer. Around that it has grown a whole little ecosystem:

| | |
|---|---|
| 🌈 **Visualizer** | Reacts to whatever's playing, with themes you can recolor to your taste |
| 📝 **Synced lyrics** | Lines that follow the song (from LRCLIB), plus an optional **Use more sources** switch for NetEase, QQ Music and KuGou |
| ▶️ **Playback control** | Play / pause / skip / pick a playlist through YouTube Music **or** Spotify |
| 📚 **Playlists** | YouTube Music and Spotify playlists side by side. Tap one to play all, or pick a song |
| ➕ **Add to playlist** | Save the current song to your YouTube or Spotify playlists |
| 📺 **YouTube section** | Search, your subscriptions, and a custom queue of links that plays one after another |
| 🔮 **Up next** | A big, clear "what's actually next" notification |
| 👯 **Friends** | Friend list, public profiles, see what friends are listening to, DMs, and "send me what you're playing" |
| 🔐 **Real accounts** | Sign up with a username and password; each account keeps its own logins |
| 🕹️ **Secret side quest** | An optional Friday Night Funkin' arrow in the YouTube section (see [below](#-optional-side-quest-friday-night-funkin)) |

> **Heads up:** Vizah doesn't stream audio itself. It *controls* the music app already on your phone (YouTube Music or Spotify) and *shows* you the magic. More on how in [How it works](#-how-it-works-in-60-seconds).

---

## 🧰 What you'll need

Grab these before you start. Most of it is free and takes five minutes.

- [ ] **An Android phone** (Android 11 or newer recommended, because of *Wireless debugging*)
- [ ] **[Termux](https://f-droid.org/packages/com.termux/)**, installed from **F-Droid** or GitHub. The Play Store version is outdated and will give you trouble
- [ ] **[Termux:API](https://f-droid.org/packages/com.termux.api/)** (same source as Termux) so the server can stay awake with the screen off
- [ ] **Chrome** (or another Chromium browser) on the phone. Chrome gives the best "Install app" experience
- [ ] **A [Last.fm](https://www.last.fm) account** and a free API key (guide in [docs/CONNECTIONS.md](docs/CONNECTIONS.md#1-lastfm-the-heart-of-the-visualizer))
- [ ] **YouTube Music and/or Spotify** installed on the phone (and Spotify Premium if you want Spotify to be controllable; that's Spotify's rule, not ours)
- [ ] About **100 MB** of free space (add ~2 GB if you want the Funkin' side quest)

Optional extras: **Termux:Widget** (home-screen start button) and **Termux:Boot** (start Vizah automatically when the phone boots).

---

## 🚀 Quick start (about 5 minutes)

Open **Termux** and paste these one at a time.

**1. Get git and download Vizah**

```bash
pkg update -y && pkg install -y git
git clone https://github.com/YOUR_GITHUB_USERNAME/vizah.git ~/vizah
```

> Clone it into your Termux **home folder** (`~/vizah`), not into phone storage like `/sdcard`. Shared storage can't hold private files or run scripts reliably.

**2. Run the one-time setup**

```bash
bash ~/vizah/setup_pwa.sh
```

This installs Python, `adb` and Termux:API, adds the optional `ytmusicapi` package, creates the Termux shortcuts, **starts the server** and **opens Vizah in Chrome**. It is safe to run again any time.

**3. Create your account**

Vizah opens to a sign-up screen. Pick a username and a password (**8+ characters**), and you're in.

**4. Follow the first-run steps**

Right after sign-up Vizah walks you through a short setup:

1. **Connect your phone over adb.** This is how Vizah presses play/pause/next for you. Step-by-step in [docs/CONNECTIONS.md](docs/CONNECTIONS.md#4-adb-wireless-debugging-so-vizah-can-press-the-buttons).
2. **Add your playlists** *(optional, skippable)*. Connect YouTube Music now, or any time later.

**5. Connect Last.fm** in **Profile & connections**: tap **Connect Last.fm**, enter your Last.fm username and API key, tap **Log in**, and the visualizer wakes up. 🎉

**6. Make it an app.** In Chrome tap **⋮ → Install app** (or tap the **Install** bar Vizah shows). Now it lives on your home screen.

That's it. Play something and watch.

---

## 🗺️ The first-launch tour

```
 Sign up ─► Phone (adb) check ─► Playlists (optional) ─► Profile & connections ─► Last.fm ─► 🎶
   │                                                          │
   └─ you can come back to any step later ─────────────────────────┴─► YouTube Music · Spotify · API key
```

Everything that needs a login lives in the **Profile & connections** panel, and each Vizah account has its **own** Last.fm, YouTube Music and Spotify connection. If your sibling signs up on your server, they can't see your playlists.

Full, friendly, screenshot-free walkthroughs for every connection are in **[docs/CONNECTIONS.md](docs/CONNECTIONS.md)**:

1. [Last.fm](docs/CONNECTIONS.md#1-lastfm-the-heart-of-the-visualizer)
2. [YouTube Music](docs/CONNECTIONS.md#2-youtube-music-playlists-and-playback)
3. [Spotify](docs/CONNECTIONS.md#3-spotify-the-alternative-engine)
4. [adb / Wireless debugging](docs/CONNECTIONS.md#4-adb-wireless-debugging-so-vizah-can-press-the-buttons)
5. [YouTube Data API key](docs/CONNECTIONS.md#5-optional-a-youtube-data-api-key)

---

## 🔁 Every day use

Vizah is a server, so **it has to be running** for the app to work.

| I want to… | Do this |
|---|---|
| **Start Vizah** | `bash ~/vizah/launch.sh` (starts the server and opens the app) |
| **Start it silently** | `bash ~/vizah/launch.sh --no-open` |
| **Stop Vizah** | `bash ~/vizah/stop.sh` |
| **Start with one tap** | Install **Termux:Widget** and add the **Vizah** widget (setup already created the shortcut) |
| **Start on boot** | Install **Termux:Boot** and open it once. Setup already created `~/.termux/boot/vizah` |
| **See what went wrong** | `tail -n 40 ~/vizah/server.log` |

If you open the app and the server isn't running, Vizah shows a friendly "server isn't running" page with the exact command to type. No panic needed.

---

## 🧠 How it works in 60 seconds

```
┌──────────────────── your phone ────────────────────┐
│                                                    │
│  Chrome / Vizah app ──► Python server (Termux)     │
│        ▲                     │                     │
│        │                     ├─► Last.fm   (what's playing)
│        │                     ├─► lyrics sources    (synced lines)
│        │                     ├─► YouTube Music / Spotify (playlists)
│        │                     └─► adb ──► YouTube Music / Spotify app
│        └─── you tap things                (play, pause, next)
└────────────────────────────────────────────────────┘
```

- The **server** (`server.py`) is plain Python with **no required pip packages**. Spotify, lyrics, accounts and DMs all use only the standard library.
- The one optional package is `ytmusicapi`, used for YouTube Music playlists. If it fails to install, everything else still works.
- **adb over Wi-Fi** lets the server send media keys (play/pause/next/previous) to the music app on the same phone.
- The page is served from `127.0.0.1`, which browsers treat as a *secure context*, so Chrome will let you install it as an app. (It's also the address Spotify's login redirect expects.)

---

## 👯 Sharing with friends

Vizah has real accounts, a friends list, public profiles, "what are my friends listening to", DMs, and a "send me what you're playing" request.

By default the server listens on your whole network (`0.0.0.0`), so friends on the **same Wi-Fi** can reach it at your phone's IP, for example `http://192.168.1.23:8000`. Two things to know:

- Friends can create accounts if sign-ups are open (the default). To lock the door after your crew has joined, restart with `VIZAH_SIGNUP=0`.
- Some features, notably **installing as an app** and **Spotify login**, want the `127.0.0.1` address, so those are best done on the phone that hosts the server.

Don't put Vizah on the open internet. It's built for your own phone and network. See [Privacy & security](#-privacy--security).

---

## ⚙️ Settings you can tweak (environment variables)

Set these in front of the launch command, like `PORT=9000 bash ~/vizah/launch.sh`.

| Variable | Default | What it does |
|---|---|---|
| `PORT` | `8000` | Port the server uses |
| `VIZAH_SIGNUP` | `1` | `0` stops new accounts being created |
| `VIZAH_DEV_ACCOUNTS` | `0` with the launch scripts | `1` auto-creates two built-in dev logins on every start. See the [note below](#-heads-up-the-built-in-dev-logins) |
| `VIZAH_CREDS` | `~/vizah/creds.txt` | Where secrets (passwords as hashes, YouTube/Spotify logins) are stored |
| `VIZAH_AUTH` | `~/.vizah_accounts.json` | Accounts, sessions, Last.fm details and themes |
| `VIZAH_MESSAGES` | `~/.vizah_messages.json` | Direct messages |
| `VIZAH_DIR` | `~/vizah` | Where `setup_pwa.sh` installs to |
| `ADB_ADDR` | *(unset)* | Phone address for `playpause.sh` if adb isn't connected yet |

### ⚠️ Heads up: the built-in dev logins

The server code includes two convenience accounts for development (`Umbrxs` and `Vizah`, both with the password `00000000`) that it re-creates on every start so the developer never has to sign up again. Because the server is reachable from your network, **`launch.sh` and `start.sh` turn this off by default.** If you ran `python3 server.py` directly, set `VIZAH_DEV_ACCOUNTS=0` yourself. Only turn it on (`VIZAH_DEV_ACCOUNTS=1`) for a private test setup that nobody else can reach.

---

## 🔒 Privacy & security

Vizah is built so your stuff stays *yours*:

- **Everything lives on your phone.** There's no Vizah cloud. Nothing is sent to anyone except the services you connect (Last.fm, YouTube, Spotify, lyric sites).
- **Passwords are never stored in plain text.** They're salted PBKDF2 hashes.
- **`creds.txt` is the vault.** It holds your YouTube cookie and Spotify tokens, is kept at file mode `600`, and the web server refuses to serve it.
- **Per-account separation.** Each Vizah account keeps its own YouTube Music and Spotify logins.
- **Other users never see** your API keys, password data or YouTube data.
- The YouTube Music login is a *browser cookie*, which is powerful. Treat it like a password and never post it anywhere.

Golden rules: **never commit or share `creds.txt`, `config.json` or `~/.vizah_*.json`** (the included `.gitignore` already keeps them out of git), and don't expose the port to the public internet.

---

## 🕹️ Optional side quest: Friday Night Funkin'

Vizah can host a playable web build of *Friday Night Funkin'* behind a little arrow in the YouTube section.

```bash
pkg install git          # if you haven't already
bash ~/vizah/install_fnf.sh
```

- It downloads about **1.6 GB** (use Wi-Fi!) and keeps roughly **850 MB** afterwards. Have about **2 GB** free.
- A complete install has **2037** files. The script tells you if any are missing.
- Then open the arrow in the YouTube section and press **Play**.

> **Please read:** the game's art and music belong to *The Funkin' Crew Inc.*, whose license doesn't allow others to host them publicly. Use this for **private, personal use on your own device or network only**. Never put `games/fnf` on a public website. That's why it's left out of this repo.

---

## 🔄 Updating

```bash
cd ~/vizah
bash stop.sh
git pull
bash launch.sh
```

Your accounts, logins and settings live in `creds.txt` and `~/.vizah_*.json`, none of which git touches, so updates won't log anyone out.

## 💾 Back up

Copy these somewhere safe if you care about your accounts, friends and messages:

```bash
cp ~/vizah/creds.txt ~/.vizah_accounts.json ~/.vizah_messages.json /sdcard/Download/
```

(They contain secrets. Delete the copies when you're done, and don't upload them anywhere.)

## 🧹 Wipe all accounts

```bash
bash ~/vizah/stop.sh
python3 ~/vizah/clear_users.py
```

It asks you to type `DELETE`, saves a `.bak` of each file first, then removes every account, saved login and DM. The next person to sign up becomes the first account again.

## 🗑️ Uninstall

```bash
bash ~/vizah/stop.sh
rm -rf ~/vizah ~/.shortcuts/Vizah ~/.termux/boot/vizah ~/.vizah_accounts.json ~/.vizah_messages.json
```

Then remove the app from your home screen (long-press → Uninstall) and, if you like, `pkg uninstall android-tools`.

---

## 🆘 Something's not working?

The most common hiccups, in one breath:

| Symptom | Quick fix |
|---|---|
| "Vizah server isn't running" | In Termux: `bash ~/vizah/launch.sh` |
| No **Install app** option in Chrome | Open Vizah at `http://127.0.0.1:8000/` exactly (not your Wi-Fi IP), then **⋮ → Install app** |
| Buttons don't control the music | adb isn't connected. Re-run the phone step in Profile & connections (Wireless debugging must be on) |
| Spotify says *invalid redirect URI* | The URI in your Spotify app must match the one Vizah shows, character for character |
| Server dies when the screen turns off | Install **Termux:API**, and turn off battery optimization for Termux |
| Lyrics are missing or out of time | Turn on **Use more sources** in the lyrics settings to also search NetEase, QQ Music and KuGou |

The full list, with the "why" behind each one, is in **[docs/TROUBLESHOOTING.md](docs/TROUBLESHOOTING.md)**.

---

## 🗂️ What's in the box

```
vizah/
├── server.py          the brain: accounts, Last.fm, media control, playlists
├── index.html         the whole app UI
├── spotify.py         Spotify login (PKCE) + playlists + playback
├── social.py          DMs, friends, public profiles
├── lyrics_src.py      extra synced-lyrics sources
├── creds.py           the private secrets file helper
├── ytm_setup.py       command-line YouTube Music login setup
├── bio.js, dm.js      profile and messaging front-end bits
├── sw.js, manifest.json, pwa/    installable-app plumbing
├── ytm-helper/        browser extension: one-click YouTube Music connect
├── setup_pwa.sh       one-time setup (run this first)
├── launch.sh / stop.sh / start.sh    start, stop, run in foreground
├── playpause.sh       standalone media-key helper for the terminal
├── clear_users.py     wipe all accounts
├── install_fnf.sh     optional Funkin' download
└── docs/              connection guides + troubleshooting
```

---

## 🙌 Credits & fine print

- Music data from **[Last.fm](https://www.last.fm)**. Playlists via **YouTube Music** and **Spotify**. Lyrics from public sources. Vizah is an independent project and is not affiliated with or endorsed by any of them.
- Use of YouTube, Spotify and lyric sources is subject to their own terms. Use Vizah for personal listening.
- Friday Night Funkin' is © The Funkin' Crew Inc.

Made for people who like their music with a little extra glow. ✨
If Vizah brightened your day, a ⭐ on the repo is a lovely thank-you.
