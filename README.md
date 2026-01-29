# Backend.AI GO Engine Registry

Official engine package registry for [Backend.AI GO](https://github.com/lablup/backend.ai-go).

This registry provides pre-built inference engine packages that Backend.AI GO can automatically download and install.

## Available Engines

### llama.cpp

High-performance GGUF model inference engine with support for various hardware accelerators.

| Platform | Architecture | Accelerator | Notes |
|----------|-------------|-------------|-------|
| macOS    | arm64       | Metal       | Apple Silicon optimized |
| Linux    | arm64       | CPU         | ARM64 servers |
| Linux    | x64         | CPU         | Generic x86_64 |
| Linux    | x64         | Vulkan      | AMD/Intel GPU via Vulkan |
| Windows  | x64         | CPU         | Generic x86_64 |
| Windows  | x64         | CUDA 12     | NVIDIA GPU (CUDA 12.x) |
| Windows  | x64         | CUDA 13     | NVIDIA GPU (CUDA 13.x) |
| Windows  | x64         | HIP         | AMD GPU (ROCm) |
| Windows  | x64         | SYCL        | Intel GPU (oneAPI) |
| Windows  | x64         | Vulkan      | Cross-vendor GPU |

## Available Runtimes

Runtime packages provide GPU acceleration libraries required by engine packages.

| Runtime | Platform | Description |
|---------|----------|-------------|
| cuda12-runtime | Windows | NVIDIA CUDA 12 libraries (cuBLAS, cuBLASLt, cuDNN) |
| cuda13-runtime | Windows | NVIDIA CUDA 13 libraries (cuBLAS, cuBLASLt, cuDNN) |
| hip-runtime | Windows | AMD HIP runtime libraries (rocBLAS, hipBLASLt) |

## Usage

Backend.AI GO automatically fetches the package index from this registry and downloads appropriate engine packages based on your system configuration.

### Registry URL

```
https://raw.githubusercontent.com/lablup/backend.ai-go-engine-registry/main/packages.json
```

### Manual Download

Engine packages can also be downloaded directly from [GitHub Releases](https://github.com/lablup/backend.ai-go-engine-registry/releases).

## Package Format

### Engine Packages (`.baiengine`)

Tar.gz archives containing:

```
package/
├── manifest.json     # Package metadata
├── llama-server      # Server binary (or .exe on Windows)
└── libs/             # (Optional) Bundled libraries
```

### Runtime Packages (`.bairuntime`)

Tar.gz archives containing GPU runtime libraries required by engine packages.

## Offline Installation

1. Download the `.baiengine` file from GitHub Releases
2. Place it in the engines directory:
   - macOS: `~/Library/Application Support/Backend.AI GO/engines/incoming/`
   - Windows: `%APPDATA%\Backend.AI GO\engines\incoming\`
   - Linux: `~/.config/Backend.AI GO/engines/incoming/`
3. The app will detect and offer to import the package

## License

Engine packages are distributed under their respective upstream licenses:
- llama.cpp: MIT License
