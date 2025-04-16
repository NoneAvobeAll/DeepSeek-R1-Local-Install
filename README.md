# Guide to Installing Ollama and Running DeepSeek-R1 Model (600+B)

## Introduction
Ollama is a lightweight, extensible framework designed to run large language models (LLMs) locally, offering a simple API to create, manage, and interact with models. It supports a variety of models, including DeepSeek-R1, a reasoning-focused model developed by DeepSeek, which is noted for performance comparable to advanced models like OpenAI’s o1. The user’s reference to “r1 model 600+B” likely indicates the DeepSeek-R1 model with 671 billion parameters, as it exceeds 600 billion and is the largest variant available. This guide provides detailed steps to install Ollama on Linux and run the DeepSeek-R1 model, addressing hardware requirements and alternatives for less powerful systems.

## Step 1: Installing Ollama on Linux
Ollama provides multiple installation methods for Linux, with the automatic installation script being the most straightforward. Below are the steps and options:

### Automatic Installation
Run the following command in your terminal to download and install Ollama:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

This script handles the download and setup process, installing Ollama to `/usr/bin/ollama`. After installation, verify it by checking the version:

```bash
ollama -v
```

### Manual Installation (Optional)
For more control, you can install Ollama manually:

1. Download the binary:
   ```bash
   curl -L https://ollama.com/download/ollama-linux-amd64.tgz -o ollama-linux-amd64.tgz
   ```
2. Extract and install:
   ```bash
   sudo tar -C /usr -xzf ollama-linux-amd64.tgz
   ```
3. Start the server:
   ```bash
   ollama serve
   ```
4. Verify installation:
   ```bash
   ollama -v
   ```

### Setting Up as a System Service
To ensure Ollama runs automatically on boot, configure it as a systemd service:

1. Create a user for Ollama:
   ```bash
   sudo useradd -r -s /bin/false -U -m -d /usr/share/ollama ollama
   sudo usermod -a -G ollama $(whoami)
   ```
2. Create the service file `/etc/systemd/system/ollama.service` with:
   ```ini
   [Unit]
   Description=Ollama Service
   After=network-online.target

   [Service]
   ExecStart=/usr/bin/ollama serve
   User=ollama
   Group=ollama
   Restart=always
   RestartSec=3
   Environment="PATH=$PATH"

   [Install]
   WantedBy=multi-user.target
   ```
3. Enable and start the service:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable ollama
   sudo systemctl start ollama
   ```
4. Check the service status:
   ```bash
   sudo systemctl status ollama
   ```

### Hardware-Specific Installations
- **AMD GPU**: Download the ROCm version:
  ```bash
  curl -L https://ollama.com/download/ollama-linux-amd64-rocm.tgz -o ollama-linux-amd64-rocm.tgz
  sudo tar -C /usr -xzf ollama-linux-amd64-rocm.tgz
  ```
  Ensure AMD ROCm drivers are installed from [AMD Drivers](https://www.amd.com/en/support/linux-drivers).
- **ARM64 Systems**: Use the ARM64 binary:
  ```bash
  curl -L https://ollama.com/download/ollama-linux-arm64.tgz -o ollama-linux-arm64.tgz
  sudo tar -C /usr -xzf ollama-linux-arm64.tgz
  ```

## Step 2: Starting the Ollama Server
To make models available for use, start the Ollama server:

```bash
ollama serve
```

This runs Ollama in the background, enabling model execution via terminal commands or the Ollama API.

## Step 3: Running the DeepSeek-R1 Model
The DeepSeek-R1 model is available in multiple sizes, with the 671B parameter version being the largest and likely the “600+B” model referenced. Below are the details for running it and alternatives for different hardware capabilities.

### DeepSeek-R1:671B (Full Model)
To run the full DeepSeek-R1 model with 671 billion parameters, use:

```bash
ollama run deepseek-r1:671b
```

#### Hardware Requirements
- **Size**: Approximately 404 GB (4-bit quantized).
- **VRAM**: Over 400 GB, typically requiring at least 6 NVIDIA H100 GPUs or equivalent.
- **RAM**: Likely hundreds of GB, far exceeding typical consumer hardware.
- **Storage**: Ensure sufficient disk space (at least 404 GB free).

#### Challenges
Running the 671B model is resource-intensive and may not be feasible for most users without access to high-end server hardware, such as an EPYC server or a multi-GPU cloud setup. For example, a [Digital Spaceport guide](https://digitalspaceport.com/how-to-run-deepseek-r1-671b-fully-locally-on-2000-epyc-rig/) describes running it on a $2000 EPYC server, indicating the scale of hardware needed.

### Alternative: Distilled DeepSeek-R1 Models
Ollama offers smaller, distilled versions of DeepSeek-R1, fine-tuned from the 671B model using Llama and Qwen architectures. These are more practical for standard hardware:

| Model Command                     | Parameters | Size   | Minimum RAM |
|-----------------------------------|------------|--------|-------------|
| `ollama run deepseek-r1:1.5b`     | 1.5B       | 1.0 GB | 4 GB        |
| `ollama run deepseek-r1:7b`       | 7B         | 4.7 GB | 8 GB        |
| `ollama run deepseek-r1:14b`      | 14B        | 9.1 GB | 16 GB       |
| `ollama run deepseek-r1:32b`      | 32B        | 20 GB  | 32 GB       |
| `ollama run deepseek-r1:70b`      | 70B        | 43 GB  | 64 GB       |

These models maintain strong reasoning capabilities and are suitable for users with consumer-grade hardware. For example, the 70B model is noted to outperform some closed-source models like OpenAI’s o1-mini on benchmarks ([Ori Cloud Guide](https://blog.ori.co/how-to-run-deepseek-r1)).

### Quantized Versions
Quantized versions of the 671B model reduce memory requirements, making them accessible on less powerful hardware:

- **1.58-bit Quantized Model**:
  ```bash
  ollama run SIGJNF/deepseek-r1-671b-1.58bit
  ```
  - Can run on a single NVIDIA 4090 GPU ([Ollama Quantized Model](https://ollama.com/SIGJNF/deepseek-r1-671b-1.58bit)).
  - Maintains much of the 671B model’s performance with a smaller footprint.

- **1.73-bit Quantized Model**:
  ```bash
  ollama run Huzderu/deepseek-r1-671b-1.73bit
  ```
  - Similarly optimized for single-GPU setups ([Ollama Quantized Model](https://ollama.com/Huzderu/deepseek-r1-671b-1.73bit)).

These quantized models are ideal for users who want to experiment with the 671B model’s capabilities without requiring extensive hardware.

## Step 4: Interacting with the Model
Once a model is running, you can interact with it in two ways:

### Terminal Interaction
After running `ollama run deepseek-r1:671b` (or another variant), you can type prompts directly in the terminal to interact with the model.

### API Interaction
To integrate the model into applications, use the Ollama API. For example, send a prompt using `curl`:

```bash
curl http://localhost:11434/api/chat -d '{
  "model": "deepseek-r1:671b",
  "messages": [{"role": "user", "content": "Solve: 25 * 25"}],
  "stream": false
}'
```

This allows programmatic access to the model’s capabilities ([DataCamp Tutorial](https://www.datacamp.com/tutorial/deepseek-r1-ollama)).

## Additional Considerations
- **Model Licensing**: DeepSeek-R1 models are licensed under the MIT License, supporting commercial use and modifications. Distilled models may have additional licensing considerations (e.g., Qwen models under Apache 2.0, Llama under its own license).
- **Updates**: To update Ollama, re-run the installation script or download the latest binary.
- **Logs**: View service logs with:
  ```bash
  journalctl -e -u ollama
  ```
- **Uninstallation**: To remove Ollama:
  ```bash
  sudo systemctl stop ollama
  sudo systemctl disable ollama
  sudo rm /etc/systemd/system/ollama.service
  sudo rm $(which ollama)
  sudo rm -r /usr/share/ollama
  sudo userdel ollama
  sudo groupdel ollama
  sudo rm -rf /usr/local/lib/ollama
  ```

## Conclusion
Installing Ollama on Linux is straightforward using the provided script, enabling you to run powerful models like DeepSeek-R1. The 671B parameter model, likely the “600+B” model referenced, offers exceptional reasoning capabilities but requires significant hardware, such as multiple high-end GPUs. For most users, distilled models (e.g., 70B or 7B) or quantized versions (e.g., 1.58-bit on a single 4090) provide a practical alternative. By following the steps above, you can set up Ollama and choose the DeepSeek-R1 variant that best fits your hardware and needs.

For further details, explore the [Ollama Library](https://ollama.com/library/deepseek-r1) or refer to community guides like the [DataCamp Tutorial](https://www.datacamp.com/tutorial/deepseek-r1-ollama).
