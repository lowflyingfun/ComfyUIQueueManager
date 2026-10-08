# ComfyUI Queue Manager

A Windows desktop app for building and running a batch queue of Flux.1 Kontext image edits against a running ComfyUI instance.

Pick an image, write an edit instruction, add it to the queue, and repeat. When you're ready, run the entire batch and let the app handle the rest.

## Why use it?

- Build repeatable queues of image edits
- Keep prompts and model settings together with presets
- Run large batches without manually re-submitting each job
- Resume safely if the app closes mid-run
- Move a queue between PCs without losing the source image list
- Works with or without a local ComfyUI install

## What you get

This download includes two Windows programs:

- `ComfyQueueManager.exe` — the queue builder and runner
- `CreateDummyComfyUI.exe` — optional helper to create a fake ComfyUI folder for preparing queues on a PC without ComfyUI installed

No Python required. Windows 10/11, 64-bit.

## Install

1. Download `ComfyQueueManager.exe` from the Releases page.
2. If needed, also download `CreateDummyComfyUI.exe`.
3. Put the EXEs in a normal writable folder, for example `C:\Tools\ComfyQueueManager\`.
4. Avoid placing them in `Program Files`.
5. Double-click `ComfyQueueManager.exe`.

On first launch, the app creates these folders/files next to the EXE:

```text
Input\            source images + queue.csv
Output\           finished edits
setup.ini         settings
presets.json      saved presets (created when you save one)
queuemanager.log  diagnostic log for bug reports
```

> Windows SmartScreen / antivirus: unsigned EXEs built with PyInstaller are often flagged. If Windows shows "Windows protected your PC", click More info → Run anyway.

## Set up your environment

### Option A: You have ComfyUI on this PC

#### Requirements

- A current ComfyUI install
- Flux Kontext nodes: `FluxKontextImageScale` and `ReferenceLatent`
- Required model files in the ComfyUI `models` folders:
  - Flux Kontext dev model in `diffusion_models`
  - `clip_l.safetensors` in `text_encoders`
  - T5-XXL encoder in `text_encoders`
  - `ae.safetensors` in `vae`
  - optional upscale models in `upscale_models`

#### Steps

1. Start ComfyUI normally. Default URL: `http://127.0.0.1:8188`
2. Start `ComfyQueueManager.exe`
3. Confirm the connection indicator at the top turns green: `ComfyUI: Connected`
4. Open Settings and check:
   - `ComfyUI URL` if ComfyUI is not running on port `8188`
   - CLIP-L / T5-XXL / VAE file names match the real files in your ComfyUI install
5. Continue with the workflow below

### Option B: You do not have ComfyUI on this PC

This is useful when you want to prepare a queue on a weaker machine and run it later on a stronger one.

1. Run `CreateDummyComfyUI.exe`
2. Leave the defaults or adjust:
   - fake ComfyUI folder (default: `C:\ComfyUI`)
   - Queue Manager folder (normally the folder containing the EXEs)
   - model, T5, and upscaler names
3. Click `Create Dummy Tree`
4. Start `ComfyQueueManager.exe`
5. Build the queue; it cannot be executed until a real ComfyUI instance is available
6. Move the queue to the real machine when ready

> Important: the model, T5, and upscaler names saved in the queue must match the real files on the machine that runs the jobs. Otherwise ComfyUI rejects the job.
>
> Do not use the dummy tool if you already have a real ComfyUI at that path.

## Using the queue

1. Pick an image using Browse Input… or by dropping images into the `Input` folder
2. Step through images with the left/right buttons or keyboard arrows
3. Write the edit instruction, for example: `change the sky to sunset, keep everything else`
4. Choose the Kontext model; press refresh if you just started ComfyUI
5. Set the denoise value:
   - `100%` is a standard Kontext edit
   - lower values stay closer to the input image
6. Optionally choose an upscale mode: Off, 2x, or 4x
7. Set the output path; default is `Output\<name>_out.png`
8. Use Advanced settings for steps, Flux guidance, sampler, scheduler, seed, and T5 encoder
9. Click `+ Add to Queue`
10. Repeat for additional images
11. Click `▶ Execute Queue` to process the batch

Each queue row turns green when completed and red on error.

## Queue controls

- Stop cancels the current job and leaves it pending so it can be retried
- Right-click a row to load it back into the form, duplicate it, reset it, or remove it
- Double-click a row to load it into the form
- Use the arrow keys to reorder jobs
- `Clear Done/Error` tidies up completed and failed entries
- Click a column header to sort
- Presets save and reload a prompt plus settings combination
- The queue is saved automatically
- Closing the app mid-run is safe; interrupted jobs return to pending

## Results and portability

- Results are saved to your chosen output folder
- ComfyUI also keeps its own copy in its own `output` folder
- Queue entries store image paths relative to the `Input` folder, so a queue is portable

### Moving a queue to another PC

1. On the first PC, place all source images in the `Input` folder
2. Zip the entire `Input` folder (which contains `queue.csv`) and copy it over
3. On the second PC, extract it into the Queue Manager's `Input` folder
4. Alternatively, use `File → Import Queue CSV…`
5. Start ComfyUI, then execute the queue

## Troubleshooting

| Problem | Fix |
| --- | --- |
| Status stays red: Offline | Start ComfyUI and check Settings → ComfyUI URL. |
| Model dropdown shows "(ComfyUI offline…)" | Start ComfyUI and press refresh, or set Settings → ComfyUI folder so models are read from disk. |
| Jobs turn red | A dialog will list the reason. Common causes include model, CLIP, T5, or VAE file names not matching the installed files, or an outdated ComfyUI version missing the Kontext nodes. |
| Dummy models do not appear | Make sure the dummy tool is pointed at the folder containing the EXE, then restart the Queue Manager. |
| App cannot save settings | Move the app out of `Program Files`. If the folder is not writable, it falls back to `%APPDATA%\ComfyQueueManager`. |

## Project summary

ComfyUI Queue Manager is a simple Windows utility for batching Flux.1 Kontext edits in ComfyUI. It is designed to make repeatable image workflows faster, easier to manage, and more portable across machines.

For support or bug reports, attach `queuemanager.log` when possible.
