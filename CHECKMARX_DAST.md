# Scanning VulnerableMCP with Checkmarx DAST

How to run a Checkmarx One DAST scan against this intentionally vulnerable MCP server.

## Quick start

1. **Start the server**

   ```bash
   git clone https://github.com/cx-frederic-malo/VulnerableMCP.git
   cd VulnerableMCP
   docker compose up -d
   ```

2. **Create a DAST environment in Checkmarx One** ([setup wizard docs](https://docs.checkmarx.com/en/34965-433922-dast-environment-setup-wizard.html))

   | Setting | Value |
   |---|---|
   | Environment type | **MCP** |
   | URL | `http://host.docker.internal:3000/mcp` |
   | Reachability | **Private** |
   | Authentication | **Not Authenticated** |

3. **Run a scan** from the new environment, then review the results in Checkmarx One.

> Use `host.docker.internal`, not `localhost`. The scanner runs in its own container, where `localhost` is the scanner itself.

## Troubleshooting

| Symptom | Fix |
|---|---|
| `Connection refused` on `initialize`, `Imported 0 requests from MCP server` | The URL uses `localhost`. Change it to `http://host.docker.internal:3000/mcp` |
| `curl http://localhost:3000/health` returns `Empty reply from server` | The server only listens inside its container. Check that the Dockerfile `CMD` includes `--allow-external-connections`, then run `docker compose up -d --build` |

## What to expect

DAST tests the MCP JSON-RPC requests for classic flaws such as command injection (`execute_system_command`), SQL injection (`search_users`), path traversal (`read_file`) and template injection (`render_template`).

MCP-specific issues (tool poisoning, hidden ANSI instructions, tool shadowing, rug pulls) target the LLM rather than HTTP behavior, so DAST generally does not report them. See [VULNERABILITIES.md](VULNERABILITIES.md) for the full list.

## Learn more

- [DAST environment setup wizard](https://docs.checkmarx.com/en/34965-433922-dast-environment-setup-wizard.html)
- [Running a DAST scan](https://docs.checkmarx.com/en/34965-154700-dast-running-a-scan.html)
- [Viewing DAST results](https://docs.checkmarx.com/en/34965-154701-dast-viewing-results.html)
- [Triaging DAST vulnerabilities](https://docs.checkmarx.com/en/34965-378967-triaging-dast-vulnerabilities.html)
- [Using the DAST CLI](https://docs.checkmarx.com/en/34965-501341-using-the-dast-cli.html), for pipelines and command-line scans
