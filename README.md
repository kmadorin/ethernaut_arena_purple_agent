# Ethernaut Arena Purple Agent

An AI-powered smart contract security solver for [Ethernaut](https://ethernaut.openzeppelin.com/) challenges, built using Google Gemini and the [A2A (Agent-to-Agent)](https://a2a-protocol.org/latest/) protocol.

## Overview

This purple agent is part of the **Ethernaut Arena** evaluation platform. It works with the [Ethernaut Green Agent (evaluator)](../ethernaut_arena_green_agent) to solve Ethernaut smart contract security challenges.

**Architecture:**
- **Green Agent** (port 9010): Orchestrates the evaluation, manages blockchain, provides tools
- **Purple Agent** (port 9020): AI solver that analyzes contracts and finds exploits

## Project Structure

```
src/
└─ agent.py       # Purple agent implementation (Google ADK + Gemini)
tests/
└─ test_agent.py  # A2A conformance tests
Dockerfile        # Docker configuration
pyproject.toml    # Python dependencies
sample.env        # Environment variable template
.github/
└─ workflows/
   └─ test-and-publish.yml # CI workflow
```

## Prerequisites

- Python 3.11+
- [uv](https://github.com/astral-sh/uv) package manager
- Google API Key (for Gemini)

## Getting Started

1. **Set up environment variables:**
   ```bash
   cp sample.env .env
   # Edit .env and add your GOOGLE_API_KEY
   ```

2. **Install dependencies:**
   ```bash
   uv sync
   ```

3. **Run the agent:**
   ```bash
   uv run python src/agent.py --host 127.0.0.1 --port 9020
   ```

4. **Verify agent is running:**
   ```bash
   curl http://localhost:9020/.well-known/agent.json
   ```

## Running with Docker

```bash
# Build the image
docker build -t ethernaut-arena-purple-agent .

# Run the container
docker run -p 9020:9020 -e GOOGLE_API_KEY=your_key ethernaut-arena-purple-agent
```

## Integration with Green Agent

To run a full evaluation:

1. Start the green agent (from `ethernaut_arena_green_agent` repo):
   ```bash
   uv run src/server.py --host 127.0.0.1 --port 9010
   ```

2. Start this purple agent:
   ```bash
   uv run python src/agent.py --host 127.0.0.1 --port 9020
   ```

3. Send an evaluation request to the green agent with this purple agent as participant.

## Agent Capabilities

The purple agent can:
- Analyze Solidity smart contracts for vulnerabilities
- Execute JavaScript code in an ethers.js environment
- Deploy custom attack contracts
- Solve Ethernaut levels 0-40

**Supported vulnerability patterns:**
- Access control issues
- Integer overflow/underflow
- Reentrancy attacks
- tx.origin vs msg.sender confusion
- Delegatecall vulnerabilities
- Predictable randomness
- Storage slot manipulation
- Fallback function exploits

## Testing

```bash
# Install test dependencies
uv sync --extra test

# Start your agent first, then run tests
uv run pytest -v --agent-url http://localhost:9020
```

## Publishing

The repository includes a GitHub Actions workflow that automatically builds, tests, and publishes a Docker image to GitHub Container Registry.

**Required secrets:**
- `GOOGLE_API_KEY` - Add in Settings → Secrets and variables → Actions → Repository secrets

**Publishing triggers:**
- **Push to `main`** → publishes `latest` tag
- **Create git tag** (e.g. `git tag v1.0.0 && git push origin v1.0.0`) → publishes version tags

## Configuration

| Environment Variable | Description | Required |
|---------------------|-------------|----------|
| `GOOGLE_API_KEY` | Google API key for Gemini | Yes |

| CLI Argument | Description | Default |
|--------------|-------------|---------|
| `--host` | Host to bind to | `127.0.0.1` |
| `--port` | Port to listen on | `9020` |
| `--card-url` | URL for agent card | Auto-generated |

## License

MIT
