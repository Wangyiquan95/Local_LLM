# LLM_local_setup

This guide will walk you through setting up the **DeepSeek** model or other models and running it locally with a chat UI (100% free).

## Complete Guide to Run Local DeepSeek on Server and Chat with Your Own PC

### 1. Install Ollama on Remote Server

First, connect to your server via **SSH**.

#### If you have `sudo` privileges, run the following command to install Ollama:
```sh
curl -fsSL https://ollama.com/install.sh | sh
```
#### else, do this:
```sh
curl -L https://ollama.com/download/ollama-linux-amd64.tgz -o ollama-linux-amd64.tgz
tar -xvzf ollama-linux-amd64.tgz -C ollama

```
You should now be able to run Ollama using:
```sh
./ollama/bin/ollama
```
To make it easier to run Ollama from any terminal session, add it to your `PATH`:
```sh
echo 'export PATH=/data2/yiquan2/llama/ollama/bin:$PATH' >> ~/.zshrc
source ~/.zshrc
```
### 2. Start Using and Installing DeepSeek
Once Ollama is installed, you can start using the DeepSeek model. 
Open one termial and run:
```sh
ollama serve
```
Open another terminal and run the following command to launch it:
```sh
ollama run deepseek-r1:8b
```
You can also use DeepSeek to analyze a specific file. For example, to summarize a scientific paper, you can run:
```sh
ollama run deepseek-r1:32b "Please summarize the following scientific paper. Include the main objectives, key findings, methods, and conclusions in a concise format. Focus on the most important points that describe the significance and novelty of the study. Here's the paper text: $(cat test.txt)"
```
### 3. Install WebUI and Serve on the Same Server
In a new terminal session (or using tmux), run the following command to install and serve Open WebUI:
```sh
pip install open-webui
open-webui serve --port 8080

```
### 4. Connect from Your PC
To connect from your local PC, use SSH port forwarding:
```sh
ssh -N -L 8080:localhost:8080 user@server.ip.address
```
### 5. Open in Your Browser and Start chatting
```
http://0.0.0.0:8080/
```
![chatbot](./image2.png)

### 6. define knowledge and build RAG
first, download embedding model by ollama:
```sh
ollama pull nomic-embed-text
```
go to webui page setting -> admin settings -> documents: 

choose Embedding Model Engine as ollama

embedding model as nomic-embed-text:latest

embedding batch size --> 256

Top k --> 10

Chunk size --> 1500

Chunk overlap --> 300

then go to workspace -> knowledge, add knowledge.

### 7. image generation: by AUTOMATIC1111
first, download stable diffusion model:
```sh
wget -q https://raw.githubusercontent.com/AUTOMATIC1111/stable-diffusion-webui/master/webui.sh
chmod +x ./webui.sh
```
then, run diffusion-webui by:
```sh
./webui.sh --api --listen
```
Setting Up Open WebUI with AUTOMATIC1111
In Open WebUI, navigate to the Admin Panel > Settings > Images menu.

Set the Image Generation Engine field to Default (Automatic1111).

In the API URL field, enter the address where AUTOMATIC1111's API is accessible:

http://<your_automatic1111_address>:7860/

### 8. image generation: by ComfyUI
#### First, download ComfyUI:
```sh
git clone https://github.com/comfyanonymous/ComfyUI.git
cd ComfyUI
```
then, install required packages by:
```sh
pip install -r requirements.txt
```
to start ComfyUI:
```sh
python main.py
```
#### Download Flux model
We are going to use Flux1-dev model, Go to:
👉 https://huggingface.co/black-forest-labs/FLUX.1-dev

Log in to your Hugging Face account.

Click the “Agree and access repository” button.

Then, download model by huggingface-cli:
```sh
huggingface-cli logout
huggingface-cli login
huggingface-cli download black-forest-labs/FLUX.1-dev flux1-dev.safetensors \                                                                      
  --local-dir ComfyUI/models/diffusion_models
cp models/diffusion_models/flux1-dev.safetensors models/checkpoints
huggingface-cli download black-forest-labs/FLUX.1-dev ae.safetensors \                                                                             
  --local-dir ComfyUI/models/vae
huggingface-cli download comfyanonymous/flux_text_encoders clip_l.safetensors \                                                                    
  --local-dir ComfyUI/models/clip
huggingface-cli download comfyanonymous/flux_text_encoders t5xxl_fp16.safetensors \                                                                
  --local-dir ComfyUI/models/clip
```
now you can use ComfyUI by open: http://<your_comfyui_address>:8188/


#### Setting Up Open WebUI with ComfyUI
In Open WebUI, navigate to the Admin Panel > Settings > Images menu.

In the Image Generation Engine field, choose ComfyUI.

In the API URL field, enter the address where ComfyUI's API is accessible, following this format: http://<your_comfyui_address>:8188/.

Set the environment variable COMFYUI_BASE_URL to this address to ensure it persists within the WebUI.

Click the "Click here to upload a workflow.json file" button.

Select the workflow_api.json file to import the exported workflow from ComfyUI into Open WebUI.

After importing the workflow, you must map the ComfyUI Workflow Nodes according to the imported workflow node IDs:
Prompt : text, 6
Model : ckpt_name, 12
Width : width, 27
Height : height, 27
Steps : steps, 17
Seed : seed, 25

Set Set Default Model to the name of the model file being used: flux1-dev.safetensors

### 9. Generation in WebUI
First, use a text generation model to write a prompt for image generation.

After the response has finished, you can click the Picture icon to generate an image.

After the image has finished generating, it will be returned automatically in chat.

### 10. Tool usage
Tools are small Python scripts that add superpowers to your LLM. When enabled, they allow your chatbot to do amazing things — like search the web, scrape data, generate images, talk back using AI voices, and more.
There are two easy ways to install Tools in Open WebUI:

- Go to Community Tool Library -> Choose a Tool, then click the Get button -> Enter your Open WebUI instance’s IP address or URL -> Click “Import to WebUI” — done!
- write custom tool using python

### (optional) 11. Add ollama to your VScode

First, you need ssh connection if you are using remote server: 

```
ssh -N -L 8080:localhost:8080 -L 11434:localhost:11434 user@server.ip.address
```
second, install continue extension on you vscode.

Once installed you should now have a 'continue' tab in the side bar. Open this.

Click on the Assistant selector above the main chat input. Then hover over "Local Assistant" and you should see a settings icon (looks like a cog).

Once you click on the settings icon, a config.yaml should open up in the editor. 

Here you'll be able to configure continue to use ollama on vscode.
```
name: Local Assistant
version: 1.0.0
models:
  - name: qwen2.5-coder
    provider: ollama
    model: qwen2.5-coder:latest
    roles:
      - chat
      - edit
      - apply
      - autocomplete
    defaultCompletionOptions:
      contextLength: 200000
      maxTokens: 64000
  - name: nomic-embed-text
    provider: ollama
    model: nomic-embed-text:latest
    embedOptions:
      maxChunkSize: 8192
    roles:
      - embed
context:
  - provider: file
```

### (optional) 12. Add ollama to n8n workflow

First, install Docker(https://www.docker.com/) and open it.

Open terminal to install and run n8n locally:
```
docker volume create n8n_data
docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
```
Once running, you can access n8n by opening: http://localhost:5678

Optionally, install n8n by npm:
```
# Download and install nvm:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
# restarting the shell
# Download and install Node.js:
nvm install 22
# Verify the Node.js version:
node -v # Should print "v22.16.0".
nvm current # Should print "v22.16.0".
# Verify npm version:
npm -v # Should print "10.9.2".
# install n8n
npm install n8n -g
# After the installation, start n8n by running:
n8n
```


Start by adding a Chat trigger node, which is the workflow starting point for building chat interfaces with n8n.

Then, add a Ollama Model Node, set the base url: http://host.docker.internal:11434 (http://host.docker.internal:11434 for non-docker)

Select model and start build workflow
