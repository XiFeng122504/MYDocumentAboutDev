# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **learning-focused project** consisting of two components:
1. **VimUsing Server** (`VimUsing/`) - A completed Linux Epoll-based TCP server (reference only)
2. **Qt Client** (`client/`) - An in-development Qt5-based cross-platform client (current focus)

**Primary Goal**: Educational - guide learners through building a complete network client from scratch, understanding each concept before proceeding.

## Build System

### Environment Setup
```bash
# Required for Qt 5.12 compatibility
export Qt5_DIR=/Volumes/SolidDisk/QT5.12/5.12.12/clang_64
export PATH=$Qt5_DIR/bin:$PATH
```

### Build Commands

**Main Client:**
```bash
cd client
mkdir -p build && cd build
cmake ..
make
./bin/VimUsingClient
```

**Protocol Tests (draft directory):**
```bash
cd client/draft
mkdir -p build && cd build
cmake ..
make
./test_protocol    # Tests message encoding
./test_decode      # Tests message decoding
```

**Architecture Note**: Project is configured for x86_64 architecture to match Qt 5.12 binaries (`CMAKE_OSX_ARCHITECTURES "x86_64"`).

## Architecture

### Layered Design (dependencies flow downward only)
```
UI Layer (client/ui/)
    ↓
Services Layer (client/services/)
    ↓
Core Layer (client/core/)
    - Protocol: Message encoding/decoding
    - NetworkClient: TCP socket management (planned)
```

### Key Files

**Core Protocol Layer:**
- `client/core/Protocol.h` - Protocol interface and message structures
- `client/core/Protocol.cpp` - Implementation (encode/decode complete)

**Test Code:**
- `client/draft/test_protocol.cpp` - Encoding tests
- `client/draft/test_decode.cpp` - Decoding tests with packet fragmentation scenarios

**Configuration:**
- `client/CMakeLists.txt` - Main build configuration
- `client/draft/CMakeLists.txt` - Test programs configuration

## Protocol Specification

### Binary Message Format
```
[1 byte: MessageType] [4 bytes: Payload Length] [N bytes: Payload]
```

- **Byte Order**: BigEndian (network standard)
- **Header Size**: 5 bytes (1 type + 4 length)
- **MessageType Values**:
  - `1` - LoginRequest (payload: `"username:password"`)
  - `2` - EchoRequest
  - `3` - FileUploadBegin
  - `4` - FileUploadChunk
  - `5` - FileUploadEnd
  - `100` - Ack (server response)
  - `101` - Error (server response)

### Critical Implementation Details

**Message Encoding** (`Protocol::encodeMessage`):
- Use `QDataStream` with `BigEndian` byte order
- Cast enum types: `static_cast<quint8>(message.type)`
- Never use `QByteArray::append()` for multi-byte integers (only writes 1 byte)
- Correct: `stream << quint32_value` writes all 4 bytes

**Message Decoding** (`Protocol::decodeMessage`):
- TCP delivers byte streams, not complete messages
- Handle **packet fragmentation** (partial messages) and **coalescing** (multiple messages)
- Always check buffer size before reading: `if (buffer.size() < HEADER_SIZE) return false;`
- Check twice: header completeness (5 bytes), then payload completeness
- On success: extract message, remove processed bytes, keep remaining data in buffer
- On failure: return false, leave buffer unchanged for next attempt

## Development Workflow

### Current Stage: Protocol Layer Complete (60%)
**Next Step**: NetworkClient implementation with QTcpSocket

### Testing Strategy
1. Write isolated tests in `client/draft/` to verify understanding
2. Once concept is validated, implement in main codebase
3. Test edge cases: empty data, fragmentation, coalescing
4. Cross-reference with server code (`VimUsing/src/protocol/Message.cpp`)

### Planned Development Order
1. ✅ Protocol implementation
2. NetworkClient (QTcpSocket wrapper)
3. Services layer (EchoService → AuthService → FileUploadService)
4. UI layer (LoginWindow → MainWindow)

## AI Assistant Guidelines

**CRITICAL**: This is a teaching project. The following principles override normal coding practices:

### Core Teaching Principles

1. **Never provide complete code unless explicitly requested**
   - Explain concepts → Provide hints → Guide implementation
   - Break tasks into small steps, let learner implement each step
   - Point out errors and explain why they're wrong

2. **Progressive Learning**
   - Introduce one concept at a time
   - Start simple (e.g., `quint8` before `quint32`)
   - Ensure understanding before advancing

3. **Hands-On Verification**
   - Encourage writing test programs in `client/draft/`
   - Compare correct vs incorrect approaches
   - Validate understanding through actual execution

4. **Documentation Discipline**
   - Update `client/claudeGuide.md` after learning sessions (detailed log)
   - Update `ClientTaskOutlook.md` progress tracking
   - Compress completed topics, preserve key insights

### When Learner Encounters Issues
1. Confirm the confusion point
2. Review related fundamentals
3. Provide simplified examples (preferably testable in `draft/`)
4. Guide learner to implement solution themselves
5. Provide feedback and corrections

### Example: Bad vs Good Response

❌ **Bad**: "Here's the complete `NetworkClient` implementation: [500 lines of code]"

✅ **Good**: "Let's understand QTcpSocket first. What does the `connected()` signal do? Let's write a small test that connects to a server and prints when connection succeeds. Try implementing this in `draft/test_socket.cpp`..."

## Important Technical Notes

### Qt Type System
- `quint8`: unsigned 8-bit (0-255), use for message types
- `quint32`: unsigned 32-bit (0-4GB), use for payload lengths
- `QByteArray`: byte array with `mid()`, `remove()`, `append()`
- `QDataStream`: handles binary serialization and byte order conversion

### TCP vs Application Protocol
- **TCP Layer**: Guarantees delivery, order, no duplication (OS handled)
- **Application Layer**: Our responsibility to identify message boundaries
- **Key Insight**: TCP is a byte stream, not a message stream

### Byte Order (Endianness)
- **BigEndian**: Most significant byte first (network standard)
- **LittleEndian**: Least significant byte first (x86 architecture)
- **VimUsing Protocol**: Uses BigEndian
- **Qt Implementation**: `stream.setByteOrder(QDataStream::BigEndian)`

### Common Mistakes to Avoid
1. Using `append(quint32)` instead of `QDataStream << quint32`
2. Forgetting to set byte order on both encode and decode
3. Reading from buffer without checking size first
4. Assuming TCP delivers complete messages
5. Directly casting enum to quint8 without `static_cast`

## Reference Materials

**Server Implementation** (for protocol compatibility):
- `VimUsing/src/protocol/Message.cpp` - Server-side encode/decode
- `VimUsing/include/protocol/Message.h` - Protocol definitions
- `VimUsing/LEARNING_GUIDE.md` - Server architecture explanation

**Learning Logs**:
- `ClientTaskOutlook.md` - Project overview, progress tracking, AI guidelines
- `client/claudeGuide.md` - Detailed learning journal with key insights

## Project Structure Rules

### Directory Layout (DO NOT modify this structure)
```
client/
├── core/           # Protocol, NetworkClient
├── services/       # AuthService, FileUploadService, EchoService
├── models/         # User, UploadTask data models
├── ui/             # LoginWindow, MainWindow
├── draft/          # Temporary test/learning code
└── main.cpp        # Entry point
```

### Naming Conventions
- Class names: PascalCase (`NetworkClient`)
- File names: Match class name (`NetworkClient.h`, `NetworkClient.cpp`)
- Function names: camelCase (`encodeMessage`)

### Dependency Rules
- UI → Services → Core (strict hierarchy)
- Core layer never depends on upper layers
- Each `.h` must have corresponding `.cpp` (except pure templates)

## Technology Stack

- **Language**: C++17
- **Framework**: Qt 5.12.12
- **Build System**: CMake 3.10+
- **Platform**: macOS (ARM64 host building for x86_64 target)
- **Qt Modules**: Core, Network, Widgets
