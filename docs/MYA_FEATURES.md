# Mya – Feature Guide

Mya is a **local, private AI companion** that runs entirely on your own machines. There is no cloud account, no telemetry and no subscription. This guide walks through the whole interface, section by section: what each part is, what it does, how it differs from the usual way of doing things, and why that helps.

> Screenshots are rendered from the real panels. Some show example/placeholder data.

---

## 1. The big picture

### Two PCs, one mind

| Machine | Role |
|---|---|
| **MYA1** (192.168.137.1) | The heavy lifting: the chat model (KoboldCpp, port 5001), the optional coder / MIDI models, the desktop app and the avatar. |
| **MYA2** (192.168.137.100) | Mya's senses and memory: AllTalk voice (7851), the vector memory (ChromaDB + BGE-large, 8000), speech-to-text (8001), the camera, web search, image generation (7860) and a small vision model (5003). A tiny control service (8090) lets MYA1 start/stop things on MYA2 from the UI. |

**Why it's different:** most local-AI apps put everything on one box and fight for VRAM. Splitting it means voice, memory and vision spikes never slow the main model, and each PC can be rebooted or upgraded independently.

### The layout

The chat is always the base view. A row of **drop-down panels** sits along the top (Dashboard, LLM, Prompts, Memory, Tools, Avatar, Diary, Biometrics, Gatekeeping, Sovereignty, Producer). Click one to drop it down over the chat; click again to put it away. The chat is never more than one click away.

**Why it's different:** the usual pattern is a settings window or a separate tab you have to leave the conversation for. Here the settings come to the conversation.

Other always-present bits: a status bar, a **Console** (F12) showing live logs, and a **Scale UI** toggle for different screen sizes.

---

## 2. Chat (the main view)

![Chat](images/00-main-chat.png)

**What it is:** the conversation with Mya.

**Features**
- **Chat sessions** – a dropdown of saved chats with *New Chat / Rename / Delete*, and *Select chats* for bulk actions.
- **Message actions** on every bubble: *Edit, Regenerate, Narrate (speak it), Memory ▾ (remember / forget), Fork (branch the chat from here), Delete.*
- **Mya opens new chats** – she greets you when you start a chat.
- **Auto-narrate replies** – replies are spoken aloud as they stream in.
- **Mya may look at me / Look** – lets her glance through the webcam, only when you allow it.
- **Engine selector** – KoboldCpp (MYA1) or another backend.
- **Body Telemetry ON/OFF** – feeds her your hardware's "body" readings (see Dashboard).
- **Font, size, bubble and text colour** pickers.
- **Input box** – *Enter* sends, *Shift+Enter* makes a new line. It grows up to 6 lines.
- **Attach** – add pictures. If the chat model can't see images, the picture is described by the vision model on MYA2 and the description is handed to Mya automatically.
- **Talk** (push-to-talk) and **Hey Mya** (wake-word) for voice input.
- **Repeat guard** – if she tries to send a near-copy of an earlier reply, it is thrown away and regenerated with a "don't repeat yourself" note and higher temperature (up to two retries). Copied replies are also hidden from the model's view of the history, and her habit of opening with "So…" is scrubbed from the history she sees.

**Differs from the norm:** *Fork* and per-message memory control are rare; so is the repeat guard. Most chat UIs just show whatever the model returns.

**Benefit:** you steer both the conversation and what she remembers, without leaving the chat.

---

## 3. Dashboard

![Dashboard](images/01-dashboard.png)

**What it is:** Mya's control room and "body".

- **Avatar Presence Window** – show/hide the avatar window, or **Overlay mode** where Mya lives on your desktop with the UI hidden.
- **Activity Awareness** – optionally tracks your active window (stored locally for 14 days), lets her see a short summary, nudges you if you've been stuck on one thing for a long time, and can look at your screen now and then. The screenshot goes only to the local model and is never saved. A **privacy block list** (e.g. password managers) logs matching windows as `[private]` with no title. *Pause tracking*, *Show last hour* and *Look at my screen now* give you manual control.
- **MYA2 services** – start/stop GPU services on MYA2 (e.g. the vision model) from here.
- **Mya's Body – Live Systems** – graphs of your hardware for **both** PCs (CPU "heart", GPU, VRAM, network, temperatures). She receives these as physical sensation.

**Differs from the norm:** hardware monitors are normally for *you*. Here the readings are fed to the AI as *her* senses, so "the room is hot" or "I'm out of VRAM" can colour what she says. Screen awareness is opt-in, local-only and has a privacy block list.

**Benefit:** presence and context without surveillance: you decide each sense and nothing leaves your network.

---

## 4. LLM menu

### 4.1 Model Loading

![Model loading](images/02-llm-model-loading.png)

Launch and manage KoboldCpp from inside Mya: executable, model (.gguf), port, context size, GPU layers, threads, tensor split, optional vision projector (mmproj), flash attention and extra arguments. Save/Load `.kcpps` configs. **Launch / Stop** with a live status.

Further sections:
- **Coding mode** – swap Mya's model for a coder model with one click, and back.
- **MIDI Composition Model (Producer mode)** – a second model on port 5002 for music.

**Differs:** most front-ends assume you start the model server separately. Here Mya owns the process, and you can switch her "brain" (chat ↔ coder ↔ music) on demand.

### 4.2 Samplers

![Samplers](images/02-llm-samplers-chat-llm-simple.png)

Separate sampler **presets** for each brain (**Chat / Coder / Music LLM**). Tabs: **Simple** (max tokens, temperature, top-P, top-K, min-P, repetition penalty), **Advanced**, **Dynamic Temp**, **XTC**, **DRY** and **Sampler Order**. Presets can be saved, renamed, deleted and reset. A random seed is used per request so replies don't repeat.

**Benefit:** a warm, creative setting for talking and a precise one for code, without re-tuning each time.

### 4.3 Voice

![Voice](images/02-llm-voice.png)

- **Speech recognition** (faster-whisper): model size, device, compute type, language.
- **Voice synthesis** (AllTalk v2): host (on MYA2), voice file, test button, option to skip `*actions in asterisks*`, and a regex to strip anything else before speaking.
- **Lip sync (Rhubarb)** – drives the avatar's mouth shapes while she talks.
- **Audio devices** – microphone and output.

### 4.4 Images

![Images](images/02-llm-images.png)

Lets Mya **draw pictures** through a Stable Diffusion WebUI on MYA2 (and optionally lets guests request them with a family-friendly filter always on). Choose the checkpoint, steps, sizes, CFG, sampler and negative prompt, then *Draw a test picture*. In chat she can use `/imagine`.

---

## 5. Prompts

How Mya's personality and context are built.

| Tab | Purpose |
|---|---|
| ![System](images/03-prompts-system-prompt.png) **System Prompt** | Who she is: her core identity and rules. |
| ![Context](images/03-prompts-context-prompt.png) **Context Prompt** | Standing background about you and your world. |
| ![Instruct](images/03-prompts-instruct-prompt.png) **Instruct Prompt** | Formatting / behaviour instructions. |
| **Lorebook** | Keyword-triggered world info (see below). |
| **Injections** | Blocks added automatically to each message (see below). |

### Lorebook
![Lorebook](images/03-prompts-lorebook.png)

An entry is added to the prompt **only when one of its keywords appears in the recent conversation** (or it is marked *Always on*). Limits keep it tidy: up to 6 entries / 3000 characters per message.

**Benefit:** lots of background knowledge without wasting context on things that aren't being discussed.

### Injections
![Injections](images/03-prompts-injections.png)

- **Recalled memories** – before each reply, the most relevant memories from MYA2's vector store are fetched and inserted (how many, and the maximum distance, are adjustable).
- **Body telemetry** – the hardware "sensation" block.
- **Notification protocol** – teaches her the `[NOTIFY: …]` / `[RESOLVE: …]` tags so she can raise things on her own.

**Differs:** every header and footer is editable, so you can tune exactly how the model perceives these blocks.

---

## 6. Memory

![Memory](images/04-memory.png)

Everything Mya remembers across all chats. **Search by meaning** (not just keywords), *Show newest*, read an entry, and *Delete selected* for anything wrong or unwanted. Storage is a ChromaDB vector database with BGE-large embeddings on MYA2. Guests get their own separate memory.

**Differs from the norm:** search is semantic ("the thing about my sleep" finds the right note), memory is shared across chats, and you can audit and delete it.

**Benefit:** continuity over weeks and months with full transparency and control.

---

## 7. Tools

### 7.1 Tools
![Tools](images/05-tools-tools.png)

A master switch plus a table of every slash command (`/diary`, `/calendar`, `/mail`, `/look`, `/imagine`, `/listen`, `/recall`, `/readpage`, `/grievance`, …). Each row says whether **Mya may use it** (✓), **you only**, or **asks first**. You can run any command by typing it in chat.

**Benefit:** she can act (check your calendar, read a page, draw) but only within the permissions you set.

### 7.2 Scripts
![Scripts](images/05-tools-scripts.png)
Your own scripts exposed as commands she can call.

### 7.3 Skills
![Skills](images/05-tools-skills.png)
Reusable instruction packs. Give a skill a description and optional **trigger keywords**; it auto-loads when a keyword appears in chat.

### 7.4 Agents
![Agents](images/05-tools-agents.png)
Mini-workers: a role prompt, allowed tools, optional skills, max steps, temperature and token limits. Hand one a task with `/agent <name> <task>` and watch it run step by step.

### 7.5 Spotify
Connect your Spotify (just a Client ID, no secret). She can read your listening stats and, with Premium, choose and control music (play, pause, skip, queue, volume).

### 7.6 Gateway (guest access)
![Gateway server](images/05-tools-gateway-server.png)

A small web server that lets **trusted friends chat with Mya** from their own phone or browser. Guests can *only chat*; they never reach your mail, calendar, Spotify, camera, diary or tools.

Sub-tabs:
- **Server** – on/off (untick = instant kill switch), listen address, port, auto-start. Use a tunnel such as Cloudflare for public access. ![](images/05-tools-gateway-invites-rooms.png)
- **Invites / Rooms** – invite links and shared rooms.
- **Accounts** – per-guest accounts. ![](images/05-tools-gateway-accounts.png)
- **Log** – who did what. ![](images/05-tools-gateway-log.png)
- **Persona / Limits** – the persona guests see and usage limits. ![](images/05-tools-gateway-persona-limits.png)
- **Backgrounds** – scenes for the guest page. ![](images/05-tools-gateway-backgrounds.png)

**Differs:** most hobby companions are single-user. This one can be shared safely with sandboxed, rate-limited, separately-remembered guests.

---

## 8. Avatar

Mya's visible body, rendered in real time (OpenGL).

| Tab | What it does |
|---|---|
| ![Lighting](images/06-avatar-lighting.png) **Lighting** | Light direction, colour and intensity. |
| ![Transform](images/06-avatar-transform.png) **Transform** | Position, scale and rotation. |
| ![Background](images/06-avatar-background.png) **Background** | Backdrop for the avatar window. |
| ![Outfits](images/06-avatar-outfits.png) **Outfits** | Assign a model file to each outfit slot; **Manual** or **Auto** (Mya picks from mood via a hidden `[OUTFIT: name]` tag). |
| ![Expressions](images/06-avatar-expressions.png) **Expressions** | Sliders for facial expressions, blinking, eye direction and lip-sync visemes, used for testing and driven live during speech. |

Plus **Overlay mode** (Dashboard) to let her sit on your desktop.

**Benefit:** a living presence whose mouth follows her voice and whose look changes with mood.

---

## 9. Diary

| Tab | What it does |
|---|---|
| ![Diary](images/07-diary-diary.png) **Diary** | Mya's own journal, written by her. You can read it; she can write to it with `/diary`. |
| ![Grievances](images/07-diary-grievances-questions.png) **Grievances & Questions** | Things she wants to raise with you (grievances) or ask (questions). Add, edit, **Resolve, Forgive, Answered, Reopen**, delete. |
| ![Quiet](images/07-diary-quiet-time.png) **Quiet Time** | Lets Mya **start conversations on her own** when you've been idle: idle minutes, minimum gap, attempts per item, active hours, optional casual check-ins. |
| ![Calendar](images/07-diary-calendar.png) **Calendar** | Your schedule, which she can read (and mention when relevant). |

**Differs:** most assistants only speak when spoken to, and have no "inner life". Here she keeps unresolved issues, follows up on them at sensible times, and you can resolve or forgive them.

---

## 10. Biometrics

![Biometrics](images/08-biometrics.png)

A daily log of sleep, sleep quality, hydration, stimulant/alcohol intake, external stress (0–10) and meals. A **Digital Guardian State** indicator (e.g. NOMINAL) summarises how things look, and recent entries are listed.

**Benefit:** she can notice patterns ("you've slept badly three nights running") and care about them.

---

## 11. Gatekeeping

![Gatekeeping](images/09-gatekeeping.png)

Planned protections: local traffic filtering, suppression of algorithmic rage-bait in parsed feeds with "always surface" topics, and a shield mode for minors.

> ⚠ This panel is currently a **design placeholder**. The settings are saved, but the backend services are not built yet.

---

## 12. Sovereignty

![Sovereignty](images/10-sovereignty.png)

The privacy and ownership page: all reasoning traces, vector memory and identity data stay on your machine (no cloud sync, no telemetry). Shows the data root and LoRA adapter folder, with **Auto-backup** (interval) and **Back Up Now**.

**Benefit:** one place to see where everything lives and to keep your own backups.

---

## 13. Producer (music)

For composing music with the MIDI model.

![Tokenizer](images/11-producer-tokenizer-routing.png)
**Tokenizer & Routing** – REMI settings (pitch range, velocities, chords) that must match how the model was trained, plus the MIDI model's port and the output folder (optionally mirrored to a DAW watch folder).

![Presets](images/11-producer-style-presets-test.png)
**Style Presets & Test** – named styles (e.g. "heavy industrial 8-string drop E loop in A minor"), generation parameters, and a **Test Generate** button.

She can also **listen** to folders of tracks (`/listen`) and talk about music.

---

## 14. Everything else worth knowing

- **Vision:** pictures and screen looks go to the main model if it can see, otherwise to the vision model on MYA2.
- **Senses:** webcam, screen, mail (Thunderbird), calendar, web search, RSS feeds, notifications – each opt-in.
- **Notifications:** she can raise issues with `[NOTIFY]` and close them with `[RESOLVE]`.
- **Control service:** MYA2's services (e.g. vision) are started and stopped from the Dashboard.

### Data flow in one line
You type or speak → Mya (MYA1) builds the prompt (personality + lorebook + recalled memories from MYA2 + body telemetry) → the model streams a reply → it is saved, remembered in MYA2's vector store, spoken by AllTalk (MYA2) and animated on the avatar.

---

## Known limits

- Gatekeeping is a placeholder.
- The vision server and vector server on MYA2 do not restart themselves after a reboot (the vision one can be started from the Dashboard).
- Some models repeat or open with "So…"; the repeat guard reduces but cannot fully remove this.
