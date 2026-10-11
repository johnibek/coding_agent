# Coding Agent 🤖

An interactive AI-powered coding assistant built with Claude that can read, list, and edit files in your project. Chat naturally with Claude to manage your codebase!

## Features ✨

- **Interactive Chat Interface**: Communicate naturally with Claude about your code
- **File Operations**: Read, list, and edit files programmatically through natural language
- **Tool Integration**: Seamless integration of file management tools with Claude's API
- **Error Handling**: Robust error handling for file operations
- **Customizable**: Easy to extend with additional tools

## Prerequisites 📋

- Python 3.12 or higher
- Anthropic API key (get one at [console.anthropic.com](https://console.anthropic.com))

## Installation 🚀

1. Clone the repository:
```bash
git clone <repository-url>
cd coding-agent
```

2. Install dependencies using uv (recommended):
```bash
uv sync
```

Or using pip:
```bash
pip install -r requirements.txt
```

3. Create a `.env` file in the project root with your API credentials:
```env
ANTHROPIC_API_KEY=your-api-key-here
# Optional: specify a custom base URL
ANTHROPIC_BASE_URL=https://api.anthropic.com
```

## Usage 🎯

### Running the Agent

```bash
python main.py
```

Once started, you'll see:
```
Chat with Claude ('ctrl-d' to quit).
```

### Example Interactions

**Reading a file:**
```
You: Read the main.py file for me
Claude: [reads and summarizes the file content]
```

**Listing files:**
```
You: What files are in the project directory?
Claude: [lists all files and directories]
```

**Editing files:**
```
You: Change the function name from 'old_name' to 'new_name' in utils.py
Claude: [performs the edit]
```

**Complex tasks:**
```
You: Create a new Python file called helpers.py with a function that reverses strings
Claude: [creates the file with the requested function]
```

## Project Structure 📁

```
coding-agent/
├── main.py              # Main entry point and CodingAgent class
├── tools.py             # Tool definitions for file operations
├── pyproject.toml       # Project metadata and dependencies
├── .env                 # API credentials (not in version control)
└── README.md            # This file
```

## Available Tools 🛠️

### 1. **read_file**
Read the contents of a file.
- **Input**: `path` (relative path to the file)
- **Output**: File contents as string

### 2. **list_files**
List all files and directories in a given path.
- **Input**: `path` (optional, defaults to current directory)
- **Output**: JSON array of file/directory names

### 3. **edit_file**
Create or modify files by replacing text.
- **Inputs**:
  - `path`: File path (creates if doesn't exist)
  - `old_str`: Text to find and replace
  - `new_str`: Replacement text
- **Output**: Status message

## Architecture 🏗️

The project follows a modular design:

```python
CodingAgent
├── __init__()          # Initialize Anthropic client with tools
├── run()               # Main conversation loop
├── run_inference()     # Call Claude API with conversation history
├── execute_tool()      # Execute requested tools
└── get_message()       # Handle user input
```

## Dependencies 📦

- **anthropic**: Official Anthropic SDK for Claude API access
- **pydantic**: Data validation and parsing
- **python-dotenv**: Environment variable management
- **termcolor**: Colored terminal output

See `pyproject.toml` for specific version requirements.

## Configuration ⚙️

### Environment Variables

```env
ANTHROPIC_API_KEY=sk-ant-...  # Required: Your Anthropic API key
ANTHROPIC_BASE_URL=...         # Optional: Custom API endpoint
```

### Chat Settings

You can modify the following in `main.py`:
- `max_tokens`: Maximum response length (default: 10000)
- `model`: Claude model to use (default: claude-haiku-4-5)

## Error Handling 🚨

The agent handles various error scenarios:
- Missing or invalid file paths
- File not found errors
- Permission denied errors
- Text replacement errors when `old_str` is not found

All errors are caught and returned to Claude for appropriate user communication.

## Tips & Tricks 💡

1. **Be specific**: Describe file paths and operations clearly
2. **Check before editing**: Ask the agent to read a file before suggesting edits
3. **Complex operations**: Break complex tasks into smaller steps
4. **Context**: The agent remembers conversation history within a session

## Examples 📝

### Create a new file
```
You: Create a file called test.py with a function that says hello
```

### Edit an existing file
```
You: In main.py, change 'claude-haiku-4-5' to 'claude-opus'
```

### Project analysis
```
You: Show me all Python files in the project
```

## Troubleshooting 🔧

| Issue | Solution |
|-------|----------|
| API Key Error | Ensure `ANTHROPIC_API_KEY` is set in `.env` |
| File Not Found | Verify the relative path from the project root |
| Connection Error | Check internet connection and API endpoint |
| EOFError | Use Ctrl+D to exit gracefully |

## Performance 🚄

- **Fast**: Uses Claude Haiku for quick responses
- **Efficient**: Maintains conversation history for context
- **Scalable**: Can handle large files and projects

## Security 🔒

- API keys should never be committed to version control
- Use `.env` files for local development only
- Consider using environment management tools for production

## Contributing 🤝

This is a personal project. Feel free to fork and extend it with:
- Additional file tools
- Different Claude models
- Web interface
- Code analysis features

## Limitations ⚠️

- File operations are relative to the current working directory
- Cannot delete files (only read and edit)
- No version control integration
- Single-threaded conversation handling

## Future Enhancements 🔮

- [ ] File deletion capability
- [ ] Directory creation
- [ ] Git integration
- [ ] Web UI
- [ ] Multi-user support
- [ ] Persistent conversation history
- [ ] Code analysis and suggestions

## License 📄

This project is open source and available under the MIT License.

## Support 📞

For issues or questions:
1. Check the troubleshooting section
2. Review Anthropic's documentation: https://docs.anthropic.com
3. Open an issue on GitHub

---

**Happy coding with Claude!** 🎉
