# Develop AI Agents in Azure

The exercises in this repo are designed to provide you with a hands-on learning experience in which you'll explore common tasks that developers perform when building AI agents on Microsoft Azure.

If you choose to run the code from applications in this repository locally, it will require Python 3.13+.

## Workshop Overview

This workshop is organized as a progressive path from environment setup to advanced multi-agent patterns:

1. Setup and [prerequisites](Exercises/00.Prerequisites.md)
2. Agent creation in Foundry portal and VS Code
3. Custom tools and MCP integration
4. Enterprise knowledge and Microsoft 365 integrations
5. Workflow orchestration and Agent Framework patterns
6. Multi-agent and remote agent communication with A2A

Primary content locations:

- Exercises: step-by-step workshop instructions
- Labfiles: starter code and runnable samples for each exercise
- Clone the repository to your local machine using `git clone <repository-url>`
- Navigate into the cloned repository using `cd <repository-directory>`


## Exercises At A Glance

| Exercise | Description | Est. time |
| --- | --- | --- |
| [00. Prerequisites](Exercises/00.Prerequisites.md) | Validate required tools, Azure access, Python version, and environment readiness before starting hands-on labs. | 10-15 min |
| [01. Build agent portal and VS Code](Exercises/01-build-agent-portal-and-vscode.md) | Build an end-to-end AI agent using Foundry portal and VS Code, including grounding and code interpreter usage. | 45 min |
| [02. Agent custom tools](Exercises/02-agent-custom-tools.md) | Add custom function tools to an agent and implement function-calling logic in Python. | 50 min |
| [03. MCP integration](Exercises/03-mcp-integration.md) | Extend an agent with Model Context Protocol tools to connect external services and data. | 60 min |
| [04. Integrate agent with Foundry IQ](Exercises/04-integrate-agent-with-foundry-iq.md) | Create a knowledge-grounded agent with Foundry IQ and query it programmatically from VS Code. | 45 min |
| [05a. M365 Teams integration](Exercises/05a-m365-teams-integration.md) | Publish an enterprise agent to Microsoft Teams and optionally Microsoft 365 Copilot. | 40 min |
| [05b. Work IQ integration](Exercises/05b-work-iq-integration.md) | Build an agent that uses Work IQ to reason over Microsoft 365 workplace context. | 40 min (optional/advanced) |
| [06. Build workflow in Foundry](Exercises/06-build-workflow-ms-foundry.md) | Create a UI-based workflow for ticket triage, routing, and automated response generation. | 30 min |
| [07. Agent Framework](Exercises/07-agent-framework.md) | Build a chat agent with Microsoft Agent Framework and Azure AI Agent Service. | 30 min |
| [08. Agent Framework multi-agents](Exercises/08-agent-framework-multi-agents.md) | Implement sequential orchestration with multiple specialized agents for feedback processing. | 30 min |
| [09. Multi-remote agents with A2A](Exercises/09-multi-remote-agents-with-a2a.md) | Connect remote agents using the A2A protocol for collaborative content generation tasks. | 30 min |
