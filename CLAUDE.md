# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

PrettyPick is a LangBot plugin that provides beauty image functionality. It's built using the LangBot Plugin SDK and follows the LangBot plugin architecture pattern.

**Official Documentation**: https://docs.langbot.app/en/plugin/dev/tutor.html

## Development Commands

### Running the Plugin
```bash
lbp run
```

### Debugging
The project includes VSCode debug configuration at `.vscode/launch.json`:
- Launch configuration: "LangBot Plugin Debug"
- Uses `debugpy` and runs `lbp run`

### Environment Setup
1. Copy `.env.example` to `.env`
2. Configure `DEBUG_RUNTIME_WS_URL` (default: `ws://127.0.0.1:5401/debug/ws`)
3. Optionally set `PLUGIN_DEBUG_KEY` for runtime authentication

## Architecture

### Plugin Structure
This is a component-based LangBot plugin with the following architecture:

**Main Plugin Class** (`main.py`):
- Entry point: `PrettyPick(BasePlugin)`
- Lifecycle methods: `initialize()` and `__del__()`
- Declared in `manifest.yaml` under `execution.python.attr`

**Component System**:
- Components are organized in the `components/` directory
- Each component has its own directory with:
  - `__init__.py` - Component registration
  - `default.py` - Component implementation
  - `default.yaml` - Component metadata and configuration

**EventListener Component** (`components/event_listener/`):
- Inherits from `EventListener` base class
- Registered in `manifest.yaml` under `spec.components.EventListener.fromDirs`
- Handles plugin events through the `initialize()` method

### Configuration (manifest.yaml)

The plugin is defined by `manifest.yaml` which specifies:
- **Metadata**: author, version, descriptions (en_US, zh_Hans)
- **User Configuration**:
  - `beauty_trigger`: Trigger keyword (default: "看妹妹")
  - `image_count`: Number of images to return (default: 3)
- **Components**: EventListener loaded from `components/event_listener/`
- **Execution**: Points to `main.py:PrettyPick`

### Key Dependencies
- `langbot-plugin`: Core LangBot Plugin SDK
  - `langbot_plugin.api.definition.plugin.BasePlugin`
  - `langbot_plugin.api.definition.components.common.event_listener.EventListener`
  - `langbot_plugin.api.entities.events`
  - `langbot_plugin.api.entities.context`

## File Organization

```
PrettyPick/
├── main.py                          # Plugin entry point (PrettyPick class)
├── manifest.yaml                    # Plugin metadata and configuration
├── requirements.txt                 # Python dependencies
├── components/                      # Plugin components
│   └── event_listener/             # Event handling component
│       ├── default.py              # DefaultEventListener implementation
│       └── default.yaml            # Component metadata
├── assets/                         # Static assets (icon, etc.)
└── data/                           # Runtime data directory
    └── temp/                       # Temporary files
```

## Development Notes

- Plugin components are auto-discovered from directories specified in `manifest.yaml`
- Component YAML files must match the component's Python file name (e.g., `default.yaml` → `default.py`)
- The plugin supports bilingual configuration (en_US, zh_Hans)
- Configuration values from `manifest.yaml` are accessible to the plugin at runtime

## Features

### Beauty Image API Integration
- API Endpoint: `https://3650000.xyz/api/?type=json&mode=1,3,5,8`
- Response format: `{"code": 200, "url": "http://..."}`
- Supports concurrent image fetching with retry mechanism (max 3 retries)
- Configurable image count limit (default: 3)

### Message Sending Modes
1. **Normal Mode**: When requesting 1 image or when forward is disabled
   - Sends images directly using `platform_message.Image`
2. **Forward Mode**: When requesting multiple images (>1) and `use_forward` is enabled
   - Uses OneBot v11 merge forward message API
   - Requires `utils/forward_message.py` module
   - Falls back to normal mode if forward fails

### Security
- Supports OneBot API access token authentication via `onebot_access_token` config
- Token is passed in HTTP Authorization header as Bearer token

## Configuration

Available configuration options in `manifest.yaml`:
- `beauty_trigger`: Keyword to trigger image fetch (default: "看妹妹")
- `image_count_limit`: Maximum images per request (default: 3)
- `use_forward`: Enable merge forward for multiple images (default: true)
- `onebot_api_url`: OneBot HTTP API URL (default: "http://127.0.0.1:3000")
- `onebot_access_token`: OneBot API access token for authentication (optional)

## Dependencies

Required Python packages (see `requirements.txt`):
- `langbot-plugin`: Core LangBot Plugin SDK
- `httpx`: HTTP client for API requests
- `aiohttp`: Async HTTP client for forward message sending
