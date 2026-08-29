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
- **Remembers what worked.** A task that succeeds is recorded with its
  arguments; ask the same thing in different words later and it recalls the
  approach. Name a run and it becomes a routine you can replay.

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

**[agent-ultra-2.1.2.apk](https://github.com/dafarusd/Ultra-Agent-Release/raw/main/agent-ultra-2.1.2.apk)** — 8.3 MB, free, no account.

### If you installed 2.0.0 or 2.1.0, please replace them

Those two builds were signed with Android's **debug key** — a key that ships
inside the Android SDK, which anyone can sign an app with. Android decides
whether an update is genuine by checking the signature, so a stranger could
have built an "update" to those versions and had it install straight over the
top. Nothing of the sort is known to have happened, and both downloads have
been removed.

2.1.2 is signed with a real key held only by me. Because the signature is
different, Android will not install it over the old one:

1. Uninstall Agent Ultra.
2. Install 2.1.2.
3. Set your allowed apps and provider again — uninstalling clears them.

You can check any build you download:

```
apksigner verify --print-certs agent-ultra-2.1.2.apk
```

It should read `CN=Dafarus, OU=Agent Ultra` with SHA-256 fingerprint
`0d04510d51d67a43effa2b29b855e84b329fe73e16433896aa5145e151ca241f`. Anything
else did not come from me.

## Setting it up

Requires Android 8.0 or newer, arm64. Nothing here is optional except where it
says so, and none of it can be skipped by the app on your behalf — Android
gives these decisions to you, which is the point.

**1. Install it.** Your browser will warn that it did not come from the Play
Store and ask whether to allow installs from it. That warning is correct: check
the fingerprint above if you want to be sure. Google Play Protect may then
offer to send the app to Google for a security check — either answer is fine.

**2. Turn on the accessibility service. Nothing works before this.** Open the
app and tap the red **agent: a11y off** at the top; it takes you straight to
Android's accessibility screen. Find Agent Ultra under installed apps or
downloaded apps and switch it on. Android will warn you that the service can
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

- In-app navigation handles direct tasks well and still struggles with long
  multi-step flows inside unfamiliar apps.
- Structured extraction reads *ordered* fields. It knows which lines belong to
  one item; it does not label which is the title and which is the rating.
- Some apps detect accessibility services and refuse to run. That is their
  choice and it is not worked around.
- Speed on-device depends on the model you pick. A 7B is a far better offline
  brain and a noticeably slower quick answer than a 1B; the app accounts for
  that when it decides where a request goes.

## Source

The source is private. The build is free to download and use, and the security
research it is built on is public: [github.com/dafarusd/gate](https://github.com/dafarusd/gate)

---

**[@Dafarusd on X](https://x.com/Dafarusd)** · Copyright (c) 2026 Dafarus
