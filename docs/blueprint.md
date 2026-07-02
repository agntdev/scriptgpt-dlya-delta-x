# ScriptGPT for Delta X — Bot specification

**Archetype:** custom

**Voice:** professional and concise — write every user-facing message, button label, error, and empty state in this voice.

Telegram bot that generates and delivers Delta X scripts based on user requests. Provides script snippets with usage notes, supports saving/retrieving scripts, and includes admin monitoring. All interactions in Russian with professional, concise tone.

> This is the complete contract for the bot. Implement EVERY entry point, flow, feature, integration, and edge case below. The completeness review checks the bot against this document after each build pass.

## Primary audience

- Russian-speaking developers
- Delta X power users
- script hobbyists

## Success criteria

- Users can request and receive functional Delta X scripts via text prompts
- Saved scripts persist across sessions
- Admin receives request logs for monitoring

## Entry points

Every feature must be reachable from the bot's command/button surface (button-first; only /start and /help are slash commands).

- **/start** (command, actor: user, command: /start) — Send greeting and example requests in Russian
- **/help** (command, actor: user, command: /help) — Display brief usage instructions
- **/saved** (command, actor: user, command: /saved) — List all saved scripts
- **/get <name>** (command, actor: user, command: /get) — Retrieve specific saved script by name
- **сохранить <имя>** (command, actor: user, command: /сохранить) — Save last returned script with specified name
- **еще** (command, actor: user, command: /еще) — Request alternative script for same query

## Flows

### script_request
_Trigger:_ user text message

1. Receive free-text request
2. Generate matching script snippet
3. Display script with usage note and disclaimer

_Data touched:_ script_request

### script_followup
_Trigger:_ еще

1. Retrieve alternative script version
2. Display with same metadata

_Data touched:_ script_request

### script_save
_Trigger:_ сохранить <имя>

1. Store script with user-defined name
2. Confirm save with timestamp

_Data touched:_ saved_script

### script_retrieval
_Trigger:_ /get <name>

1. Fetch saved script by name
2. Format as preformatted text block

_Data touched:_ saved_script

## Data entities

Durable data (must survive a restart) uses the toolkit's persistent store, never in-memory maps.

- **user_profile** _(retention: persistent)_ — Telegram user metadata
  - fields: telegram_id, display_name
- **script_request** _(retention: persistent)_ — User query and generated script
  - fields: query_text, generated_script, timestamp, tags
- **saved_script** _(retention: persistent)_ — User-saved script with metadata
  - fields: script_text, saved_name, owner_id, timestamp

## Integrations

- **Telegram** (required) — Private chat interactions and group support
- **Admin Channel** (optional) — Request logging and monitoring
Call external APIs against their real contract (correct endpoints, ids, params); credentials from env. Do not fake responses.

## Owner controls

- Enable/disable admin notifications
- Configure script generation rules
- Manage saved script storage

## Notifications

- Forward script requests to admin channel (configurable)

## Permissions & privacy

- Store minimal user data (ID and display name)
- Require opt-in for group interactions
- Anonymize admin logs by default

## Edge cases

- Invalid script names in /get
- Non-existent saved scripts
- Malformed 'сохранить' commands
- Empty/ambiguous user requests

## Required tests

- End-to-end script request -> save -> retrieve flow
- Admin notification toggling
- Russian text formatting with pre blocks

## Assumptions

- Script generation logic is handled server-side
- Delta X compliance is owner's responsibility
- Basic text formatting meets copy-paste needs
