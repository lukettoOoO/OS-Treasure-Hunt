# OS Treasure Hunt

A multi-process treasure hunt management system implemented in C for Operating Systems coursework. This project demonstrates advanced OS concepts including process management, inter-process communication (IPC), signals, pipes, and file operations.

## 📋 Project Overview

The Treasure Hunt system allows users to create and manage multiple treasure hunts, where each hunt contains treasures with geographic coordinates, clues, and point values. The system uses a hub-monitor architecture with signal-based communication between processes.

## 🏗️ Architecture

The project consists of three main components:

### 1. **treasure_manager**
Handles CRUD operations for hunts and treasures:
- Create new hunts with unique IDs
- Add treasures to existing hunts
- List all treasures in a hunt
- View individual treasure details
- Remove treasures or entire hunts
- Maintains audit logs for all operations

### 2. **treasure_hub**
Main interface process that:
- Spawns and manages a monitor child process
- Sends signals to coordinate operations
- Uses pipes for inter-process communication
- Provides an interactive menu-driven interface

### 3. **calculate_score**
Scoring system that:
- Iterates through all active hunts
- Aggregates treasure values per user
- Generates score reports using child processes

## 🚀 Features

- **Process Management**: Fork-based architecture with parent-child process coordination
- **Signal Handling**: Custom signal handlers (SIGUSR1, SIGUSR2, SIGPIPE, SIGTERM)
- **IPC Mechanisms**: 
  - Pipes for data transfer between processes
  - Temporary files for parameter passing
  - Symbolic links for log file access
- **Binary File Operations**: Efficient storage using binary `.dat` files
- **Logging**: Comprehensive activity logging in `logged_hunt.log` files
- **Multi-user Support**: Track and score multiple users across hunts

## 📁 Project Structure

```
SO/
├── treasure_manager.c      # Hunt and treasure CRUD operations
├── treasure_manager         # Compiled executable
├── treasure_hub.c          # Main hub with monitor process
├── treasure_hub            # Compiled executable
├── calculate_score.c       # Score calculation system
├── calculate_score         # Compiled executable
├── test_commands.txt       # Example commands for testing
├── hunt001/                # Example hunt directory
│   ├── treasure.dat        # Binary treasure data
│   └── logged_hunt.log     # Operation logs
├── hunt002/                # Another hunt directory
├── hunt004/                # Another hunt directory
└── logged_hunt-*           # Symbolic links to hunt logs
```

## 🛠️ Data Structures

### Treasure
```c
typedef struct {
    int id;
    char username[MAX_USERNAME_LEN];
    float latitude;
    float longitude;
    char clue[MAX_CLUE_LEN];
    int value;
} Treasure;
```

### User Score
```c
typedef struct {
    char username[MAX_USERNAME_LEN];
    int score;
} User;
```

## 💻 Compilation

Compile all three programs:

```bash
gcc -o treasure_manager treasure_manager.c
gcc -o treasure_hub treasure_hub.c
gcc -o calculate_score calculate_score.c
```

## 📖 Usage

### Starting the Treasure Hub

Run the main hub interface:

```bash
./treasure_hub
```

### Menu Options

```
1 - Start monitor       # Initialize the monitor child process
2 - List hunts         # Display all hunts with their treasures
3 - List treasures     # List treasures from a specific hunt
4 - View treasure      # View details of a specific treasure
5 - Calculate score    # Calculate and display user scores
6 - Stop monitor       # Terminate the monitor process
0 - Exit              # Exit the application
```

### Command-Line Operations

**Add a new hunt and treasure:**
```bash
./treasure_manager --add <hunt_id>
# Then enter: id, username, latitude, longitude, clue, value
```

Example:
```bash
./treasure_manager --add hunt001
0
luca
90.5
30.2
Find the hidden chest near the old oak tree
800
```

**List all treasures in a hunt:**
```bash
./treasure_manager --list <hunt_id>
```

**View a specific treasure:**
```bash
./treasure_manager --view <hunt_id> <treasure_id>
```

**Remove a treasure:**
```bash
./treasure_manager --remove_treasure <hunt_id> <treasure_id>
```

**Remove an entire hunt:**
```bash
./treasure_manager --remove_hunt <hunt_id>
```

## 🔧 Technical Implementation

### Process Communication Flow

1. **Hub → Monitor**: Signal-based commands (SIGUSR1, SIGUSR2, SIGPIPE)
2. **Monitor → Hub**: Data via pipes (treasure lists, details)
3. **Hub → Manager**: Process execution with parameters
4. **Manager → Hub**: Output via redirected stdout to pipe

### Signal Usage

- **SIGUSR1**: Trigger "list all hunts" operation
- **SIGUSR2**: Trigger "list treasures from specific hunt"
- **SIGPIPE**: Trigger "view specific treasure"
- **SIGTERM**: Terminate monitor process

### File Organization

Each hunt creates:
- A directory named after the hunt ID
- `treasure.dat`: Binary file storing treasure structs
- `logged_hunt.log`: Text log of all operations
- Symbolic link in parent directory for easy log access

## 🧪 Testing

Use the provided test commands:

```bash
# Add treasures to different hunts
./treasure_manager --add hunt001 0 luca 90 30 "north bound" 800
./treasure_manager --add hunt002 1 bia 67 29 "east side" 800

# List treasures
./treasure_manager --list hunt001

# View specific treasure
./treasure_manager --view hunt001 0

# Remove and re-add
./treasure_manager --remove_treasure hunt001 0
./treasure_manager --list hunt001
```

## 🎯 Learning Objectives

This project demonstrates proficiency in:

- **Process Management**: Creating, managing, and coordinating multiple processes
- **Inter-Process Communication**: Pipes, signals, and file-based communication
- **Signal Handling**: Custom signal handlers with `sigaction()`
- **File I/O**: Binary file operations, directory manipulation, symbolic links
- **Memory Management**: Dynamic allocation and proper cleanup
- **Error Handling**: Comprehensive error checking with `perror()`
- **System Calls**: `fork()`, `exec()`, `wait()`, `pipe()`, `kill()`, `open()`, `read()`, `write()`

## 👤 Author

**Luca Adrian Mihut**  
Group: 2.2TI  
Course: Operating Systems

## 📝 Notes

- The system uses `STDOUT_FILENO` redirection for process output capture
- Temporary files are created with unique names using process PIDs
- Binary file format ensures efficient storage and retrieval
- All operations are logged for audit purposes
- Monitor process runs in an infinite loop, responding only to signals

## ⚠️ Known Limitations

- Maximum of 64 users per hunt (MAX_USERS_PER_HUNT)
- String length limits enforced via defined constants
- Monitor must be started before performing hunt operations
- No concurrent access control for multi-user scenarios

## 📄 License

Academic project for educational purposes.
