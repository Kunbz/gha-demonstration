# GitHub Actions Demonstration for CI/CD Migration from Jenkins

This repository demonstrates a simple CI/CD pipeline using GitHub Actions to show how teams running legacy Jenkins setups can adopt GitHub Actions. The demo highlights how Actions integrates closely with the code in the repository via events, and how GitHub-hosted and self-hosted runners remove the need to manage Jenkins agents.

Repository files
- [Dockerfile](Dockerfile)
- [main.py](main.py) — app entry; see [`main.app`](main.py), [`main.read_health`](main.py), [`main.read_root`](main.py)
- [requirements.txt](requirements.txt)
- [.dockerignore](.dockerignore)
- [.gitignore](.gitignore)
- [Workflow: build-docker-image.yml](.github/workflows/build-docker-image.yml)
- [Current README](readme.md)
- User shell config (context): [/Users/mac/.zshrc](/Users/mac/.zshrc)

Goal
- Show how GitHub Actions can replace Jenkins jobs for common CI/CD tasks (build, test, build-and-push Docker image).
- Demonstrate tighter integration with the codebase by triggering pipelines from repository events.
- Explain runner options so teams can stop managing fleet of Jenkins agents.

Why migrate from Jenkins to GitHub Actions
- Closer integration with the repository: workflows live next to code and are triggered by GitHub events (push, pull_request, tags).
- Reduced maintenance: no need to maintain a separate CI server and Jenkins agents. Use GitHub-hosted runners or run your own self-hosted runners inside client infrastructure.
- Simpler secrets and permissions: GitHub Secrets, environments, and fine-grained permissions reduce operational overhead.
- Reproducible pipelines stored as YAML in the repository, improving visibility and reviewability.

How this demo integrates with your code
- The Actions workflow [build-docker-image.yml](.github/workflows/build-docker-image.yml) runs on push to `main`. It checks out the repo, sets up Docker Buildx, logs in to Docker Hub, obtains a short SHA, and builds & pushes a Docker image.
- The workflow operates directly on the repository files:
  - Builds using [Dockerfile](Dockerfile).
  - Installs dependencies listed in [requirements.txt](requirements.txt).
  - Runs the application defined in [main.py](main.py) (FastAPI app object [`main.app`](main.py), endpoints [`main.read_health`](main.py) and [`main.read_root`](main.py)) when the image is run.

Runner options: no Jenkins agents to manage
- GitHub-hosted runners
  - Fully managed by GitHub.
  - Fast to start, maintained OS images, auto-scaling.
  - Ideal for typical CI workloads — no agent maintenance.
- Self-hosted runners
  - Install runners inside your client environment (on-prem or private cloud) when data locality or network constraints require it.
  - Offer the same GitHub Actions experience without running a separate Jenkins control plane.
- Benefit over Jenkins agents
  - Jenkins typically requires a master and a fleet of agents you must provision, secure, and scale.
  - Actions unifies orchestration into workflows and offers both managed and self-hosted runner models without the traditional Jenkins agent management burden.

What this demo shows
1. Build-and-push Docker image on push to `main` using [.github/workflows/build-docker-image.yml](.github/workflows/build-docker-image.yml).
2. Image contents originate from the repository and [Dockerfile](Dockerfile), and dependencies come from [requirements.txt](requirements.txt).
3. Application endpoints are defined in [main.py](main.py); use `/health` to verify basic readiness.

Quick local test
1. Build image locally:
   ```sh
   docker build -t gha-demo:local -f Dockerfile .

