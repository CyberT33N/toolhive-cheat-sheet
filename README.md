# toolhive-cheat-sheet
- https://github.com/stacklok/toolhive?tab=readme-ov-file



## Install

### CLI
- https://docs.stacklok.com/toolhive/tutorials/quickstart-cli?installer=homebrew

<details><summary>Click to expand..</summary>

1. Install
```shell
brew tap stacklok/tap
brew install thv

# Verify your installation
thv version
```

If you get `Error: Broken pipe`

<details><summary>Click to expand..</summary>

```shell
brew cleanup -s
rm -rf $(brew --cache)
brew install thv

```

</details>


## Step 2: Register your client

```sh
thv client setup
```

Use spacebar to select clients, then press Enter.

**Check status:**

```sh
thv client status
```

Example output:

```
┌────────────────┬───────────┬────────────┐
│ CLIENT TYPE    │ INSTALLED │ REGISTERED │
├────────────────┼───────────┼────────────┤
│ vscode         │ ✅ Yes    │ ❌ No      │
└────────────────┴───────────┴────────────┘
```

---

## Step 3: Find an MCP server to run

```sh
thv registry list
```

Example output:

```
NAME       DESCRIPTION                                     TIER        STARS   PULLS
atlassian  MCP server for Atlassian products              Community   2194    7789
fetch      Provides web content fetching for AI clients   Community   56714   9078
github     GitHub MCP Server                              Official    16578   5000
notion     Official Notion MCP server                     Official    2358    1109
```

Inspect a server:

```sh
thv registry info fetch
```

---

## Step 4: Run the Fetch MCP server

```sh
thv run fetch
```

Example output:

```
8:41AM INF MCP server ghcr.io/stackloklabs/gofetch/server:latest is verified successfully
8:41AM INF Pulling image: ghcr.io/stackloklabs/gofetch/server:latest
Status: Image is up to date
8:41AM INF MCP server is running in the background (PID: 16834)
8:41AM INF Use 'thv stop fetch' to stop the server
```

---

## Step 5: Verify the server is running

```sh
thv list
```

Example output:

```
NAME    PACKAGE                                      STATUS    URL                                PORT    TOOL TYPE   CREATED AT
fetch   ghcr.io/stackloklabs/gofetch/server:latest   running   http://127.0.0.1:15266/sse#fetch   15266   mcp         2025-07-10 08:41:56 -0400 EDT
```

---

## Step 6: Use the MCP server with your AI client

Restart your AI client (VS Code, Cursor, etc.), then try:

```
Fetch content from https://toolhive.dev and summarize it.
```

The AI should use the Fetch MCP server to retrieve and process the website.

---

## Step 7: Stop the server when you're done

Stop server:

```sh
thv stop fetch
```

Remove completely:

```sh
thv rm fetch
```

</details>

</details>
















<br><br>

---

<br><br>



# CLI

## MCP Servers

### Run
- This guide explains how to run Model Context Protocol (MCP) servers using ToolHive. It covers how to run servers from the ToolHive registry, customize server settings, and run custom servers using Docker images or protocol schemes.


<details><summary>Click to expand..</summary>

## Run a server from the registry

To run an MCP server from the ToolHive registry, use:

```bash
thv run <SERVER_NAME>
````

The server name is the same as in the registry.

### Local containerized servers

Example: run the **fetch** server:

```bash
thv run fetch
```

### Remote MCP servers

Remote servers use ToolHive’s transparent HTTP proxy:

```bash
thv run neon
thv run stripe
```

### Naming convention

* `notion-remote` → remote version of `notion`
* `neon`, `stripe` → only remote, no suffix

Run remote Notion:

```bash
thv run notion-remote
```

Discover servers:

```bash
thv search <NAME>
thv registry list
```

---

## Run registry groups

Run multiple servers in a group:

```bash
thv group run <GROUP_NAME>
```

Example:

```bash
thv group run dev-toolkit
```

---

## What happens under the hood?

### Local servers

* Pulls image & launches container
* Starts HTTP proxy on random port
* Labels container with:

  * `toolhive: true`
  * `toolhive-name: <SERVER_NAME>`

### Remote servers

* Uses pre-configured URL & authentication
* Handles OAuth/OIDC
* Starts HTTP proxy on random port
* No container is created

---

## Customize server settings

### Custom name

```bash
thv run --name <FRIENDLY_NAME> <SERVER>
```

Example:

```bash
thv run --name my-fetch fetch
```

### Secrets

```bash
thv run --secret <SECRET_NAME>,target=<ENV_VAR> <SERVER>
```

Example:

```bash
thv run --secret github,target=GITHUB_PERSONAL_ACCESS_TOKEN github
```

### Groups

```bash
thv run --group <GROUP_NAME> <SERVER>
```

### File system access

Use `--volume` or `.thvignore`.

### Network isolation

```bash
thv run --isolate-network <SERVER>
```

### Extra arguments

```bash
thv run <SERVER> -- <ARGS>
```

Check defaults before overriding:

```bash
thv registry info <SERVER> --format json | jq '.args'
```

### Proxy port

```bash
thv run --proxy-port <PORT_NUMBER> <SERVER>
```

---

## Tool filtering & overrides

Filter tools:

```bash
thv run --tools <TOOL_1> <SERVER>
thv run --tools <TOOL_1> --tools <TOOL_2> <SERVER>
```

Override tool names/descriptions with JSON:

```json
{
  "toolsOverride": {
    "fetch": {
      "name": "toolhive-docs-fetch",
      "description": "Fetches docs from ToolHive."
    }
  }
}
```

Run with override:

```bash
thv run --tools-override override.json fetch
```

---

## Run a custom MCP server

### Docker image

```bash
thv run [--name <NAME>] [--transport <stdio|sse|streamable-http>] <IMAGE> -- <ARGS>
```

Example:

```bash
thv run --name my-mcp --transport streamable-http my-mcp-server:latest -- --arg1 v1
```

### Protocol schemes

Run directly from package managers:

```bash
thv run uvx://<PACKAGE>@<VERSION>
thv run npx://<PACKAGE>@<VERSION>
thv run go://<PACKAGE>@<VERSION>
```

Example:

```bash
thv run --name aws-docs uvx://awslabs.aws-documentation-mcp-server@latest
```

---

## Transport & ports

Map target port:

```bash
thv run --transport streamable-http --target-port <PORT> <SERVER>
```

Example:

```bash
thv run --transport streamable-http --target-port 3000 my-mcp -- http --port 3000
```

---

## Custom CA certificates

### Global config

```bash
thv config set-ca-cert /path/to/ca.crt
thv config get-ca-cert
thv config unset-ca-cert
```

### Per-command

```bash
thv run --ca-cert /path/to/other-ca.crt uvx://some-package
```

Priority:

1. `--ca-cert`
2. Global config
3. Default

---

## Run a remote MCP server

Run by URL:

```bash
thv run https://api.example.com/mcp
```

With name:

```bash
thv run https://api.example.com/mcp --name my-server
```

With SSE transport:

```bash
thv run https://api.example.com/sse --transport sse
```

### Authentication

#### OIDC

```bash
thv run https://api.example.com/mcp \
  --remote-auth-issuer https://auth.example.com \
  --remote-auth-client-id my-client-id \
  --remote-auth-client-secret my-client-secret
```

#### OAuth2

```bash
thv run https://api.example.com/mcp \
  --remote-auth-authorize-url https://auth.example.com/oauth/authorize \
  --remote-auth-token-url https://auth.example.com/oauth/token \
  --remote-auth-client-id my-client-id \
  --remote-auth-client-secret my-client-secret
```

#### Options

* Custom scopes: `--remote-auth-scopes read,write`
* Timeout: `--remote-auth-timeout 2m`
* Skip browser: `--remote-auth-skip-browser`

---

## Remote server management

* List: `thv list`
* Logs: `thv logs <SERVER>`
* Stop: `thv stop <SERVER>`
* Restart: `thv restart <SERVER>`
* Remove: `thv rm <SERVER>`

---

## Share & reuse configs

### Export

```bash
thv export <SERVER> ./config.json
```

### Import

```bash
thv run --from-config ./config.json
```



</details>



