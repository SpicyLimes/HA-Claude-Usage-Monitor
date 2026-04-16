# Claude Usage Monitor

A custom Home Assistant Community Store (HACS) Integration that monitors your Claude (Anthropic) subscription usage by exposing usage metrics as sensor entities within Home Assistant.

> **Forked from [trickv/hass-claude-usage](https://github.com/trickv/hass-claude-usage)** — original author: [@trickv](https://github.com/trickv).

---

## Important Notes

> **Issue Tracking:** This repository exists primarily for personal use and must remain public so that a private Home Assistant instance can access it via HACS. Issues and pull requests are not actively monitored. That said, if you find this integration useful and spot something worth flagging, you're welcome to open an issue — no guarantees on response time.

> **Compatibility:** This fork has been reviewed and updated from the original. See [Changes from Original](#changes-from-original) below.

---

## Sensors

| Sensor | Description |
|---|---|
| **Session Usage** | Current 5-hour session utilization (%) |
| **Session Reset Time** | When the session limit resets |
| **Week Usage** | Current 7-day utilization, all models (%) |
| **Week Usage Pace** | How your weekly usage compares to the time elapsed in the week (%) |
| **Weekly Reset Time** | When the weekly limit resets |
| **Weekly Sonnet Usage** | Current 7-day Sonnet model utilization (%) |
| **Weekly Sonnet Reset Time** | When the Sonnet weekly limit resets |
| **Extra Usage Enabled** | Whether extra (paid) usage is enabled |
| **Extra Usage** | Extra usage utilization (%) |
| **Extra Usage Credits** | Credits consumed this month |
| **Extra Usage Limit** | Monthly credit limit |
| **API Error** | Returns 1 if the last API poll failed, 0 if successful |

---

## Installation

### HACS (Recommended)

1. In HACS, go to **Integrations** → three-dot menu → **Custom repositories**
2. Add `https://github.com/SpicyLimes/HA-Claude-Usage-Monitor` with category **Integration**
3. Install **Claude Usage Monitor**
4. Restart Home Assistant
5. Go to **Settings → Devices & Services → Add Integration → Claude Usage**
6. Follow the OAuth setup steps below

### Manual

1. Copy `custom_components/claude_usage_monitor/` into your HA `custom_components/` directory
2. Restart Home Assistant
3. Add the integration via **Settings → Devices & Services → Add Integration**

---

## Setup

This integration uses Anthropic's OAuth 2.0 + PKCE flow — no API key required.

1. When adding the integration, you'll be shown an authorization URL
2. Open the URL in your browser and log in to your Anthropic account
3. After authorizing, you'll be redirected to a page with an authorization code
4. Copy the **entire code string** (it may include a `#` character — paste all of it) and enter it into the Home Assistant config flow
5. The integration will fetch your account name and subscription level (Pro/Max) and display them on the device

Tokens are refreshed automatically in the background — you should only need to authenticate once.

---

## Options

- **Update interval** — How often to poll the usage API (default: 300 seconds, min: 60, max: 3600)

---

## Rate Limiting

Anthropic rate-limits the usage API if polled too frequently. A few dozen requests per minute is enough to trigger it, and the backoff period is approximately 24 hours — during which usage will not be visible here, in Claude Code, or on claude.ai.

**Recommendation: keep the update interval at the default 300 seconds.**

---

## Changes from Original

The following changes were made during a review of the original integration:

| Area | Change |
|---|---|
| **Security** | Removed `org:create_api_key` from OAuth scopes — this integration only reads usage data and never creates API keys; the scope was unnecessarily broad |
| **Security** | Fixed OAuth CSRF state validation — the state check was bypassable if the user submitted a code without a `#state` suffix; it is now unconditionally enforced |
| **Bug fix** | Missing refresh token now raises `ConfigEntryAuthFailed` (triggers HA reauth UI) instead of `UpdateFailed` (which silently retried indefinitely) |
| **Bug fix** | Malformed timestamps in the `week_usage_pace` calculation now log a debug message instead of silently dropping the sensor value |
| **Code quality** | Deduplicated OAuth URL builder and PKCE initialisation — the identical block was previously copy-pasted between the initial auth and reauth flows |
| **Manifest** | Version updated to semver format (`1.0.0`) as required by hassfest |
| **CI** | Workflow branch references updated from `master` to `main` |
| **CI** | Added daily scheduled trigger to HACS/hassfest workflow to catch upstream breakage |

---

## Development

### Manual Formatting

```bash
pip install black isort ruff
black custom_components/claude_usage_monitor/
isort custom_components/claude_usage_monitor/
ruff check --fix custom_components/claude_usage_monitor/
```

---

## License

MIT License — see [LICENSE](LICENSE) for details.

Original work copyright © [trickv](https://github.com/trickv). Modifications made in this fork are released under the same license.
