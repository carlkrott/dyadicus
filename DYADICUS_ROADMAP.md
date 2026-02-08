# DyadICUS - Enhanced Dyad IDE Fork

**Repository:** https://github.com/carlkrott/dyadicus
**Upstream:** https://github.com/dyad-sh/dyad
**Created:** 2026-02-07
**Status:** 🟢 Fork Created, Planning Phase

---

## Overview

DyadICUS is our enhanced fork of Dyad, the open-source AI app builder. While Dyad focuses on building apps via chat, DyadICUS expands into a full-featured AI-assisted IDE with unique audio engineering integration.

### Why Fork?

1. **Upstream is feature-gating** - Pro features in `src/pro/` are FSL licensed (converts to Apache after 4 years)
2. **Different vision** - We want code editing + audio integration, not just app building
3. **MCP Router integration** - Connect to our unified tool routing system
4. **Audio-first features** - Voice annotations, audio code comments, TTS documentation

---

## Tech Stack (Inherited from Dyad)

| Component | Technology |
|-----------|------------|
| Framework | Electron |
| UI | React + TanStack Router + React Query |
| Editor | Monaco Editor |
| Database | SQLite (Drizzle ORM) |
| AI | AI SDK (multi-provider: Anthropic, OpenAI, Google, etc.) |
| MCP | @modelcontextprotocol/sdk (already integrated!) |
| Styling | Tailwind CSS v4 |
| Testing | Vitest (unit), Playwright (E2E) |
| Build | Vite + electron-forge |

---

## Architecture Understanding

### Electron Processes
- **Renderer (UI):** React components, chat interface, Monaco editor
- **Main (Backend):** File system access, AI calls, tool execution

### Core Files
```
src/
├── components/
│   └── chat/
│       └── DyadMarkdownParser.tsx  # Renders <dyad-*> XML tags
├── ipc/
│   └── processors/
│       └── response_processor.ts   # Executes tool actions
├── prompts/
│   └── system_prompt.ts            # LLM instructions
├── db/
│   └── schema.ts                   # Drizzle ORM schema
└── pro/
    └── main/
        └── ipc/
            └── handlers/
                └── local_agent/
                    ├── local_agent_handler.ts  # Agent loop
                    └── tool_definitions.ts     # Available tools
```

### Tool System
Dyad uses two approaches:
1. **XML-style pseudo-tools:** `<dyad-write>`, `<dyad-delete>`, etc. (legacy, but works everywhere)
2. **Formal tool calling:** Used in Pro agent mode for better model compatibility

---

## Enhancement Roadmap

### Phase 1: Foundation (Week 1) ✅
- [x] Fork repository
- [x] Set up development environment
- [x] Install dependencies (`npm install`)
- [x] Create userData directory
- [x] Run locally (`npm start`)
- [x] Verify build works
- [x] Create `dyadicus` branch for our changes → `feature/dyadicus-branding`

### Phase 2: Branding & Theme (Week 2) ✅ COMPLETE
- [x] Update app name to "DyadICUS" (package.json, productName)
- [x] Apply our design system colors (Red theme)
  - Primary: `#D62828` (Red) → oklch(0.52 0.20 27)
  - Secondary: `#2C2C34` (Dark) → oklch(0.25 0.01 280)
  - Accent: `#F77F00` (Orange) → oklch(0.72 0.18 55)
- [x] Update protocol schemes (dyad → dyadicus)
- [x] Update UI strings (13 files, 33 text changes)
- [x] Light + dark mode themes applied
- [x] TypeScript check passes
- [ ] Replace logo/icons (pending - need logo assets)
- **Branch:** `feature/dyadicus-branding`
- **Commit:** 19b3940

### Phase 3: MCP Router Integration (Week 3)
- [ ] Connect to our MCP Router backend API
- [ ] Add tool routing panel
- [ ] Show available MCP tools in sidebar
- [ ] Enable tool testing from IDE
- [ ] Link: `http://localhost:3001/api/mcp/servers`

### Phase 4: Audio Features (Week 4-5)
- [ ] **Voice Annotations**
  - Record voice notes on functions (Web Audio API)
  - Store in project metadata (SQLite)
  - Playback on hover
  - Auto-transcription (via MSI voice server or Whisper)
- [ ] **Audio Code Comments**
  - `// @audio:note-id` syntax
  - Monaco decorator to show audio icon
  - Click to play
- [ ] **TTS Documentation**
  - "Read aloud" button on comments
  - Use MSI voice server Piper TTS

### Phase 5: Enhanced Navigation (Week 6)
- [ ] Visual breadcrumb for Go to Definition
- [ ] Find References with count badges
- [ ] Fuzzy file search (already has via Monaco?)
- [ ] Quick actions panel (Cmd+K)

### Phase 6: AI Enhancements (Week 7-8)
- [ ] `/refactor` command
- [ ] `/explain` for code blocks
- [ ] Inline suggestions with Accept/Reject
- [ ] History navigation for suggestions
- [ ] Connect to our model orchestrator (when ready)

### Phase 7: Real-Time Collaboration (Future)
- [ ] WebSocket sync layer
- [ ] Multi-cursor display
- [ ] Live chat sidebar
- [ ] Conflict resolution UI

---

## Development Commands

```bash
# Install dependencies
npm install

# Create userData directory (required for SQLite)
mkdir -p userData

# Run in development mode
npm start

# Run tests
npm test

# E2E tests
npm run build && npm run e2e

# Storybook
npm run storybook
```

---

## Integration Points

### Our MCP Router
```typescript
// API connection
const MCP_ROUTER_API = 'http://localhost:3001/api/mcp';

// Get available tools
fetch(`${MCP_ROUTER_API}/servers`)
  .then(res => res.json())
  .then(data => {
    // Display tools in sidebar
  });

// Call a tool
fetch(`${MCP_ROUTER_API}/call`, {
  method: 'POST',
  body: JSON.stringify({
    server: 'gmail',
    tool: 'search_emails',
    args: { query: 'is:unread' }
  })
});
```

### Voice Server (MSI)
```typescript
// TTS endpoint
const VOICE_API = 'http://100.64.0.5:8765';

// Text to speech
fetch(`${VOICE_API}/tts`, {
  method: 'POST',
  body: JSON.stringify({ text: 'Hello world' })
});

// STT (transcription)
fetch(`${VOICE_API}/stt`, {
  method: 'POST',
  body: audioBlob
});
```

---

## Sync with Upstream

To pull changes from upstream Dyad:
```bash
git fetch upstream
git checkout main
git merge upstream/main
# Resolve conflicts, test, push
```

---

## License Considerations

- Code outside `src/pro/` is Apache 2.0 (free to modify)
- Code in `src/pro/` is FSL 1.1 (becomes Apache after 4 years)
- Our additions go in `src/dyadicus/` to keep clean separation
- All our additions are Apache 2.0

---

## Files to Create

```
src/dyadicus/
├── components/
│   ├── AudioAnnotation.tsx
│   ├── ToolRouter.tsx
│   └── VoiceRecorder.tsx
├── hooks/
│   ├── useAudioNotes.ts
│   ├── useMCPRouter.ts
│   └── useVoiceServer.ts
├── services/
│   ├── mcpRouterApi.ts
│   └── voiceApi.ts
└── types/
    └── audio.ts
```

---

## Success Metrics

1. **Build passes** on Linux (our dev machine)
2. **MCP tools accessible** from IDE sidebar
3. **Voice notes recordable** and playable on code
4. **All upstream features preserved**
5. **Clean separation** of our code in `src/dyadicus/`

---

*Last updated: 2026-02-07 23:20 GMT*
