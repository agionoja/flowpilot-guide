# FlowPilot — User Guide

FlowPilot is a Chrome extension that runs many prompts on **Google Flow** for you: it types
each prompt, waits for the result, and downloads every image or video with the file name you
chose. You write a list of prompts once; FlowPilot does the clicking.

> **Writing prompts with an AI assistant?** Give it the section
> [For AI assistants: how to write FlowPilot prompts](#for-ai-assistants-how-to-write-flowpilot-prompts).
> It contains the exact format FlowPilot expects.

## Contents

1. [Install](#install)
2. [Quick start](#quick-start)
3. [Modes](#modes)
4. [Prompt format](#prompt-format)
5. [For AI assistants: how to write FlowPilot prompts](#for-ai-assistants-how-to-write-flowpilot-prompts)
6. [Settings](#settings)
7. [Downloads](#downloads)
8. [Queue, Failed items and Retry](#queue-failed-items-and-retry)
9. [Tips for reliable runs](#tips-for-reliable-runs)
10. [Troubleshooting](#troubleshooting)
11. [Known limitations](#known-limitations)
12. [Privacy](#privacy)

## Install

1. Download the latest `flowpilot-vX.Y.Z.zip` from the releases page and unzip it.
2. Open `chrome://extensions`, turn on **Developer mode** (top right), click **Load unpacked**
   and choose the unzipped folder.
3. Pin FlowPilot and click its icon to open the side panel.
4. **Sign in with Google** in the panel for unlimited prompts. Without signing in you get 10
   prompts per day.

When a new version is released, the panel tells you. Download the new zip, replace the folder,
and click **reload ↻** on FlowPilot in `chrome://extensions`, then refresh the Flow tab.

## Quick start

1. Open a project on [Google Flow](https://labs.google/fx/tools/flow) (or any Flow page:
   FlowPilot creates a project when needed).
2. In the panel's **Control** tab, pick a mode (for example **Text to Image**).
3. Paste your prompts, one per block, separated by a **blank line**.
4. Choose the download quality and folder, then press **Run**.
5. Watch progress in the **Prompt Queue** and the **Debug Logs** tab. Files arrive in Chrome's
   download folder, inside the folder you set.

## Modes

| Mode | What it does | Inputs |
| --- | --- | --- |
| **Text to Video** | Creates videos from text. | Prompts. |
| **Frames to Video** | Animates a start image (optionally start + end image). | Prompts + images. |
| **Ingredients to Video** | Creates a video from several reference images (characters, objects, places). | Prompts + images. |
| **Text to Image** | Creates images from text. | Prompts. |
| **Image to Image** | Creates new images from reference images. | Prompts + images. |
| **Agent** | Lets Flow's AI agent produce images or videos from your request. | Prompts. |

Prompts can also be imported from a `.txt` file or a spreadsheet (`.xlsx`, `.csv`) with the
**Upload** buttons above the prompt box.

## Prompt format

### Prompts are blocks separated by a blank line

```text
A red apple on a white table, studio light.

A blue bicycle leaning on a brick wall, morning sun.
```

That is two prompts. A single line break stays inside the same prompt; only an **empty line**
starts a new one.

### `name=` sets the file name

Put `name=` on the **first line** of a block. The rest of the block is the prompt.

```text
name=scene-01
Wide shot of a small locksmith shop at dawn, warm light.
```

The downloaded file is `scene-01.jpeg` (or `.mp4` for videos). Rules:

- `name=` must be the very first line of the block, written exactly `name=` (lowercase, no
  space before it).
- The name may not contain `/ \ : * ? " < > |`, may not start or end with a dot or a space, and
  is at most 100 characters.
- Every name in a run must be different (upper/lower case counts as the same).
- A block with `name=` must have prompt text under it.
- With more than one output per prompt, files get a letter: `scene-01_a`, `scene-01_b`, …
- If a file with that name already exists, Chrome adds a number (`scene-01 (1).jpeg`); FlowPilot
  never overwrites files.

Prompts without `name=` are saved with FlowPilot's automatic names.

### Attaching your images by mentioning them

In modes that take images, upload your images in the panel. To attach an image to a prompt,
**write the image's file name (without the extension) in the prompt text**. FlowPilot attaches
it at that position.

Uploaded `bram.jpg` and `shop.jpg`:

```text
name=scene-02
bram stands behind the counter of the shop, holding a brass key.
```

Both `bram` and `shop` are attached. Matching ignores upper/lower case and needs the whole
word. Give images short, unique, one-word names (`bram`, `mira`, `shop-interior`) so they are
easy to mention and never match by accident.

**Characters** created in Flow can be attached the same way by name when **Auto-add
character** is on (use **Scan Characters** once in a Flow project to load them).

### Continuing from the previous result (concat)

In video modes, choosing a **concat** duration (for example `8s-concat`) makes the next prompt
start from the **last frame** of this prompt's video, so clips continue each other.

## For AI assistants: how to write FlowPilot prompts

Follow these rules exactly. FlowPilot parses them literally.

1. Output **plain text only**: no Markdown, no numbering, no bullet points, no quotes around
   prompts.
2. Separate prompts with **exactly one empty line**. Never put an empty line inside a prompt.
3. To name the output file, make the **first line** of the block `name=<file-name>`, with no
   space before `name=`. Use only letters, digits, `-` and `_` (for example `scene-03`,
   `shot_12b`). Never repeat a name in the same list. Always write prompt text under it.
4. To attach an uploaded image or a Flow character, write its exact name (the file name without
   extension) as a plain word in the prompt, where it fits the sentence. Don't add `@`.
5. Write each prompt as a complete, self-contained description (subject, setting, framing,
   lighting, style). The model does not see the other prompts.
6. Don't put settings (aspect ratio, model, duration, count) in the prompt text; they are set in
   the panel.

Template:

```text
name=<scene-01>
<Shot type and framing>. <character names> <action> in <location>. <Lighting>. <Style>.

name=<scene-02>
<Shot type and framing>. <character names> <action> in <location>. <Lighting>. <Style>.
```

Example (images `bram.jpg` and `mira.jpg` uploaded):

```text
name=shop-01
Wide shot of a small locksmith shop at dawn, wooden counter, keys on the wall, warm light, cartoon style.

name=shop-02
Medium shot, bram stands behind the counter holding a brass key, warm dawn light, cartoon style.

name=shop-03
Close-up of mira at the shop door, curious expression, warm dawn light, cartoon style.
```

Common mistakes FlowPilot rejects or mishandles:

- An empty line between `name=` and its prompt (the name ends up with no prompt text: FlowPilot
  blocks the run).
- Two prompts with the same `name=`.
- `name=` not on the first line, or with spaces or symbols like `/` or `:` in the name.
- Image names written with `@` or with the file extension (`bram.jpg`); write `bram`.

## Settings

| Setting | What it does |
| --- | --- |
| Default Mode | The mode selected when you start. |
| Default Agent Mode | Images or videos for Agent mode. |
| Default Aspect Ratio | Frame shape (16:9, 9:16, …). |
| Outputs per Prompt | How many images/videos each prompt creates (x1–x4). |
| Concurrent Prompts | How many prompts run at the same time. See [Tips](#tips-for-reliable-runs). |
| Random Delay | A random pause before each prompt (looks more human, eases rate limits). |
| Model / Image Model | Flow's video model / image model (for example Nano Banana 2.1). |
| Default Video Option | Duration (4s, 6s, 8s, 10s) and concat variants. |
| Default Image Mode Option | Create new, or the image concat option. |
| Max input images per prompt | How many uploaded images each prompt can use, per mode. |
| Max Retries on Failure | How many times a failed generation or download is retried (up to 20). |
| Auto Download Quality | Video 720p / 1080p / 4K, image 1K / 2K / 4K, or no download. |
| Save to folder | Sub-folder inside Chrome's download folder. |
| Auto change file name | Use `name=` and FlowPilot's names instead of Flow's own file names. |
| Language | Panel language (20 languages). |

## Downloads

- **1K** (images) and **720p** (videos) download directly. **2K/4K** and **1080p/4K** ask
  Flow to upscale first; FlowPilot shows progress and waits for the file.
- **4K needs a paid Flow plan.** On a free account the option is locked; FlowPilot reports
  "Flow refused 4K: this option needs a higher Google Flow plan".
- Every download step is checked and retried automatically, with growing pauses between tries
  (5 s, 10 s, 20 s), up to **Max Retries**. Only then does a download count as failed.
- Downloads run in the background: the next prompt starts while earlier files are still
  upscaling.

## Queue, Failed items and Retry

- **Prompt Queue** shows each run and its progress. The header counts only runs still active.
- **Failed items** lists every prompt that did not finish, with the reason. **Copy** puts the
  failed prompts back into text; **Retry** runs them again.
- **Debug Logs** shows every step. Use **Copy** to share a log when reporting a problem; **Clear**
  empties it (permanently).

## Tips for reliable runs

- **Concurrent Prompts 1–4.** Higher values are faster while Flow is healthy; if Flow slows down
  ("unusual activity"), use 1–2.
- **One browser per Google account.** Several browsers on the same account don't finish more
  work; they make Flow throttle you.
- **Don't click pictures in Flow while a run is typing.** A click opens Flow's editor. FlowPilot
  leaves the editor by itself, but a prompt typed into the editor fails and is retried.
- **Keep the Flow window visible**, or Chrome pauses the page in the background. On a hidden
  window or another desktop, start Chrome with
  `--disable-backgrounding-occluded-windows --disable-renderer-backgrounding --disable-background-timer-throttling`.
- **Use only one automation extension at a time** for Flow (two extensions that rename downloads
  interfere with each other).
- After reloading the extension, **refresh the Flow tab** before pressing Run.

## Troubleshooting

| You see | What it means | What to do |
| --- | --- | --- |
| "Failed to send job — Please refresh Flow page" | The Flow tab isn't connected to the current extension. | Refresh the Flow tab, then Run again. |
| "Not on a Flow Project Page" | The active tab isn't Flow. | Open Flow or press **Navigate to Flow**. |
| "Retrying Tile N in 10s" | A download step didn't happen (slow page). | Nothing; it retries by itself. |
| "Flow refused 4K: …needs a higher Google Flow plan" | Your Flow plan has no 4K. | Choose 2K/1080p or upgrade Flow. |
| "Flow confirmed the download but no file arrived" | Flow upscaled but never sent the file. | It retries; if it keeps failing, Flow is overloaded. |
| "Could not find tile IDs" / "Couldn't find this prompt's tile on Flow" | Flow didn't show a result for the prompt (rejected, slow, or the page changed). | It retries; check Flow for errors or credits. |
| "↩️ Left Flow's editor" | A click opened Flow's editor; FlowPilot went back. | Nothing. |
| "Generation did not complete within timeout" | Flow stayed at 99% too long. | Flow is slow; the prompt goes to Failed items. |
| "Couldn't leave Flow's editor" | The editor didn't close. | Click Flow's back arrow and refresh. |
| "We noticed unusual activity" (in Flow) | Flow is rate-limiting your account. | Lower Concurrent Prompts; use one browser. |
| "You're out of Google Flow credits" (in Flow) | Flow refuses generations. | Wait for credits or upgrade Flow. |
| The panel stops accepting typing (buttons still work) | A browser glitch. | Quit Chrome fully (⋮ → Exit) and reopen. |
| "Manual Update Extension Required" | A newer FlowPilot exists. | Install the new zip; if you already did, click reload ↻. |

## Known limitations

- **Reuse (use the previous image as input) doesn't work** on the current version of Google
  Flow. Use concat for videos, or upload the image and mention it by name.
- Images that mix several references can come out smudged; that comes from Flow's model, not
  from FlowPilot. Fewer or clearer references help.
- FlowPilot follows Flow's page. When Google changes Flow, some steps may need an update.

## Privacy

FlowPilot works inside your own browser and your own Google Flow account. Your prompts and
images are sent only to Google Flow. Signing in tells the FlowPilot service only your account
and your daily usage count.
