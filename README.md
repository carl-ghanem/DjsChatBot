# AYL+ Discord Bot

A multi-purpose Discord bot built with **Node.js** and **discord.js v12**. AYL+ combines moderation tools, music playback, utility commands, search features, tickets, reaction roles, reminders, mini-games, and server-management helpers in a single bot.

The project uses a modular command/event architecture: commands are loaded automatically from the `commands/` directory, while Discord events are handled from `events/`.

## Features

### General commands
- `+hello` — Simple greeting command.
- `+help` — Displays the available command categories.
- `+rules` — Shows server rules.
- `+faq` — Displays frequently asked questions.
- `+media` — Shows social-media information.
- `+instagram` — Shares the configured Instagram page and can assign a notification role.
- `+invite` — Sends the Discord server invitation.
- `+ping` — Shows bot latency.
- `+vibe` — Fun/vibe command.
- `+guess` — Number-guessing mini-game.

### Search and utility commands
- `+google <query>` / `+g <query>` — Generates a Google search link.
- `+youtube <query>` / `+yt <query>` — Searches YouTube and returns a matching video.
- `+ytsearch` / `+yts` — Interactive YouTube search using the YouTube API.
- `+spotifysearch <song>` / `+sp <song>` — Creates a Spotify search link.
- `+image <query>` / `+img <query>` — Searches for an image.
- `+weather <city>` — Displays weather information for a city.
- `+lyrics <artist>` — Looks up song lyrics.
- `+avatar [user]` — Displays a user's avatar.
- `+number <max>` — Generates a random number.
- `+question <text>` / `+qst <text>` — Gives a randomized answer to a question.
- `+remind <time> <message>` — Sends a reminder through DM after a delay such as `10m`, `2h`, or `1d`.
- `+covid` — Retrieves COVID-19 statistics.
- `+embed ...` — Creates a custom Discord embed.

### Music system
The bot can join a voice channel and stream audio from YouTube.

- `+play <song or YouTube URL>` / `+p`
- `+pause`
- `+resume`
- `+skip`
- `+stop`
- `+queue`
- `+loop one`
- `+loop all`
- `+loop off`

Music queues are maintained independently per Discord server.

### Moderation
Permission checks are built into moderation commands.

- `+clear <number>` — Deletes messages.
- `+warn <user> [reason]` — Warns a member and logs the warning.
- `+kick <user>` — Kicks a member.
- `+ban <user>` — Bans a member.
- `+mute <user>` — Mutes a member.
- `+unmute <user>` — Removes a mute.
- `+say ...` — Makes the bot send a message.
- `+activity ...` — Changes the bot activity.
- `+shutdown <password>` — Stops the bot process.
- `+mod` — Sends the moderation command list to an administrator.

### Server-management features
- Automatic welcome message for new members.
- Automatic assignment of a `new member` role when a user joins.
- Member-count channel updated every minute.
- Rotating Discord presence/activity.
- Sticky-message logic for a configured channel.
- Ticket creation with administrator-controlled close/delete reactions.
- Reaction-role support.
- Suggestion channel with 👍 / 👎 voting.
- Bug-report forwarding to a configured channel.
- Warning/logging channel support.

### Slash commands
The project also contains an early Discord.js v12 implementation of slash commands, including:

- `/info`
- `/rules`
- `/embed`

## Project structure

```text
DjsChatBot-main/
├── commands/              # Prefix-command implementations
├── counters/
│   └── member-counter.js  # Automatic member-count channel updater
├── events/
│   ├── client/
│   │   └── ready.js       # Ready event, presence, sticky message logic
│   └── guild/
│       └── message.js     # Prefix parsing, permissions and cooldowns
├── handlers/
│   ├── command_handler.js # Dynamically loads commands
│   └── event_handler.js   # Dynamically loads event modules
├── slashcommands/         # Experimental slash-command implementation
├── main.js                # Bot entry point
├── package.json
└── package-lock.json
```

## Tech stack

- **JavaScript / Node.js**
- **discord.js 12.5.3**
- **dotenv** for environment configuration
- **ytdl-core** and **yt-search** for music playback/search
- **youtube-search** / **ytsr** for YouTube search
- **weather-js** for weather information
- **lyrics-finder** for lyrics lookup
- **images-scraper** for image search
- **quick.db** for lightweight local storage support
- **ms** for parsing reminder durations
- **opusscript** for Discord voice/audio support

## Installation

### 1. Clone the repository

```bash
git clone <your-repository-url>
cd DjsChatBot-main
```

### 2. Install dependencies

```bash
npm install
```

> This project was written for **discord.js v12**, which is now a legacy version. For the easiest setup, use a Node.js version compatible with the dependency versions in `package-lock.json`. A future migration to discord.js v14 would require changes to intents, events, permissions, interactions, voice support, and several API calls.

### 3. Create a Discord application

1. Open the Discord Developer Portal.
2. Create an application and add a bot user.
3. Copy the bot token.
4. Invite the bot to your server with the permissions required by the commands you intend to use.

For message-based commands, make sure the necessary Discord gateway/message permissions are enabled for your bot configuration.

### 4. Configure environment variables

Create a `.env` file in the project root:

```env
DISCORD_TOKEN=your_discord_bot_token
PREFIX=+

guildID=your_server_id
welcomechannel=welcome_channel_id
membercount=member_count_channel_id
stickychannel=sticky_message_channel_id

ticketcategory=ticket_category_id
warnchannel=warning_log_channel_id
bugchannel=bug_report_channel_id
notifrole=notification_role_id

YTcode=your_youtube_data_api_key
password=your_shutdown_command_password
```

Not every variable is necessary if you disable the feature that uses it.

| Variable | Purpose |
| --- | --- |
| `DISCORD_TOKEN` | Discord bot token used to log in. |
| `PREFIX` | Prefix used for message commands, e.g. `+`. |
| `guildID` | Discord server ID used by member counting and slash-command setup. |
| `welcomechannel` | Channel receiving new-member welcome messages. |
| `membercount` | Channel whose name is updated with the current member count. |
| `stickychannel` | Channel used by the sticky-message feature. |
| `ticketcategory` | Category where ticket channels are created. |
| `warnchannel` | Channel used for moderation/search warnings. |
| `bugchannel` | Channel receiving bug reports. |
| `notifrole` | Role assigned/checked by the Instagram command. |
| `YTcode` | YouTube Data API key for the interactive `ytsearch` command. |
| `password` | Password required by the shutdown command. |

> **Security:** never commit your `.env` file or Discord token to GitHub. If a token is ever exposed, regenerate it immediately from the Discord Developer Portal.

### 5. Run the bot

```bash
node main.js
```

Once connected successfully, the console should display:

```text
AYL+ is online!
```

## Command architecture

Commands export metadata and an `execute` function:

```js
module.exports = {
    name: 'hello',
    description: 'I will say hello back.',
    execute(client, message, cmd, args, Discord) {
        // command logic
    }
};
```

`handlers/command_handler.js` automatically loads every `.js` file inside `commands/` into a `Discord.Collection`.

The message event then:

1. Checks the configured prefix.
2. Parses the command and arguments.
3. Resolves aliases.
4. Verifies Discord permissions.
5. Applies per-user command cooldowns.
6. Executes the corresponding command module.

This structure makes new commands easy to add without modifying the main command dispatcher.

## Customization notes

Some features are currently server-specific and should be customized before deploying the bot elsewhere:

- The welcome role is searched by the name `new member`.
- `reactionrole.js` contains predefined Fortnite role names and emojis.
- The suggestions command searches for a channel named `💡¦suggestions`.
- The rules, FAQ, social-media links, embed text, and some images are specific to the original AYL server.
- Several commands expect the bot to have moderation or role-management permissions.
- Slash-command code in `main.js` uses the older low-level Discord.js v12 interaction API.

## Known limitations

This repository reflects an older Discord.js ecosystem, so some integrations may no longer work without updates. In particular:

- Discord.js v12 is deprecated.
- Discord API behavior and gateway requirements have changed since v12.
- YouTube streaming/search dependencies may require upgrades when upstream APIs change.
- Some commands are tightly coupled to a specific Discord server configuration.
- The project does not currently include automated tests.

For a production deployment, migrating to **discord.js v14**, separating server-specific configuration from command code, adding centralized error handling, and adding automated tests would be recommended.

## License

The project is currently configured with the **ISC License** in `package.json`.
