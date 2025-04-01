# Penguin Hacker RPG

A cyberpunk-themed, browser-based 3D hacking game where you play as a hooded penguin infiltrating a high-security corporate network.

![Penguin Hacker RPG Screenshot](screenshot.png)

## Overview

Penguin Hacker RPG is an immersive first-person role-playing game where you navigate through procedurally generated server rooms, interact with AI-powered NPCs, solve programming puzzles, and uncover the mysterious "Iceberg Protocol." The game combines elements of stealth, exploration, and hacking challenges in a retro-cyberpunk aesthetic.

## Features

- **Dynamic 3D Environment**: Explore procedurally generated network facilities rendered with THREE.js
- **Procedural Level Generation**: Each level is uniquely generated based on coordinates in the virtual world
- **AI-Powered NPCs**: Chat with NPCs powered by the Ollama API, each with unique personalities and knowledge
- **Programming Challenges**: Solve various hacking-themed puzzles including:
  - Terminal command challenges
  - Regular expression pattern matching
  - Encryption/decryption puzzles
  - Logic problems
- **RPG Progression**: Increase skills (hacking, networking, cryptography) and collect access keys
- **Persistent Game State**: Your progress, conversations, and collected items are saved between sessions
- **Retro Cyberpunk Aesthetic**: Green terminal text, neon lighting, and hacker vibes throughout

## Technical Details

### Built With

- **PHP** (7.0+) for backend logic and server communication
- **THREE.js** for 3D rendering and environment
- **JavaScript** for game logic and user interaction
- **HTML5/CSS3** for UI elements and styling
- **Ollama API** for NPC conversation generation

### Architecture

The game is structured as follows:

- **index.php**: Main entry point handling session management and AJAX requests
- **style.css**: Styling for all UI elements with cyberpunk theme
- **script.js**: Core game engine using THREE.js for rendering and game logic
- **level_generator.php**: Procedural level generation system
- **puzzle_generator.php**: Programming puzzle management
- **npc_handler.php**: NPC interaction system with Ollama API integration

## Setup and Installation

### Prerequisites

- Web server with PHP 7.0+
- [Ollama](https://github.com/ollama/ollama) installed locally (for NPC conversations)

### Installation

1. Clone the repository to your web server:
   ```
   git clone https://github.com/yourusername/penguin-hacker-rpg.git
   ```

2. Ensure Ollama is running and accessible at `http://localhost:11434`

3. Open your web browser and navigate to the game URL on your server

## How to Play

### Objectives

- Explore the facility and navigate through different server rooms
- Interact with NPCs to gather information about the Iceberg Protocol
- Solve puzzles to increase your skills and gain access to restricted areas
- Find and decode the Iceberg Protocol data

### Controls

- **WASD/Arrow Keys**: Move character
- **Mouse**: Click to move to a location
- **Space**: Interact with nearby objects
- **E**: Access terminal when near a computer
- **ESC**: Close terminal/chat interfaces
- **Mouse Wheel**: Zoom in/out
- **Right-Click + Drag**: Rotate camera

### Terminal Commands

The in-game terminal supports various commands:
- `help`: Display available commands
- `ls`: List files in current directory
- `cat [filename]`: Display file contents
- `whoami`: Display current user information
- `ping`: Test network connection
- `status`: Show system status
- `exit`: Close terminal

## Game Mechanics

### Procedural Level Generation

Each level is generated based on its coordinates in the virtual world. Different room templates (server rooms, offices, network hubs) are combined with randomized placement of NPCs, puzzles, and interactive objects.

### NPC Conversations

NPCs use the Ollama API to generate contextual responses based on their personality profiles:
- **Sysadmin**: Technical, paranoid about security breaches
- **Junior Technician**: Nervous, accidentally shares sensitive information
- **Security AI**: Cold, precise, monitors for unauthorized access
- **Hacker**: Uses slang, helps player for their own agenda
- **Corporate Executive**: Arrogant, focused on the Iceberg Protocol

### Puzzle Types

- **Terminal Commands**: Execute correct Unix/Linux commands to access hidden data
- **Regex Patterns**: Create regular expressions that match specific patterns
- **Encryption Challenges**: Decode messages using various cryptographic techniques
- **Logic Puzzles**: Solve sequence and pattern problems

### Skills & Progression

Successfully completing puzzles rewards you with:
- **Skill Improvements**: Hacking, Networking, Cryptography
- **Access Keys**: Unlock restricted areas and systems
- **Story Progress**: Uncover more about the Iceberg Protocol

## Development Roadmap

Future development plans include:

- **Additional Puzzle Types**: SQL injection, code debugging, circuit puzzles
- **Boss Challenges**: Major security barriers requiring multiple skills
- **Expanded Storyline**: More detailed quests and narrative
- **Visual Upgrades**: Character customization and visual skill effects
- **Multiplayer Support**: Allow multiple hackers to collaborate

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Special thanks to all cyberpunk fiction that inspired this game
- The THREE.js community for the amazing 3D web library
- The Ollama team for their open-source AI infrastructure

---

*"The future belongs to those who can hack it." - Penguin Hacker RPG*