# Roadmap

Most of the original roadmap has shipped. What's left is one parked item.

Contributions welcome. If you're picking something up, open an issue first so we don't double-dip.

## Parked

### Notifiarr

Notification hub with a Discord focus. Two shapes from arr-cli:

1. Read: pull recent notifications across all *arrs in one feed.
2. Write: send ad-hoc notifications from scripts.

```bash
media notifiarr recent
media notifiarr send "Library backup complete"
```

Parked because the primary maintainer doesn't run Notifiarr (uses Signal via n8n). Happy to land it if a contributor wants it; open an issue and we'll spec the verbs.

## Not Planned

For clarity, things that get asked about but won't land here:

- **Profilarr** - Recyclarr already syncs TRaSH-guide custom formats and quality profiles. Profilarr is the same job with a different distribution model. One is enough.
- **Ombi** - Jellyseerr is already covered; Ombi would be a duplicate surface.
- **Sabnzbd / NZBGet** - arr-cli is torrent-first by design. A `media usenet` namespace could happen if there's real demand, but it's not on the roadmap today.
- **Plex** - See [jellyfin-mcp](https://github.com/solomonneas/jellyfin-mcp) for the playback side. Plex is out of scope.

## Already Shipped

What used to live in this file:

| Feature       | Command(s)                               | Shipped in |
|---------------|------------------------------------------|------------|
| Readarr books | `media audiobooks ...`                   | `b5548c5` (ebooks second instance), `c95a0a3` (audiobooks fix) |
| Readarr ebooks| `media ebooks ...`                       | `b5548c5` |
| Lidarr        | `media music ...`                        | `befe882` |
| Mylar3        | `media comics ...`                       | `e57e937` |
| Recyclarr     | `media recyclarr ...`                    | shipped   |
| Hunt          | `media hunt ...` (native replacement)    | `cfd18d0` |
| Autobrr       | `media autobrr ...`                      | `494b088` |
| Cross-seed    | `media crossseed ...`                    | `eff5fff` |
| Unpackerr     | `media unpackerr ...`                    | shipped   |

Hunt is a native reimplementation of the Huntarr loop because upstream Huntarr was pulled over a security issue. `media hunt` is the canonical surface now.

## How to Propose Changes

Open an issue with `roadmap:` in the title, or a PR editing this file. Low bar, just argue for it.
