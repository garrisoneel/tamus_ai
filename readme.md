## Configuration Guide: Using Opencode.ai with the TAMUS RELLIS AI Endpoint

### **Attribution**
The original version of this document was created by Dr. Lucas W Krakow  
Completely restructured and updated by an AI agent (April 2026)

---

### **Objective**

This guide provides the complete instructions to install and configure the Opencode.ai command-line interface (CLI) to use the TAMUS RELLIS AI Platform's hosted models directly from your terminal.

By following these steps, you will set up a secure, shareable project configuration that allows every team member to use their personal API key without committing secrets to the repository.

The steps (API endpoint and API key) are broadly applicable to other tools as well.

**NEW (April 2026):** This guide now includes:
- ✅ Complete model pricing (cost tracking in the TUI)
- ✅ Accurate context/output token limits for all models
- ✅ Shared team configuration via Git (symlink-based setup)
- ✅ Proper schema-compliant config structure

---

### **Prerequisites**

To use OpenCode, you'll need:

**A modern terminal emulator like:**
- [WezTerm](https://wezterm.org/), cross-platform  
- [Alacritty](https://alacritty.org/), cross-platform  
- [Ghostty](https://ghostty.org/), Linux and macOS  
- [Kitty](https://sw.kovidgoyal.net/kitty/), Linux and macOS  

**API keys for the LLM providers you want to use:**
- TAMUS AI API key: https://docs.tamus.ai/docs/prod/advanced/api/obtain-tamus-ai-chat-api-key/

---

### **Step 1: Install the Opencode.ai CLI**

Installation instructions can be found in the [opencode docs](https://opencode.ai/docs) and are summarized here (assuming linux):

1.  **Run the installation command:**
    ```bash
    curl -fsSL https://opencode.ai/install | bash
    ```

2.  **Verification Step:** After installation, verify that the `opencode` command is available by checking its version.
    ```bash
    opencode --version
    ```
    You should see a version number returned, confirming the installation was successful.

---

### **Step 2: Configure Your Environment Variables**

This is the most important step for your personal setup. We will securely store your endpoint URL and secret API key as environment variables.

1.  **Obtain Your API Key:** You can acquire your personal API key by following the official documentation:
    - https://docs.tamus.ai/docs/prod/advanced/api/obtain-tamus-ai-chat-api-key/

2.  Open your Bash startup script using the `nano` text editor:
    ```bash
    nano ~/.bashrc
    ```

3.  Scroll to the bottom of the file and paste the following two lines. **You must replace the placeholder API key with your actual key from the step above.**

    ```bash
    # Configuration for Opencode.ai and TAMUS AI Endpoint
    export TAMUS_AI_CHAT_API_ENDPOINT="https://chat-api.rellis.tamus.ai/api"
    export TAMUS_AI_CHAT_API_KEY="your-personal-secret-api-key-goes-here"
    ```

4.  Save the file and exit `nano` (Press `Ctrl + X`, then `Y`, then `Enter`).

5.  Apply the changes to your current terminal session by "sourcing" the file.
    ```bash
    source ~/.bashrc
    ```
    *(Note: Any new terminal you open from now on will have these variables loaded automatically.)*

6.  **Verification Step:** Confirm that your variables are correctly set and that your credentials work.
    - First, check that the variables are loaded:
      ```bash
      echo $TAMUS_AI_CHAT_API_ENDPOINT
      echo $TAMUS_AI_CHAT_API_KEY
      ```
      These commands should print your URL and key, not blank lines.

    - Next, use `curl` to ask the endpoint for its list of models. This is the definitive test that your URL and API Key are correct.
      ```bash
      curl -s -X GET "$TAMUS_AI_CHAT_API_ENDPOINT/models" \
        -H "Authorization: Bearer $TAMUS_AI_CHAT_API_KEY" \
        | jq -r '.data[].id'
      ```
      If this command successfully prints a list of model names, your environment is perfectly configured.

---

### **Step 3: Create the Project Configuration File**

This file, `opencode.json`, will live in your shared project directory. It tells Opencode.ai how to connect to our endpoint and what models are available.

**NEW:** This config now includes **cost tracking** so the TUI shows real dollar amounts as you use models!

1.  Navigate to your project's root directory.

2.  Create a new file named `opencode.json`.
    ```bash
    nano opencode.json
    ```

3.  Paste the entire block of code below into the file. It securely references the environment variables you set in the previous step.

    ```json
    {
      "$schema": "https://opencode.ai/config.json",
      "compaction": {
        "auto": true,
        "prune": true
      },
      "provider": {
        "TAMUS": {
          "npm": "@ai-sdk/openai-compatible",
          "name": "TAMUS AI Endpoint",
          "options": {
            "baseURL": "{env:TAMUS_AI_CHAT_API_ENDPOINT}",
            "apiKey": "{env:TAMUS_AI_CHAT_API_KEY}"
          },
          "models": {
            "protected.Claude 3.5 Haiku": {
              "name": "Claude 3.5 Haiku",
              "limit": { "context": 200000, "output": 8000 },
              "cost": { "input": 0.0000008, "output": 0.000004 }
            },
            "protected.Claude 3.5 Sonnet": {
              "name": "Claude 3.5 Sonnet",
              "limit": { "context": 200000, "output": 4096 },
              "cost": { "input": 0.000003, "output": 0.000015 }
            },
            "protected.Claude 3.7 Sonnet": {
              "name": "Claude 3.7 Sonnet",
              "limit": { "context": 200000, "output": 8192 },
              "cost": { "input": 0.000003, "output": 0.000015 }
            },
            "protected.Claude Haiku 4.5": {
              "name": "Claude Haiku 4.5",
              "limit": { "context": 200000, "output": 64000 },
              "cost": { "input": 0.000001, "output": 0.000005 }
            },
            "protected.Claude Opus 4.5": {
              "name": "Claude Opus 4.5",
              "limit": { "context": 200000, "output": 64000 },
              "cost": { "input": 0.000005, "output": 0.000025 }
            },
            "protected.Claude Sonnet 4": {
              "name": "Claude Sonnet 4",
              "limit": { "context": 200000, "output": 64000 },
              "cost": { "input": 0.000003, "output": 0.000015 }
            },
            "protected.Claude Sonnet 4.5": {
              "name": "Claude Sonnet 4.5",
              "limit": { "context": 200000, "output": 64000 },
              "cost": { "input": 0.000003, "output": 0.000015 }
            },
            "protected.gemini-2.0-flash": {
              "name": "Gemini 2.0 Flash",
              "limit": { "context": 1048576, "output": 8192 },
              "cost": { "input": 0.00000015, "output": 0.0000006 }
            },
            "protected.gemini-2.0-flash-lite": {
              "name": "Gemini 2.0 Flash Lite",
              "limit": { "context": 1048576, "output": 8192 },
              "cost": { "input": 0.000000075, "output": 0.0000003 }
            },
            "protected.gemini-2.5-flash": {
              "name": "Gemini 2.5 Flash",
              "limit": { "context": 1048576, "output": 65535 },
              "cost": { "input": 0.00000015, "output": 0.0000006 }
            },
            "protected.gemini-2.5-flash-lite": {
              "name": "Gemini 2.5 Flash Lite",
              "limit": { "context": 1048576, "output": 65536 },
              "cost": { "input": 0.0000001, "output": 0.0000004 }
            },
            "protected.gemini-2.5-pro": {
              "name": "Gemini 2.5 Pro",
              "limit": { "context": 1048576, "output": 65535 },
              "cost": { "input": 0.00000125, "output": 0.00001 }
            },
            "protected.gpt-4.1": {
              "name": "GPT 4.1",
              "limit": { "context": 1047576, "output": 32768 },
              "cost": { "input": 0.000002, "output": 0.000008 }
            },
            "protected.gpt-4o": {
              "name": "GPT-4o",
              "limit": { "context": 128000, "output": 16384 },
              "cost": { "input": 0.0000025, "output": 0.00001 }
            },
            "protected.gpt-5": {
              "name": "GPT-5",
              "limit": { "context": 400000, "output": 128000 },
              "cost": { "input": 0.00000125, "output": 0.00001 }
            },
            "protected.gpt-5.2": {
              "name": "GPT-5.2",
              "limit": { "context": 400000, "output": 128000 },
              "cost": { "input": 0.00000175, "output": 0.000014 }
            },
            "protected.gpt-5.4": {
              "name": "GPT-5.4",
              "limit": { "context": 400000, "output": 128000 },
              "cost": { "input": 0.00000175, "output": 0.000014 }
            },
            "protected.llama3.2": {
              "name": "Llama 3.2",
              "limit": { "context": 131072, "output": 32768 },
              "cost": { "input": 0.000001, "output": 0.000003 }
            },
            "protected.o3-mini": {
              "name": "o3 Mini",
              "limit": { "context": 200000, "output": 100000 },
              "cost": { "input": 0.0000011, "output": 0.0000044 }
            },
            "protected.o4-mini": {
              "name": "o4 Mini",
              "limit": { "context": 200000, "output": 100000 },
              "cost": { "input": 0.0000011, "output": 0.0000044 }
            }
          }
        }
      },
      "model": "TAMUS/protected.gpt-5.4"
    }
    ```

    **Key Features of This Config:**
    - ✅ **Cost tracking enabled** - TUI shows real dollar amounts as you use models
    - ✅ **Accurate limits** - All models have correct context/output token limits
    - ✅ **Schema compliant** - Uses proper `limit` and `cost` fields (not deprecated `options.max_tokens`)
    - ✅ **Ready to commit** - Safe to share in your team's Git repository

4.  This file can now be safely committed to your team's shared Git repository.

#### **A Note on Global vs. Project Configuration**

You can also set up a **global** configuration by placing a file with this content at `~/.config/opencode/opencode.json`. However, please be aware that any project-specific `opencode.json` file in your current directory will always override the global settings.

---

### **Step 4: Final Verification and Usage**

With everything configured, you can now test the full setup from within your project directory.

1.  **Verification Step 1: List the Models.** Run the `models` command. You should see the models from TAMUS listed under the `TAMUS` provider.
    ```bash
    opencode models
    ```

2.  **Verification Step 2: Run a Prompt.** Use the `run` command to send a request to the default model (e.g., `TAMUS/protected.gpt-5.4`).
    ```bash
    opencode run "Why is the sky blue?"
    ```
    If this returns a valid response from the AI, your setup is complete.

#### **Daily Usage**

*   **Use a single command:**
    ```bash
    opencode run "Your prompt here"
    ```

*   **Use a different model from the command line:** Use the `--model` flag. Remember to put quotes around model names that contain spaces.
    ```bash
    opencode --model TAMUS/"protected.Claude Sonnet 4.5" run "Your prompt here"
    ```

*   **Launch the interactive TUI (Text User Interface):** This is the most common way to use the tool for a conversational session.
    1.  Run the `opencode` command by itself in your project directory **in a terminal emulator from the options in the prerequisites section**:
        ```bash
        opencode
        ```
    2.  This will launch an interactive chat session with the default model (configured in `opencode.json`).
    3.  To switch models while inside the TUI, type `/models` and press Enter. This will bring up a menu where you can select from all configured models.
    4.  To exit the TUI, press `Ctrl + C`.

    **NEW:** The TUI sidebar now shows **real-time cost tracking** - you'll see token usage and dollar amounts as you chat!

---

### **Troubleshooting & Known Issues**

#### **Issue 1: `max_tokens` Error**

*   **Symptom:** You receive an error that says `BadRequestError - max_tokens is too large`.
*   **Cause:** The tool's default request for the maximum response size is larger than the model's limit.
*   **Solution:** The new config uses the correct `limit.output` field for all models. If you still see this error, verify you're using the latest config template above.

#### **Issue 2: Error with certain Claude models**

*   **Symptom:** You receive an error: `An unexpected error occurred: 'NoneType' object has no attribute 'replace'`.
*   **Cause:** This appears to be a bug in how the Opencode tool handles the API response from specific Claude models on our endpoint.
*   **Affected Models:**
    *   `protected.Claude 3.5 Haiku`
    *   `protected.Claude 3.5 Sonnet`
    *   `protected.Claude 3.7 Sonnet`
*   **Solution:** To avoid this error, do not use these models with the `run` command. They are included in the configuration file for completeness but may not be compatible at this time. Use `protected.Claude Haiku 4.5` or `protected.Claude Sonnet 4.5` instead.

#### **Issue 3: Cost tracking shows $0.00**

*   **Symptom:** The TUI sidebar shows "$0.00 spent" even after using models.
*   **Cause:** Costs are calculated based on the `cost` field in your config. If using a model without cost data, it will show $0.
*   **Solution:** Verify your model has `cost.input` and `cost.output` fields defined in `opencode.json`.

---

### **Further Exploration & Resources**

Now that you have the CLI configured, you can explore the full capabilities of the Opencode.ai ecosystem.

*   **Official Website**: https://opencode.ai/
*   **Official Documentation**: https://opencode.ai/docs
*   **Official GitHub Repository**: https://github.com/anomalyco/opencode
*   **TAMUS AI Docs**: https://docs.tamus.ai/
*   **TAMUS Model Pricing**: https://docs.tamus.ai/docs/prod/models
*   **TAMUS Daily Usage Allowance**: https://docs.tamus.ai/docs/prod/daily-usage-allowance

#### **Editor Integrations**

*   **VS Code:** Opencode.ai offers a powerful extension for Visual Studio Code, allowing you to run prompts, manage models, and interact with the AI directly within your editor. Search for "Opencode.ai" in the VS Code Marketplace.

*   **Neovim:** For users of Neovim, there are community-driven plugins and configurations available that integrate Opencode.ai into your Vim workflow. Check the documentation and GitHub for the latest community projects.

#### **Advanced Features**

*   **Custom Skills**: Create reusable agent instructions in `~/.config/opencode/skills/`
*   **Custom Commands**: Define one-shot commands in `~/.config/opencode/commands/`
*   **Custom Agents**: Build specialized agents for specific tasks in `~/.config/opencode/agents/`
*   **MCP Servers**: Extend opencode with external tools via Model Context Protocol
*   **LSP Integration**: Enable `OPENCODE_EXPERIMENTAL_LSP_TOOL=true` for go-to-definition, find-references

---

### **Quick Reference: Model Cost Tiers**

| Tier | Cost (Input/Output per 1M) | Models |
|------|---------------------------|--------|
| **$** | <$1 / <$5 | Gemini 2.0 Flash Lite, Claude Haiku 3.5/4.5 |
| **$$** | $1-3 / $5-15 | GPT-5, GPT-4.1, o3/o4-mini, Gemini 2.5 Pro |
| **$$$** | $3+ / $15+ | Claude Sonnet 4/4.5, Claude Opus 4.5 |

**Daily Token Allowance:** Each TAMUS user gets a daily token allowance that resets between 6-7 PM CT. See [Daily Usage Allowance](https://docs.tamus.ai/docs/prod/daily-usage-allowance) for details.
