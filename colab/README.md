# Véore Colab Video Generator — README

**File:** `veore-colab-video.ipynb`
**What it does:** Generates short vertical product clips (~3 s, 9:16) with an open-source AI video model, running on Google Colab's **free** T4 GPU. You run it yourself in the browser, download the MP4, and send it back for QC/editing.

---

## Which model, and why

**Chosen: LTX-Video 2B** (by Lightricks), loaded with the Hugging Face `diffusers` library from the model repo **`Lightricks/LTX-Video`**.

Why this one:

- **It actually fits the free T4.** The diffusers documentation shows LTX-Video running in about **10 GB of VRAM** with memory-saving settings; the notebook adds sequential CPU offloading + fp16, so it stays inside the free tier's ~16 GB VRAM and ~13 GB RAM.
- **It is fast for its size.** LTX was designed for near-real-time generation. With the notebook's low step count (25), a 3-second clip takes roughly **3–7 minutes** on a free T4.
- **Text-to-video AND image-to-video** in one model — you can animate a real product photo as the first frame, which is much better for product ads.
- **License allows commercial use** (Open RAIL-M for the 2B model), so clips can be used in ads.

Why not the others:

- **Wan 2.1 T2V-1.3B** (Alibaba) — good quality and only 8.19 GB VRAM, but it needs ~50 steps: the official figure is ~4 minutes for a 5 s 480p clip on an RTX 4090, and a free T4 is several times slower, so each clip would take far longer than LTX.
- **CogVideoX-2B** — light on VRAM, but it generates only **720×480 landscape**, so it cannot make proper vertical 9:16 clips.

Sources:
- Model page: https://huggingface.co/Lightricks/LTX-Video
- Diffusers pipeline docs (incl. memory figures): https://huggingface.co/docs/diffusers/main/api/pipelines/ltx_video
- A working free-T4 Colab LTX notebook (proof it runs on T4): https://github.com/Isi-dev/Google-Colab_Notebooks/blob/main/LTX_Video_Tx_to_Vid.ipynb
- LTX on Colab/ComfyUI overview: https://stable-diffusion-art.com/ltx-video-0-9-5/
- Model/VRAM comparison with official figures: https://viralmint.net/blog/run-ai-video-generation-locally/
- Wan 2.1 1.3B hardware figures: https://www.blog.brightcoding.dev/2026/04/21/wan21-revolutionary-video-ai-that-runs-on-your-gaming-gpu

---

## How to open it in Colab

**Option A — upload (easiest)**
1. Go to `colab.research.google.com` and sign in with your Google account.
2. Menu **File → Upload notebook**, and choose `veore-colab-video.ipynb`.

**Option B — from GitHub**
1. Put the notebook file in any of your GitHub repositories.
2. In Colab, **File → Open notebook → GitHub tab**, paste the repository URL, and pick the notebook.

## Set the GPU (important, do this first)

**Runtime → Change runtime type → Hardware accelerator: T4 GPU → Save.**
If you skip this, Step 1 stops with an error message telling you the same thing.

## Run it

1. **Runtime → Run all.** Steps 1–3 check the GPU, install libraries (~1–2 min), and download + load the model.
2. The model download is **~28 GB** and takes a few minutes — **only on the first run of a session** (see limits below).
3. Edit the **PROMPT** at the top of **Step 4** and run that cell to generate a clip. For more clips, change the prompt or the `SEED` and re-run **only Step 4**.
4. **Step 5** shows a video preview inside the notebook and downloads the MP4 to your device.

### Settings in Step 4 (defaults)

| Setting | Default | Meaning |
|---|---|---|
| `MODE` | `text-to-video` | Or `image-to-video` to animate an uploaded photo |
| `WIDTH` × `HEIGHT` | 448 × 800 | Vertical 9:16 (both must divide by 32; swap for landscape) |
| `NUM_FRAMES` | 73 | ≈3 s at 24 fps. 97 ≈ 4 s, 121 ≈ 5 s (must be 8k+1) |
| `STEPS` | 25 | 20–30 is the free-T4 sweet spot |
| `SEED` | 42 | Same seed = same clip. `-1` = random |
| Output | `/content/veore_clip.mp4` | Also visible in Colab's Files panel (left sidebar) |

Prompt tips: write **long, detailed English prompts** — product, one clear action, camera style ("static camera", "slow push-in"), and lighting. One action per clip works best. Example prompts for the LED bar and the under-sink organizer drawer are the kind of wording that works well; the notebook starts with an LED-bar example.

## Free-tier limits to expect (please read)

- **Sessions reset.** Free Colab disconnects after ~90 minutes idle and at most ~12 hours total. When a session ends, everything in `/content` is deleted — **download your clips before closing the tab**.
- **The ~28 GB model download repeats every new session.** Tip inside Step 3 of the notebook: mount Google Drive and set `HF_HOME` to a Drive folder so the model is cached between sessions (uses ~28 GB of Drive space).
- **GPU availability varies.** At busy times Google may give you no GPU or a slower start; just try again later.
- **Speed:** expect about **3–7 minutes per 3-second clip** at the default settings on a T4. Bigger resolution or more frames = slower, and can run out of memory — if that happens, drop to 384×672 or 49 frames, restart the runtime, and run Steps 3–4 again (this is also written inside the notebook).
- **Quality:** this is a small open model, not Veo/Flow quality. Clips are best used as short B-roll/product-action shots inside an edited video — exactly the workflow of generating clips and sending them back for QC and editing.

---

*Prepared for Véore (veore.shop). Model: Lightricks LTX-Video 2B (`Lightricks/LTX-Video`) via `diffusers` 0.42.1.*
