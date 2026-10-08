ComfyUI Queue Manager
A Windows desktop tool for building and running a batch queue of Flux.1 Kontext [dev] image edits against a running ComfyUI. Pick an image, write an edit instruction, add it to the queue, repeat, then run the whole queue unattended.
The download contains two programs:
Program	What it does
ComfyQueueManager.exe	The queue builder / runner.
CreateDummyComfyUI.exe	Optional helper. Creates a fake ComfyUI folder so you can build queues on a PC that can't run ComfyUI.
No Python needed. Windows 10/11, 64-bit.
---
Install
Download ComfyQueueManager.exe (and CreateDummyComfyUI.exe if you need it) from the Releases page.
Put the exe(s) together in a normal folder you can write to, for example `C:\\\\Tools\\\\ComfyQueueManager\\\\`. Avoid `Program Files`.
Double-click ComfyQueueManager.exe.
On first launch it creates these next to the exe:
```
Input\\\\             your source images + queue.csv
Output\\\\            finished edits
setup.ini          settings
presets.json       saved presets (created when you save one)
queuemanager.log   diagnostic log, attach this when reporting a bug
```
> \\\*\\\*Windows SmartScreen / antivirus:\\\*\\\* unsigned exes built with PyInstaller are often flagged. If Windows shows "Windows protected your PC", click \\\*\\\*More info → Run anyway\\\*\\\*. If your antivirus quarantines it, add an exception.
---
Which setup are you?
A) You have ComfyUI on this PC (normal use)
Requirements
ComfyUI, up to date. It needs the Flux Kontext nodes (`FluxKontextImageScale`, `ReferenceLatent`).
These files in your ComfyUI `models` folders:
a Flux Kontext dev model (`diffusion\\\_models`)
`clip\\\_l.safetensors` and a T5-XXL encoder (`text\\\_encoders`)
`ae.safetensors` (`vae`)
optional: upscale models (`upscale\\\_models`)
Steps
Start ComfyUI as usual (default address `http://127.0.0.1:8188`).
Start ComfyQueueManager.exe. The dot at the top turns green: ComfyUI: Connected.
Open Settings and check:
ComfyUI URL: change it if your ComfyUI isn't on port 8188.
CLIP-L / T5-XXL / VAE file: must match the real file names in your ComfyUI.
Carry on with Using the queue below.
B) You do NOT have ComfyUI on this PC (build queues only)
Use this on a weak PC to prepare a queue that you run later on a stronger machine.
Run CreateDummyComfyUI.exe.
Leave the defaults, or edit them:
the fake ComfyUI folder (default `C:\\\\ComfyUI`)
the Queue Manager folder (where `setup.ini` is written, normally the folder where you saved the exes)
the model, T5 and upscaler names
Click Create Dummy Tree.
Start ComfyQueueManager.exe. Your dummy models now appear in the dropdowns.
Build your queue. You can't execute it here. Execute needs a real ComfyUI, and the program will tell you it's offline.
Move the queue to the real machine (see Moving a queue to another PC).
> \\\*\\\*Important:\\\*\\\* the model, T5 and upscaler names saved in the queue must match the real file names on the machine that runs it, otherwise ComfyUI rejects the job. Use your real file names in the dummy tool.
>
> \\\*\\\*Don't use the dummy tool if you already have a real ComfyUI at that path.\\\*\\\* It never overwrites existing files, but it still repoints `setup.ini` and adds a marker file there.
---
Using the queue
Pick an image. Use Browse Input…, or drop images into the `Input` folder and step through them with ◀ ▶ (or the Left/Right arrow keys).
Write the edit instruction, for example "change the sky to sunset, keep everything else".
Choose the Kontext model. Press ↻ to rescan if you just started ComfyUI.
Denoise: 100% is a normal Kontext edit. Lower values stay closer to the input.
Upscale (optional): Off / 2x / 4x, with either a plain Lanczos resize or an upscale model.
Output path: defaults to `Output\\\\<name>\\\_out.png`. Existing files are never overwritten; names are auto-numbered (`\\\_0001`, …).
Advanced (▶ Advanced settings): steps, Flux guidance, sampler, scheduler, seed (`-1` = random each run) and T5 encoder.
Click ➕ Add to Queue. Repeat for more images.
Click ▶ Execute Queue. Progress shows next to the buttons. Each row turns green when done and red on error.
Queue controls
■ Stop cancels the current job and leaves it pending, so it can run again.
Right-click a row: Load into form, Duplicate, Reset to pending, Remove. Double-click also loads it into the form.
↑ / ↓ reorder, Clear Done/Error tidies up, and clicking a column header sorts.
Presets save and load a prompt plus settings combo.
The queue is saved automatically. Closing the app mid-run is safe: interrupted jobs go back to pending.
Results are saved to your chosen output path. ComfyUI also keeps its own copy in its own `output` folder.
Moving a queue to another PC
Image paths in the queue are stored relative to the `Input` folder, so a queue is portable:
On the first PC, put all source images in the `Input` folder.
Zip the whole `Input` folder (it contains `queue.csv`) and copy it over.
On the second PC, extract it over the `Input` folder of the Queue Manager. Alternatively use File → Import Queue CSV… on the `queue.csv`.
Start ComfyUI, then Execute Queue.
---
Troubleshooting
Problem	Fix
Status stays red: Offline	Start ComfyUI, and check Settings → ComfyUI URL.
Model dropdown shows "(ComfyUI offline…)"	Start ComfyUI and press ↻, or set Settings → ComfyUI folder so models are read from disk.
Jobs turn red	A dialog lists the reason. Common causes: model/CLIP/T5/VAE file name doesn't match what ComfyUI has, or ComfyUI is outdated and lacks the Kontext nodes. Full details are in `queuemanager.log`.
Dummy models don't appear	Make sure the dummy tool's Queue Manager folder is the folder containing the exe, then restart the Queue Manager.
App can't save settings	Move it out of `Program Files`. It falls back to `%APPDATA%\\\\ComfyQueueManager` if the folder isn't writable.
