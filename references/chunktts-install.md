# chunktts Install Fallback

Use this only when `command -v chunktts` fails.

Path policy:
- Use absolute user paths only (`$HOME/.local/...`).
- Never install into a relative workspace path such as `./.local/...`.
- Never create `.local` under the current project directory.

Release page:
- `https://github.com/planetis-m/chunktts/releases/latest`

## Linux x86_64

```bash
set -euo pipefail
mkdir -p "$HOME/.local/opt/chunktts" "$HOME/.local/bin"
curl -fsSL -o "$HOME/.local/opt/chunktts/chunktts.tar.gz" \
  "https://github.com/planetis-m/chunktts/releases/latest/download/chunktts-linux-x86_64.tar.gz"
rm -rf "$HOME/.local/opt/chunktts/current"
mkdir -p "$HOME/.local/opt/chunktts/current"
tar -xzf "$HOME/.local/opt/chunktts/chunktts.tar.gz" -C "$HOME/.local/opt/chunktts/current"
ln -sfn "$HOME/.local/opt/chunktts/current/chunktts" "$HOME/.local/bin/chunktts"
export PATH="$HOME/.local/bin:$PATH"
chunktts --help >/dev/null
```

## macOS arm64

```bash
set -euo pipefail
mkdir -p "$HOME/.local/opt/chunktts" "$HOME/.local/bin"
curl -fsSL -o "$HOME/.local/opt/chunktts/chunktts.tar.gz" \
  "https://github.com/planetis-m/chunktts/releases/latest/download/chunktts-macos-arm64.tar.gz"
rm -rf "$HOME/.local/opt/chunktts/current"
mkdir -p "$HOME/.local/opt/chunktts/current"
tar -xzf "$HOME/.local/opt/chunktts/chunktts.tar.gz" -C "$HOME/.local/opt/chunktts/current"
ln -sfn "$HOME/.local/opt/chunktts/current/chunktts" "$HOME/.local/bin/chunktts"
export PATH="$HOME/.local/bin:$PATH"
chunktts --help >/dev/null
```

## Windows x86_64 (PowerShell)

```powershell
$ErrorActionPreference = "Stop"
$dst = "$HOME\.local\opt\chunktts"
New-Item -ItemType Directory -Force -Path $dst | Out-Null
$zip = Join-Path $env:TEMP "chunktts.zip"
Invoke-WebRequest -Uri "https://github.com/planetis-m/chunktts/releases/latest/download/chunktts-windows-x86_64.zip" -OutFile $zip
Remove-Item -Recurse -Force (Join-Path $dst "current") -ErrorAction SilentlyContinue
New-Item -ItemType Directory -Force -Path (Join-Path $dst "current") | Out-Null
Expand-Archive -Path $zip -DestinationPath (Join-Path $dst "current") -Force
$exe = Get-ChildItem -Path (Join-Path $dst "current") -Recurse -Filter "chunktts.exe" | Select-Object -First 1
$exeDir = Split-Path -Parent $exe.FullName
$env:Path = "$exeDir;$env:Path"
chunktts --help | Out-Null
```

## DeepInfra API key configuration

`chunktts` requires an API key. After installation, present these instructions to
the user before generating audio.

**Recommended: environment variable**
Linux/macOS: `export DEEPINFRA_API_KEY="your_api_key"`
Windows PowerShell: `$env:DEEPINFRA_API_KEY = "your_api_key"`

**Alternative: update config.json**
Create or edit `config.json` inside the directory where the real binary lives
and set:
```json
{
  "api_key": "your_deepinfra_api_key"
}
```

## Notes

- Keep `config.json` and any bundled runtime libraries with the real binary.
- Do not copy only `chunktts`/`chunktts.exe` into another directory without its
  bundled runtime files.
- Ensure install targets are under user home (`$HOME/.local` or `%USERPROFILE%\.local`),
  not the current workspace.
- If install fails due to permission or sandbox restrictions, request escalated
  permission and retry.
