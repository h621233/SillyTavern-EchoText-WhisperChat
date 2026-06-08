<div align="center">

# 💬 EchoText — WhisperChat Fork

### Text your characters privately — and let the group and the private DMs actually share a memory.

A [SillyTavern](https://github.com/SillyTavern/SillyTavern) extension that adds a floating, iMessage-style chat panel for private conversations with your characters. **This is the WhisperChat fork**, which connects those private DMs to your group roleplay in both directions, with strict per-character isolation.

[![Version](https://img.shields.io/badge/version-1.4.0--whisper-blue.svg)](manifest.json)
[![SillyTavern](https://img.shields.io/badge/SillyTavern-Extension-orange.svg)](https://github.com/SillyTavern/SillyTavern)

</div>

---

## What this fork adds

WhisperChat is built on top of [mattjaybe's EchoText](https://github.com/mattjaybe/SillyTavern-EchoText) (all of EchoText's original features still work — see [Original EchoText features](#original-echotext-features) below). It adds four things that make private chat and group roleplay aware of each other:

| Feature | Direction | Default |
|---|---|---|
| **Group Chat Context** | Group → Private | On |
| **Reverse Injection** | Private → Group | Off |
| **Scene Direction** | You → next group round | (per-use) |
| **Multi-language (i18n)** | UI + injected prompts | Auto |

All of it respects **strict per-character isolation**: when you privately chat character A, A can see the group history and *its own* private history with you — but **never** A's private chat with character B.

---

### 1. Group Chat Context  (Group → Private)

When you privately text a group member, that character can sense what's recently happening in the group roleplay. The recent group chat is injected into the private-chat system prompt as background context (it does **not** pollute the reply bubbles — it's awareness, not script).

- Works in **Tethered** mode.
- Reads the **EchoText-selected group's actual chat** — it resolves the selected group explicitly and, if that group isn't ST's active main-window chat, fetches its messages via `POST /api/chats/group/get`. (This is the key difference from a naive `getContext().chat` read, which only sees the active window.)
- **Settings** (Settings → Context):
  - `Group Chat Context` — toggle (default **on**)
  - `Group Chat Max Messages` — how many recent group messages to include (default **20**)

### 2. Reverse Injection  (Private → Group)

When a character generates in the **ST group chat**, their private DM history with you is injected into *their* prompt — so they remember what you told them privately, and can act on it in the group (it's up to the model whether to reveal or keep it).

- Uses the standard, API-agnostic mechanism: listens for `GROUP_MEMBER_DRAFTED` (fires per member right before generation) and injects that character's private history via `setExtensionPrompt`, cleared on `GROUP_WRAPPER_FINISHED` so it never leaks into solo chats or other members.
- **Strictly per-character**: character A in the group only ever receives *your private chat with A*.
- **Setting**: `Reverse Injection` toggle (default **off** — turn it on if you want characters to remember private talks while in the group).

> **Hard vs soft secrecy:** EchoText private DMs are *hard*-isolated (other characters genuinely can't see them). An in-scene "whisper" typed inside the group chat is *soft* — it lives in the shared group log, so other characters technically have it in context. Use private DMs for real secrets; use in-scene whispers for flavor.

### 3. Scene Direction  (one-shot director's instruction)

Sometimes you want to steer the **next** group scene — e.g. *"skip to Saturday, everyone is at dinner, stop writing the after-school scene."* Open the panel's **⋮ menu → Scene Direction**, type the instruction, and **Apply · once**.

- Injected as a high-priority OOC stage direction at maximum recency (`IN_CHAT`, depth 0) into **every** member of the next group round, then **auto-consumed** after one round (one-shot semantics that match "next scene").
- A `●` badge on the menu item shows a directive is armed.

> **⚠️ Requires a narrator/scene character.** Scene Direction works best when your group contains a dedicated narrator/scene character — for example a member named **"Scene"**, **"场景"**, **"Narrator"**, or **"System"** — whose job is to describe the new environment. Trigger that character **first**: it establishes the new scene, and the other characters then follow it naturally. Without a narrator leading, individual characters may keep continuing the old scene. (The in-plugin hint in the Scene Direction popup says the same.)

### 4. Multi-language (i18n)

UI strings **and the prompts injected into the model** follow SillyTavern's UI language (`localStorage` `language` → browser language → English fallback). Ships with **English + Chinese**; add a locale by extending the `WHISPER_I18N` table in `index.js`.

---

## Installation

Install via SillyTavern's extension installer using this repository URL, or clone into your extensions folder:

```
.../SillyTavern/data/<user>/extensions/SillyTavern-EchoText/
```

This is a **pure front-end JS extension — no build step**. After updating files, hard-refresh SillyTavern (Ctrl+Shift+R). `auto_update` is **disabled** in `manifest.json` so upstream updates won't overwrite the fork.

---

## Quick start (WhisperChat)

1. Open a **group** in SillyTavern.
2. Open the EchoText floating panel (it shows the group's members along the bottom).
3. **Private chat** a member — they'll be aware of the group (Feature 1).
4. (Optional) Enable **Reverse Injection** in Settings → Context so private talks surface in the group (Feature 2).
5. (Optional) Add a **narrator/scene character** to the group and use **⋮ → Scene Direction** to jump scenes (Feature 3).

Console diagnostics (F12) are prefixed `[EchoText] [WhisperChat]` for each injection path.

---

## Credits

- Original **EchoText** by [mattjaybe](https://github.com/mattjaybe/SillyTavern-EchoText) — MIT licensed.
- WhisperChat fork additions by [h621233](https://github.com/h621233/SillyTavern-EchoText-WhisperChat).

---

## Original EchoText features

The WhisperChat fork keeps everything EchoText already does — a floating, draggable, resizable iMessage-style panel; Tethered & Untethered chat modes; per-character emotion/mood states; a memory system; proactive (self-initiated) messages; image generation & galleries; group sessions with per-character and combined modes; custom themes; world info / lorebook control; author's note & persona context; reasoning-tag stripping; import/export; and more. For the full original documentation, see the [upstream EchoText repository](https://github.com/mattjaybe/SillyTavern-EchoText).

## License

MIT — see [LICENSE](LICENSE).
