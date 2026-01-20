# PrettyPick

[English](#english) | [中文](./readme/README_zh_Hans.md)

---

### Introduction

**PrettyPick** is a LangBot plugin that fetches beautiful images from various styles. Hand-picking the most captivating beauty from a myriad of styles, just to steal your heart.

### Features

- **Smart Image Fetching**: Concurrent image retrieval with automatic retry mechanism
- **Flexible Message Modes**:
  - Single image mode for individual requests
  - Merge forward mode for multiple images (OneBot v11)
- **Configurable Limits**: Set maximum image count per request
- **Secure API Access**: Support for OneBot API token authentication
- **Bilingual Support**: Full support for English and Chinese
- **Graceful Degradation**: Auto fallback to normal message mode if forward fails

### Installation

Install via the Plugin Market in the LangBot backend web UI.

### Configuration

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `beauty_trigger` | string | Yes | "看妹妹" | Trigger keyword for image fetch |
| `image_count_limit` | integer | Yes | 3 | Maximum images per request |
| `use_forward` | boolean | No | true | Enable merge forward (OneBot only) |
| `onebot_api_url` | string | No | "http://127.0.0.1:3000" | OneBot API URL |
| `onebot_access_token` | string | No | "" | OneBot API access token for security |

### Usage

Simply send a message in group chat with the trigger keyword:

```
看妹妹          # Fetch 1 image
看妹妹 3        # Fetch 3 images
看妹妹 5        # Request 5 images (limited by config)
```

### Message Modes

#### Normal Mode
Used when:
- Requesting 1 image
- `use_forward` is disabled
- Forward mode fails (auto fallback)

Sends images directly using platform message API.

#### Forward Mode (OneBot v11)
Used when:
- Requesting multiple images (>1)
- `use_forward` is enabled
- OneBot API is available

Sends images as a merge forward message card for better user experience.

### Dependencies

- `langbot-plugin`: LangBot Plugin SDK
- `httpx`: HTTP client for API requests
- `aiohttp`: Async HTTP client for forward messages

### Issue Feedback & Feature Development

[![QQ Grup](https://img.shields.io/badge/QQ群-965312424-green)](https://qm.qq.com/cgi-bin/qm/qr?k=en97YqjfYaLpebd9Nn8gbSvxVrGdIXy2&jump_from=webapi&authKey=41BmkEjbGeJ81jJNdv7Bf5EDlmW8EHZeH7/nktkXYdLGpZ3ISOS7Ur4MKWXC7xIx)
