# 🔌 Connecting everything

Vizah is a hub, and a hub is only as fun as what's plugged into it. This guide walks you through every connection, in the order you'll probably want them.

**The short version:**

| Connection | Needed? | What it unlocks |
|---|---|---|
| [Last.fm](#1-lastfm-the-heart-of-the-visualizer) | ✅ Yes | The visualizer, lyrics, "now playing", friends' listening |
| [adb](#4-adb-wireless-debugging-so-vizah-can-press-the-buttons) | ✅ For playback buttons | Play / pause / next / previous, starting playlists |
| [YouTube Music](#2-youtube-music-playlists-and-playback) | Pick one or both | Your YouTube Music playlists |
| [Spotify](#3-spotify-the-alternative-engine) | Pick one or both | Your Spotify playlists, plus Spotify as the playback engine |
| [YouTube API key](#5-optional-a-youtube-data-api-key) | ❌ Optional | Official YouTube search instead of the built-in one |

Everything below happens in the Vizah app, mostly in the **Profile & connections** panel (the settings screen, opened from the profile/settings icon). Each Vizah account has its own set of connections.

---

## 1. Last.fm: the heart of the visualizer

Vizah asks Last.fm "what is this person listening to *right now*?" and builds the show around the answer. No Last.fm, no show.

### Get a free API key

1. Sign in (or sign up) at [last.fm](https://www.last.fm).
2. Go to **<https://www.last.fm/api/account/create>**.
3. Give your "application" any name and description (for example "Vizah for me"). A callback URL isn't needed.
4. Submit. Last.fm shows an **API key**. Copy it.

### Plug it into Vizah

1. In Vizah open **Profile & connections** and tap **Connect Last.fm**.
2. Enter your **Last.fm username** and the **API key**, then tap **Log in**.
3. The visualizer wakes up as soon as Last.fm reports something playing.

### ⚠️ The scrobbling catch (read this!)

Vizah can only see what **Last.fm sees**, so your music app needs to be *scrobbling* to Last.fm:

- **Spotify** has a built-in Last.fm link: *Spotify → Settings → Apps / Social → Last.fm*.
- **YouTube Music** does **not** scrobble on its own. Use a scrobbler app such as the official Last.fm app or a third-party one like Pano Scrobbler, and make sure it's allowed to read notifications.
- Quick test: play a song, then open your Last.fm profile in a browser. If it shows "Scrobbling now", Vizah will see it too.

To disconnect, use **Disconnect** in the Last.fm card. Your account and theme stay put.

---

## 2. YouTube Music: playlists and playback

This lets you browse and start your YouTube Music playlists from inside Vizah.

> **How it works:** YouTube has no friendly "log in with Vizah" button, so Vizah borrows your browser's YouTube Music login (a cookie) and keeps it in `creds.txt` on **your** phone. Treat that cookie like a password.

### Easiest: the one-click helper (needs a computer's browser)

The helper is a tiny browser extension. It reads your YouTube login in *your* browser and hands it to *your* Vizah server. Nothing goes anywhere else.

1. On your computer, make sure you're on the **same Wi-Fi** as the phone.
2. Find your phone's address: **Settings → Wi-Fi**, tap your network, and look for **IP address** (it looks like `192.168.1.23`).
3. On the computer, open `http://<phone-address>:8000` and **sign in to Vizah**.
4. In **Profile & connections**, tap **Connect YouTube Music**, then **Get the one-click helper**. (Or on the phone, you can use the same button during the first-run playlists step.) You'll download `vizah-ytm-helper.zip`. **Unzip it.**
5. Add it to your browser, once:
   - **Chrome / Edge / Brave:** open `chrome://extensions`, switch on **Developer mode**, click **Load unpacked**, and pick the unzipped folder.
   - **Firefox:** open `about:debugging`, choose **This Firefox**, click **Load Temporary Add-on**, and pick the `manifest.json` inside the folder. (Firefox forgets temporary add-ons when it restarts. Just load it again.)
6. Open **[music.youtube.com](https://music.youtube.com)** and make sure you're **signed in**.
7. Click the helper's icon. A Vizah tab pops open and finishes the connection by itself. ✅

> The helper downloads with your server's address already baked in, which is why step 3 matters: download it from the address you want it to talk to.

### No computer? Paste your login instead

In **Profile & connections**, tap **Connect YouTube Music** and open **No helper? Paste your login instead**:

- **Firefox:** open music.youtube.com signed in → `F12` → **Network** → reload → click any `music.youtube.com` request → **Headers → Request Headers** → right-click the **Cookie** row → **Copy Value** → paste into Vizah → **Connect**.
- **Other browsers:** DevTools → **Network** → right-click any request → **Copy as cURL (bash)** → paste into Vizah → **Connect**.

### Or from Termux

Save the copied request or cookie into a text file, then:

```bash
python3 ~/vizah/ytm_setup.py cookie.txt
# multiple Google accounts? pick which one:
python3 ~/vizah/ytm_setup.py cookie.txt --authuser 1
```

Long cookies get cut off when pasted straight into a terminal, so the file route is more reliable.

### Needs `ytmusicapi`

Setup installs it for you. If playlists don't load, try `pip install --upgrade ytmusicapi` (add `--break-system-packages` if pip complains).

---

## 3. Spotify: the alternative engine

Connect Spotify and you get your Spotify playlists (and Liked Songs) right next to your YouTube ones, and you can switch playback to Spotify instead of YouTube Music. It uses Spotify's official, secure login (OAuth with PKCE), so Vizah **never sees your Spotify password**.

> 🎟️ Spotify only allows apps to control playback for **Premium** accounts. That's Spotify's rule.

### Make a Spotify app (free, 2 minutes)

1. Go to **<https://developer.spotify.com/dashboard>** and sign in.
2. Click **Create app**. Any name and description will do.
3. When asked which API you plan to use, tick **Web API**.
4. After tapping **Connect Spotify** in Vizah's **Profile & connections** you'll see a **Redirect URI** (it looks like `http://127.0.0.1:8000/spotify/callback`). **Copy it and paste it into the Redirect URIs box on your Spotify app, then save.** It must match **exactly**, character for character.
5. Copy your app's **Client ID**. A **Client Secret** is optional but recommended.

### Plug it into Vizah

1. **Open Vizah at `http://127.0.0.1:8000/`** on the phone itself. Spotify only accepts `https` addresses or the local loopback address, so this is the one that works.
2. In **Profile & connections**, tap **Connect Spotify**, paste the **Client ID** (and secret if you like), and tap **Save & connect Spotify**.
3. Spotify asks you to approve access. Say yes. You land back in Vizah. ✅

> If Spotify says your app is in *development mode*, you may need to add your own account under **User Management** on the dashboard before it will let you log in.

### Pick your playback engine

In the **Playback** card you choose what the play, skip and playlist buttons control: **YouTube Music** or **Spotify**. Features that used to be YouTube-Music-only (play a playlist, pick a song, add songs to playlists) work with Spotify too.

---

## 4. adb (Wireless debugging): so Vizah can press the buttons

Vizah can't tap your music app directly. Instead it talks to Android through **adb** (Android Debug Bridge) over Wi-Fi and sends media keys, the same ones your headphone buttons send. You set this up once.

You need **Android 11 or newer** and a **Wi-Fi connection** (Wireless debugging only works while connected to Wi-Fi, even if there's no internet).

### A. Turn on Developer options

1. **Settings → About phone** and tap **Build number** seven times. Enter your PIN if asked.
2. Go back to **Settings → System → Developer options** (location varies slightly by brand).
3. Switch on **Wireless debugging** and allow it on your current network.

### B. Pair (first time only)

1. Tap **Wireless debugging** itself (the words, not the switch) and choose **Pair device with pairing code**. A popup shows a **6-digit code** and a **pairing address** like `192.168.1.23:41234`.
2. In Vizah's phone step, open **First time? Pair the phone first** and enter the **pairing address** and the **6-digit code**.

> 🪄 **Pro tip:** that popup vanishes the moment you leave the Settings screen. Use **split-screen** or a **floating window** so Settings and Vizah are visible at once, or type fast!

### C. Connect

1. Back on the main Wireless debugging screen, note the **IP address & port** under the switch. This is a **different port** from the pairing one, like `192.168.1.23:37000`.
2. Enter it in Vizah under **Connection address** and tap **Connect**.
3. The status dot turns green. Done. 🎉

> If you see **unauthorized**, look for an *"Allow wireless debugging?"* popup on the phone and tap **Allow** (tick *Always allow*).

### Heads up: the port changes

Android picks a **new connection port** whenever Wireless debugging is switched off and on, or the phone restarts. If the buttons stop working after a reboot, open the Wireless debugging screen, read the new port, and connect again in Vizah.

### Prefer the terminal?

```bash
adb pair 192.168.1.23:41234        # enter the 6-digit code when asked
adb connect 192.168.1.23:37000
adb devices                        # should say "device"
```

There's also a standalone helper if you just want a button in the shell:

```bash
bash ~/vizah/playpause.sh          # toggle
bash ~/vizah/playpause.sh next     # play | pause | next | prev
echo "192.168.1.23:37000" > ~/.playpause_adb   # remember the address
```

---

## 5. Optional: a YouTube Data API key

The YouTube section's search works with **no setup**. If you'd like YouTube's *official* search instead (it quietly falls back to the normal one if the key ever fails), add a key:

1. Open the [Google Cloud Console](https://console.cloud.google.com/) and create a project (any name).
2. Open **APIs & Services → Library**, search for **YouTube Data API v3**, and click **Enable**.
3. Open **APIs & Services → Credentials → Create credentials → API key**. Copy it.
4. In Vizah's YouTube section, open **Use an official API key (optional)**, paste the key and tap **Save key**.

It's stored in `creds.txt` on your phone only. Consider restricting the key to the YouTube Data API in the Cloud Console so it's useless if it ever leaks.

---

Something not clicking? Head to **[TROUBLESHOOTING.md](TROUBLESHOOTING.md)**.
