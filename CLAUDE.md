# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

QCC (Quick Claude Config, formerly FastCC) is a modern Python CLI tool for managing Claude Code configurations with multi-profile support, cross-platform cloud synchronization, and encrypted storage. Published on PyPI as `qcc`, it supports multiple storage backends including GitHub Gist, local cloud drives (iCloud/OneDrive/Dropbox), and local-only storage. Uses contemporary Python packaging with `uv`/`uvx` for zero-installation deployment.

## Commands

### Development Commands
```bash
# Set up virtual environment for development
python -m venv fastcc_env
source fastcc_env/bin/activate  # On Windows: fastcc_env\Scripts\activate

# Install in development mode
pip install -e .

# Run development version directly
python -m fastcc.cli --help

# Test with actual command name
qcc --help
```

### Build and Release Commands
```bash
# Install build tools
pip install build twine

# Build package
python -m build

# Upload to PyPI (requires API token)
python -m twine upload --username __token__ --password [PYPI_TOKEN] dist/*
```

### Testing Commands
```bash
# Set up test environment
source fastcc_test_env/bin/activate

# Run individual tests
python3 tests/test_providers.py        # Provider configuration manager tests
python3 tests/test_fc_command.py       # Full fc command workflow tests
python3 tests/test_simplified_fc.py    # Simplified workflow tests

# Run all tests (requires pytest)
python3 -m pytest tests/ -v
```

### Testing End-User Experience
```bash
# Test published package installation
pip install qcc

# Test uvx zero-installation experience
uvx qcc

# Test global installation with uv
uv tool install qcc

# Core commands for testing functionality
qcc init                    # Initialize with storage backend selection
qcc add work "Work config"  # Add configuration profile
qcc fc                      # Provider-guided fast configuration
qcc list                    # List all profiles
qcc use work               # Apply specific profile
qcc config                 # Change storage settings
qcc uninstall              # Clean local configuration
```

### Core CLI Commands
- `qcc` - Smart launch with intelligent profile selection (primary entry point)
- `qcc init` - Initialize configuration (one-time setup with storage backend choice)
- `qcc add <name>` - Add new configuration profile
- `qcc list` - List all configuration profiles
- `qcc use <name>` - Apply specific profile and launch Claude Code
- `qcc fc` - Fast Config: Provider-guided configuration with browser-assisted setup
- `qcc config` - Configuration management (change storage backend, view settings)
- `qcc status` - Show system status and backend information
- `qcc sync` - Manual cloud synchronization
- `qcc default <name>` - Set default profile for smart launch
- `qcc remove <name>` - Delete configuration profile
- `qcc uninstall` - Remove all local configuration data (preserves cloud data)

### User Experience Flow
1. **Storage Backend Selection**: On first run, user chooses between GitHub (cross-platform), cloud drives (platform-specific), or local-only storage
2. **Authentication**: GitHub option uses device flow for secure authentication without local web server
3. **Profile Management**: Create, list, and switch between named configuration profiles
4. **Provider-Guided Setup**: `qcc fc` provides streamlined configuration with cloud-based provider registry and browser-assisted account creation
5. **Smart Launch**: 3-second timeout profile selection with intelligent defaults
6. **Claude Integration**: Direct manipulation of `~/.claude/settings.json` for immediate Claude Code configuration

## Code Architecture

### Modular Design
The project follows a layered architecture with clear separation of concerns:

```
fastcc/
├── core/           # Core business logic
│   └── config.py   # ConfigManager and ConfigProfile classes with intelligent storage backend selection
├── storage/        # Multiple storage backend implementations
│   ├── base.py     # StorageBackend abstract base class with common interface
│   ├── github_gist.py     # Legacy GitHub Gist (OAuth flow)
│   ├── github_simple.py  # GitHub with personal access tokens
│   ├── cloud_file.py     # iCloud/OneDrive/Dropbox auto-detection
│   └── jsonbin.py        # JSONBin.io cloud storage (experimental)
├── providers/      # Provider-guided configuration system
│   ├── manager.py  # ProvidersManager for fetching cloud-based provider configs
│   └── browser.py  # Browser integration and user interaction utilities
├── auth/           # Authentication modules
│   └── oauth.py    # GitHub OAuth device flow implementation
├── utils/          # Utility modules
│   ├── crypto.py   # End-to-end encryption for API keys
│   └── ui.py       # Interactive prompts with timeout support
└── cli.py          # Click-based CLI with comprehensive subcommands
```

### Key Components

#### CLI Interface (`cli.py`)
- **Click Framework**: Modern command-line interface with subcommands
- **Smart Launch**: Default behavior optimized for zero-config experience
- **uvx Integration**: Designed for seamless operation with uvx package runner
- **Interactive UI**: Uses `utils/ui.py` for timeout-based selections and status display

#### Storage Layer (`storage/`)
- **Abstract Interface**: `StorageBackend` defines common operations (save/load/delete config, authentication)
- **Multiple Backends**: GitHub (simple/OAuth), cloud files (auto-detected), JSONBin (experimental)
- **Cross-Platform**: Intelligent detection of available cloud storage on macOS/Windows/Linux
- **Fallback Strategy**: GitHub → Cloud Files → Local-only with graceful degradation
- **Error Handling**: Comprehensive exception handling with user-friendly error messages

#### Authentication (`auth/oauth.py`)
- **Device Flow Protocol**: GitHub's device flow for CLI applications (no local server required)
- **Polling Mechanism**: Handles authorization_pending and slow_down responses
- **Token Persistence**: Secure local storage with proper file permissions (600)

#### Configuration Management (`core/config.py`)
- **Profile System**: Named configuration profiles with metadata (description, last_used, creation time)
- **Storage Backend Selection**: User preference persistence with `storage_backend_type` and `storage_initialized` flags
- **Intelligent Initialization**: Remembers user choice, only prompts on first use or explicit configuration change
- **Bi-directional Sync**: Local caching (`~/.fastcc/cache.json`) with cloud synchronization
- **Claude Integration**: Direct manipulation of `~/.claude/settings.json` for immediate effect
- **Uninstall Support**: Local cleanup while preserving cloud data for re-installation

#### Provider-Guided Configuration (`providers/`)
- **ProvidersManager**: Fetches AI provider configurations from cloud-based registry (GitHub Gist)
- **Browser Integration**: Cross-platform browser launching for provider account registration
- **Streamlined Workflow**: Five-step guided setup: provider selection → browser registration → API key input → URL confirmation → profile creation
- **Cloud Registry**: Centrally maintained provider database with signup URLs, documentation links, and setup instructions

#### Utility Modules (`utils/`)
- **Cryptography**: End-to-end encryption with user-specific key derivation
- **User Interface**: Timeout-based prompts and cross-platform compatibility
- **Status Display**: Rich console output with icons and structured information

### Security Architecture

1. **User-Owned Data**: All configuration data stored in user's own GitHub Gist
2. **Encrypted Storage**: API keys encrypted before upload to cloud
3. **Secure Token Storage**: OAuth tokens stored with restricted file permissions
4. **No Shared Infrastructure**: No centralized database or shared storage

### Integration Points

- **Claude Code**: Updates `~/.claude/settings.json` to configure API settings
- **GitHub API**: Uses Gists for cloud storage and user authentication
- **Local Filesystem**: Caches configuration in `~/.fastcc/` directory

## Development Notes

### Modern Python Packaging
- **pyproject.toml**: Uses modern Python packaging standards with Hatchling build backend
- **uvx Compatibility**: Configured for zero-installation deployment via uvx
- **Entry Point**: Single CLI entry point `qcc` for simplified user experience
- **Version Management**: Dynamic versioning from `fastcc/__init__.py`

### Dependencies and Compatibility
- **Core Dependencies**: Click (CLI), requests (HTTP), cryptography (encryption)
- **Python 3.7+**: Broad compatibility while supporting modern features
- **Cross-Platform**: Works on macOS, Linux, and Windows with platform-specific adaptations
- **No Optional Dependencies**: All functionality included in base installation

### Authentication Implementation
- **Device Flow**: Replaces traditional OAuth web callback for better CLI UX
- **Public Client**: Uses GitHub CLI's established client ID for community trust
- **Graceful Degradation**: Handles network failures and authentication timeouts
- **Token Refresh**: Currently single-use tokens (consider refresh token implementation for production)

### Legacy Components
- **setup_claude_config.py**: Original simple configuration script (retained for reference)
- **requirements.txt**: Maintained for pip compatibility alongside pyproject.toml