# Stable Diffusion How-To
Run Stable Diffusion on your M1 Mac’s GPU (Intel and non-Apple PCs are also supported).

* [Popular Stable Diffusion Models](#popular-stable-diffusion-models)
* [Alternative Stable Diffusion Models](#alternative-stable-diffusion-models)
* [LoRA Models configuration](#lora-models-configuration)
* [Disabling the NSFW filter](#disabling-the-nsfw-filter)
* [Performance tuning](#performance-issues)

## Installation
   >**NOTE:** For **x86/Windows/Linux** follow installation instruction [here](https://github.com/AUTOMATIC1111/stable-diffusion-webui#installation-and-running).
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

## Popular Stable Diffusion Models
If you don't have any models to use, Stable Diffusion models can be downloaded from [Hugging Face](https://huggingface.co/models?pipeline_tag=text-to-image&sort=downloads). To download, click on a model and then click on the Files and versions header. Look for files listed with the ".ckpt" or ".safetensors" extensions, and then click the down arrow to the right of the file size to download them.
Here are most popular modules to download:

[Stable DIffusion 1.4](https://huggingface.co/CompVis/stable-diffusion-v1-4) ( [sd-v1-4.ckpt](https://huggingface.co/CompVis/stable-diffusion-v-1-4-original/resolve/main/sd-v1-4.ckpt) )

[Stable Diffusion 1.5](https://huggingface.co/runwayml/stable-diffusion-v1-5) ( [v1-5-pruned-emaonly.ckpt](https://huggingface.co/runwayml/stable-diffusion-v1-5/resolve/main/v1-5-pruned-emaonly.ckpt) )

[Stable Diffusion 1.5 Inpainting](https://huggingface.co/runwayml/stable-diffusion-inpainting) ( [sd-v1-5-inpainting.ckpt](https://huggingface.co/runwayml/stable-diffusion-inpainting/resolve/main/sd-v1-5-inpainting.ckpt) )

Stable Diffusion **2.0** and **2.1** require both a `model` and a `configuration file`, and the image width & height will need to be set to **768** or higher when generating images:

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
## Alternative Stable Diffusion Models
**[CivitAI](https://civitai.com)** is the most popular hub for other models that can be used with the Web UI. To have access to all the list of models, you will need to create an account. Once you have an account, you can download any models (including NSFW). 
 
There are 2 types of models that can be downloaded - **Lora** and **Stable Diffusion**: 
* **Stable Diffusion** models have `.ckpt` file extension(TensorFlow checkpoint) and needs to be placed into the `models/Stable-diffusion` directory. Once this done - restart Web-UI and choose the model from the dropdown menu.
* **LoRA** models (Logistic Regression with Adversarial examples) have `.safetensors` file extension (modified TensorFlow checkpoin) and needs to be placed into the `models/Lora` directory. All the LoRA models are based on main Stable Diffusion model, so you will need to download the main model as well.

## LoRA Models configuration
Lets have example for configuring LoRA model in the WebUI based on [realistEngine_v10 model](https://civitai.com/models/17277/realism-engine):
1. First you need to download the model and configuration file (press down arrow on the `Download` button):
<p align="center">
<img src="https://github.com/d3vilh/stable-diffusion-mac/raw/main/pictures/0.1-CvitAI-RealismEngine-v2.1.png" alt="CvitAI Realism Engine page" width="800" border="1" />
</p>

2. Then you will need to place them into the `models/Lora` directory as shown below:
   ```bash
   d3vilh@M1Prou Lora % pwd && ls -lrth realismEngine_v10*
   /Users/d3vilh/stable-diffusion-webui/models/Lora
   -rw-r--r--@ 1 d3vilh  rockers   1.8K Mar 27 12:32 realismEngine_v10.yaml
   -rw-r--r--@ 1 d3vilh  rockers   2.4G Mar 27 13:07 realismEngine_v10.safetensors
   d3vilh@M1Prou Lora %
   ```
   >**Note:** Keep in mind the **filename** (`realismEngine_v10`), it will be necessary for the configuration steps.
3. Once this done - you will need to **restart WebUI**, then go to the `Settings` tab and choose the model from the dropdown menu of `Extra Networks` and `Stable Diffusion Checkpoint` options, as shown on the picture below:
<p align="center">
<img src="https://github.com/d3vilh/stable-diffusion-mac/raw/main/pictures/0.4-WebUI-Settings-Lora.png" alt="WebUI Settings" width="800" border="1" />
</p>

4. Click `Apply Settiong` and then `Reload UI` duttons to apply the changes.

5. Now lets back to the realistEngine CivitAI hub page and try to generate similar images to the one that we have on the page. To do this we will need to use the `Prompts` feature.
6. Lets copy the text from the `Prompt` and `Negative Prompt` sections and paste it into the `Prompts` and `Negative Prompts` text areas in the WebUI: 
   <p align="center">
   <img src="https://github.com/d3vilh/stable-diffusion-mac/raw/main/pictures/0.2-CvitAI-Options-RE.v2.1.png" alt="CvitAI Realism Engine examples" width="800" border="1" />
   </p>

   **Very important** is to use `<lora:>` tags to apply necessary LoRA model to our picture. As a modelname we will use `<lora:realismEngine_v10>` where `realismEngine_v10` is a filename we keep in mind on the first step.
   In addition there are other parameters such as `CFG Scale`, `Steps`, `Sampler` and other, which you can apply to your image. 
   Here is the example of the ported settings that we used to generate the image below :
   <p align="center">
   <img src="https://github.com/d3vilh/stable-diffusion-mac/raw/main/pictures/0.3-CvitAI-Headgehog-RE.v2.1.png" alt="WebUI Settings" width="800" border="1" />
   </p>
   
   Here is copy/paste of the `Prompts` and `Negative Prompts` from our example:

   **Prompts:**
   ```
   Hedgehog in Palm forest Comforting atmosphere Sunlight lighting, <lora:realismEngine_v10>
   ```
   **Negatove Prompts:**
   ```
   nrealfixer, 3d render, cgi, painting, drawing, cartoon, anime, ((blurry)), animated, cartoon, duplicate, dirty face, oversaturated, high contrast
   ```

7. Click `Generate` button and wait for the image to be generated.
   Here is our result:
   <p align="center">
   <img src="https://github.com/d3vilh/stable-diffusion-mac/raw/main/pictures/0.5-Result-1.png" alt="Model execution result" width="600" border="1" />
   </p>

### Disabling the NSFW filter
By default, the Web UI will filter out any images that are flagged as NSFW. If you want to disable this, you can do so by adding the `--disable-safe-unpickle` flag to the `webui.sh` script:
```bash
./webui.sh --disable-safe-unpickle
```

## Performance Issues:

Currently GPU acceleration on macOS uses a lot of memory. If performance is poor (if it takes more than a minute to generate a 512x512 image with 20 steps with any sampler) first try starting with the `--opt-split-attention-v1` command line option (i.e. `./webui.sh --opt-split-attention-v1`) and see if that helps. If that doesn't make much difference, then open the Activity Monitor application located in /Applications/Utilities and check the memory pressure graph under the Memory tab. If memory pressure is being displayed in red when an image is generated, close the web UI process and then add the `--medvram` command line option (i.e. `./webui.sh --opt-split-attention-v1 --medvram`). If performance is still poor and memory pressure still red with that option, then instead try `--lowvram` (i.e. `./webui.sh --opt-split-attention-v1 --lowvram`). If it still takes more than a few minutes to generate a 512x512 image with 20 steps with with any sampler, then you may need to turn off GPU acceleration. Open `webui-user.sh` in Xcode and change `#export COMMANDLINE_ARGS=""` to `export COMMANDLINE_ARGS="--skip-torch-cuda-test --no-half --use-cpu all"`.
