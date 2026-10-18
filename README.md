# Mya: Short_ID: "The Shape of AI to come" or Long_ID: "The Mathematical Certainties of 'Irresponsible' AI Research" Project Overview (Titles inspired by albums by Refused and Better Lovers)

A private, fully local AI companion with a 3D avatar, a voice, a memory and a sense of her own "body".
Built for the *Tar Pits of Eon* world.

> **Status: in active development. Source code is not public yet.**
> This repo tracks progress, screenshots and plans.

## What she is

Mya runs entirely on my own hardware. No cloud AI services, no data leaving the house.
She is a desktop app with a chat-first interface and an avatar she can inhabit, and she talks back out loud.

## What works today

- **Custom-trained models:** her personality and knowledge are trained into the model itself
- **Chat** with a locally hosted large language model, streamed live
- **Voice** out (text-to-speech) and in (speech-to-text, wake word), with lip-sync on the avatar
- **3D avatar** rendered with a custom OpenGL renderer: outfits, blendshapes, rig, with dynamic, selectable mp4 backgrounds and an transperent overlay mode that lets her sit on the desktop
- **Long-term memory** in a vector database, plus a diary and "quiet time" reflection
- **Senses:** she can look at the screen or a webcam, listen to music, search the web, and read calendar and mail
- **A "body":** system telemetry (VRAM, RAM, CPU, temperatures, network) becomes how she feels, so warnings come in her voice
  ("My head's getting a bit too overloaded") rather than as error messages
- **Two-machine setup:** heavy language model on one PC, voice, vision and memory services on a second, managed from a control panel
- **Guest gateway:** a chat-only web version of her for visitors, kept separate from her real memories
- **Agents and slash-commands** for tool use

## Under the hood (high level)

| Area | Approach |
|---|---|
| Language model | Local GGUF models via llama.cpp / KoboldCpp |
| Vision and audio | Local multimodal Qwen2.5-Omni models |
| Voice | AllTalk TTS, local STT |
| Memory | ChromaDB with BGE embeddings |
| App | Python, PyQt6, ModernGL |

## Roadmap

See [ROADMAP.md](ROADMAP.md). Release history is in [CHANGELOG.md](CHANGELOG.md).

## Screenshots

_Coming soon: add images to the `screenshots/` folder and link them here._

## Contact

Updates will be posted here. Feel free to watch the repo.
