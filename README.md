# MCPB Build Action

A GitHub Action for building Model Context Protocol (MCP) packages using the MCPB CLI.

[![GitHub release](https://img.shields.io/github/v/release/entro314-labs/mcpb-build-action)](https://github.com/entro314-labs/mcpb-build-action/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Features

- Validates `manifest.json` before building
- Builds `.mcpb` packages from your MCP server
- Supports multiple package managers (pnpm, npm, yarn)
- Optionally uploads built package as workflow artifact
- Outputs package metadata (name, version, size, SHA256)
- Marketplace-ready with branding

## Usage

### Basic Usage

```yaml
- name: Build MCPB Package
  uses: entro314-labs/mcpb-build-action@v1
```

### With Custom Options

```yaml
- name: Build MCPB Package
  id: mcpb
  uses: entro314-labs/mcpb-build-action@v1
  with:
    manifest: 'manifest.json'
    output: 'my-mcp-server'
    package-manager: 'pnpm'
    node-version: '22'

- name: Use Build Outputs
  run: |
    echo "Package: ${{ steps.mcpb.outputs.package-name }}@${{ steps.mcpb.outputs.package-version }}"
    echo "Size: ${{ steps.mcpb.outputs.package-size }}"
    echo "SHA256: ${{ steps.mcpb.outputs.shasum }}"
```

### Validate Only (No Build)

```yaml
- name: Validate Manifest
  uses: entro314-labs/mcpb-build-action@v1
  with:
    validate-only: 'true'
```

### Without Artifact Upload

```yaml
- name: Build MCPB Package
  uses: entro314-labs/mcpb-build-action@v1
  with:
    upload-artifact: 'false'
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `manifest` | Path to manifest.json file | No | `manifest.json` |
| `output` | Output filename for the .mcpb package (without extension) | No | Uses manifest name |
| `working-directory` | Working directory for the build | No | `.` |
| `validate-only` | Only validate the manifest without building | No | `false` |
| `node-version` | Node.js version to use | No | `22` |
| `package-manager` | Package manager to use (pnpm, npm, yarn) | No | `pnpm` |
| `upload-artifact` | Upload the built package as a workflow artifact | No | `true` |
| `artifact-name` | Name for the uploaded artifact | No | `mcpb-package` |
| `artifact-retention-days` | Number of days to retain the artifact | No | `30` |

## Outputs

| Output | Description |
|--------|-------------|
| `package-path` | Absolute path to the built .mcpb package |
| `package-name` | Name of the built package |
| `package-version` | Version of the built package |
| `package-size` | Human-readable size of the built package |
| `shasum` | SHA256 checksum of the package |

## Example Workflows

### Release with MCPB Package

```yaml
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build MCPB Package
        id: mcpb
        uses: entro314-labs/mcpb-build-action@v1
        with:
          output: 'my-package'
          upload-artifact: 'false'

      - name: Create Release
        uses: softprops/action-gh-release@v2
        with:
          files: |
            ${{ steps.mcpb.outputs.package-name }}.mcpb
```

### CI Validation

```yaml
name: CI

on:
  pull_request:

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Validate MCPB Manifest
        uses: entro314-labs/mcpb-build-action@v1
        with:
          validate-only: 'true'
```

### Build with Different Package Managers

```yaml
# Using npm
- uses: entro314-labs/mcpb-build-action@v1
  with:
    package-manager: 'npm'

# Using yarn
- uses: entro314-labs/mcpb-build-action@v1
  with:
    package-manager: 'yarn'

# Using pnpm (default)
- uses: entro314-labs/mcpb-build-action@v1
  with:
    package-manager: 'pnpm'
```

## Requirements

- Node.js 22+ (configurable via `node-version` input)
- A valid `manifest.json` following the [MCPB specification](https://github.com/anthropics/anthropic-cookbook/tree/main/misc/mcpb)
- One of: pnpm, npm, or yarn

## Versioning

This action uses semantic versioning. We recommend pinning to a major version:

```yaml
uses: entro314-labs/mcpb-build-action@v1  # Recommended
uses: entro314-labs/mcpb-build-action@v1.0.0  # Pin to specific version
uses: entro314-labs/mcpb-build-action@main  # Latest (not recommended for production)
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT License - see [LICENSE](LICENSE) for details.

## Related

- [MCPB CLI](https://github.com/anthropics/anthropic-cookbook/tree/main/misc/mcpb) - The CLI tool this action wraps
- [Model Context Protocol](https://modelcontextprotocol.io/) - The protocol specification
- [AI Changelog Generator](https://github.com/entro314-labs/AI-changelog-generator) - An MCP server built with this action
