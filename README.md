# containers

Podman-based development environments with AI coding agents.

## What's included

A shared base image (`agent-base`) provides a full multi-language toolchain on Ubuntu 24.04. Five agent images derive from it:

| Image | Agent | Containerfile |
|---|---|---|
| `opencode-dev` | OpenCode | `Containerfile-opencode` |
| `claude-dev` | Claude Code | `Containerfile-claude` |
| `codex-dev` | Codex | `Containerfile-codex` |
| `cursor-dev` | Cursor | `Containerfile-cursor` |
| `gemini-dev` | Gemini CLI | `Containerfile-gemini` |

### Toolchain (base image)

| Language / Domain | Toolchain |
|---|---|
| C / C++ | clang, clangd, clang-format, clang-tidy, clang-tools, cmake, build-essential |
| Java | OpenJDK 21, Maven, Gradle |
| Rust | rustup (minimal), rust-analyzer, clippy, rustfmt, just |
| Python | uv, Python 3.12, pyright, PyTorch (CPU), NumPy, SciPy, pandas, scikit-learn, etc. |
| Node.js | Node.js 22, npm |
| Android | cmdline-tools, platform-tools (NDK install commented out — enable in Containerfile) |
| CLI tools | ripgrep, fd, git, zsh |

LSP servers (clangd, pyright, rust-analyzer, JDTLS) are managed by the agent at runtime.

## Prerequisites

- [Podman](https://podman.io/) (not Docker)

The launcher locates the Containerfile relative to itself, so the repo can live anywhere on disk. Clone it to wherever you like, e.g. `~/containers`.

## Quick start

### One-time setup

```bash
git clone https://github.com/sgaflv/containers ~/containers
export PATH="$HOME/containers:$PATH"
```

Add the `export` line to your shell profile (`~/.bashrc`, `~/.zshrc`, etc.) to make it permanent.

### Launch an agent

```bash
cd /path/to/your/project
opencode    # or: claude, codex, cursor, gemini
```

On first run the image is built (slow), then a per-project container is created and the agent launches inside it. Subsequent runs reuse the existing container.

## Usage

| Command | Description |
|---|---|
| `opencode` | Launch OpenCode in the current project's container |
| `opencode sh` | Open a bash shell in the existing OpenCode container |
| `opencode rm` | Remove all OpenCode containers and images |
| `claude` | Launch Claude Code in the current project's container |
| `claude sh` | Open a bash shell in the existing Claude container |
| `claude rm` | Remove all Claude containers and images |
| `codex` | Launch Codex in the current project's container |
| `codex sh` | Open a bash shell in the existing Codex container |
| `codex rm` | Remove all Codex containers and images |
| `cursor` | Launch Cursor in the current project's container |
| `cursor sh` | Open a bash shell in the existing Cursor container |
| `cursor rm` | Remove all Cursor containers and images |
| `gemini` | Launch Gemini CLI in the current project's container |
| `gemini sh` | Open a bash shell in the existing Gemini container |
| `gemini rm` | Remove all Gemini containers and images |

Each project gets its own container (`<agent>-<projectname>`), so bind mounts never cross projects.

## Security

- `--cap-drop=ALL` — all Linux capabilities dropped
- `--security-opt=no-new-privileges` — prevents privilege escalation
- `--userns=keep-id` — UID/GID mapped to match the host user
- Agent config (`~/.config/opencode`, `~/.claude`, `~/.codex`, `~/.cursor`, `~/.gemini`) is bind-mounted, not baked into the image

## Customization

- **UID/GID:** Pass build args `--build-arg UID=$(id -u) --build-arg GID=$(id -g)` to match your host user.
- **PyTorch GPU:** Replace the CPU `--index-url` in the Containerfile with the appropriate CUDA index.
- **Android NDK:** Uncomment the `sdkmanager` install step in the Containerfile and accept licenses during build.
