# Stable Diffusion How-To
Run Stable Diffusion on your x86 PC or M1 Mac’s GPU.

* [Popular Stable Diffusion Models](#popular-stable-diffusion-models)
* [Alternative Stable Diffusion Models](#alternative-stable-diffusion-models)
* [LoRA Models configuration](#lora-models-configuration)
* [Disabling the NSFW filter](#disabling-the-nsfw-filter)
* [Performance tuning](#performance-issues)
* [Prompts, Accents and Modifiers guide](#accents-modifiers-and-punctuation-in-text-prompts-for-ai-models)

## Installation
   >**NOTE:** For **x86/Windows/Linux** follow installation instruction [here](https://github.com/AUTOMATIC1111/stable-diffusion-webui#installation-and-running).
1. First, install all the dependencies via Homebrew ( Use [Brew.sh](https://brew.sh/) to install it ):
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

4. Download the Stable-Diffusion model in safetensors format. The latest and advanced one available at the moment is [version 2.1-768](https://huggingface.co/stabilityai/stable-diffusion-2-1) ( [v2-1_768-ema-pruned.safetensors](https://huggingface.co/stabilityai/stable-diffusion-2-1/resolve/main/v2-1_768-ema-pruned.safetensors) ):
   ```bash
   curl -Lo models/Stable-diffusion/v2-1_768-ema-pruned.safetensors https://huggingface.co/stabilityai/stable-diffusion-2-1/resolve/main/v2-1_768-ema-pruned.safetensors
   ```

   
   >**Note:** Some specific Lora models may require earlier versions, links to which can be found below.

   ###### 5.1 Stable Diffusion M1/Apple Silicon 10-25% speed improvement:
   This will install the latest PyTorch version, which is not guaranteed to be stable. But it will give you a significant speed boost (10-25%).
   To apply latest PyTorch - modify `webui-macos-env.sh` file in the `stable-diffusion-webui` folder.
      * Comment out/remove the following line:
       `export TORCH_COMMAND="pip install torch==1.12.1 torchvision==0.13.1"`
      * and replace it with:
       `export TORCH_COMMAND="pip install --pre torch torchvision --extra-index-url https://download.pytorch.org/whl/nightly/cpu"`

6. Run WebUI (this will take a while the first time you run it, as it will download and compile the dependencies):
    ```bash
    ./webui.sh
    ```
    >**Note:** If you get an error about the script not being executable, run `chmod +x webui.sh` and try again.

7. Once it's done, you can open the Web UI by going to http://localhost:7860 in your browser.

## Popular Stable Diffusion Models
Stable Diffusion models can be downloaded from [Hugging Face](https://huggingface.co/models?pipeline_tag=text-to-image&sort=downloads). To download, click on a model and then click on the Files and versions header. Look for files listed with the ".safetensors" or ".ckpt" extensions, and then click the down arrow to the right of the file size to download them.
Here are most popular models to download:

[Stable DIffusion 1.4](https://huggingface.co/CompVis/stable-diffusion-v1-4) ( [sd-v1-4.ckpt](https://huggingface.co/CompVis/stable-diffusion-v-1-4-original/resolve/main/sd-v1-4.ckpt) )

[Stable Diffusion 1.5](https://huggingface.co/runwayml/stable-diffusion-v1-5) ( [v1-5-pruned-emaonly.ckpt](https://huggingface.co/runwayml/stable-diffusion-v1-5/resolve/main/v1-5-pruned-emaonly.ckpt) )

[Stable Diffusion 1.5 Inpainting](https://huggingface.co/runwayml/stable-diffusion-inpainting) ( [sd-v1-5-inpainting.ckpt](https://huggingface.co/runwayml/stable-diffusion-inpainting/resolve/main/sd-v1-5-inpainting.ckpt) )

Stable Diffusion **2.0** and **2.1** require both a `model` and a `configuration file`, and the image width & height will need to be set to **768** or higher when generating images:

[Stable Diffusion 2.0](https://huggingface.co/stabilityai/stable-diffusion-2) ( [768-v-ema.safetensors](https://huggingface.co/stabilityai/stable-diffusion-2/resolve/main/768-v-ema.safetensors) )

[Stable Diffusion 2.1](https://huggingface.co/stabilityai/stable-diffusion-2-1) ( [v2-1_768-ema-pruned.safetensors](https://huggingface.co/stabilityai/stable-diffusion-2-1/resolve/main/v2-1_768-ema-pruned.safetensors) )

**Configuration files** for Stable Diffusion **2.0** and **2.1** needs to be in the same directory as the model files, with the same filename, but `.yaml` extension. For example:
    
   ```bash
   d3vilh@M1Prou Stable-diffusion % pwd && ls -lrt v2-1_768-ema-pruned*
   /Users/d3vilh/stable-diffusion-webui/models/Stable-diffusion
   -rw-r--r--@ 1 d3vilh  rockers  5214865159 Mar 26 15:21 v2-1_768-ema-pruned.ckpt
   -rw-r--r--@ 1 d3vilh  rockers        1815 Mar 26 15:37 v2-1_768-ema-pruned.yaml
   d3vilh@M1Prou Stable-diffusion %
   ```
## Alternative Stable Diffusion Models
**[CivitAI](https://civitai.com)** is the most popular hub for other models that can be used with the Web UI. To have access to all the list of models, you'll need to create an account. Once you have it - you can download any models (including NSFW). 
 
There are 2 types of models that can be downloaded - **Lora** and **Stable Diffusion**: 
* **Stable Diffusion** models have `.ckpt` (TensorFlow checkpoint) or `.safetensors` (safe .ckpt with all the scripts removed) and needs to be placed into the `models/Stable-diffusion` directory. Once this done - restart Web-UI and choose the model from the dropdown menu.
* **LoRA** models (Logistic Regression with Adversarial examples) have most of all `.safetensors` file extension and needs to be placed into the `models/Lora` directory. All the LoRA models are based on main Stable Diffusion model, in most cases you will need to download the main model as well.

## LoRA Models configuration
Lets have example for configuring LoRA model in the WebUI based on ~~[realismEngine_v10 model](https://civitai.com/models/17277/realism-engine)~~ [DreamShaper](https://civitai.com/models/4384/dreamshaper):
 >**Note Junly 2023:** RealismEngine model has been removed from all the open sources by unknown to me reasons.
You can use any other models from civitai and the result will be pretty similar. Try [DreamShaper](https://civitai.com/models/4384/dreamshaper) or [AbsoluteReality](https://civitai.com/models/81458/absolutereality) or [A-Zovoya RPG](https://civitai.com/models/8124). 
1. First you need to download the model and configuration file (press down arrow on the `Download` button):
<p align="center">
<img src="https://github.com/d3vilh/stable-diffusion-mac/raw/main/pictures/0.1-CvitAI-RealismEngine-v2.1.png" alt="CvitAI Realism Engine page" width="800" border="1" />
</p>

2. Then you will need to place both files into the `models/Lora` directory as shown below:
   ```bash
   d3vilh@M1Prou Lora % pwd && ls -lrth realismEngine_v10*
   /Users/d3vilh/stable-diffusion-webui/models/Lora
   -rw-r--r--@ 1 d3vilh  rockers   1.8K Mar 27 12:32 realismEngine_v10.yaml
   -rw-r--r--@ 1 d3vilh  rockers   2.4G Mar 27 13:07 realismEngine_v10.safetensors
   d3vilh@M1Prou Lora %
   ```
   >**Note:** Keep in mind the **filename** (`realismEngine_v10`), it will be necessary for the configuration steps.
3. When this done - **restart WebUI**, then go to the `Settings` tab and choose the model name from the dropdown menu of `Extra Networks` and `Stable Diffusion Checkpoint` options, as shown on the picture below:
<p align="center">
<img src="https://github.com/d3vilh/stable-diffusion-mac/raw/main/pictures/0.4-WebUI-Settings-Lora.png" alt="WebUI Settings" width="800" border="1" />
</p>

4. Click `Apply Settings` and then `Reload UI` to apply the changes.

5. Now lets back to the ~~[realismEngine_v10 model](https://civitai.com/models/17277/realism-engine)~~ [DreamShaper](https://civitai.com/models/4384/dreamshaper) and try to generate similar images to the one that we have on the page. To do this we will use the `Prompts` feature.
6. Copy the text from the `Prompt` and `Negative Prompt` sections of example image and paste it into the `Prompts` and `Negative Prompts` text areas in the WebUI: 
   <p align="center">
   <img src="https://github.com/d3vilh/stable-diffusion-mac/raw/main/pictures/0.2-CvitAI-Options-RE.v2.1.png" alt="CvitAI Realism Engine examples" width="800" border="1" />
   </p>

   **Very important** is to use `<lora:>` tags to apply necessary LoRA model to our picture. As a modelname we will use `<lora:realismEngine_v10>` where `realismEngine_v10` is a filename we keep in mind on the first step. You can use several models at the same time, just separate them with comma `,`: `<lora:realismEngine_v10, astonMartinDBX_epoch3>`.
   In addition there are other parameters such as `CFG Scale`, `Steps`, `Sampler` and other, which you can apply to your image. 
   Here is the example of the ported settings that we used to generate the image below :
   <p align="center">
   <img src="https://github.com/d3vilh/stable-diffusion-mac/raw/main/pictures/0.3-CvitAI-Headgehog-RE.v2.1.png" alt="WebUI Settings" width="800" border="1" />
   </p>
   
   You can copy/paste the `Prompts` and `Negative Prompts` from our example:

   **Prompts:**
   ```
   Hedgehog in Palm forest Comforting atmosphere Sunlight lighting, <lora:realismEngine_v10>
   ```
   **Negative Prompts:**
   ```
   nrealfixer, 3d render, cgi, painting, drawing, cartoon, anime, ((blurry)), animated, cartoon, duplicate, dirty face, oversaturated, high contrast
   ```

7. Now lets click `Generate` button and wait for the image to be generated.
   Here is our result:
   <p align="center">
   <img src="https://github.com/d3vilh/stable-diffusion-mac/raw/main/pictures/0.5-Result-1.png" alt="Model execution result" width="600" border="1" />
   </p>

### Disabling the NSFW filter (disputible content)
As per @AUTOMATIC1111 Stable-Diffusion-Web-ui [Wiki page](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Command-Line-Arguments-and-Settings), this parameter "*disable checking pytorch models for malicious code*". Some NSFW Checkpoint models need this parameter to be enabled to run. However it is not recommended to use this parameter for any not confirmed as "100% safe models" as such models can have malicious code enabled.

If you still want to disable this, you can do so by adding the `--disable-safe-unpickle` flag to the `webui.sh` script:
```bash
./webui.sh --disable-safe-unpickle
```

## Performance Issues:
Currently GPU acceleration on macOS uses a lot of memory. If performance is poor (if it takes more than a minute to generate a 512x512 image with 20 steps with any sampler) first try starting with the `--opt-split-attention-v1` command line option (i.e. `./webui.sh --opt-split-attention-v1`) and see if that helps. If that doesn't make much difference, then open the Activity Monitor application located in /Applications/Utilities and check the memory pressure graph under the Memory tab. If memory pressure is being displayed in red when an image is generated, close the web UI process and then add the `--medvram` command line option (i.e. `./webui.sh --opt-split-attention-v1 --medvram`). If performance is still poor and memory pressure still red with that option, then instead try `--lowvram` (i.e. `./webui.sh --opt-split-attention-v1 --lowvram`). If it still takes more than a few minutes to generate a 512x512 image with 20 steps with with any sampler, then you may need to turn off GPU acceleration. Open `webui-user.sh` in Xcode and change `#export COMMANDLINE_ARGS=""` to `export COMMANDLINE_ARGS="--skip-torch-cuda-test --no-half --use-cpu all"`.


## Accents, Modifiers and Punctuation in Text Prompts for AI Models
[DOBF paper](https://arxiv.org/abs/2102.07492) describes how to use Prompts, Punctuation and Accents to fine-tune the model.
Here I will share my How-to which is applicable for AUTOMATIC11111 web UI and different models.

### Accents
The following accents can be used to indicate priority and de-prioritization in a text prompt:
   * `^` (circumflex) - indicates that a word or phrase should be prioritized
   * `~` (tilde) - indicates that a word or phrase should be de-prioritized

### Modifiers
The following modifiers can be used to adjust the weighting of specific words or phrases in a text prompt:
   * `+` (plus sign) - indicates that a word or phrase should be weighted more heavily in the output
   * `-` (minus sign) - indicates that a word or phrase should be weighted less heavily in the output
   * `!` (exclamation point) - indicates that a word or phrase should be treated as a hard constraint and must be included in the output

### Punctuation
The following punctuation marks can be used to structure and clarify text prompts:
   * `,` (comma) - separates different ideas or phrases within a sentence or prompt
   * `.` (period) - indicates the end of a sentence or idea
   * `:` (colon) - can be used to introduce a list or to indicate that what follows is an explanation or example. Also used for weighted modifiers.
   * `;` (semicolon) - can be used to separate related but distinct ideas within a sentence
It's important to note that not all AI models will respond to these accents, modifiers, and punctuation in the same way. 

Additionally, the effectiveness of these techniques can vary depending on the specific AI model and the complexity of the desired output. As such, it may take some trial and error to find the right combination of prompts and modifiers to generate the desired output.

### Weights
In the context of image generation prompts, weights can be added to accents or modifiers in the prompt to indicate how strongly the AI model should prioritize certain features. Weights are a way to indicate the relative importance or priority of different elements or characteristics in the generated image.

Here are some examples of text prompts with different modifiers and weights:
   * `A beautiful ^sunset over the ^ocean` - This prompt uses accents to prioritize the words `sunset` and `ocean,` indicating that the AI model should focus on generating text that emphasizes these features.
   * `The +adorable kitten+ played with the -shiny ball-` - This prompt uses modifiers to indicate that the phrase `adorable kitten` should be weighted more heavily in the output and the phrase `shiny ball` should be weighted less heavily.
   * `The !brave knight! fought the !fearsome dragon! with his +mighty sword+` - This prompt uses exclamation points to indicate that the phrases `brave knight` and `fearsome dragon` should be treated as hard constraints that must be included in the output and the phrase `mighty sword` should be weighted more heavily.
   * `house, (^red:1.5), walls, windows, door, roof.(red)` - In this prompt, the `^red` accent indicates that the color red should be given priority, and the `weight of 1.5` indicates that this is very important. The `(red)` modifier indicates that the roof should be specifically red.

Weights can be used to fine-tune the image generation process and help the AI model better understand your desired outcome. However, it's important to use weights judiciously, as too many weights or too high weights can lead to overfitting or unrealistic results.

### Examples
All the examples below are based on the [Realism Engine 1.0](https://civitai.com/models/17277/realism-engine) Checkpoint model. With sampling method - `Euler A`, `20` Sampling steps and `512x512` image.  As a Negative prompts I used the following list of tags: `nrealfixer, 3d render, cgi, painting, drawing, cartoon, anime, ((blurry)), animated, cartoon, duplicate, dirty face, oversaturated, high contrast`.
So, here are more examples of text prompts with different modifiers and my explanations:

###### A prompt for generating an image of a sunset over the ocean:
`sunset, (^ocean:1.5), sky, clouds, (orange:1.2), water.`
In this prompt, the `^ocean` accent indicates that the ocean should be given priority, and the weight of `1.5` indicates that this is very important. The `(orange)` modifier indicates that the color orange should be added to the image, and the weight of `1.2` indicates that this is somewhat important.

   <p align="center">
   <img src="/pictures/Examples.0.1.png" alt="A prompt for generating an image of a sunset over the ocean" width="512">
   </p>

###### A prompt for generating an image of a forest with fog:
`forest, (~fog:1.2), trees, bushes, (green:1.2)`
In this prompt, the `~fog` accent indicates that there should be some fog in the image, and the weight of `1.2` indicates that this is somewhat important. The `(green)` modifier indicates that the image should have a green color scheme, and the weight of `1.2` indicates that this is somewhat important.

   <p align="center">
   <img src="/pictures/Examples.0.2.png" alt="A prompt for generating an image of a forest with fog" width="512">
   </p>

###### A prompt for generating an image of a snowy mountain landscape:
`landscape, (+snow:1.5), mountains, sky, trees, (white:1.2)`
In this prompt, the `+snow` accent indicates that there should be snow in the image, and the weight of `1.5` indicates that this is very important. The `(white)` modifier indicates that the image should have a white color scheme, and the weight of `1.2` indicates that this is somewhat important.

   <p align="center">
   <img src="/pictures/Examples.0.3.png" alt="A prompt for generating an image of a snowy mountain landscape" width="512">
   </p>

###### A prompt for generating an image of a desert with a cactus:
`desert, (-water:1.2), sand, sun, (cactus), (brown:1.2)`
In this prompt, the `-water` accent indicates that there should not be much water in the image, and the weight of `1.2` indicates that this is somewhat important. The `(cactus)` modifier indicates that there should be a cactus in the image, and the `(brown)` modifier indicates that the image should have a brown color scheme.

   <p align="center">
   <img src="/pictures/Examples.0.4.png" alt="A prompt for generating an image of a desert with a cactus" width="512">
   </p>

###### A prompt for generating an image of a dog playing with a ball:
`dog, (!playing:1.5), ball, grass, (brown:1.2)`
In this prompt, the `!playing` accent indicates that the dog should paying, and the weight of `1.5` indicates that this is very important. The other elements in the prompt (`ball, grass, (brown:1.2)`)` are still included, but they are not as strongly prioritized as the playing dog.

   <p align="center">
   <img src="/pictures/Examples.0.5.png" alt="A prompt for generating an image of a dog playing with a ball" width="512">
   </p>

###### A prompt for generating an image of a city skyline:
`city, (:skyline), buildings, (blue:1.2)`
In this prompt, the `:skyline` modifier indicates that the image should focus on the city skyline. The `(blue)` modifier indicates that the image should have a blue color scheme, and the weight of `1.2` indicates that this is somewhat important.

   <p align="center">
   <img src="/pictures/Examples.0.6.png" alt="A prompt for generating an image of a city skyline" width="512">
   </p>

###### A prompt for generating an image of a flower arrangement:
`flowers, (;arrangement), vase, (pink:1.2), (yellow:0.8)`
This prompt includes several accents, modifiers, and a semicolon. It indicates that the model should generate images of flowers in a vase, with a slight emphasis on the arrangement of the flowers. The semicolon indicates that the feature `arrangement` is optional, so the model can generate images with or without a specific arrangement. The prompt also specifies that the vase should be included in the image. The modifier `(pink:1.2)` indicates that the model should prioritize generating images with a pink color, with a weighting of `1.2`. The modifier `(yellow:0.8)` indicates that the model should generate images with a yellow color, but with a lower priority, with a weighting of `0.8`.
   <p align="center">
   <img src="/pictures/Examples.0.7.png" alt="A prompt for generating an image of a flower arrangement" width="512">
   </p>

   <p align="center">
Hey Reddit! <img src="https://gelbooru.com/extras/db/db0.jpg" alt="A prompt for generating an image of a city skyline" width="8">
   </p>
