# Task Management API ? Jenkins Part 2

This repository contains the Part 2 solution with **two Jenkins agents**:
- `ci` agent for checkout, npm install, tests, and build metadata
- `docker` agent for credentials usage and Docker build/run/health check

## What is included

- Node.js + Express API (`GET /health`, `GET /tasks`)
- Jest + Supertest tests
- Dockerfile for the app image
- Jenkinsfile with split agents (`ci` + `docker`)
- Agent image with Docker CLI (for docker build/run)

## Requirements

- Jenkins Controller (already running from Part 1)
- Two Jenkins agents:
  - `ci-agent` (label: `ci`)
  - `docker-agent` (label: `docker`, Docker CLI access)

## Jenkins setup (agents)

Create two nodes in Jenkins:

**Node 1:**
- Name: `ci-agent`
- Labels: `ci`
- Remote root: `/home/jenkins/agent`
- Launch method: connect agent to controller

**Node 2:**
- Name: `docker-agent`
- Labels: `docker`
- Remote root: `/home/jenkins/agent`
- Launch method: connect agent to controller

Paste each node secret into `C:\Users\Dawid\Desktop\Jenkins\part_2\docker-compose.yml`.

Start both agents:

```powershell
cd C:\Users\Dawid\Desktop\Jenkins\part_2
docker compose up -d --build
```

## Jenkins credentials

Add **Secret text** credential:
- ID: `demo-token`
- Secret: any value (e.g., `my-secret`)

The pipeline validates the secret without printing it to logs.

## Pipeline behavior

Stages:
- Checkout (ci)
- Install Dependencies (ci)
- Run Tests (ci)
- Build Metadata + artifact (ci)
- Credentials Check (docker)
- Docker Build (docker)
- Docker Run + health check (docker)

Health check:
```
http://host.docker.internal:3000/health
```

## How to run

1) Configure the pipeline job in Jenkins:
- Definition: Pipeline script from SCM
- Repo URL: `https://github.com/Dav1D-G/task-management-api-part-2.git`
- Branch: `*/main`

2) Run **Build Now**.

## Artifact

`build-info.txt` is archived by Jenkins on the CI agent.

