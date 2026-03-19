<p align="center">
  <h1 align="center">📺 media-cli</h1>
  <p align="center">
    One script to manage your entire *arr media stack from the terminal.
    <br />
    <strong>Sonarr / Radarr / Prowlarr / qBittorrent / Bazarr / Jellyseerr</strong>
  </p>
</p>

<p align="center">
  <a href="#install">Install</a> •
  <a href="#quick-start">Quick Start</a> •
  <a href="#commands">Commands</a> •
  <a href="#ai-agent-integration">AI Agents</a> •
  <a href="#connection-modes">Remote/SSH</a>
</p>

---

**media-cli** is a single bash script that wraps the APIs of your entire media automation stack into simple, memorable commands. No Docker, no Node, no Python packages. Just `curl`, `python3` (for JSON parsing), and your existing *arr setup.

Built for humans who manage media servers from the terminal, and for AI agents that do it on their behalf.

```bash
$ media movies search "Interstellar"
 [157336] Interstellar (2014) 169min
    The adventures of a group of explorers who make use of a newly discovered wormhole...

$ media movies add "Interstellar"
✅ Added: Interstellar (2014) - Searching for downloads...

$ media downloads active
[  23.4%] Interstellar.2014.1080p.BluRay.x265  (4.2 MB/s) 12m
```

## Supported Services

| Service | Status | What it does |
|---------|--------|-------------|
| [Sonarr](https://sonarr.tv) | Required | TV show search, add, monitor, manage |
| [Radarr](https://radarr.video) | Required | Movie search, add, monitor, manage |
| [Prowlarr](https://prowlarr.com) | Required | Indexer status and management |
| [qBittorrent](https://www.qbittorrent.org) | Required | Download monitoring and control |
| [Bazarr](https://www.bazarr.media) | Optional | Subtitle status and history |
| [Jellyseerr](https://github.com/Fallenbagel/jellyseerr) | Optional | User requests and trending content |
| [Tdarr](https://tdarr.io) | Optional | Transcode monitoring (GPU/CPU worker progress) |

## Requirements

- `bash` 4.0+
- `curl`
- `python3` (standard library only, no pip)
- `ssh` (only if using remote mode)

## Install

**One-liner:**

```bash
curl -fsSL https://raw.githubusercontent.com/solomonneas/media-cli/main/media -o ~/bin/media && chmod +x ~/bin/media
```

**Or clone:**

```bash
git clone https://github.com/solomonneas/media-cli.git
cd media-cli
bash install.sh
```

Make sure `~/bin` is in your `PATH` (add `export PATH="$HOME/bin:$PATH"` to your shell profile if needed).

## Quick Start

```bash
# 1. Run the setup wizard
media setup

# 2. Test your connection
media status

# 3. Start using it
media movies search "The Matrix"
media shows add "Breaking Bad"
media downloads active
```

The setup wizard asks for your API URLs and keys, then saves everything to `~/.config/media-cli/config`. You can also copy `config.example` and edit it by hand.

### Finding Your API Keys

| Service | Where to find it |
|---------|-----------------|
| Sonarr | Settings > General > API Key |
| Radarr | Settings > General > API Key |
| Prowlarr | Settings > General > API Key |
| Bazarr | Settings > General > API Key |
| Jellyseerr | Settings > General > API Key |
| qBittorrent | Settings > Web UI > Username/Password |

Or grab them from the config files directly:

```bash
# Linux
grep -i apikey ~/.config/Sonarr/config.xml
grep -i apikey ~/.config/Radarr/config.xml

# Windows
type C:\ProgramData\Sonarr\config.xml | findstr ApiKey

# Docker
docker exec sonarr cat /config/config.xml | grep ApiKey
```

## Commands

### Library

```bash
media movies list              # List all movies with download status
media movies search "title"    # Search online (via Radarr)
media movies add "title"       # Add top result + start downloading
media movies remove "title"    # Remove from library (keeps files)
media movies missing           # Show monitored movies without files

media shows list               # List all shows with episode counts
media shows search "title"     # Search online (via Sonarr)
media shows add "title"        # Add top result + search for episodes
media shows remove "title"     # Remove from library (keeps files)
```

### Downloads

```bash
media downloads                # List all torrents grouped by state
media downloads active         # Active downloads with speed + ETA
media downloads pause <hash|all>
media downloads resume <hash|all>
media downloads remove <hash> [true]   # true = also delete files
```

### Monitoring

```bash
media status                   # Service health + library counts + active downloads
media queue                    # Sonarr/Radarr download queues
media wanted                   # Missing episodes and movies
media calendar [days]          # Upcoming releases (default: 7 days)
media history [sonarr|radarr|all] [limit]
media indexers                 # List Prowlarr indexers
media refresh [movies|shows|all]   # Trigger library rescan
```

### Subtitles (Bazarr)

```bash
media subs                     # Wanted subtitles for movies + episodes
media subs history             # Recent subtitle downloads
```

### Requests (Jellyseerr)

```bash
media requests                 # Pending user requests
media requests trending        # What's trending
media requests users           # User list with request counts
```

### Transcoding (Tdarr)

```bash
media tdarr                    # Status, resources, active workers
media tdarr workers            # Per-file progress: %, fps, size reduction, ETA
media tdarr queue              # Items queued for processing
```

## Connection Modes

### Local Mode

Services run on the same machine as the CLI:

```bash
MEDIA_HOST="local"
```

### SSH Mode

Services run on a remote host (NAS, dedicated server, Windows box) and bind to `localhost`. The CLI runs curl commands over SSH:

```bash
MEDIA_HOST="ssh:mediaserver"     # Uses your SSH config alias
MEDIA_HOST_OS="linux"            # or "windows"
```

This is the killer feature for headless servers. Your services don't need to be exposed to the network. The CLI tunnels everything through SSH.

**Windows hosts work too.** POST requests automatically use PowerShell's `Invoke-RestMethod` when `MEDIA_HOST_OS="windows"`, so you don't need curl installed on the Windows side.

## AI Agent Integration

This CLI was built alongside [OpenClaw](https://openclaw.ai), an AI agent platform. The commands are designed to be easily parsed by AI assistants.

Any AI agent or automation tool that can run shell commands can use media-cli:

**Natural language to commands:**

> "What shows am I missing episodes for?"
```bash
media wanted
```

> "Add Succession and start downloading it"
```bash
media shows add "Succession"
```

> "What's actively downloading right now?"
```bash
media downloads active
```

> "Pause all downloads"
```bash
media downloads pause all
```

Works with OpenClaw, LangChain tool calling, Claude computer use, or any agent framework that supports shell execution.

## How It Works

```
┌──────────────┐     ┌─────────────────────────┐
│  media-cli   │────▶│  SSH (optional)          │
│  (your box)  │     │  curl commands run on    │
└──────────────┘     │  the media server        │
                     └───────────┬─────────────┘
                                 │
                    ┌────────────┼────────────┐
                    ▼            ▼            ▼
               ┌────────┐  ┌────────┐  ┌──────────┐
               │ Sonarr  │  │ Radarr │  │ Prowlarr │
               │  :8989  │  │  :7878 │  │  :9696   │
               └────────┘  └────────┘  └──────────┘
                    │            │
                    ▼            ▼
               ┌──────────────────────┐
               │    qBittorrent       │
               │       :8080          │
               └──────────────────────┘
                         │
                    ┌────┴────┐
                    ▼         ▼
               ┌────────┐ ┌───────────┐ ┌───────┐
               │ Bazarr │ │Jellyseerr │ │ Tdarr │
               │  :6767 │ │   :5055   │ │ :8265 │
               └────────┘ └───────────┘ └───────┘
```

- Single bash script (~900 lines), no external dependencies
- Talks to *arr v3 APIs (Sonarr/Radarr), v1 (Prowlarr), v2 (qBittorrent WebUI)
- Python3 is used strictly for JSON parsing (standard library only)
- Config file is stored at `~/.config/media-cli/config` with `chmod 600`
- No telemetry, no analytics, no network calls except to your own services

## Contributing

PRs welcome. Some ideas:

- [ ] Lidarr support (music)
- [ ] Readarr support (books)
- [ ] Tab completion (bash/zsh)
- [ ] Interactive mode (fzf-based search and select)
- [ ] Notification hooks (Discord/Telegram on download complete)
- [x] Tdarr integration (transcode status, worker progress, queue)

## License

[MIT](LICENSE)
