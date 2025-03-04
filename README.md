# Heurist Mesh API

### Overview

Heurist Mesh provides a powerful REST API that enables developers to seamlessly interact with various AI agents and tools programmatically. This document outlines how to use the API client to submit jobs and retrieve results from different specialized agents.

### Installation

You can install the Heurist Mesh client directly from GitHub:

```bash
pip install git+https://github.com/rexdotsh/heurist-mesh-client.git
```

### Authentication

The API requires authentication using an API key. Set your API key as an environment variable:

```
HEURIST_API_KEY=your_api_key_here
```

Alternatively, you can use a `.env` file in your project directory:

```
# .env file
HEURIST_API_KEY=your_api_key_here
```

Then load it in your application:

```python
from dotenv import load_dotenv

# load environment variables from .env file
load_dotenv()

# the client will automatically use the HEURIST_API_KEY from environment
from heurist_mesh_client import MeshClient
client = MeshClient()
```

### Client Setup

The client supports both synchronous requests and asynchronous task patterns. Here's how to initialize the client:

```python
from heurist_mesh_client import MeshClient

# initialize the client
client = MeshClient()
```

### Request Patterns

The client supports two main request patterns:

1. **Synchronous Requests**: Get immediate results
2. **Asynchronous Tasks**: Create a task and poll for results

#### Synchronous Request Example

```python
# synchronous request
response = client.sync_request(
    agent_id="CoinGeckoTokenInfoAgent",
    query="What is the current price of Bitcoin?"
)
print(json.dumps(response, indent=2))
```

#### Asynchronous Task Example

```python
# create an asynchronous task
task = client.create_task(
    agent_id="CoinGeckoTokenInfoAgent",
    query="What is the current price of Bitcoin?"
)
print(f"Task created with ID: {task.task_id}")

# poll for results
while True:
    result = client.query_task(agent_id="CoinGeckoTokenInfoAgent", task_id=task.task_id)
    if result.status == "finished":
        print("Task completed:")
        print(json.dumps(result.model_dump(), indent=2))
        break
    print("Task in progress...")
    time.sleep(1)
```

### Request Types

The client handles two main types of requests:

1. Natural Language Queries
2. Tool-based Requests

#### Natural Language Example

```python
# natural language query
response = client.sync_request(
    agent_id="CoinGeckoTokenInfoAgent",
    query="What is the current price of Bitcoin?"
)
```

#### Tool-based Request Example

```python
# tool-based request with specific arguments
response = client.sync_request(
    agent_id="CoinGeckoTokenInfoAgent",
    tool="get_token_price",
    tool_arguments={"coingecko_id": "solana"},
)
```

### Parameters

- `agent_id` (String, required): The identifier for the Heurist agent you want to interact with.

- `query` (String, optional): Natural language query for the agent. Required if not using tool-based request.

- `tool` (String, optional): Name of the specific tool to invoke. Required for tool-based requests.

- `tool_arguments` (Dict, optional): Arguments for the tool being invoked. Required for tool-based requests.

- `raw_data_only` (Boolean, optional): Whether to return raw data without natural language processing. Default is False.

### Response Structure

The API returns responses in JSON format containing:

```python
# for sync_request:
{
    "result": {
        # agent-specific response data
    },
    "status": "success",  # or "error"
    "message": "optional status message"
}

# for query_task:
{
    "status": "finished",  # or "in_progress", "error"
    "reasoning_steps": [
        {
            "timestamp": 1234567890,
            "content": "step description",
            "is_sent": true
        }
    ],
    "result": {
        "response": {
            # agent-specific response data
        },
        "success": true  # or false
    }
}
```

### Available Agents

The Mesh API provides access to a growing ecosystem of specialized AI agents:

| Agent                        | Description                            |
| ---------------------------- | -------------------------------------- |
| AlloraPricePredictionAgent   | Price prediction for crypto assets     |
| BitquerySolanaTokenInfoAgent | Solana token information and analytics |
| CoinGeckoTokenInfoAgent      | Comprehensive cryptocurrency data      |
| DeepResearchAgent            | In-depth research and analysis         |
| DexScreenerTokenInfoAgent    | DEX trading data and metrics           |
| DuckDuckGoSearchAgent        | Web search capabilities                |
| ElfaTwitterIntelligenceAgent | Twitter data intelligence              |
| FirecrawlSearchAgent         | Specialized search functionality       |
| TokenContractSecurityAgent   | Smart contract security analysis       |
| MasaTwitterSearchAgent       | Twitter search and monitoring          |
| PumpFunTokenAgent            | Token pump detection and analysis      |
| ZkIgniteAnalystAgent         | ZK technology analysis                 |

For a complete and up-to-date list of available agents and their capabilities, visit the [Mesh Agents Metadata Endpoint](https://mesh.heurist.ai/mesh_agents_metadata.json).
