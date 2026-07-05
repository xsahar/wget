# wget

A simple `wget` clone written in Rust, built on `tokio` and `reqwest`.

## Features

- Single or multiple URL downloads, sequential or concurrent (via `-i`)
- Resume partial downloads (`-c`)
- Download rate limiting (`--rate-limit`)
- Custom output filename / directory (`-O`, `-P`)
- Background downloads with logging to `wget-log` (`-B`)
- Recursive website mirroring with link conversion for offline browsing (`--mirror`)
- Progress bar with byte count, percentage, throughput, and ETA

## Build

```bash
cargo build --release
```

The binary is written to `target/release/wget`.

## Usage

```bash
wget [OPTIONS] [URLS]...
```

### Options

| Flag | Description |
| --- | --- |
| `-O, --output <FILE>` | Write the downloaded document to `FILE` |
| `-P, --directory-prefix <DIR>` | Save downloaded file(s) into `DIR` |
| `-B, --background` | Download in the background, logging to `wget-log` |
| `--rate-limit <RATE>` | Limit download rate, e.g. `200k`, `2M` |
| `-i, --input-file <FILE>` | Download every URL listed in `FILE` (4-way concurrency) |
| `-c, --continue-download` | Resume a partially downloaded file |
| `--timeout <SECS>` | Request timeout in seconds (default: `30`) |
| `--user-agent <UA>` | Override the `User-Agent` header |
| `--mirror` | Recursively mirror a website |
| `-R, --reject <SUFFIXES>` | Comma-separated file suffixes to skip (mirror mode only) |
| `-X, --exclude-dirs <DIRS>` | Comma-separated path prefixes to skip (mirror mode only) |
| `--convert-links` | Rewrite links in mirrored HTML/CSS for offline viewing (mirror mode only) |

### Examples

```bash
# Basic download
./wget https://example.com/file.zip

# Save under a specific name and directory
./wget -O out.zip -P ~/Downloads https://example.com/file.zip

# Resume an interrupted download, rate-limited to 1 MB/s
./wget -c --rate-limit=1M https://example.com/largefile.zip

# Download a list of URLs concurrently
./wget -i urls.txt

# Run in the background
./wget -B https://example.com/largefile.zip

# Mirror a site for offline browsing
./wget --mirror --convert-links -R=.jpg,.gif -X=/cache https://example.com
```

## Project layout

| Module | Purpose |
| --- | --- |
| [`src/cli`](src/cli) | Argument parsing and validation (`clap`) |
| [`src/http`](src/http/readme.md) | HTTP client: streaming downloads, resume, rate limiting |
| [`src/download`](src/download) | Concurrent download manager and progress reporting |
| [`src/resume`](src/resume/readme.md) | `Range` header helpers powering `-c` |
| [`src/rate`](src/rate) | Token-bucket rate limiter for `--rate-limit` |
| [`src/mirror`](src/mirror/readme.md) | Recursive site crawler and link rewriting for `--mirror` |
| [`src/io`](src/io) | Reading URL lists from file (`-i`) |
| [`src/utils`](src/utils/readme.md) | Shared helpers (filename extraction, path display) |

See each module's `readme.md` for implementation details.
