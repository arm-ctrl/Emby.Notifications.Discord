# Changelog

## 1.2.4.0 - 2026-10-28

- Suppress duplicate deliveries of the same Emby event to the same Discord webhook within five seconds.
- Keep separate events distinct by including Emby's event date, event type, and message content in the duplicate key.
- Allow a later send after a webhook request fails.
