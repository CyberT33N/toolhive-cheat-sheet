# toolhive-cheat-sheet


# toolhive
- https://github.com/stacklok/toolhive?tab=readme-ov-file
- https://github.com/CyberT33N/homebrew-cheat-sheet/tree/main#install

## Install

### CLI
- https://docs.stacklok.com/toolhive/tutorials/quickstart-cli?installer=homebrew

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
