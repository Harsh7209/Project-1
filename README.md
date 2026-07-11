# 🐳 Docker Troubleshooter Agent

<div align="center">

![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=for-the-badge&logo=python&logoColor=white)
![LangChain](https://img.shields.io/badge/LangChain-1C3C3C?style=for-the-badge&logo=langchain&logoColor=white)
![Ollama](https://img.shields.io/badge/Ollama-Local_LLM-black?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

**An autonomous AI agent that diagnoses and reasons about Docker container issues — powered by a local LLM, zero cloud dependencies.**

[Features](#-features) · [Architecture](#-architecture) · [Installation](#-installation) · [Usage](#-usage) · [How It Works](#-how-it-works) · [Contributing](#-contributing)

</div>

---

## 🧠 What Is This?

Docker Troubleshooter Agent is a **ReAct-based AI agent** that autonomously investigates your Docker environment. You ask a question in plain English — the agent decides which tools to call, interprets the results, reasons through the problem, and delivers a diagnosis.

No more copy-pasting `docker ps -a` output into ChatGPT. The agent *does the digging itself.*

```
> Why is my postgres container restarting?

Thinking...

I'll start by listing all containers to find the postgres container...
[Calls: list_containers]

Found container 'postgres' with status 'Restarting (1) 3 seconds ago'.
Now I'll pull the logs to understand the crash reason...
[Calls: get_logs(postgres)]

The logs reveal: "FATAL: data directory /var/lib/postgresql/data has wrong ownership"
This is a volume permission issue. Here's how to fix it...
```

---

## ✨ Features

| Feature | Details |
|---|---|
| 🤖 **Autonomous reasoning** | Uses the ReAct loop — Reason → Act → Observe — to iteratively diagnose issues without hand-holding |
| 🔒 **Fully local** | Runs on [Ollama](https://ollama.com) with `gemma4` — your container data never leaves your machine |
| 🛠️ **Built-in Docker tools** | Lists containers, fetches logs, and deep-inspects container configs in one agent |
| 💬 **Natural language interface** | Ask questions the way you'd ask a senior DevOps engineer |
| 🔌 **Extensible** | Add new tools (e.g., `docker stats`, `docker network inspect`) in minutes |
| ⚡ **Lightweight** | Pure Python, minimal dependencies, no infrastructure to manage |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    User (CLI Input)                      │
└───────────────────────┬─────────────────────────────────┘
                        │ natural language question
                        ▼
┌─────────────────────────────────────────────────────────┐
│              LangChain ReAct Agent                       │
│                                                          │
│   ┌──────────────────────────────────────────────────┐  │
│   │              ChatOllama (gemma4)                  │  │
│   │         Reason → Plan → Decide tool               │  │
│   └───────────┬──────────────────────────────────────┘  │
│               │                                          │
│   ┌───────────▼──────────────────────────────────────┐  │
│   │              Tool Dispatcher                      │  │
│   └───┬───────────────┬──────────────────┬───────────┘  │
│       │               │                  │               │
│  ┌────▼────┐   ┌──────▼──────┐   ┌──────▼──────┐        │
│  │  list_  │   │  get_logs   │   │  inspect_   │        │
│  │containe-│   │(container)  │   │  container  │        │
│  │  rs()   │   │             │   │             │        │
│  └────┬────┘   └──────┬──────┘   └──────┬──────┘        │
└───────┼───────────────┼─────────────────┼───────────────┘
        │               │                 │
        ▼               ▼                 ▼
┌───────────────────────────────────────────────────────┐
│                  Docker Engine (CLI)                   │
│       docker ps -a  /  docker logs  /  docker inspect │
└───────────────────────────────────────────────────────┘
```

The agent follows the **ReAct (Reasoning + Acting)** paradigm:
1. **Reason** — The LLM thinks about what information it needs
2. **Act** — It calls the appropriate Docker tool
3. **Observe** — It reads the tool output
4. **Repeat** — Until it has enough context to answer
5. **Respond** — Delivers a final, grounded diagnosis

---

## 📦 Prerequisites

Before you begin, make sure you have the following installed:

- **Python 3.11+**
- **Docker** — running and accessible via CLI
- **Ollama** — for running the local LLM

```bash
# Verify Docker is running
docker ps

# Verify Ollama is installed
ollama --version
```

---

## 🚀 Installation

### 1. Clone the repository

```bash
git clone https://github.com/your-username/docker-troubleshooter-agent.git
cd docker-troubleshooter-agent
```

### 2. Create a virtual environment

```bash
python3 -m venv venv
source venv/bin/activate        # macOS/Linux
# .\venv\Scripts\activate       # Windows
```

### 3. Install dependencies

```bash
pip install langchain langchain-ollama
```

Or, if a `requirements.txt` is present:

```bash
pip install -r requirements.txt
```

### 4. Pull the LLM model via Ollama

```bash
ollama pull gemma4
```

> 💡 This downloads the `gemma4` model locally (~5GB). It only needs to happen once.

---

## ▶️ Usage

```bash
python3 module-2/agent.py
```

You'll see the interactive prompt:

```
Docker Troubleshooter Agent
------------------------------
Ask me about your Docker containers. Type 'quit' to exit.

>
```

### Example queries you can try

```bash
# Container health
> Which containers are currently stopped?
> Are any containers in a restart loop?

# Log analysis
> What errors are in the nginx container logs?
> Why did my redis container crash?

# Deep inspection
> What port is my postgres container exposed on?
> What environment variables is the api container using?
> Is my web container attached to a custom network?

# General diagnosis
> Something is wrong with my app container. Can you investigate?
> Which container is most likely causing issues right now?
```

Type `quit`, `exit`, or `q` to exit the agent.

---

## 🔧 Available Tools

The agent has access to three core Docker tools:

### `list_containers()`
Runs `docker ps -a` to get a full picture of all containers — their names, status, image, and ports.

### `get_logs(container_name)`
Fetches the last 50 lines from a container's stdout/stderr — the agent uses this to spot crashes, errors, or misconfigurations.

### `inspect_container(container_name)`
Runs `docker inspect` to retrieve the full container metadata — network config, environment variables, volume mounts, restart policies, and more.

---

## 🔄 Switching the LLM

The agent uses `gemma4` by default, but you can swap in any model supported by Ollama:

```python
# In agent.py
llm = ChatOllama(model="llama3.2", temperature=0)   # Meta Llama 3.2
llm = ChatOllama(model="mistral", temperature=0)     # Mistral 7B
llm = ChatOllama(model="qwen2.5", temperature=0)     # Qwen 2.5
```

> 💡 For best results, use models with strong instruction-following and tool-use capabilities. `temperature=0` keeps the agent deterministic and focused.

---

## 🗂️ Project Structure

```
docker-troubleshooter-agent/
│
├── module-2/
│   └── agent.py          # Main agent — tools, LLM setup, ReAct loop
│
├── requirements.txt      # Python dependencies
└── README.md             # You're reading it
```

---

## 🛣️ Roadmap

- [ ] Add `docker stats` tool for real-time resource monitoring
- [ ] Add `docker network inspect` for network topology diagnosis
- [ ] Support multi-container issue correlation (e.g., service mesh problems)
- [ ] Add a `fix_container` tool with safe remediation actions
- [ ] Web UI frontend (Streamlit or Gradio)
- [ ] Support for Docker Compose projects (`docker compose ps`, `docker compose logs`)
- [ ] OpenAI / Anthropic model support as a fallback option

---

<div align="center">

Built with 🐳 Docker · 🦜 LangChain · 🦙 Ollama

*If this saved you a debugging session, give it a ⭐*

</div>
