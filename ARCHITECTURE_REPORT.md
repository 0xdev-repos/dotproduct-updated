# Architecture Report: Authentication and Networking

## Overview
This report addresses questions about the current authentication and networking architecture of the dotproduct game.

## Question 1: Is this repo still using Facebook authentication?

**Answer: YES** ✅

### Evidence:

1. **login.html** (lines 18-46):
   - Contains active Facebook SDK integration
   - Implements `facebookLogin()` function that calls `FB.login()`
   - Loads Facebook SDK from `//connect.facebook.net/en_US/all.js`
   - Facebook App ID: `409286079118102`
   - Initializes Facebook SDK with `FB.init()` and handles authentication callbacks

2. **Authentication Flow**:
   - Users can choose between two login strategies:
     - **Anonymous Login**: Passes `strategy=anonymous` with a dummy access token
     - **Facebook Login**: Passes `strategy=facebook` with Facebook access token
   - Login data is sent to the server via Protocol.login() in Application.ts

3. **index.html** (lines 10-11):
   - Contains Facebook Open Graph metadata
   - Includes `fb:admins` and `fb:app_id` meta tags
   - Indicates Facebook integration for social sharing

### Current Implementation:
- The login page at `login.html` provides a "Log in" button that triggers Facebook OAuth
- Upon successful Facebook authentication, the app redirects to the game with the Facebook access token
- The application also supports anonymous play without Facebook authentication

---

## Question 2: Is it still delegating UDP traffic to/from a proxy/oracle server?

**Answer: NO** ❌

### Evidence:

1. **Protocol.ts** (lines 31, 41):
   ```typescript
   private socket: WebSocket;
   
   constructor(url: string) {
     this.socket = new WebSocket(url, Protocol.PROTOCOL_VERSION);
   }
   ```
   - The Protocol class uses **WebSocket** for client-server communication
   - No UDP or datagram socket implementation is present

2. **Application.ts** (lines 61-64):
   ```typescript
   let socketUri = 'ws://' + window.location.host + '/dotproduct/v1/' + 'trench';
   if (window.location.protocol === 'https:') {
     socketUri = socketUri.replace('ws:', 'wss:');
   }
   ```
   - Connections use WebSocket protocol (`ws://` or `wss://`)
   - No UDP proxy or oracle server configuration

3. **Communication Architecture**:
   - All game communication goes through WebSocket connections
   - Uses JSON-serialized packets for bidirectional messaging
   - Implements clock synchronization over the WebSocket connection
   - No evidence of UDP relaying or proxy servers in the codebase

### Current Implementation:
- The game uses a pure WebSocket-based architecture
- WebSocket provides reliable, ordered delivery (similar to TCP)
- All game state updates, player positions, and events are sent over WebSocket
- No UDP protocol or proxy/oracle server delegation exists in the current codebase

---

## Summary

| Feature | Status | Protocol/Method |
|---------|--------|----------------|
| Facebook Authentication | ✅ Active | OAuth via Facebook SDK |
| Anonymous Login | ✅ Active | Direct access with dummy token |
| UDP Traffic | ❌ Not Used | N/A |
| Proxy/Oracle Server | ❌ Not Used | N/A |
| Primary Network Protocol | ✅ Active | WebSocket (ws:// / wss://) |

## Recommendations

If you're looking to:
- **Remove Facebook authentication**: Modify `login.html` to remove FB SDK integration
- **Implement UDP**: Would require significant refactoring of the Protocol class and server infrastructure
- **Add proxy support**: Would need to implement a proxy layer between client and game server

---

*Report Generated: 2026-01-29*
