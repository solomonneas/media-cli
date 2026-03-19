# media-cli

A single-file bash CLI for managing your Jellyfin/*arr media stack. Search, add, download, and monitor your entire media library from the terminal or through an AI agent.

Works locally or over SSH for headless/remote media servers.

## Supported Services

| Service | Purpose |
|---------|---------|
| **Sonarr** | TV show management |
| **Radarr** | Movie management |
| **Prowlarr** | Indexer management |
| **qBittorrent** | Download client |
| **Bazarr** | Subtitle downloads (optional) |
| **Jellyseerr** | User request portal (optional) |

## Requirements

- `bash` (4.0+)
- `curl`
- `python3`
- `ssh` (only for remote mode)

## Install

```bash
git clone https://github.com/solomonneas/media-cli.git
cd media-cli
bash install.sh
```

Or just copy the `media` script to your PATH:

```bash
curl -o ~/bin/media https://raw.githubusercontent.com/solomonneas/media-cli/main/media
chmod +x ~/bin/media
```

## Setup

Interactive setup wizard:

```bash
media setup
```

This creates `~/.config/media-cli/config` with your API keys and connection settings. Or copy `config.example` and edit manually.

### Connection Modes

**Local** (services on this machine):
```
MEDIA_HOST="local"
```

**SSH** (services on a remote host, binding to localhost):
```
MEDIA_HOST="ssh:myserver"
MEDIA_HOST_OS="linux"    # or "windows"
```

The SSH mode runs curl commands on the remote host, so it works even when services only bind to `127.0.0.1`.

## Usage

```bash
# Check everything is working
media status

# Search and add content
media movies search "Interstellar"
media movies add "Interstellar"
media shows search "Breaking Bad"
media shows add "Breaking Bad"

# Library management
media movies list
media movies missing
media shows list

# Downloads
media downloads active
media downloads list
media downloads pause all
media downloads resume all

# Monitoring
media queue                  # Sonarr/Radarr queues
media wanted                 # Missing content
media calendar 14            # Upcoming releases
media history                # Recent activity
media indexers               # Prowlarr indexers

# Subtitles (requires Bazarr)
media subs status
media subs history

# Requests (requires Jellyseerr)
media requests list
media requests trending

# Maintenance
media refresh                # Trigger library scan
```

## AI Agent Integration

This CLI was originally built for use with [OpenClaw](https://openclaw.ai) (an AI agent platform), but works great with any AI assistant or automation tool that can run shell commands.

Example prompt for your AI agent:
> "Search for 'The Good Place' and add it to my library"

The agent runs:
```bash
media shows search "The Good Place"
media shows add "The Good Place"
```

## How It Works

- Single bash script, no dependencies beyond curl and python3
- Talks to *arr APIs (v3 for Sonarr/Radarr, v1 for Prowlarr)
- qBittorrent uses cookie-based auth (WebUI API v2)
- All credentials stay in your local config file (chmod 600)
- Python is used only for JSON parsing (no pip packages needed)

## API Key Locations

| Service | Config File | Key Field |
|---------|------------|-----------|
| Sonarr | `config.xml` in Sonarr data dir | `<ApiKey>` |
| Radarr | `config.xml` in Radarr data dir | `<ApiKey>` |
| Prowlarr | `config.xml` in Prowlarr data dir | `<ApiKey>` |
| Bazarr | `config.yaml` in Bazarr data dir | `auth.apikey` |
| Jellyseerr | `settings.json` in Jellyseerr config | `main.apiKey` |
| qBittorrent | WebUI settings | Username/password |

## License

MIT
