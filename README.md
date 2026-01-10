# Backend.AI Engine Registry Template

This directory contains template files for setting up the `backend.ai-engine-registry` repository.

## Repository Structure

```
backend.ai-engine-registry/
├── engines.json                 # Registry index (main entry point)
├── packages/                    # (Optional) Local package storage
│   └── *.baiengine
├── scripts/
│   └── update-registry.py       # Update registry after builds
└── .github/
    └── workflows/
        └── release-engine.yml   # Release automation
```

## Setup Instructions

### 1. Initialize Repository

Copy the template files to the new repository:

```bash
# Clone the new repository
git clone git@github.com:lablup/backend.ai-engine-registry.git
cd backend.ai-engine-registry

# Copy template files
cp -r /path/to/backend.ai-go/docs/engine-registry-template/* .

# Commit and push
git add .
git commit -m "feat: Initialize engine registry"
git push origin main
```

### 2. Configure engines.json

Edit `engines.json` to add your engine definitions:

```json
{
  "schemaVersion": 1,
  "lastUpdated": "2025-01-10T00:00:00Z",
  "baseUrl": "https://github.com/lablup/backend.ai-engine-registry/releases/download",
  "engines": [
    {
      "id": "llama-cpp",
      "name": "llama.cpp",
      "description": "High-performance inference engine for GGUF models",
      "latestVersion": "b4618",
      "modelFormats": ["gguf"],
      "packages": [
        {
          "os": "macos",
          "arch": "aarch64",
          "accelerator": "metal",
          "acceleratorVersion": null,
          "filename": "llama-cpp-b4618-macos-aarch64-metal.baiengine",
          "size": 12345678,
          "sha256": "abc123..."
        }
      ]
    }
  ]
}
```

### 3. Package Naming Convention

Engine packages must follow this naming pattern:

```
{engine_id}-{version}-{os}-{arch}-{accelerator}.baiengine
```

Examples:

- `llama-cpp-b4618-macos-aarch64-metal.baiengine`
- `llama-cpp-b4618-linux-x86_64-cuda12.baiengine`
- `llama-cpp-b4618-windows-x86_64-cpu.baiengine`

### 4. Release Workflow

#### Manual Release

1. Build `.baiengine` packages for all platforms
2. Upload packages to GitHub Release with tag format: `{engine_id}-v{version}`
3. Run update script:

```bash
python scripts/update-registry.py \
  --engine llama-cpp \
  --version b4618 \
  --packages-dir ./packages
```

4. Commit and push `engines.json`

#### Automated Release (GitHub Actions)

1. Trigger the `Release Engine Package` workflow
2. Provide:
   - Engine ID (e.g., `llama-cpp`)
   - Version (e.g., `b4618`)
   - Pre-release flag (optional)

The workflow will:

- Download build artifacts from the build workflow
- Create a GitHub Release with all packages
- Update `engines.json` with checksums and sizes
- Commit and push the updated registry

## Platform Matrix

| OS      | Architecture | Accelerators       |
|---------|--------------|-------------------|
| macos   | aarch64      | metal, cpu        |
| macos   | x86_64       | cpu               |
| linux   | x86_64       | cuda, cpu, rocm   |
| windows | x86_64       | cuda, cpu         |

## URL Resolution

The app constructs download URLs using:

```
{baseUrl}/{tag}/{filename}
```

Example:

```
https://github.com/lablup/backend.ai-engine-registry/releases/download/llama-cpp-vb4618/llama-cpp-b4618-macos-aarch64-metal.baiengine
```

## Future: Static Hosting

To migrate to static hosting (S3, CDN, etc.):

1. Update `baseUrl` in `engines.json`:
   ```json
   {
     "baseUrl": "https://cdn.example.com/engines"
   }
   ```

2. Upload packages to the new location
3. No app changes required - the registry URL is configurable

## Offline Installation Support

Users can download `.baiengine` files manually and:

1. Place them in `~/Library/Application Support/Backend.AI GO/engines/incoming/` (macOS)
2. The app will detect and offer to import them
3. Or use the "Import Package" button in the Engines UI

## .baiengine Package Format

Each `.baiengine` file is a tar.gz archive containing:

```
package/
├── manifest.json     # Package metadata
├── llama-server      # Server binary (or .exe on Windows)
└── libs/             # (Optional) Bundled libraries
```

### manifest.json Schema

```json
{
  "engineId": "llama-cpp",
  "version": "b4618",
  "accelerator": "metal",
  "acceleratorVersion": null,
  "os": "macos",
  "arch": "aarch64",
  "serverBinary": "llama-server",
  "modelFormats": ["gguf"],
  "minAppVersion": "0.11.0"
}
```
