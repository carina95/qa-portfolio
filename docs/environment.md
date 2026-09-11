# Test environment

**Machine:** MacBook, Apple Silicon (arm64)
**OS:** macOS 26.6.2 (Darwin 25.6.0)
**Recorded:** 2026-09-09

## Browsers
| Browser | Version | Notes |
|---|---|---|
| Google Chrome | 152.0.7977.83 | primary; `chrome://version` |
| Safari | bundled with macOS 26.6.2 | secondary |
| Firefox | not installed | needed for Lesson 47 |

## Command-line tools
| Tool | Version | Location |
|---|---|---|
| node | v26.8.1 | /opt/homebrew/bin/node |
| npm | 11.19.0 | /opt/homebrew/bin/npm |
| docker | 29.7.2 | /usr/local/bin/docker (symlink into Docker.app) |
| git | 2.50.1 | /usr/bin/git |
| curl | 8.7.1 | /usr/bin/curl |
| sqlite3 | 3.51.0 | /usr/bin/sqlite3 |
| Homebrew | 6.0.22 | /opt/homebrew/bin/brew |

## Services
| Service | Status | How to check |
|---|---|---|
| Docker daemon | running, ServerVersion 29.7.2 | `docker info` |

## System under test
| | |
|---|---|
| Application | (Lesson 5) |
| URL | http://localhost:3000 |
| Build version | (Lesson 5) |