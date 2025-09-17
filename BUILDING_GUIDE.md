# How to Build This MongoDB AI Agent - Step by Step Guide

## 🎯 **What You'll Actually Build**

You're going to create an AI chatbot that can talk to your MongoDB database. Instead of writing complex database queries, you'll just ask questions in plain English.

## 📋 **Prerequisites (What You Need Before Starting)**

1. **Python 3.13+** installed on your computer
2. **Node.js** installed (for the MongoDB MCP server)
3. **A MongoDB database** (free tier on MongoDB Atlas works fine)
4. **A Google API key** (free from Google AI Studio)

## 🛠️ **Step 1: Set Up Your Project Structure**

Create a new folder and set up the basic structure:

```bash
mkdir my-mongodb-ai-agent
cd my-mongodb-ai-agent
```

Create these files:
- `pyproject.toml` (tells Python what packages to install)
- `.env` (stores your secret keys)
- `mongodb_agent/` folder
- `mongodb_agent/__init__.py`
- `mongodb_agent/agent.py`
- `mongodb_agent/prompt.py`

## 📦 **Step 2: Install the Required Tools**

### Install UV (Python package manager):
```bash
# On Windows (PowerShell):
pip install uv

# On Mac/Linux:
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Install Node.js:
- Go to https://nodejs.org
- Download and install the LTS version

## 🔑 **Step 3: Get Your API Keys**

### Get Google API Key:
1. Go to https://aistudio.google.com/
2. Click "Get API Key"
3. Create a new API key
4. Copy it (you'll need it later)

### Get MongoDB Connection String:
1. Go to https://cloud.mongodb.com/
2. Create a free account
3. Create a new cluster (free tier)
4. Go to "Connect" → "Connect your application"
5. Copy the connection string (looks like: `mongodb+srv://username:password@cluster.mongodb.net/database`)

## 📝 **Step 4: Create the Configuration Files**

### Create `pyproject.toml`:
```toml
[project]
name = "my-mongodb-ai-agent"
version = "0.1.0"
description = "AI agent that talks to MongoDB"
requires-python = ">=3.13"
dependencies = [
    "google-adk>=1.14.1",
    "python-dotenv>=1.1.1",
]
```

### Create `.env` file:
```env
GOOGLE_GENAI_USE_VERTEXAI=FALSE
GOOGLE_API_KEY=your_google_api_key_here
MDB_MCP_CONNECTION_STRING=your_mongodb_connection_string_here
```

**Replace the placeholder values with your actual keys!**

## 🤖 **Step 5: Create the AI Agent Code**

### Create `mongodb_agent/__init__.py`:
```python
from . import agent
```

### Create `mongodb_agent/prompt.py`:
```python
INSTRUCTION = """
You are a helpful MongoDB assistant. You can help users:
- Explore their databases and collections
- Run queries and find data
- Suggest optimizations
- Create indexes and collections (with permission)

Always be helpful and explain what you're doing.
Ask for confirmation before making any changes to the database.
"""
```

### Create `mongodb_agent/agent.py`:
```python
import os
import logging
from google.adk.agents import LlmAgent
from google.adk.tools.mcp_tool.mcp_toolset import MCPToolset
from google.adk.tools.mcp_tool.mcp_session_manager import StdioConnectionParams
from google.adk.tools.mcp_tool.mcp_session_manager import StdioServerParameters
from dotenv import load_dotenv

# Load environment variables
ROOT = os.path.dirname(os.path.dirname(__file__))
load_dotenv(dotenv_path=os.path.join(ROOT, ".env"))

# Set up logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

# Get MongoDB connection string
mongodb_connection_string = os.environ.get("MDB_MCP_CONNECTION_STRING")

if not mongodb_connection_string:
    logger.error("Please set MDB_MCP_CONNECTION_STRING in your .env file")
    exit(1)

# Create the MongoDB toolset
mongodb_toolset = MCPToolset(
    connection_params=StdioConnectionParams(
        server_params=StdioServerParameters(
            command='npx',
            args=["-y", "mongodb-mcp-server@latest"],
            env={"MDB_MCP_CONNECTION_STRING": mongodb_connection_string}
        ),
        timeout=60.0
    ),
)

# Create the AI agent
from .prompt import INSTRUCTION

root_agent = LlmAgent(
    model='gemini-2.0-flash',
    name='mongodb_assistant',
    instruction=INSTRUCTION,
    tools=[mongodb_toolset],
)

logger.info("MongoDB AI Agent is ready!")
```

## 🚀 **Step 6: Install Dependencies and Run**

### Install dependencies:
```bash
uv sync
```

### Run the agent:
```bash
adk web
```

This will open a web interface where you can chat with your MongoDB database!

## 🧪 **Step 7: Test Your Agent**

Try asking questions like:
- "What databases do I have?"
- "Show me the collections in my database"
- "Find all documents in my users collection"
- "Create a new collection called 'test'"

## 🎉 **What You've Built**

You now have an AI agent that can:
- Connect to your MongoDB database
- Understand natural language questions
- Execute database operations
- Present results in a friendly format

## 🔧 **Troubleshooting Common Issues**

### "Connection failed":
- Check your MongoDB connection string
- Make sure your MongoDB cluster is running
- Verify your username/password are correct

### "Google API key error":
- Make sure your Google API key is valid
- Check that you've set `GOOGLE_GENAI_USE_VERTEXAI=FALSE`

### "MCP server not found":
- Make sure Node.js is installed
- The system will automatically download the MongoDB MCP server

## 📚 **Next Steps**

Once this basic version works, you can:
- Add more sophisticated prompts
- Create custom tools
- Add error handling
- Deploy it to the cloud
- Add a custom web interface

## 💡 **Key Concepts You've Learned**

1. **AI Agents**: Programs that can understand and respond to natural language
2. **MCP (Model Context Protocol)**: A way for AI to connect to external tools
3. **Environment Variables**: Secure way to store API keys and secrets
4. **Package Management**: Using `uv` to manage Python dependencies
5. **Database Integration**: Connecting AI to real databases

---

**Remember**: This is a learning project. Start simple, get it working, then gradually add more features. The goal is to understand how AI agents work with real data!
