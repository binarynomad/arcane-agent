# Arcane Agent — Docker Compose Setup

This folder runs the [Arcane](https://getarcane.app) agent in Docker Compose so it can be managed by your main Arcane instance.

## Files

| File | Purpose |
|---|---|
| `compose.yaml` | The compose stack definition |
| `.env.example` | Example environment variables — copy to `.env` and fill in |

## Steps

### 1. Get the agent token from your Arcane server

The agent authenticates to your main Arcane instance with a token minted there:

1. Open the **Arcane web UI** on your main instance.
2. Go to **Environments → Add Environment**.
3. Choose **Direct**, give the environment a name, and set the agent address to this host with port `3553`.
4. Click **Generate Agent Configuration** — this creates an API key (token) that starts with `arc_`.
5. **Copy the token now.** Arcane will not show it again.

### 2. Create your `.env` file

```bash
cp .env.example .env
```

Then edit `.env` and replace the placeholder with the token from step 1:

```
AGENT_TOKEN=arc_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

The token lives only in `.env` — never hard-code it in `compose.yaml`. Docker Compose automatically reads `.env` from this directory and substitutes it into the compose file.

> Tip: keep `.env` out of version control (it should be in `.gitignore`).

### 3. (Optional) Manage local Docker projects

By default the agent only manages its own stack. If you want Arcane to manage compose projects in a folder on this machine, uncomment these lines in `compose.yaml`:

```yaml
environment:
  #- PROJECTS_DIRECTORY=/opt/docker

volumes:
  #- /opt/docker:/opt/docker
```

Adjust `/opt/docker` to wherever you keep your compose project folders.

### 4. Set the folder ownership

The arcane agent process drops down to **UID/GID 65532** by default. For it to read and write your Docker project folder, set the owner to that UID:

```bash
sudo chown -R 65532:65532 /opt/docker
```

### 5. Start the agent

```bash
docker compose up -d
```

Check the logs to confirm it connects to your Arcane instance:

```bash
docker compose logs -f arcane-agent
```

The agent connects outbound to your main Arcane instance, so you don't need to open any ports on this machine beyond the local `3553` binding.