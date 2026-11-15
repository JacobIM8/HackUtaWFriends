# HackUTA — FaceTime Drawing Agent

Call a phone number on FaceTime, describe something ("draw a cat"), and the agent draws it live on its video feed while talking to you.

Built on [Chert](https://www.trychert.com) (gives the agent a real FaceTime/iMessage line) and [LiveKit Agents](https://docs.livekit.io/agents/) (runs the voice + video agent).

## What's here

| Path | What it is |
| --- | --- |
| `drawing-agent/` | Chert's video starter agent. Answers FaceTime, talks with OpenAI Realtime, and publishes a drawn face as its camera. |
| `drawing-agent/agent.mjs` | The agent: instructions, greeting, voice session, video track. |
| `drawing-agent/face.mjs` | Draws each video frame (RGBA pixels). **Replace this to draw what the caller asks for.** |
| `drawing-agent/greet.mjs`, `chert-rooms.mjs` | Chert plumbing: greet once FaceTime is ready, join the right rooms. Leave as is. |

## Setup (each teammate)

Needs Node.js 22, 24 or 26 (`brew install node` on a Mac).

1. Install dependencies:
   ```bash
   cd drawing-agent
   npm install
   ```
2. Create your env file and fill it in **privately** (never commit it, never paste keys in chat):
   ```bash
   cp .env.example .env.local
   ```
   - `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`: from one LiveKit Cloud project (free at https://cloud.livekit.io → Settings → Keys). All three must be from the same project.
   - `OPENAI_API_KEY`: needs Realtime API access and credits. This costs money per call minute.
   - `AGENT_NAME`: keep `drawing-agent` unless you run a second copy.
3. Sign in to Chert on your computer (once; the project owner approves it in the browser):
   ```bash
   npx --yes @trychert/facetime@0.1.0-beta.21 login
   ```

## Run it

Two terminals, both inside `drawing-agent/`, both kept running:

```bash
npm start
```

```bash
npx --yes @trychert/facetime@0.1.0-beta.21 dev --agent drawing-agent
```

Check first with `npx --yes @trychert/facetime@0.1.0-beta.21 doctor --agent drawing-agent`.

Then FaceTime the line: **+1 (646) 391-6614**. Only phones verified on the Chert project can call it.

## Things to know

- **Trial limits:** calls max out at 5 minutes, 50 FaceTime minutes total, and the trial expires about 24–48 hours after it started.
- **Shared line:** one call at a time. Ctrl+C on `chert dev` frees the line.
- **Greet fast:** the call drops if the agent is silent for 10 seconds after FaceTime is ready. The starter already handles this.
- **Known issue:** Chert's hosted demo calls were silent for us (no agent audio). Test our own agent early. If it's also silent, contact Chert support with the call IDs.
- Chert help: `npx --yes @trychert/facetime@0.1.0-beta.21 guide fix` · Docs: https://docs.trychert.com

## Plan

1. Get a call working with audio + the starter's face on video.
2. Replace `face.mjs` with a canvas that draws SVG-style strokes.
3. Have the model produce drawing paths for "draw a ___", and animate them stroke by stroke.
4. Voice edits ("make it bigger", "add a hat").
5. Text the finished drawing over iMessage after the call.
