# OpenClaw Installation Guide (Windows)

OpenClaw (formerly Clawdbot) is an AI assistant that runs locally on your machine. This guide covers the setup process using **WSL (Windows Subsystem for Linux)** and the **WhatsApp** integration.

---

## 1. Install Windows Subsystem for Linux (WSL)

Open **PowerShell** as an Administrator and run:

```powershell
wsl --install

```

* **Note:** Restart your computer if prompted. After restarting, a Linux (Ubuntu) terminal will open to finish the setup.
* **Documentation:** [Getting started – Molt docs](https://docs.openclaw.ai/start/getting-started).

---

## 2. Install OpenClaw & Daemon

Inside your WSL terminal, run the following to install and configure the AI model:

```bash
npm install -g openclaw@latest
```

After install

```bash
openclaw onboard --install-daemon

```

* The `--install-daemon` flag ensures OpenClaw stays running in the background as a system service.

The wizard will ask for permissions.

* **Principle of Least Privilege:** Grant **basic** permissions initially. You can increase them later if needed.
* **Security:** Avoid granting full file-system access unless you specifically need the AI to manage your local files.
---

## 3. The Gateway: Connecting to WhatsApp

The **Gateway** is the bridge between OpenClaw and messaging platforms. During the setup wizard, select **WhatsApp (QR link)**.

### Initialization & Commands

* **View QR Code:** If you miss the initial prompt, force the QR code to appear in your terminal:
```bash
openclaw channels login

```

then choose whatsapp


* **Check Status:** Verify the connection to WhatsApp servers:
```bash
openclaw gateway status

```

## Done

Once the QR code is scanned and the following instrctions completed, your bot is live. Any message sent to the linked number will be processed by the AI.

