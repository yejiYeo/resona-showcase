<p align="right"><b>English</b> | <a href="README.kr.md">한국어</a></p>

<p align="center">
  <img src="assets/app-icon.png" width="96" alt="Resona app icon" />
</p>

<h1 align="center">Resona</h1>

<p align="center">
  <b>A 3D character that listens to what you say and reacts the way a listener would</b>
</p>

<p align="center">
  <a href="https://hello-resona.vercel.app"><b>▶ Website</b></a> ·
  <a href="https://youtu.be/m4W5PU3zmM0"><b>Intro video</b></a>
</p>

---

## At a glance

| | |
|---|---|
| **Period** | Sep 29 – Oct 21, 2026 (about 3 weeks, solo) |
| **Type** | Personal portfolio project · planning, design, development and deployment |
| **Platforms** | Web (desktop and mobile) · Android app · Chrome extension |
| **Stack** | Next.js 16 · React 19 · TypeScript · three.js / React Three Fiber · Google Gemini (analysis + Live voice) · Vercel |
| **Source code** | Private repository. Review access is available on request. |

## What it is

Most "emotion avatars" mirror **the emotion of the sentence**: write "I failed my exam" and the avatar looks sad.
Resona shows **how the person hearing it would react**. The same words can make a listener worried, sympathetic or surprised.

The character doesn't just change its face; it moves like **someone who is actually listening**.

- While you talk, it keeps eye contact, turns one ear toward you, and nods when you pause.
- When it reacts, the expression and head movement arrive together (surprise pulls the head back, sadness drops the gaze), then settle naturally after a few seconds.
- When nothing is said, it blinks and glances around, and after a long wait it starts to look bored.

## Features

| Feature | Description |
|---|---|
| **Text and voice input** | Type or speak into the mic; it reads the context and reacts to the most recent thing you said. |
| **Live voice chat with AI** | Talk with Gemini Live inside the app. The character listens to both you and the AI and reacts, following the AI's reply as it is spoken. |
| **Use alongside ChatGPT** | A Chrome extension relays the chatgpt.com conversation to Resona in the browser's side panel. While you talk it holds its expression and nods; it reacts when ChatGPT answers. *(Personal and test use only until the terms of service are reviewed.)* |
| **Four characters** | They differ not only in looks but in **personality**: how strongly they emote, how fast they react, how often they nod or look around. |
| **Android app** | A Trusted Web Activity that opens the deployed site full screen in Chrome, so deploying the web updates the app too. |
| **Korean / English** | Switches the UI, the speech recognition language and the character descriptions. |

## Characters

| <img src="assets/character-joy.png" width="180"/> | <img src="assets/character-rever.png" width="180"/> | <img src="assets/character-alex.png" width="180"/> | <img src="assets/character-harley.png" width="180"/> |
|:---:|:---:|:---:|:---:|
| **Joy**<br/>Empathetic and quick to react | **Rever**<br/>Calm and composed | **Alex**<br/>Chic, hip and laid-back | **Harley**<br/>Charismatic and full of fun |

All four characters are built from **a single 3D face model** (ARKit 52 blendshapes). Brows, lips, moles and freckles are painted in shaders; fringes, the mohawk, long curly hair, the headset and glasses are generated in code and fitted to the head's surface. When the external avatar service (Ready Player Me) shut down, I chose to build them myself with no outside dependency.

<p align="center"><img src="assets/mobile-characters.png" width="300" alt="Mobile screen: choosing a character"/></p>

## Technical challenges

**1. Turning "a listener's reaction" into structured values**
Gemini is given a JSON schema and returns only an intensity vector over seven emotions (happy, sad, angry, surprised, worried, confused, disappointed). The vector is filtered through the character's personality (per-emotion weights, reaction threshold) and then mapped to combinations of ARKit blendshapes.

**2. A request policy balancing responsiveness and cost**
- Typing and voice input: a 400 ms debounce, and any in-flight request is cancelled when new input arrives (only the latest context counts).
- Streaming conversation (AI replies): the approach above froze the face until a reply ended, so it keeps a single request in flight and sends the newest context as soon as it returns.
- While the user is speaking, it doesn't analyse at all and only shows listening behaviour, cutting unnecessary calls and awkward mid-sentence expression changes.

**3. A behaviour engine that feels alive (PresenceEngine)**
The state (Idle / Listening / Reacting) only changes how often and how strongly things move; layers for expression, blinking, gaze, nods and micro-motion always run. Intervals are drawn from log-normal distributions, head motion uses non-periodic noise, and transitions use springs, so no repeating pattern shows.

**4. Free, and never blocked**
- Uses only Gemini's free tier. It tries several models in order and tracks rate-limit cooldowns per model; if all fail, it falls back to keyword analysis so the character keeps reacting.
- Rate limits per device, account, IP and globally, and users can connect their own Gemini key to raise their limit.
- Explicit opt-in consent before any conversation leaves the device; without it, analysis runs on the device only.

**5. Unlocking a costly feature without payments**
As a solo developer I couldn't add payments, so live chat is unlocked with a **partner code**. A request in the app emails the operator a signed approval link; approving it automatically emails the code to the applicant. No state is stored on the server.

**6. Architecture**
Split into Model (`lib`, server-only code in `lib/server`), ViewModel (custom hooks) and View (components). Conversation sources (Gemini Live over WebSocket, OpenAI Realtime over WebRTC, the Chrome extension over postMessage) share one `ConversationSource` interface, so the character reacts the same way whatever the source.

## Try it

- **Web:** https://hello-resona.vercel.app (Chrome on desktop or mobile recommended; allow the mic when asked)
- **Live chat with AI:** in the app, open "Live chat with AI" and request access; the partner code arrives by email.
- **Android app or source code access:** please get in touch.

## Contact

Yeji Yeo · yejiyeo@gmail.com
