# AI-Enhanced Wazuh SOC Automation

A home-lab Security Operations Center (SOC) automation project that combines Wazuh, Hermes, n8n, Ollama, PostgreSQL, and Telegram to monitor endpoint security events, analyze alerts with a local AI model, and notify operators based on severity.

This project is designed to demonstrate a practical, self-hosted SOC workflow in a local environment using open-source tooling.

## Overview

The system monitors Windows and Linux endpoints through Wazuh, collects alerts, filters and deduplicates them with Hermes, forwards them to n8n for orchestration, and uses a local language model (`qwen2.5:1.5b`) running via Ollama to assist with alert triage and severity classification.

Processed decisions are stored in PostgreSQL and routed by severity for Telegram notifications.

## Key Features

- Windows and Linux endpoint monitoring
- Wazuh SIEM alert collection and analysis
- AI-assisted alert triage using Ollama
- Automated orchestration with n8n
- PostgreSQL storage for AI verdicts and alerts
- Severity-based alert routing and notifications
- Deduplication, filtering, and fallback logic
- Local, lab-friendly deployment with Docker Compose

## Architecture

```text
[Windows/Linux Endpoints]
        |
        v
[Wazuh Manager + Agents]
        |
        v
[Hermes Poller]
        |
        v
[n8n Workflow Engine]
     /      |      \
    /       |       \
   v        v        v
[Ollama LLM] [PostgreSQL] [Telegram Bot]
```

## Workflow

1. Wazuh monitors hosts and emits security alerts.
2. Hermes polls the Wazuh API every 30 seconds.
3. Alerts are filtered by severity and deduplicated.
4. Valid alerts are forwarded to n8n.
5. n8n normalizes the alert payload.
6. Ollama analyzes the alert and returns a verdict with reasoning.
7. Verdicts are saved to PostgreSQL.
8. Alerts are routed by severity and sent to Telegram.

## Technology Stack

- Wazuh
- Hermes
- n8n
- Ollama
- PostgreSQL
- Telegram Bot API
- Docker and Docker Compose
- Python

## Project Goals

The objective of this project is to demonstrate how to build a home-lab SOC automation stack for:

- SIEM integration
- AI-assisted threat triage
- Security workflow orchestration
- Alert routing and operational response
- Practical home-lab security engineering

## Repository Structure

```text
wazuh-soc-automation/
├── README.md
├── docker-compose.yml
├── .env.example
├── .gitignore
├── services/
│   ├── hermes/
│   │   ├── app.py
│   │   ├── config.yaml
│   │   ├── Dockerfile
│   │   └── requirements.txt
│   ├── postgres/
│   │   └── init/
│   │       └── 01_schema.sql
│   └── n8n/
│       └── workflows/
│           └── soc_alert_workflow.json
├── scripts/
│   └── init-ollama.sh
└── .github/
    └── workflows/
        └── ci.yml
```

## Prerequisites

- Docker
- Docker Compose
- A Linux machine or VM for local deployment
- Wazuh manager access or a Wazuh container
- Telegram bot token and chat ID
- Basic familiarity with Docker networking and environment variables

## Quick Start

1. Clone the repository.
2. Copy `.env.example` to `.env` and update secrets and URLs.
3. Build and start the stack:

```bash
docker compose up -d --build
```

4. Pull the Ollama model:

```bash
docker exec -it ollama ollama pull qwen2.5:1.5b
```

5. Import the n8n workflow and configure the webhook.
6. Start generating alerts from Wazuh to validate the workflow.

## Notes

This project is intentionally designed for a home-lab or local environment. It is a practical demonstration of SOC automation and should be hardened before exposing it to public networks or production use.

## Security Warning

Do not expose the services directly to the internet without:

- proper firewall rules
- TLS termination
- authentication and authorization controls
- secret management
- rate limiting
- monitoring and logging

## License

This project is licensed under the MIT License.

## Contributing

Contributions are welcome. Please open an issue or submit a pull request with improvements, bug fixes, or documentation updates.
