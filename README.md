![ghidra_MCP_logo](https://github.com/user-attachments/assets/4986d702-be3f-4697-acce-aea55cd79ad3)

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://www.apache.org/licenses/LICENSE-2.0)
[![GitHub release (latest by date)](https://img.shields.io/github/v/release/kariemoorman/GhidraMCP)](https://github.com/kariemoorman/GhidraMCP/releases)
[![GitHub stars](https://img.shields.io/github/stars/kariemoorman/GhidraMCP)](https://github.com/kariemoorman/GhidraMCP/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/kariemoorman/GhidraMCP)](https://github.com/kariemoorman/GhidraMCP/network/members)
[![GitHub contributors](https://img.shields.io/github/contributors/kariemoorman/GhidraMCP)](https://github.com/kariemoorman/GhidraMCP/graphs/contributors)


# ghidraMCP
ghidraMCP is a Model Context Protocol server for allowing LLMs to autonomously reverse engineer applications. It exposes numerous tools from core Ghidra functionality to MCP clients.

<details>
<summary>Demo</summary>


https://github.com/user-attachments/assets/36080514-f227-44bd-af84-78e29ee1d7f9

</details>


## Features
### MCP Server + Ghidra Plugin

- Decompile and analyze binaries in Ghidra
- Automatically rename methods and data
- List methods, classes, imports, and exports

## Installation

### Prerequisites
- Install [Ghidra](https://ghidra-sre.org)
- Python3.10+
- MCP [SDK](https://github.com/modelcontextprotocol/python-sdk)
- Gradle (optional)


### Connecting MCP Server to Ghidra
---
<details>
<summary><b>Step 1: Download the Latest GhidraMCP Release</b></summary>
<br>

Option 1: Download latest release directly via Github Releases
- https://github.com/kariemoorman/GhidraMCP/releases

Option 2: Manually build from source using Gradle
- Run the following command: `gradle`

The generated zip file includes the built Ghidra plugin and its resources. These files are required for Ghidra to recognize the new extension:

- lib/GhidraMCP.jar
- extensions.properties
- Module.manifest

</details>

<details>
<summary><b>Step 2: Directly Import the Plugin into Ghidra</b></summary>

#### Plugin Installation
1. Run Ghidra
2. Select `File` -> `Install Extensions`
3. Click the `+` button
4. Select the `GhidraMCP-1-4.zip` (or your chosen version) from the downloaded release
5. Restart Ghidra

#### Plugin Configuration & Activation
6. Create or open a Ghidra project in the Project Manager
7. Import and open a binary for analysis (the plugin requires an active program)
8. Open the CodeBrowser tool (double-click your imported program or use Tools → CodeBrowser)
9. In the CodeBrowser, navigate to `File` → `Configure` → `Developer`, and enable the GhidraMCPPlugin in the Developer tools list

*Note: The HTTP server will start automatically when the plugin is enabled with an active program*

#### Server Configuration
10. *Optional*: Configure the server port in CodeBrowser via `Edit` → `Tool Options` → `GhidraMCP HTTP Server` (Default server address: `http://127.0.0.1:8080/`)

*Note: The HTTP server only runs when (1)  CodeBrowser is open, (2) a program is loaded, and (3) GhidraMCPPlugin is enabled*

Video Installation Guide:

https://github.com/user-attachments/assets/75f0c176-6da1-48dc-ad96-c182eb4648c3

</details>



### Connecting MCP Server MCP Clients
---
Integrate GhidraMCP server into your MCP Client of choice, e.g.,

<details>
<summary><b>Claude Desktop</b></summary>

To set up Claude Desktop as a Ghidra MCP client, go to `Claude` -> `Settings` -> `Developer` -> `Edit Config` -> `claude_desktop_config.json` and add the following:

```json
{
  "mcpServers": {
    "ghidra": {
      "command": "python",
      "args": [
        "/ABSOLUTE_PATH_TO/bridge_mcp_ghidra.py",
        "--ghidra-server",
        "http://127.0.0.1:8080/"
      ]
    }
  }
}
```

Alternatively, edit this file directly:

```
/Users/YOUR_USER/Library/Application Support/Claude/claude_desktop_config.json
```

The server IP and port are configurable and should be set to point to the target Ghidra instance. If not set, both will default to localhost:8080.

</details>


<details>
<summary><b>Claude CLI</b></summary>

To add GhidraMCP to Claude CLI, use the following command:

```bash
 claude mcp add ghidra "python3" "ABSOLUTE_PATH_TO/bridge_mcp_ghidra.py" "--ghidra-server" "http://localhost:8080"
```

</details>

<details>
<summary><b>LM Studio</b></summary>

To use GhidraMCP with [LM Studio](https://lmstudio.ai), go to Tool Icon (top right) -> Program -> Install -> Edit mcp.json, and add the following:

If using the GhidraMCP outside of a container:

```json
{
  "mcpServers": {
    "ghidra": {
      "command": "python",
      "args": [
        "/Users/kmo/Github/GhidraMCP/bridge_mcp_ghidra.py",
        "--ghidra-server",
        "http://127.0.0.1:8080/"
      ]
    }
  }
}
```

If using the GhidraMCP inside a VM (replace URL with your VM’s IP from ipconfig):

```json
{
  "mcpServers": {
    "ghidra": {
      "url": "http://196.xxx.xxx:8081/mcp"
    }
  }
}
```

If using GhidraMCP inside a Docker container (replace Docker image name with your image name from `docker build` or `docker-compose build` step): 

```json
{
  "mcpServers": {
    "my-mcp-server": {
      "command": "docker",
      "args": [
        "run",
        "--rm",
        "-i",
        "ghidra-app:latest"
      ]
    }
  }
}
```

</details>


---