# Agent Ultra

**[Site, screenshots and roadmap →](https://dafarusd.github.io/Ultra-Agent-Release/)**

**An Android agent that runs its own brain on the phone.**

Not a chat app with a cloud API behind it. A local LLM — Qwen2.5 7B, Llama 3.1 8B,
Gemma, Phi, your choice — loaded into memory on the handset, driving real tools:
the accessibility service, the camera flash, the clipboard, the browser, other
apps' user interfaces. With the network off, it still works.

<p align="center">
  <a href="https://x.com/Dafarusd"><strong>Built by @Dafarusd</strong></a>
</p>

---

## What it actually does

- **Runs on-device.** Pick a model that fits your phone and it downloads on
  demand. Nothing is bundled — the APK is 8.3 MB. Airplane mode, Wi-Fi off, no
  SIM: it still answers and still operates the phone.
- **Drives any app you allow.** It reads the screen through Android's
  accessibility tree and acts on it — taps, typing, scrolling, navigation.
- **Reads a page properly.** Not the first forty labels. It scrolls the whole
  thing and returns *structured items*, so a price belongs to its own product
  rather than to whichever line happened to be nearby.
- **Speaks and listens.** Hold the side button, talk, put the phone down, hear
  the answer. Transcription is on-device.
- **Learns a route by watching you do it once.** Say "watch me", do the task on
  your phone, then name it. The first time it replays, it works out for itself
  which control opens each screen; after that it goes straight there. On a real
  phone that took a route from eight taps down to two.
- **Tells you what it actually knows.** Ask for your routines and each one says
  whether it has ever done the job itself or only watched you do it — and if a
  control it had learned has since moved, it says the app has changed.
- **Remembers what worked.** A task that succeeds is recorded with its
  arguments; ask the same thing in different words later and it recalls the
  approach.

## What it will not do

This is the part worth reading.

- **It only enters apps you tick.** Allowlist by default, and it fails closed.
  Whatever the agent reads is sent to the cloud model to decide the next step,
  so "don't look" matters as much as "don't act".
- **Messages, contacts and location are off by default**, behind their own
  switch, because they read Android's databases rather than the screen — the
  app list does not cover them.
- **A tap that commits something stops and asks.** Pay, buy, order, confirm,
  send, transfer, delete, subscribe. Both indexed and raw-coordinate taps, so
  the check cannot be sidestepped by choosing coordinates. An unanswered prompt
  is a refusal, never an approval.
- **It stops when the screen disagrees with you.** Ask it to pay 240 on a screen
  showing 2,400 and it does not ask you to confirm — it stops before pressing
  anything and tells you both numbers. A confirmation you have approved fifty
  times gets approved the fifty-first without being read, so where it can tell
  something is wrong it refuses rather than asking.
- **A code stays in the app it came from.** A one-time code or card number read
  on one screen cannot be typed, copied, messaged or saved anywhere else.
  Anything not explicitly cleared to carry it is refused, so a way out that
  nobody thought of is refused by default.
- **It will not press pay, send or delete to find its way.** While working out
  the next step of a routine it only tries controls it can undo. A guess is not
  a reason to ask you to approve a payment; if a route ends behind such a
  button, it stops and says so.
- **It cannot pass your fingerprint or PIN.** Anything behind one ends with the
  phone in your hand.
- **Notification logging is off by default** and never records a protected app.

## The three projects behind it

**gatellml — the policy gate.** A deterministic enforcement layer, ported to
Kotlin and running on the handset. Every tool call is checked against a
deny-by-default manifest before it executes: a tool that is not declared cannot
run at all. Arguments carry their origins, so an action whose target never
appeared in your request is blocked and handed back to you to confirm. The model
is never trusted; the program is. Research and measurements:
[github.com/dafarusd/gate](https://github.com/dafarusd/gate)

**gate — the resolve channel.** The research named an untested gap: a gate with
no interactive channel can only refuse. That gap is closed here. A block that a
human could legitimately cure pauses the run, shows the exact target, and waits.
Your tap mints it trusted for that episode and the work continues. Taint, spoofing
and undeclared tools are never confirmable, by design.

**Mind Meld — the split that made the small models usable.** A 1B model on a
phone will not emit clean JSON, and no amount of prompting fixes that. Mind Meld
established the division: **the model owns intent, the engine owns structure.**
Ask for the flashlight and the model supplies only the intent; a deterministic
parser builds the call. That one idea is why a phone-sized model can drive real
tools instead of merely talking about them — and it is used again for routine
names, after a 70B model read "run my morning briefing" as a question about
which model it was.

## Download

**[agent-ultra-2.4.0.apk](https://github.com/dafarusd/Ultra-Agent-Release/releases/download/v2.4.0/agent-ultra-2.4.0.apk)** — 8.4 MB, free, no account.

2.4.0: the confirm question now shows on top of the app you're in. Before, asking pulled you
out of the app, the app forgot what was selected, and a delete you had approved never happened.
It also reads a screen by its words instead of by numbered boxes, sees what a screen shows and
not only what can be tapped, can press and hold, and finds a row in a long list by itself. Say
"no, that's wrong" and it takes back what that run taught it.

Measured on AndroidWorld, Google's public phone benchmark — it sets the task and checks the
phone, nothing of mine grades. 33 simpler tasks picked by rule, details it had never seen: 8 done
on its own, 13 when it was given steps from one checked run by a stronger model. That stronger
model scores 13 and 14. No model was retrained. The memory that keeps those steps runs on my
laptop beside an emulator, so the download can't learn them by itself yet. Every round, with the
guess I wrote down before it:
[androidworld-results.md](https://github.com/dafarusd/Ultra-Agent/blob/native/ultra-native/tools/androidworld-results.md).

2.3.2: the safety gate now matches what you named as a whole word. Before, naming
`alice@example.com` also let `ce@example.com` through.

*Running 2.2.0, 2.3.1 or 2.3.2? Install straight over — same signing key. Running 2.0.0, 2.1.0
or 2.3.0? Uninstall first — see [withdrawn versions](#withdrawn-versions) at
the bottom.*

## Setting it up

Requires Android 8.0 or newer, arm64. Nothing here is optional except where it
says so, and none of it can be skipped by the app on your behalf — Android
gives these decisions to you, which is the point.

**1. Install it.** Android will try to stop you four times. This is what an app
from outside the Play Store looks like, and none of it means anything is wrong —
but the wording is alarming and one of the buttons quietly gives up, so here is
the whole path, screen by screen. Walked on a real phone, in this order:

| You will see | What to tap |
|---|---|
| **"File might be harmful"** | **Download anyway** |
| **"Permission required"** — your browser is not allowed to install apps | **Settings**, turn on **Allow permission**, then come back and tap the download again |
| **"Install this app?"** | **Install** |
| **"App blocked to protect your device"** — *"Play Protect hasn't seen an app from this developer before."* | **More details**, then the small **Install anyway** link |

**That last one is the step people give up on.** The big obvious button says
**Got it**, and tapping it cancels the install. The way through is the quiet
link underneath.

Play Protect is telling the truth, and it is worth understanding rather than
just clicking past. It is not saying the app is dangerous. It is saying it has
never seen it before, which is true: it is a new app, downloaded from a page
rather than a shop, and Google has nothing on file about it. Every app that is
not in the Play Store gets this, on the first install, forever.

If you would rather check than trust, the fingerprint above is what it is for.

**2. Turn on the accessibility service. Nothing works before this.** Open the
app and tap the red **agent: a11y off** at the top; it takes you straight to
Android's accessibility screen. On a Samsung the path from there is
**Installed apps → Agent Ultra**, where it will read *Off*; other makers word
that middle step differently — look for downloaded, installed or downloaded
services. Android will warn you that the service can
observe and act on your screen. It can — that is how it reads and drives other
apps, and it is why the next step exists.

The label turns to **agent: ready** when it has worked.

**3. Give it a brain.** One of:

- **Settings → AI PROVIDER** — any OpenAI-compatible endpoint and key. You
  need an account with whichever provider you choose; the app itself needs
  none.
- **Settings → ON-DEVICE MODEL** — pick one and download it. The list is
  scored against your phone's memory: fits comfortably, tight, or too big. This
  path needs no account and no network once the file is down.

**4. Choose which apps it may enter.** **Settings → WHERE THE AGENT MAY GO →
Choose allowed apps.** It starts with nothing ticked and can see nothing until
you tick something. Search for an app by name; the list marks anything that
looks like banking, health or a password manager.

**5. Allow the parts you want.** **Settings → PERMISSIONS ANDROID CONTROLS.**
Voice, camera, texts, contacts, calendar, location and notifications all start
refused, and the matching features do nothing until you allow them. Each has an
Allow button, or take them all at once.

Allowing one of these does **not** let the agent use it. Step 4 and the
personal-data switch still decide that. This only decides whether the phone
lets the app try at all.

**6. Optional — messages, contacts and location.** **Settings → PERSONAL
DATA.** Off by default. These read Android's own databases rather than the
screen, so the app list in step 4 does not cover them.

**7. Optional — reading notifications.** In the same permissions section, next
to **Reading notifications**, tap Open. Android keeps this one on a screen of
its own and no app is allowed to ask for it directly.

### If something does nothing at all

Almost always step 2 or step 5. The status at the top of the chat screen says
whether the service is running, and the permissions section says in words which
capabilities the phone is currently refusing.

## Honest limits

- 2.4.0 installs over 2.3.2 on my Galaxy A15, launches and drives apps there. The
  on-top confirm question has only been exercised on an emulator so far, and the benchmark
  runs are emulator runs too.
  20 of those 33 benchmark tasks still fail. Camera tasks and calendar grids are two of them;
  picking one file out of look-alikes is another.
- In-app navigation handles direct tasks well and still struggles with long
  multi-step flows inside unfamiliar apps. Teaching it a route is the reliable
  path; asking it to work out something new in an app it has never seen is not.
- Learning a route by watching has been proven in a browser, which is the hard
  case — a browser reuses one window and reports almost nothing about what you
  tapped. Other apps should be easier, and have had far less exercise.
- The check that stops a payment mismatch reads amounts of money. A wrong
  recipient, a wrong date or a wrong quantity is not caught by it.
- Structured extraction reads *ordered* fields. It knows which lines belong to
  one item; it does not label which is the title and which is the rating.
- Some apps detect accessibility services and refuse to run. That is their
  choice and it is not worked around.
- Speed on-device depends on the model you pick. A 7B is a far better offline
  brain and a noticeably slower quick answer than a 1B; the app accounts for
  that when it decides where a request goes.

## Source

The source is public: [github.com/dafarusd/Ultra-Agent](https://github.com/dafarusd/Ultra-Agent).
The build is free to download and use, and the security research it is built on
is public too: [github.com/dafarusd/gate](https://github.com/dafarusd/gate)

---

**[@Dafarusd on X](https://x.com/Dafarusd)** · Copyright (c) 2026 Dafarus

---

## Withdrawn versions

**2.0.0, 2.1.0 and 2.3.0 have been removed.** If you are running any of them,
uninstall it before installing 2.4.0 — Android will refuse the update
otherwise, and will not tell you why.

All three were signed with Android's **debug key** — a key that ships
inside the Android SDK, which anyone can sign an app with. Android decides
whether an update is genuine by checking the signature, so a stranger could
have built an "update" to those versions and had it install straight over the
top. Nothing of the sort is known to have happened, and all three downloads
have been removed.

2.3.0 was caught on 11 September 2026, during a check of this page. The build
file fell back to the debug key when the release key wasn't on the machine, so
a release build produced a debug-signed APK that looked fine. That fallback is
gone: a release build without the key now fails instead.

2.1.3, 2.2.0, 2.3.1, 2.3.2 and 2.4.0 are signed with a real key held only by me. Because the
signature is different, Android will not install them over a debug-signed one:

1. Uninstall Agent Ultra.
2. Install 2.4.0.
3. Set your allowed apps and provider again — uninstalling clears them.

You can check any build you download:

```
apksigner verify --print-certs agent-ultra-2.4.0.apk
```

It should read `CN=Dafarus, OU=Agent Ultra` with SHA-256 fingerprint
`0d04510d51d67a43effa2b29b855e84b329fe73e16433896aa5145e151ca241f`. Anything
else did not come from me.

---

Built by Dafarus — local-first software and hardware you own.

Follow the work on X: [@Dafarusd](https://x.com/Dafarusd)

My company:
- Keephaven — [keephaven.co](https://keephaven.co) · [X](https://x.com/Keephaven) · [Facebook](https://www.facebook.com/profile.php?id=61592155452190)

More work: [gate](https://github.com/dafarusd/gate) · [Sentinel](https://github.com/dafarusd/sentinel-public) · [Agent Ultra](https://github.com/dafarusd/Ultra-Agent-Release) · [EveryVoice](https://github.com/dafarusd/everyvoice) · [Mind Meld](https://github.com/dafarusd/mindmeld) · [monero-swap](https://github.com/dafarusd/monero-swap)
