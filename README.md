# QCC - Quick Claude Config

QCC (Quick Claude Config, formerly FastCC) is a modern Python CLI tool for managing Claude Code configurations with multi-profile support, cross-platform cloud synchronization, and encrypted storage. Published on PyPI as `qcc`, it supports multiple storage backends including GitHub Gist, local cloud drives (iCloud/OneDrive/Dropbox), and local-only storage.

## Features

- 🚀 **Smart Launch**: Zero-config experience with intelligent profile selection
- 🔐 **Secure Storage**: End-to-end encrypted configuration storage
- ☁️ **Cross-Platform Sync**: Multiple storage backends (GitHub, cloud drives, local)
- 📋 **Multi-Profile Management**: Work, personal, and project-specific configurations
- 🎯 **Zero Installation**: Works with `uvx` for instant deployment
- ⚡ **Modern Architecture**: Built with contemporary Python packaging standards

## Installation

### Zero Installation (Recommended)
```bash
# Install uv (if not already installed)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Run directly without installation
uvx qcc
```

### Traditional Installation
```bash
# Install globally with uv
uv tool install qcc

# Or with pip
pip install qcc
```

## Quick Start

### Smart Launch (Recommended)
```bash
# On any new computer, just run:
uvx qcc
```

First run automatically:
1. Downloads the latest version
2. Prompts for storage backend selection (GitHub/Cloud drives/Local)
3. Handles authentication (GitHub device flow)
4. Creates secure cloud storage
5. Syncs your configuration profiles
6. Smart profile selection with 3-second timeout
7. Launches Claude Code

### Manual Operations
```bash
# Initialize with storage backend selection
qcc init

# Add new configuration profile
qcc add work "Work configuration"

# List all profiles
qcc list

# Use specific profile
qcc use work

# Set default profile for smart launch
qcc default work

# Check system status
qcc status

# Manual cloud synchronization
qcc sync

# Configuration management
qcc config

# Clean local data (preserves cloud)
qcc uninstall
```

## Storage Backends

### GitHub (Cross-Platform)
- Uses GitHub Gist for cloud storage
- Device flow authentication (no local server required)
- Works on all platforms
- Data stored in your own GitHub account

### Cloud Drives (Platform-Specific)
- **macOS**: iCloud Drive auto-detection
- **Windows**: OneDrive auto-detection  
- **Linux**: Dropbox auto-detection
- Local file synchronization across devices

### Local Only
- No cloud synchronization
- Local storage in `~/.fastcc/`
- Ideal for isolated environments

## Command Reference

### Core Commands
- `qcc` - Smart launch with intelligent profile selection
- `qcc init` - Initialize configuration with storage backend choice
- `qcc add <name>` - Add new configuration profile
- `qcc list` - List all configuration profiles
- `qcc use <name>` - Apply specific profile and launch Claude Code
- `qcc default <name>` - Set default profile for smart launch
- `qcc remove <name>` - Delete configuration profile

### Management Commands
- `qcc config` - Configuration management (change storage backend)
- `qcc status` - Show system status and backend information
- `qcc sync` - Manual cloud synchronization
- `qcc uninstall` - Remove all local configuration data

## Configuration Files

- **Local cache**: `~/.fastcc/cache.json`
- **GitHub token**: `~/.fastcc/github_token.json` 
- **Claude settings**: `~/.claude/settings.json`

## Security

- **User-Owned Data**: All configuration stored in user's own accounts
- **End-to-End Encryption**: API keys encrypted before cloud upload
- **Secure Authentication**: OAuth device flow for GitHub integration
- **Local File Permissions**: Restricted access (600) for sensitive files
- **No Shared Infrastructure**: No centralized database or shared storage

## Architecture

### Modular Design
```
fastcc/
├── core/           # Configuration management
├── storage/        # Multiple storage backends
├── auth/           # Authentication (OAuth device flow)
├── utils/          # Cryptography and UI utilities
└── cli.py          # Click-based CLI interface
```

### Storage Layer
- Abstract interface with multiple implementations
- Graceful fallback: GitHub → Cloud Files → Local
- Cross-platform cloud storage detection
- Comprehensive error handling

## Development

### Setup
```bash
# Set up development environment
python -m venv fastcc_env
source fastcc_env/bin/activate  # Windows: fastcc_env\Scripts\activate

# Install in development mode
pip install -e .

# Test development version
python -m fastcc.cli --help
```

### Build and Release
```bash
# Install build tools
pip install build twine

# Build package
python -m build

# Upload to PyPI
python -m twine upload dist/*
```

## Requirements

- Python 3.7+
- click - Command-line interface framework
- requests - HTTP client for API communication
- cryptography - End-to-end encryption

## License

MIT License

## Contributing

Contributions welcome! Please submit issues and pull requests on GitHub.

## Support

For questions or issues, please create an issue on GitHub.