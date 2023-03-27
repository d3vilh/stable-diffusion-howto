# stable-diffusion-mac
Run Stable Diffusion on your M1 Mac’s GPU

## Installation
1. Firts install all the dependencies via Homebrew ( Use [Brew.sh](https://brew.sh/) to install it ):
   ```bash
   brew update
   brew install cmake protobuf rust python@3.10 git wget curl
   ```

2. Clone Stable-Diffusion-WebUI:
   ```bash
   git clone https://github.com/d3vilh/stable-diffusion-webui
   ```

3. Open the downloaded project directory:
   ```bash
   cd stable-diffusion-webui
   ```

4. Download the Stable-Diffusion model. The latest and advanced one available at the moment is [version 2.1-768](https://huggingface.co/stabilityai/stable-diffusion-2-1) ( [v2-1_768-ema-pruned.ckpt](https://huggingface.co/stabilityai/stable-diffusion-2/resolve/main/768-v-ema.ckpt) ):
   ```bash
   curl -Lo models/Stable-diffusion/v2-1_768-ema-pruned.ckpt https://huggingface.co/stabilityai/stable-diffusion-2-1/resolve/main/v2-1_768-ema-pruned.ckpt
   ```
   >**Note:** Some specific Lora models may require earlier versions, links to which can be found below.

5. Download the model configuration file (applicable for **v.2.x models only**):
   ```bash
   curl -Lo models/Stable-diffusion/v2-1_768-ema-pruned.yaml https://huggingface.co/stabilityai/stable-diffusion-2-1/resolve/main/v2-inference-v.yaml
   ```

5. Run WebUI (this will take a while the first time you run it, as it will download and compile the dependencies):
    ```bash
    ./webui.sh
    ```
    >**Note:** If you get an error about the script not being executable, run `chmod +x webui.sh` and try again.

6. Once it's done, you can open the Web UI by going to http://localhost:7860 in your browser.

### Disabling the NSFW filter
By default, the Web UI will filter out any images that are flagged as NSFW. If you want to disable this, you can do so by adding the `--disable-nsfw` and `--disable-safe-unpickle` flags to the `webui.sh` script:
```bash
./webui.sh --disable-nsfw --disable-safe-unpickle
```

## Popular Stable Diffusion Models
If you don't have any models to use, Stable Diffusion models can be downloaded from [Hugging Face](https://huggingface.co/models?pipeline_tag=text-to-image&sort=downloads). To download, click on a model and then click on the Files and versions header. Look for files listed with the ".ckpt" or ".safetensors" extensions, and then click the down arrow to the right of the file size to download them.
Here are most popular modules to download:

[Stable DIffusion 1.4](https://huggingface.co/CompVis/stable-diffusion-v1-4) ( [sd-v1-4.ckpt](https://huggingface.co/CompVis/stable-diffusion-v-1-4-original/resolve/main/sd-v1-4.ckpt) )
[Stable Diffusion 1.5](https://huggingface.co/runwayml/stable-diffusion-v1-5) ( [v1-5-pruned-emaonly.ckpt](https://huggingface.co/runwayml/stable-diffusion-v1-5/resolve/main/v1-5-pruned-emaonly.ckpt) )
[Stable Diffusion 1.5 Inpainting](https://huggingface.co/runwayml/stable-diffusion-inpainting) ( [sd-v1-5-inpainting.ckpt](https://huggingface.co/runwayml/stable-diffusion-inpainting/resolve/main/sd-v1-5-inpainting.ckpt) )
Stable Diffusion **2.0** and **2.1** require both a `model` and a `configuration file`, and the image width & height will need to be set to 768 or higher when generating images:

[Stable Diffusion 2.0](https://huggingface.co/stabilityai/stable-diffusion-2) ( [768-v-ema.ckpt](https://huggingface.co/stabilityai/stable-diffusion-2/resolve/main/768-v-ema.ckpt) )
[Stable Diffusion 2.1](https://huggingface.co/stabilityai/stable-diffusion-2-1) ( [v2-1_768-ema-pruned.ckpt](https://huggingface.co/stabilityai/stable-diffusion-2-1/resolve/main/v2-1_768-ema-pruned.ckpt) )

**Configuration files** for Stable Diffusion **2.0** and **2.1** needs to be put in the same directory as the model files with the same filename, but .yaml extension. For example:
    
   ```bash
   d3vilh@M1Prou Stable-diffusion % pwd && ls -lrt v2-1_768-ema-pruned*
   /Users/d3vilh/stable-diffusion-webui/models/Stable-diffusion
   -rw-r--r--@ 1 d3vilh  rockers  5214865159 Mar 26 15:21 v2-1_768-ema-pruned.ckpt
   -rw-r--r--@ 1 d3vilh  rockers        1815 Mar 26 15:37 v2-1_768-ema-pruned.yaml
   d3vilh@M1Prou Stable-diffusion %
   ```
## Alternate Stable Diffusion Models
[CivitAI](https://civitai.com) is the most popular hub for other models that can be used with the Web UI. To have access to all the list of models, you will need to create an account. Once you have an account, you can download any models (including NSFW). 
 
There are 2 types of models that can be downloaded - **Lora** and **Stable Diffusion**: 
* **Stable Diffusion** models have `.ckpt` file extension and needs to be placed into the `models/Stable-diffusion` directory. Once this done - restart Web-UI and choose the model from the dropdown menu.
* **Lora** models have `.safetensors` file extension and needs to be placed into the `models/Lora` directory. All the Lora models are based on main Stable Diffusion model, so you will need to download the main model as well.


## Prompts and Negative Prompts
Prompts are used to generate images that are similar to the prompt text. Negative prompts are used to generate images that are dissimilar to the prompt text. For example, to generate images that are similar to "cat" and dissimilar to "dog", you can use:
```
cat
-dog
```

## Performance Issues:

Currently GPU acceleration on macOS uses a lot of memory. If performance is poor (if it takes more than a minute to generate a 512x512 image with 20 steps with any sampler) first try starting with the `--opt-split-attention-v1` command line option (i.e. `./webui.sh --opt-split-attention-v1`) and see if that helps. If that doesn't make much difference, then open the Activity Monitor application located in /Applications/Utilities and check the memory pressure graph under the Memory tab. If memory pressure is being displayed in red when an image is generated, close the web UI process and then add the `--medvram` command line option (i.e. `./webui.sh --opt-split-attention-v1 --medvram`). If performance is still poor and memory pressure still red with that option, then instead try `--lowvram` (i.e. `./webui.sh --opt-split-attention-v1 --lowvram`). If it still takes more than a few minutes to generate a 512x512 image with 20 steps with with any sampler, then you may need to turn off GPU acceleration. Open `webui-user.sh` in Xcode and change `#export COMMANDLINE_ARGS=""` to `export COMMANDLINE_ARGS="--skip-torch-cuda-test --no-half --use-cpu all"`.