---
layout: post
title: "From Bash to Copilot: My Bioinformatics Terminal Workflow"
author: Steve Baeyen
date: 2025-07-27
categories: [bioinformatics, terminal]
tags: [WSL, tmux, vscode, github-copilot, productivity, linux]
description: "How I use WSL, tmux, SSH, VSCode, and GitHub Copilot to streamline bioinformatics research and development."
---

## 🧬 Introduction

Welcome to my latest post on bioinformatics workflows! As a researcher, I often find myself or PhD students juggling large datasets, remote compute resources, and various command-line tools. My terminal setup has evolved to meet these challenges head-on, allowing me to work efficiently and reproducibly.
Efficient command-line workflows are critical in bioinformatics, where data is large, compute is remote, and tools are often UNIX-native. Over time, I've developed a terminal setup that allows me to:

- Work on a Windows machine using Linux tools via **WSL**
- Manage remote sessions with **tmux** and **SSH over VPN**
- Write and edit scripts seamlessly with **VSCode**
- Leverage **GitHub Copilot** for fast prototyping and pipeline development

This post outlines my setup and some practical use cases, aimed at researchers looking to increase productivity and reproducibility.

---

## 🖥️ 1. Base Environment: WSL2 + Oh My Bash

### Why WSL2?

Windows Subsystem for Linux lets me use a full Ubuntu environment directly on my Windows machine without dual-booting. It's ideal for combining GUI apps (e.g., VSCode, Excel) with command-line tools (e.g., `snakemake`, `blast`, `samtools`).

### Oh My Bash Customization

I use [Oh My Bash](https://github.com/ohmybash/oh-my-bash) for a cleaner prompt and quick aliases.

Example `.bashrc` snippet:

```bash
# Bioinfo aliases
alias q='exit'
alias c='clear'
alias la='ls -lah'
alias gs='git status'
alias act='conda activate'
alias srunbio='srun --mem=32G --time=4:00:00 --pty bash'

# Prompt customization
OSH_THEME="agnoster"
```

You can also add function wrappers for reproducible workflows.

---

## 🧱 2. Terminal Multiplexing with `tmux`

`tmux` is essential for working over SSH, especially with flaky VPNs. It allows persistent, detachable sessions.

My `.tmux.conf` contains:

```bash
# Remap prefix to Ctrl-a
unbind C-b
set -g prefix C-a
bind C-a send-prefix

# Split panes
bind | split-window -h
bind - split-window -v
```

Usage example:
```bash
ssh my-hpc
tmux new -s unicycler
# Run long assembly job
```

If the VPN drops, I just reconnect and run:
```bash
tmux attach -t unicycler
```

---

## 🔐 3. SSH Access via VPN

I access clusters via VPN with `openconnect`, and use SSH config for shorthand:

```bash
# ~/.ssh/config
Host hpc
  HostName hpc.myuniversity.edu
  User steve
  IdentityFile ~/.ssh/id_rsa
```

Then I simply use:
```bash
ssh hpc
```

Pro tip: use `autossh` for auto-reconnect.

---

## 🧠 4. VSCode for Local and Remote Editing

VSCode is my go-to editor because of:

- Git integration
- Remote extensions (`Remote - WSL`, `Remote - SSH`)
- LSP-based Python + R completion

I often run:
```bash
code .
```
in WSL for native editing. For SSH:
```bash
code --remote ssh-remote+hpc /data/myproject
```

### Sample `.vscode/settings.json`:

```json
{
  "python.pythonPath": "/home/steve/miniconda3/envs/bactools/bin/python",
  "terminal.integrated.defaultProfile.linux": "bash",
  "files.exclude": {
    "**/.snakemake": true,
    "**/.ipynb_checkpoints": true
  }
}
```

---

## 🤖 5. GitHub Copilot for Code Assistance

I use [GitHub Copilot](https://github.com/features/copilot) within VSCode to speed up:

- Bash scripts
- Snakemake rules
- Python functions

### Real example:
I start typing:
```python
def parse_gff(file):
```
Copilot suggests a full parser using Biopython. I tweak it for my genome annotation workflow.

While Copilot isn't perfect (especially on biological assumptions), it cuts down repetitive coding time significantly.

---

## 🧪 6. Real-World Workflow

Example: hybrid genome assembly using **Unicycler**.

```bash
# On local WSL2
code ~/projects/xanthomonas-unicycler

# Edit script with Copilot help

# SSH into cluster
ssh hpc
tmux new -s unicycler

# Launch job
unicycler -1 sample_R1.fastq -2 sample_R2.fastq -l sample_ONT.fastq -o unicycler_out

# Monitor logs in tmux pane
```

I version my pipeline with `git`, and VSCode auto-syncs changes when working over SSH.

---

## 🧭 Conclusion

This terminal setup has made my work more efficient and reproducible. Key takeaways:

- Use **WSL2** to unify Linux tooling on Windows
- Leverage **tmux** and **SSH** to stay connected
- Let **VSCode + Copilot** speed up coding without sacrificing control

I plan to expand this setup further with:

- Singularity/Apptainer container integration
- Jupyter remote kernel support over SSH
- Dotfile automation with `chezmoi`

---

## 📎 Resources & Links

- [Oh My Bash GitHub](https://github.com/ohmybash/oh-my-bash)
- [tmux Cheatsheet](https://tmuxcheatsheet.com/)
- [GitHub Copilot](https://github.com/features/copilot)
- [VSCode Remote Development](https://code.visualstudio.com/docs/remote/remote-overview)
- [WSL2 Install Guide](https://learn.microsoft.com/en-us/windows/wsl/install)

---

_Comments or suggestions? Reach out or fork my [dotfiles repo](#link-to-be-added)._