# Telegram AI Dating Agent

Telegram-агент на базе искусственного интеллекта, который поможет вам создавать остроумные и привлекательные сообщения для ваших разговоров. Создан на основе Claude Sonnet, семантического поиска [Nia](https://trynia.ai) и полнофункциональной интеграции Telegram MCP.

## Что он делает

- ** Предложения по интеллектуальному ответу **: Получайте предложения по ответу с помощью искусственного интеллекта, основанные на контексте разговора
- **500+ Подсказки **: Семантический поиск по тщательно отобранной коллекции подсказок, проиндексированной с помощью Nia
- ** Руководства по знакомствам **: Ищите руководства о том, как разговаривать с женщинами, как начать разговор и советы по флирту
- ** Улучшение качества сообщений **: Превращайте скучные сообщения в остроумные и увлекательные
- ** Полный доступ к Telegram **: Читайте сообщения, отправляйте ответы, управляйте чатами - и все это на естественном языке

## Создано Nia

This agent uses [Nia](https://trynia.ai) as its knowledge retrieval engine. Nia indexes and searches through:
- 500+ curated pickup lines (funny, cheesy, clever, romantic)
- Guides on conversation techniques
- Tips for keeping conversations engaging

You can index your own content by creating a source at [trynia.ai](https://trynia.ai).

## Architecture

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│   CLI Agent      │────▶│  Telegram API    │────▶│    Telegram      │
│  (TypeScript)    │     │   Bridge (Py)    │     │    Servers       │
└──────────────────┘     └──────────────────┘     └──────────────────┘
         │
         ▼
┌──────────────────┐     ┌──────────────────┐
│  Claude Sonnet   │     │    Nia API       │
│   (AI Gateway)   │     │ (trynia.ai)      │
└──────────────────┘     └──────────────────┘
                         - 500+ pickup lines
                         - Dating guides
                         - Conversation tips
```

## Quick Start

### 1. Get Telegram API Credentials

Get your API credentials at [my.telegram.org/apps](https://my.telegram.org/apps).

### 2. Install & Configure

```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/telegram-mcp.git
cd telegram-mcp

# Install Python dependencies
uv sync

# Generate Telegram session string
uv run session_string_generator.py

# Configure environment
cp .env.example .env
# Edit .env with your credentials
```

### 3. Start the Telegram API Bridge

```bash
python telegram_api.py
```

This runs a FastAPI server on port 8765 that bridges the TypeScript agent to Telegram.

### 4. Run the AI Agent

```bash
cd agent
bun install
bun run dev
```

## Usage Examples

Once running, interact with natural language:

```
# Reading & Sending
> Show me messages from @her_username
> Send "Hey, I was just thinking about you" to @her_username
> Reply to her last message with something witty

# Reactions
> React to her last message with ❤️
> Send a 🔥 reaction to message 123

# Search & History
> Search our chat for "dinner plans"
> Show me the last 50 messages with her
> Find me a funny pickup line about pizza

# AI Assistance
> What should I reply to her message about coffee?
> Make this message more flirty: "want to hang out tomorrow?"
> Search for tips on how to keep a conversation going

# User Info
> Is she online right now?
> Check her status

# Message Management
> Edit my last message to fix the typo
> Delete message 456
> Forward that meme to @friend
```

### Agent Commands

- `/help` - Show help
- `/clear` - Clear conversation history
- `/status` - Check connection status
- `/quit` - Exit

## Environment Variables

Create a `.env` file in the project root:

```env
# Telegram API (Required)
TELEGRAM_API_ID=your_api_id
TELEGRAM_API_HASH=your_api_hash
TELEGRAM_SESSION_STRING=your_session_string

# AI Services (Required for agent)
AI_GATEWAY_API_KEY=your_vercel_ai_gateway_key
NIA_API_KEY=your_nia_api_key
NIA_CODEBASE_SOURCE=your_pickup_lines_source_uuid
```

## Alternative: Use as MCP Server

You can also use this as a standalone MCP server with Claude Desktop or Cursor, without the AI agent.

Add to your MCP config (`~/Library/Application Support/Claude/claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "telegram": {
      "command": "uv",
      "args": ["--directory", "/path/to/telegram-mcp", "run", "main.py"]
    }
  }
}
```

This exposes 60+ Telegram tools including messaging, contacts, groups, channels, reactions, and more.

## Available Tools

### Agent Tools (20+)

**Core Messaging**
| Tool | Description |
|------|-------------|
| `getChats` | List all conversations |
| `getMessages` | Read messages from a chat |
| `sendMessage` | Send a message |
| `getChat` | Get chat details |
| `searchContacts` | Search contacts |

**Reactions & Replies**
| Tool | Description |
|------|-------------|
| `sendReaction` | React with ❤️ 🔥 😂 etc |
| `replyToMessage` | Reply to specific messages |

**Edit & Delete**
| Tool | Description |
|------|-------------|
| `editMessage` | Fix typos after sending |
| `deleteMessage` | Remove messages |

**History & Search**
| Tool | Description |
|------|-------------|
| `getHistory` | Get up to 500 messages |
| `searchMessages` | Search chat by text |

**Forward & Pin**
| Tool | Description |
|------|-------------|
| `forwardMessage` | Forward to another chat |
| `pinMessage` | Pin important messages |
| `markAsRead` | Mark messages as read |

**User Info**
| Tool | Description |
|------|-------------|
| `getUserStatus` | Check if user is online |
| `getUserPhotos` | Get profile photos |

**Media**
| Tool | Description |
|------|-------------|
| `searchGifs` | Search for GIFs |

**Nia Search**
| Tool | Description |
|------|-------------|
| `searchPickupLines` | Search indexed pickup lines & dating advice |
| `niaSearch` | General semantic search |
| `webSearch` | Real-time web search |

**AI Tools**
| Tool | Description |
|------|-------------|
| `aiifyMessage` | Transform messages into witty responses |

### MCP Server Tools (60+)
Full Telegram API access including:
- Chat & Group Management (create, invite, admin, ban)
- Messaging (send, reply, edit, delete, forward, pin, reactions)
- Contact Management (add, search, block, import/export)
- Media & Stickers
- Privacy Settings
- And much more...

## Docker

```bash
docker build -t telegram-mcp:latest .
docker compose up --build
```

## Troubleshooting

- **Database lock errors**: Use session string auth instead of file-based
- **Auth errors**: Regenerate session string with `uv run session_string_generator.py`
- **Connection issues**: Check that `telegram_api.py` is running on port 8765
- **Error logs**: Check `mcp_errors.log` for detailed errors

## Security

- Never commit your `.env` or session string
- Session string = full Telegram account access
- All processing is local, data only goes to Telegram API

## Credits

- Built on [telegram-mcp](https://github.com/chigwell/telegram-mcp) by [@chigwell](https://github.com/chigwell)
- Knowledge retrieval powered by [Nia](https://trynia.ai)
- Uses [Telethon](https://github.com/LonamiWebs/Telethon), [MCP](https://modelcontextprotocol.io/), and [Vercel AI SDK](https://sdk.vercel.ai/)

## License

[Apache 2.0](LICENSE)
# talk-to-girlfriend-ai
