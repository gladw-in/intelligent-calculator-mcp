# Intelligent Calculator MCP

A small MCP server that exposes one tool, `solve_math_problem`, for apple-addition
and tree-distance word problems. A Groq-hosted model (via LangChain) picks the right
calculator tool, and the server is reachable from ChatGPT through an OpenAI
Secure MCP Tunnel.

## Setup

```bash
python -m venv env
# Windows: env\Scripts\activate      macOS/Linux: source env/bin/activate
pip install -r requirements.txt
cp .env.example .env      # then put your Groq key in .env
python server.py
```

The server listens on `http://127.0.0.1:8000/mcp`.

## Connect to ChatGPT with a Secure MCP Tunnel

1. Create a tunnel at https://platform.openai.com/settings/organization/tunnels and
   associate it with your ChatGPT workspace. Copy the `tunnel_id`.
2. Create a Platform runtime API key with Tunnels Read + Use.
3. Download the **full** `tunnel-client` (not the runtime-only build) from
   https://github.com/openai/tunnel-client/releases/latest.
4. In a second terminal (Windows cmd shown):

```
set CONTROL_PLANE_API_KEY=<your runtime key>
tunnel-client.exe init --sample sample_mcp_remote_no_auth --profile calculator --tunnel-id <tunnel_id> --mcp-server-url http://127.0.0.1:8000/mcp
tunnel-client.exe doctor --profile calculator --explain
tunnel-client.exe run --profile calculator
```

5. In ChatGPT: Plugins -> + -> Add custom MCP server -> Connection: **Tunnel**,
   select your tunnel, Authentication: **No authentication**.

Keep both the server and `tunnel-client run` running while you use it.

## Test prompts

See `examples.json` and try, for example:

- One person brings five apples and another brings ten. How many apples do they have together? (15)
- A tree is 12 m above eye level at a 30 degree angle of elevation. How far away is it? (about 20.78 m)
- A tree is 10 m above eye level at a 95 degree angle. (asks for a correction)

## Security

Never commit `.env` or any API key. `.gitignore` already excludes them.
