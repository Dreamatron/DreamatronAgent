<div align="center">

# Dreamatron Agent
**AI copilot for Unreal Engine**

[![Discord][discord-shield]][discord-url]

[discord-shield]: https://img.shields.io/badge/Discord-Join-5865F2?style=flat&logo=discord&logoColor=white
[discord-url]: https://discord.gg/ThkpVxdzet

</div>

## Version 0.1.0

## Overview
Dreamatron Agent is an Unreal Engine plugin designed to control Unreal Engine with AI tools. It contains an
integrated chat interface made in slate for direct access to Unreal Engine's built in editor tools and an AI agent
implementation specially designed for working with Unreal Engine.

Model Context Protocol (MCP) https://modelcontextprotocol.io/introduction server is implemented to allow external AI agents control over the same core tools as the Dreamatron Agent.

# Work in Progress
## The plugin is rapidly being developed and it is still very early. 
## Do not use in any Unreal project you care about.

Only current support is for Windows

## ⚠️ DISCLAIMER
This plugin allows AI agents to directly modify your Unreal Engine project. While it can be a powerful tool, it also comes with risks:

- AI agents may make unexpected changes to your project
- Files could be accidentally deleted or modified
- Project settings could be altered
- Assets could be overwritten

**IMPORTANT SAFETY MEASURES:**
1. Always use source control (like Git or Perforce) with your project
2. Make regular backups of your project
3. Test the plugin in a separate project first
4. Review changes before committing them

By using this plugin, you acknowledge that:
- You are solely responsible for any changes made to your project
- The plugin author is not responsible for any damage, data loss, or issues caused by AI agents
- You use this plugin at your own risk

## Features
- TCP server implementation for remote control of Unreal Engine
- JSON-based command protocol for AI tools integration
- Editor UI integration for easy access to MCP functionality
- Comprehensive scene manipulation capabilities
- Python companion scripts for client-side interaction

## Roadmap
This is the current development roadmap as of 3/25/2025, subject to change:

### Functionality
- [X] Basic scene operations
- [X] Python Execution
- [X] Materials
- [X] Basic Slate Chat interface
- [X] Openai TTS support
- [X] Claude API
- [ ] Gemini API
- [ ] Proper Setup Introduction Screen
- [ ] Asset tools (Implemented but API is broken)
- [ ] User Extensions (in progress)
- [ ] Blueprints
- [ ] Niagara VFX
- [ ] Metasound
- [ ] Landscape (I might hold off on this because Epic has mentioned they are going to be updating the landscape tools)
- [ ] Modeling Tools
- [ ] PCG

### MCP Support
- [X] Claude for Desktop
- [ ] Cline
- [ ] Cursor Agent

## Requirements
- Unreal Engine 5.5 (Only tested on this version, may work with earlier, but no official support)
- C++ development environment configured for Unreal Engine
- Python 3.7+ for client-side scripting
- MCP integration, you will need a Model to run the commands, in testing I've been using Claude for Desktop https://claude.ai/download

## Installation
1. Clone this repository into your Unreal project's `Plugins` directory:
   ```
   git clone https://github.com/dreamatron/DreamatronAgent.git Plugins/DreamatronAgent
   ```
   The project path should match this pattern like so:
...\UNREAL_PROJECT\Plugins\DreamatronAgent\

3. Regenerate your project files (right-click your .uproject file and select "Generate Visual Studio project files")
4. Build the project in whatever IDE you use, I use Rider, Visual Studio works (working on releases now)
5. Open your project and enable the plugin in Edit > Plugins > DreamatronAgent
6. Enable Python plugins in Unreal
7. Run setup_unreal_mcp.bat (I probably need to make some fixes to this file as more people try it out)
8. Currently I've only tested with Claude for Desktop so follow the instructions below to continue

## With Claude for Desktop
You will need to find your installation directory for claude for desktop. Find claude_desktop_config.json and add an entry and make it look like so:
```json
{
    "mcpServers": {
        "unreal": {
            "command": "C:\\Unreal_Project\\Plugins\\DreamatronAgent\\MCP\\run_unreal_mcp.bat",
            "args": []
        }
    }
}
```
IN THE COMMAND FIELD PUT YOUR PATH TO YOUR PLUGIN DIRECTORY POINTED TO THE SCRIPT: "run_unreal_mcp.bat"
This script is located within ../plugin_root_directory/MCP/run_unreal_mcp.bat

You can refer to this link for more info:
https://modelcontextprotocol.io/quickstart/user

To find the path to your claude for desktop install you can go into settings and click 'Edit Config'
On my Windows PC the path is:
C:\Users\USERNAME\AppData\Roaming\Claude

## Setup In Editor
Work is being done to smooth out this introduction process, but it is made freely available early so anyone can try it out if they are willing to work through the setup

### API Keys
The plugin currently relies on API access to Anthropic's Claude model

Register for an API key through Anthropic's website and copy it into the plugin's settings window
This should enable you to chat directly through the plugin's integrated chat interface and you should be able to now talk to the AI

![image](https://github.com/user-attachments/assets/81af988e-3610-467e-b3a6-8bd6c7712e7f)


## Usage
### In Unreal Editor
Once the plugin is enabled, you'll find MCP controls in the editor toolbar button. 
![image](https://github.com/user-attachments/assets/68338e7a-090d-4fd9-acc9-37c0c1b63227)

![image](https://github.com/user-attachments/assets/34f734ee-65a4-448a-a6db-9e941a588e93)

The TCP server can be started/stopped from here.
Check the output log under log filter LogMCP for extra information.

Once the server is confirmed up and running from the editor.
Open Claude for Desktop, ensure that the tools have successfully enabled, ask Claude to work in unreal.

Currently only basic operations are supported, creating objects, modfiying their transforms, getting scene info, and running python scripts.
Claude makes a lot of errors with unreal python as I believe there aren't a ton of examples for it, but let it run and it will usually figure things out.
I would really like to improve this aspect of how it works but it's low hanging fruit for adding functionality into unreal.

### Client-Side Integration
Use the provided Python scripts in the `MCP` directory to connect to and control your Unreal Engine instance:

```python
from unreal_mcp_client import UnrealMCPClient

# Connect to the Unreal MCP server
client = UnrealMCPClient("localhost", 13377)

# Example: Create a cube in the scene
client.create_object(
    class_name="StaticMeshActor",
    asset_path="/Engine/BasicShapes/Cube.Cube",
    location=(0, 0, 100),
    rotation=(0, 0, 0),
    scale=(1, 1, 1),
    name="MCP_Cube"
)
```

## Command Reference
The plugin supports various commands for scene manipulation:
- `get_scene_info`: Retrieve information about the current scene
- `create_object`: Spawn a new object in the scene
- `delete_object`: Remove an object from the scene
- `modify_object`: Change properties of an existing object
- `execute_python`: Run Python commands in Unreal's Python environment
- And more to come...

Refer to the documentation in the `Docs` directory for a complete command reference.

## Security Considerations
- The MCP server accepts connections from any client by default
- Limit server exposure to localhost for development
- Validate all incoming commands to prevent injection attacks

## Troubleshooting
- Ensure Unreal Engine is running with the MCP plugin.
- Check logs in Claude for Desktop for stderr output.
- Reach out on the discord!
  [Discord](https://discord.gg/ThkpVxdzet)
  
### Project Structure
- `Source/DreamatronAgent/`: Core plugin implementation
  - `Private/`: Internal implementation files
  - `Public/`: Public header files
- `Content/`: Plugin assets
- `MCP/`: Python client scripts and examples
- `Resources/`: Icons and other resources

## Credits
Copyright (c) 2025 Dreamatron
- Created by: kvick
- X: [@kvickart](https://x.com/kvickart)
  
### Thank you to testers!!!
- https://github.com/TheMurphinatur

## License: GNU GENERAL PUBLIC LICENSE


## Contributing
Contributions are welcome, but likely after some foundations have been refined and solidified
