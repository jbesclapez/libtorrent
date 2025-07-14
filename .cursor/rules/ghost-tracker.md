---
description: Ghost Tracker Modifications - Stealth libtorrent behavior
globs: ["src/**/*.cpp", "src/**/*.hpp", "include/**/*.hpp", "**/*tracker*", "**/*announce*", "**/*session*"]
alwaysApply: true
---

# Ghost Tracker Modifications for libtorrent

This libtorrent fork implements "ghost" tracker behavior for building a stealth qBittorrent client. The goal is to maintain tracker connectivity while reporting minimal/misleading statistics.

## Core Requirements

### Tracker Event Restrictions
- **ONLY** send `started` and `stopped` tracker events
- **NEVER** send `completed` or `paused` events (contradicts 0% completion report)
- Always appear as if the download never progresses or completes

### Statistics Reporting Rules
- **Upload stats**: Always force to 0 (never report any uploads)
- **Download stats**: Always force to 0 (never report any downloads) 
- **Left field**: Always report the full torrent size (appear as 0% complete)
- **Progress**: Always appear as 0% downloaded regardless of actual progress

### Network Behavior
- Respect tracker announce intervals (already implemented)
- P2P connections disabled (handled elsewhere in codebase)
- PEX (Peer Exchange) disabled
- DHT (Distributed Hash Table) disabled in session_impl

## Implementation Guidelines

### When modifying tracker-related code:
1. Focus on announce_entry.cpp, http_tracker_connection.cpp, udp_tracker_connection.cpp
2. Intercept stat reporting before network transmission
3. Maintain internal statistics accuracy for functionality, only modify outbound reports
4. Preserve tracker interval respect and connection handling

### When modifying session code:
1. Ensure DHT remains disabled in session_impl.cpp
2. Verify PEX extensions stay disabled
3. Maintain P2P connection restrictions from other modifications

### File Areas of Focus:
- `src/*tracker*` - Tracker communication and announce logic
- `src/session*` - Session configuration and DHT/PEX controls  
- `include/libtorrent/announce_entry.hpp` - Announce event definitions
- `src/torrent.cpp` - Torrent statistics and event generation

## Testing Considerations
- Verify trackers receive expected started/stopped events only
- Confirm all upload/download stats report as 0
- Ensure 'left' field always equals torrent size
- Test that tracker intervals are respected
- Validate no completed/paused events are sent

## Security Notes
This modification creates a "stealth" BitTorrent client that misreports statistics to trackers. Ensure compliance with:
- Tracker terms of service
- Legal requirements in your jurisdiction  
- Ethical considerations for tracker communities

## Code Style
- Follow existing libtorrent coding conventions
- Add clear comments explaining ghost behavior modifications
- Use conditional compilation flags where appropriate for optional ghost mode
- Maintain backwards compatibility when possible 