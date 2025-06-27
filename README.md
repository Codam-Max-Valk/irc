# 🚀 IRC Server - ft_irc

A fully-featured Internet Relay Chat (IRC) server implementation built in C++20, designed to handle multiple concurrent client connections with complete IRC protocol compliance.

![C++](https://img.shields.io/badge/C%2B%2B-20-blue.svg)
![Build](https://img.shields.io/badge/build-passing-brightgreen.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)

## 📋 Table of Contents

- [Overview](#overview)
- [✨ Key Features](#-key-features)
- [🏗️ Architecture](#️-architecture)
- [🎯 Commands Implementation](#-commands-implementation)
- [⚡ Exception Handling System](#-exception-handling-system)
- [🤖 Bot Integration](#-bot-integration)
- [🚀 Getting Started](#-getting-started)
- [📡 Usage](#-usage)
- [🧪 Testing](#-testing)
- [🏗️ Project Structure](#️-project-structure)
- [👥 Contributors](#-contributors)

## Overview

This IRC server is a robust implementation of the IRC protocol (RFC 1459) supporting multiple clients, channels, and advanced features. The project emphasizes clean architecture, comprehensive error handling, and full RFC compliance.

**This was a collaborative project where I specifically focused on implementing the comprehensive command system and robust exception handling architecture.**

## ✨ Key Features

### Core Server Features
- **Multi-client support** with epoll-based event handling
- **Real-time messaging** between users and channels
- **Channel management** with modes and operator privileges
- **User authentication** and registration system
- **Persistent connections** with proper cleanup
- **Signal handling** for graceful shutdown

### Advanced Features
- **AI-powered bot** with ChatGPT integration
- **Comprehensive logging** and debugging
- **RFC 1459 compliance** with proper response codes
- **Memory-safe** C++20 implementation
- **Cross-platform compatibility**

## 🏗️ Architecture

The server uses a modern C++ architecture with clear separation of concerns:

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│     Server      │────│   Connection    │────│     Client      │
│   Management    │    │    Handler      │    │   Communication │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         ▼                       ▼                       ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│    Command      │    │   Exception     │    │    Channel      │
│   Processing    │    │    Handling     │    │   Management    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## 🎯 Commands Implementation

*This section highlights my primary contribution to the project.*

I implemented a comprehensive command system supporting all major IRC commands with full RFC compliance:

### User Commands
- **NICK** - Nickname management with collision detection
- **USER** - User registration and authentication
- **PASS** - Password authentication
- **QUIT** - Graceful disconnection

### Channel Commands
- **JOIN** - Channel joining with key support
- **PART** - Leave channels with optional messages
- **PRIVMSG** - Send messages to users/channels
- **TOPIC** - Set/view channel topics
- **LIST** - Browse available channels
- **WHO** - Query user information

### Operator Commands
- **MODE** - Advanced channel and user mode management
- **KICK** - Remove users from channels
- **INVITE** - Invite users to invite-only channels

### Server Commands
- **PING/PONG** - Connection keepalive
- **INFO** - Server information
- **MOTD** - Message of the day
- **CAP** - Client capability negotiation

### Command Features
```cpp
// Example: Advanced MODE command with parameter validation
void handleModeCommand(const vector<string>& params, User* user) {
    // Comprehensive parameter validation
    if (params.size() < 2) {
        throw NotEnoughParametersException();
    }
    
    // Support for both user and channel modes
    if (params[1][0] == '#') {
        handleChannelMode(params, user);
    } else {
        handleUserMode(params, user);
    }
}
```

## ⚡ Exception Handling System

*This is another major area of my contribution - a sophisticated exception hierarchy.*

I designed and implemented a comprehensive exception system that provides precise error handling and proper IRC response codes:

### Exception Hierarchy
```cpp
IrcException (Base Class)
├── NoSuchUserException (401)
├── ErroneousNicknameException (432)  
├── NicknameInUseException (433)
├── NoSuchChannelException (403)
├── NotOnChannelException (442)
├── UserNotOperatorException (482)
├── ChannelFullException (471)
├── InviteOnlyChannelException (473)
├── BadChannelKeyException (475)
├── NotEnoughParametersException (461)
├── UserNotOnChannelException (441)
├── CannotSendToChannelException (404)
├── NoRecipientException (411)
└── NoOtherUserModeException (502)
```

### Key Exception Features

#### 1. **Smart Error Streaming**
```cpp
class IrcException {
    virtual void e_stream(IRStream &stream, User *user) const = 0;
};

// Automatic IRC-compliant error response generation
void NoSuchUserException::e_stream(IRStream &stream, User *user) const {
    stream.prefix().code(ERR_NOSUCHNICK)
          .param(user->getNickname())
          .param(param)
          .trail(message).end();
}
```

#### 2. **Context-Aware Error Messages**
Each exception carries relevant context and automatically formats proper IRC responses with correct error codes.

#### 3. **Hierarchical Error Types**
- **IrcException**: Protocol-level errors with IRC response codes
- **SetUpException**: Server initialization errors  
- **ExecutionException**: Runtime server errors
- **UserQuitException**: Clean disconnection handling

#### 4. **Comprehensive Error Coverage**
```cpp
// Example: Channel joining with multiple validation layers
try {
    validateChannelName(channelName);
    validateChannelKey(channel, providedKey);
    validateChannelCapacity(channel);
    validateUserPermissions(user, channel);
    
    channel->addUser(user);
    
} catch (const BadChannelKeyException& e) {
    e.e_stream(stream, user);
} catch (const ChannelFullException& e) {
    e.e_stream(stream, user);
} catch (const InviteOnlyChannelException& e) {
    e.e_stream(stream, user);
}
```

### IRC Response Code Compliance

I implemented full IRC response code support with over 50 different codes:

**Success Codes (200-399)**
- Welcome sequences (001-005)
- Channel information (321-329, 331-333)
- User information (311-319, 352-353)

**Error Codes (400-599)**  
- Client errors (401-432, 441-446)
- Channel errors (403-405, 471-475)
- Server errors (481-485, 501-502)

## 🤖 Bot Integration

The project includes an intelligent bot with ChatGPT integration:

- **AI-powered responses** using OpenAI API
- **Natural conversation** capabilities
- **Channel interaction** and command processing
- **Modular design** for easy extension

## 🚀 Getting Started

### Prerequisites
```bash
# Required
- C++20 compatible compiler (GCC 10+ or Clang 12+)
- Make
- Linux/macOS environment

# Optional (for bot)
- OpenAI API key
- Internet connection
```

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/ft_irc.git
cd ft_irc
```

2. **Build the server**
```bash
make
```

3. **Build the bot** (optional)
```bash
make bonus
```

## 📡 Usage

### Starting the Server
```bash
# Method 1: Command line arguments
./ircserv 6667 mypassword

# Method 2: Environment variables  
PORT=6667 PASSWORD=mypassword ./ircserv
```

### Connecting with a Client
```bash
# Using netcat for testing
nc localhost 6667

# Using standard IRC clients
/server localhost 6667 mypassword
```

### Basic IRC Commands
```irc
PASS mypassword
NICK mynickname  
USER username hostname servername :Real Name
JOIN #general
PRIVMSG #general :Hello everyone!
```

## 🧪 Testing

The project includes comprehensive testing utilities:

```bash
# Run the test suite
cd tester
./tester.sh

# Test specific commands
./tester.sh MODE
./tester.sh JOIN
```

### Test Coverage
- **Unit tests** for individual commands
- **Integration tests** for multi-client scenarios  
- **Error condition testing** for exception handling
- **RFC compliance validation**

## 🏗️ Project Structure

```
irc/
├── 📁 src/
│   ├── 📁 commands/          # IRC command implementations
│   │   ├── 📄 JOIN.cpp       # Channel joining logic
│   │   ├── 📄 MODE.cpp       # Mode management
│   │   ├── 📄 PRIVMSG.cpp    # Message handling
│   │   └── ... (17 commands total)
│   ├── 📄 Server.cpp         # Core server logic
│   ├── 📄 Channel.cpp        # Channel management
│   └── 📄 User.cpp           # User management
├── 📁 include/
│   ├── 📄 Exceptions.hpp     # Exception hierarchy
│   ├── 📄 Codes.hpp          # IRC response codes
│   ├── 📄 Server.hpp         # Server class definition
│   └── 📄 General.hpp        # Common utilities
├── 📁 bot/                   # AI bot implementation
│   ├── 📁 src/
│   └── 📁 include/
├── 📁 tester/                # Testing framework
└── 📄 Makefile               # Build configuration
```

## 👥 Contributors

This project was developed as a collaborative effort:

- **[Your Name]** - *Lead developer for commands and exception handling*
  - Implemented all 17 IRC commands with full RFC compliance
  - Designed and built the comprehensive exception handling system  
  - Created the IRC response code framework
  - Developed command parsing and validation logic

- **[Teammate Names]** - *Core server architecture and networking*
  - Server initialization and networking layer
  - Multi-client connection handling
  - Channel and user management systems

---

## 🔧 Technical Details

### Performance Features
- **Epoll-based I/O** for handling thousands of concurrent connections
- **Memory-efficient** user and channel management
- **Zero-copy** message forwarding where possible
- **Optimized** command parsing with minimal allocations

### Security Features  
- **Input validation** and sanitization
- **Buffer overflow protection**
- **Resource limiting** to prevent DoS attacks
- **Proper authentication** flow

---

*Built with ❤️ and modern C++20*

**⭐ Star this repository if you found it helpful!**
