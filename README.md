# Persian TV EPG

Automated XMLTV EPG feeds for Persian-language TV channels, rebuilt hourly by GitHub Actions and committed straight to this repository.

## Feeds

| Channel | Source | Feed URL |
|---|---|---|
| Iran International | Official schedule on iranintl.com | `https://raw.githubusercontent.com/SandObserver/farsi-channels-epg/main/output/iranintl.xml` |
| Manoto TV | Current or most recent YouTube live title | `https://raw.githubusercontent.com/SandObserver/farsi-channels-epg/main/output/manoto.xml` |

Point any XMLTV-compatible client (Jellyfin, Tvheadend, TiviMate, and similar) at either URL directly.

## How it works

A GitHub Actions workflow (`.github/workflows/generate-epg.yml`) runs on an hourly schedule and can also be triggered manually. Each run does the following:

1. `iranintl/generate.py` fetches the official schedule page and extracts the embedded schedule data, then writes it out as XMLTV.
2. `manoto/generate.py` queries the YouTube Data API for Manoto's channel and writes a single all-day programme using the current or most recent live video's title. Manoto does not publish a real schedule, so this is a stand-in, not an actual programme guide.
3. If either output file changed, the workflow commits and pushes it.

## Limitations

- Iran International's data comes from parsing an internal JSON blob embedded in the page markup, since there is no public API. If the site changes its page structure, generation can fail or produce incomplete results.
- Manoto's entry is a single day-long block titled after whatever video is currently or was most recently live. It is not a real programme schedule and should not be treated as one.
- Both feeds are best-effort and may be inaccurate, incomplete, or unavailable without notice.

## Running locally

Requires Python 3.12 and the packages in `requirements.txt`.

```
pip install -r requirements.txt
python iranintl/generate.py
YOUTUBE_API_KEY=your_key python manoto/generate.py
```

Manoto's script needs a YouTube Data API key with the YouTube Data API v3 enabled, passed as the `YOUTUBE_API_KEY` environment variable. In the deployed workflow this is stored as a repository secret.

Both scripts write their output to `output/`, relative to the current working directory, so run them from the repository root.

## License

The code in this repository is licensed under the MIT License, see `LICENSE`. This license does not extend to the schedule data or video titles the scripts fetch, those remain the property of Iran International and YouTube/the channel owner respectively.
