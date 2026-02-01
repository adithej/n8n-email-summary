# Personal Email Summarizer with n8n & Gemini AI

A self-hosted, automated tool that summarizes your daily unread emails using Google's Gemini AI (Free Tier) and sends a report to your Telegram.

![n8n](https://img.shields.io/badge/n8n-workflow-orange?style=flat-square)
![Docker](https://img.shields.io/badge/Docker-Enabled-blue?style=flat-square)
![Gemini](https://img.shields.io/badge/AI-Gemini%20Flash-magenta?style=flat-square)

## Features
- **Daily Summaries**: Runs automatically at 9 AM (configurable).
- **Spam Detection**: Identifies potential spam or ads.
- **Smart Filtering**: Summarizes only unread emails from the last 24 hours.
- **Free to Run**: Uses the free tiers of Gemini API and Telegram.
- **Privacy Focused**: Self-hosted on your machine; data doesn't leave your control except for processing.

## Prerequisite
- [Docker](https://www.docker.com/) installed on your machine.
- A **Gmail** account (or any IMAP supported email).
- A **Telegram** account.
- A **Google Cloud** account (for Gemini API).

## Setup Guide

### 1. Clone & Initialize
```bash
git clone <repository-url>
cd n8n-email-summary
```

### 2. Configure Credentials
1.  **Copy the environment file**:
    ```bash
    cp .env.example .env
    ```
2.  **Edit `.env`** and fill in your details:

    *   **Email (Gmail)**:
        *   Turn on 2-Step Verification in your Google Account.
        *   Go to [App Passwords](https://myaccount.google.com/apppasswords) and create one for "Mail".
        *   Use this app password in `EMAIL_PASS`.
    *   **Gemini API**:
        *   Get a free API Key from [Google AI Studio](https://aistudio.google.com/app/apikey).
        *   Paste it in `GEMINI_API_KEY`.
    *   **Telegram Bot**:
        *   Message [@BotFather](https://t.me/BotFather) on Telegram.
        *   Create a new bot (`/newbot`) and get the **Token**.
        *   Start a chat with your bot.
        *   Get your Chat ID (you can forward a message to @userinfobot to find it).
        *   Fill in `TELEGRAM_BOT_TOKEN` and `TELEGRAM_CHAT_ID`.

### 3. Start n8n
```bash
docker compose up -d
```
Access n8n at `http://localhost:5678`.

### 4. Import Workflow
1.  Open n8n in your browser.
2.  Create a fresh workflow.
3.  Click the "three dots" menu (top right) -> **Import from File**.
4.  Select `workflow.json` from this folder.

### 5. Finalize Configuration
The workflow handles the AI and Telegram parts automatically using the `.env` file variables. However, **Email Credentials** must be set manually in n8n for security:

1.  Double click the **Fetch Unread Emails** node (IMAP).
2.  Under "Credential for IMAP", select **Create New Credential**.
3.  Fill in:
    *   User: `{{ $env.EMAIL_USER }}` (or type your email)
    *   Password: `{{ $env.EMAIL_PASS }}` (or type your app password)
    *   Host: `imap.gmail.com`
    *   Port: `993`
    *   SSL/TLS: `true`
4.  Click **Save**.

## Usage
- **Test It**: Click **Execute Workflow** at the bottom of the canvas. You should receive a Telegram message with your email summary.
- **Activate It**: Toggle the **Active** switch (top right) to enable the daily schedule.

## Troubleshooting
- **Permission Denied for Docker**: Ensure your user has docker permissions (`sudo usermod -aG docker $USER`).
- **Gmail Login Failed**: Ensure you are using an **App Password**, not your main password.
- **Workflow Error**: Check the execution logs in n8n for detailed error messages.
