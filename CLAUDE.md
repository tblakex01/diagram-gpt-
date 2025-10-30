# Diagram GPT - Comprehensive Codebase Analysis

## Project Overview

**Diagram GPT** is a web-based application that uses artificial intelligence (OpenAI's GPT models) to generate various types of diagrams from natural language descriptions. Users describe what diagram they want in plain English, and the application generates the corresponding Mermaid diagram code and renders it in real-time.

**Purpose**: Convert natural language descriptions into visual diagrams (flowcharts, sequence diagrams, class diagrams, user journeys, gantt charts, C4C diagrams) using OpenAI's GPT API and Mermaid.js rendering engine.

**Repository**: https://github.com/fraserxu/diagram-gpt

---

## Technology Stack

### Core Framework
- **Next.js 13.2.4** - Full-stack React framework with built-in API routes and experimental App Directory
- **React 18.2.0** - UI library
- **TypeScript 5.0.3** - Type-safe JavaScript

### Frontend Libraries
- **Mermaid 10.0.2** - Diagram rendering engine
- **Tailwind CSS 3.3.1** - Utility-first CSS framework
- **Radix UI** - Unstyled, accessible component primitives
  - `@radix-ui/react-select`: Dropdown selection
  - `@radix-ui/react-popover`: Popover dialogs
  - `@radix-ui/react-hover-card`: Hover tooltips
  - `@radix-ui/react-label`: Form labels
- **Shadcn UI** - Pre-built Radix UI component library (integrated in `/components/ui`)
- **Lucide React 0.130.1** - Icon library
- **Jotai 2.0.3** - Primitive and flexible state management (atom-based)

### Backend/API Integration
- **OpenAI 3.2.1** - Official OpenAI Node.js SDK for GPT access
- **eventsource-parser 1.0.0** - SSE (Server-Sent Events) parsing for streaming responses

### Utilities
- **Pako 2.1.0** - Zlib compression for URL serialization
- **js-base64 3.7.5** - Base64 encoding/decoding for URL sharing
- **endent 2.1.0** - Template literal formatting
- **class-variance-authority 0.5.1** - Component variant utility
- **clsx 1.2.1** - Classname utility
- **tailwind-merge 1.11.0** - Merge Tailwind CSS classnames intelligently
- **buffer 6.0.3** - Node.js Buffer polyfill for browser

### Development Tools
- **ESLint 8.37.0** with Next.js config
- **PostCSS 8.4.21** with Autoprefixer
- **Vercel Analytics** - Performance monitoring

---

## Project Structure

```
diagram-gpt/
├── app/                          # Next.js 13+ App Directory
│   ├── layout.tsx               # Root layout component
│   ├── page.tsx                 # Main page (home route)
│   ├── globals.css              # Global Tailwind styles
│   ├── favicon.ico              # Site favicon
│   ├── opengraph-image.png      # Social media preview image
│   └── api/
│       └── openai/
│           └── route.ts         # OpenAI streaming API endpoint
│
├── pages/                        # Next.js Pages Router (legacy)
│   └── api/
│       └── chat.ts              # Alternative chat API endpoint
│
├── components/                   # React components
│   ├── ui/                      # Shadcn UI components (Radix-based)
│   │   ├── button.tsx
│   │   ├── input.tsx
│   │   ├── textarea.tsx
│   │   ├── label.tsx
│   │   ├── select.tsx
│   │   ├── popover.tsx
│   │   └── hover-card.tsx
│   │
│   ├── Mermaids.tsx             # Diagram renderer & theme selector
│   ├── ChatInput.tsx            # Input textarea for diagram descriptions
│   ├── ChatMessage.tsx          # Display user messages
│   ├── CodeBlock.tsx            # Display Mermaid code with copy/edit
│   ├── APIKeyInput.tsx          # OpenAI API key configuration
│   ├── SiteHeader.tsx           # Header navigation
│   ├── MainNav.tsx              # Navigation menu
│   └── Icons.tsx                # SVG icon definitions
│
├── lib/                          # Utility functions
│   ├── utils.ts                 # Core utilities:
│   │                              - cn() - Tailwind classname merger
│   │                              - OpenAIStream() - Streaming API handler
│   │                              - parseCodeFromMessage() - Code extraction
│   │                              - serializeCode() - URL serialization
│   │
│   └── atom.ts                  # Jotai atoms for global state
│                                  - apiKeyAtom
│                                  - modelAtom
│
├── types/                        # TypeScript type definitions
│   ├── type.ts                  # Core types:
│   │                              - OpenAIModel
│   │                              - Message
│   │                              - RequestBody
│   │                              - Theme
│   │
│   └── nav.ts                   # Navigation type
│
├── config/                       # Configuration
│   └── site.ts                  # Site metadata and social links
│
├── public/                       # Static assets
│   ├── next.svg
│   ├── thirteen.svg
│   └── vercel.svg
│
├── .vscode/                      # VS Code settings
├── Configuration Files
│   ├── package.json             # Dependencies and scripts
│   ├── tsconfig.json            # TypeScript configuration
│   ├── next.config.js           # Next.js configuration
│   ├── tailwind.config.js       # Tailwind CSS configuration
│   ├── postcss.config.js        # PostCSS configuration
│   ├── .eslintrc.json           # ESLint rules
│   ├── .gitignore               # Git ignore patterns
│   └── README.md                # Project README
```

---

## Key Features & Functionality

### 1. Chat-Based Diagram Generation
- User types natural language description of desired diagram
- Application streams OpenAI GPT response in real-time
- Automatic Mermaid code extraction and rendering

### 2. AI Model Selection
- **gpt-4**: Higher quality, slower
- **gpt-3.5-turbo**: Faster, lower cost
- Selection stored in localStorage for persistence

### 3. Diagram Theme Support
Available themes for Mermaid diagrams:
- `default` - Standard Mermaid theme
- `neutral` - Minimal styling
- `dark` - Dark mode
- `forest` - Green-tinted theme
- `base` - Basic styling

Theme preference persists in localStorage

### 4. Code Management
- **Copy SVG**: Extract and copy rendered diagram as SVG
- **Copy Code**: Copy Mermaid syntax to clipboard
- **Edit on Mermaid Live**: Generate shareable link with compressed diagram state

### 5. API Key Management
- API key input via secure password field
- Stored in browser localStorage
- Never sent to backend (passed directly in request)
- Can be changed anytime via header popover

---

## API Endpoints

### POST `/api/chat`
**Location**: `/pages/api/chat.ts` (Pages Router)

**Purpose**: Handle chat messages and stream OpenAI responses

**Request Body**:
```typescript
{
  messages: Message[],  // Array of chat messages
  model: "gpt-3.5-turbo" | "gpt-4",
  apiKey: string  // OpenAI API key
}
```

**Request Message Structure**:
```typescript
interface Message {
  role: "system" | "user" | "assistant",
  content: string
}
```

**Response**: Server-Sent Events (SSE) stream containing:
- Streamed text chunks from OpenAI's chat completion API
- Each chunk appended to build complete response
- Ends with `[DONE]` message when complete

**Runtime**: Edge Runtime (Vercel Edge Functions)

---

### POST `/app/api/openai/route.ts`
**Location**: `/app/api/openai/route.ts` (App Router)

**Purpose**: Same as `/api/chat` - redundant endpoints for backward compatibility

**Note**: Both endpoints use identical implementation via shared `OpenAIStream()` utility

---

## Frontend Components Architecture

### State Management (Jotai)
```typescript
// Atoms for global state
apiKeyAtom: Atom<string>      // User's OpenAI API key
modelAtom: Atom<OpenAIModel>  // Selected GPT model
```

### Main Page Flow (`app/page.tsx`)
1. **Initialization**: Load apiKey and model from localStorage on mount
2. **UI Layout**: Two-column split view
   - Left: Chat messages and input
   - Right: Code block and rendered diagram
3. **Message Handling**:
   - User input → create Message object
   - POST to `/api/chat` with streaming
   - Accumulate chunks in `draftOutputCode` state
   - Parse code and update `outputCode` for rendering
4. **Real-time Display**:
   - `draftOutputCode`: Raw streamed response (updates in real-time)
   - `outputCode`: Parsed Mermaid code (once complete)

### Key Components

#### `Mermaids.tsx` - Diagram Renderer
- Renders Mermaid diagrams with specified theme
- Theme selector dropdown
- Copy SVG button (extracts `<svg>` element)
- Uses `mermaid.run()` for client-side rendering
- Handles theme changes with chart re-initialization
- Theme persisted to localStorage

#### `ChatInput.tsx` - Input Field
- Multi-line textarea for diagram descriptions
- Enter key (without Shift) submits form
- Shift+Enter for newlines
- Placeholder: "Describe the diagram in nature language."

#### `CodeBlock.tsx` - Code Display
- Syntax-highlighted code block (black background)
- Copy button with feedback ("Copied!" tooltip)
- Edit link to Mermaid Live (serialized state in URL)
- Help tooltip linking to Mermaid documentation
- Language badge showing "mermaid"

#### `APIKeyInput.tsx` - Configuration
- Password input for OpenAI API key
- Model selector dropdown (gpt-4 | gpt-3.5-turbo)
- Save button to persist to localStorage

#### `SiteHeader.tsx` - Navigation
- Logo and app name
- GitHub link
- Twitter link
- API key input button (popover)

---

## Data Flow & Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    User Browser                          │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ChatInput (user types description)                     │
│         ↓                                               │
│  handleSubmit() creates Message object                │
│         ↓                                               │
│  fetch('/api/chat') with streaming                    │
│         │                                               │
│         ├─ Post: { messages, model, apiKey }          │
│         │                                               │
│         └─ Response: ReadableStream                    │
│              ↓                                          │
│         Reader.read() loops through chunks            │
│              ↓                                          │
│         setDraftOutputCode() - real-time updates      │
│              ↓                                          │
│         parseCodeFromMessage() - extract code         │
│              ↓                                          │
│         setOutputCode() - update rendered diagram     │
│              ↓                                          │
│  Mermaids component renders diagram                   │
│              ↓                                          │
│  Display with theme selector & copy buttons          │
│                                                         │
└─────────────────────────────────────────────────────────┘
                          ↕
┌─────────────────────────────────────────────────────────┐
│                   Vercel Edge Runtime                    │
├─────────────────────────────────────────────────────────┤
│  POST /api/chat                                         │
│       ↓                                                 │
│  OpenAIStream(messages, model, apiKey)                │
│       ↓                                                 │
│  Add system prompt to messages array                  │
│       ↓                                                 │
│  POST to https://api.openai.com/v1/chat/completions  │
│       ↓                                                 │
│  Parse SSE chunks with eventsource-parser            │
│       ↓                                                 │
│  Create ReadableStream with parsed chunks            │
│       ↓                                                 │
│  Return stream to client                             │
│                                                         │
└─────────────────────────────────────────────────────────┘
                          ↕
┌─────────────────────────────────────────────────────────┐
│               OpenAI API (api.openai.com)               │
├─────────────────────────────────────────────────────────┤
│  POST /v1/chat/completions                            │
│    Request:                                            │
│    - model: gpt-4 or gpt-3.5-turbo                    │
│    - messages: [system, ...user_messages]             │
│    - stream: true                                     │
│    - temperature: 0 (deterministic)                   │
│                                                        │
│    Response: Server-Sent Events stream                │
│    - Chunks of generated Mermaid code                 │
│    - Ends with: { choices: [{ delta: {} }] }         │
│    - Final event: "[DONE]"                           │
│                                                        │
└─────────────────────────────────────────────────────────┘
```

## System Prompt

The backend uses a carefully crafted system prompt to constrain OpenAI's output:

```
You are an assistant to help user build diagram with Mermaid.
You only need to return the output Mermaid code block.
Do not include any description, do not include the ```.
Code (no ```):
```

**Purpose**: 
- Instructs AI to output ONLY Mermaid syntax
- Prevents explanatory text or markdown code blocks
- Ensures clean, parseable output

---

## Configuration Files

### `next.config.js`
```javascript
{
  experimental: {
    appDir: true,  // Enable Next.js 13 App Directory
  }
}
```

### `tsconfig.json`
- Compilation target: ES5
- Module resolution: ESNext with Node.js resolution
- Path alias: `@/*` maps to root directory
- Strict mode enabled

### `tailwind.config.js`
- Scans `./app` and `./components` for class names
- Custom container settings (1360px max width at 2xl)
- Uses Tailwind default theme

### `postcss.config.js`
- Tailwind CSS processing
- Autoprefixer for vendor prefixes

### `.eslintrc.json`
- Extends Next.js core-web-vitals configuration
- Enforces web performance and accessibility best practices

---

## Build & Deployment

### Development Server
```bash
npm run dev
# Runs on http://localhost:3000
```

### Production Build
```bash
npm run build
# Generates optimized Next.js build
```

### Production Server
```bash
npm start
# Serves optimized production build
```

### Linting
```bash
npm run lint
# Runs ESLint with Next.js rules
```

### Deployment Platform
- Built for **Vercel** (original creators of Next.js)
- `.vercel` directory support
- Edge Runtime configuration for API routes
- Vercel Analytics integrated

---

## Testing

**Current Status**: No testing setup configured

**Test Framework**: Not implemented
**Test Files**: None present

**Recommendation**: Consider adding:
- Jest for unit testing
- React Testing Library for component testing
- Playwright/Cypress for E2E testing

---

## Environment Configuration

### Runtime Environment Variables
```
OPENAI_API_KEY  # Fallback if user doesn't provide key (from process.env)
```

**Note**: Currently uses user-provided API key from frontend, not environment variable

### Client-Side Storage (localStorage)
- `apiKey` - OpenAI API key (sensitive!)
- `model` - Selected model (gpt-3.5-turbo | gpt-4)
- `theme` - Selected diagram theme

---

## Dependencies Summary

| Dependency | Version | Purpose |
|-----------|---------|---------|
| next | 13.2.4 | Web framework |
| react | 18.2.0 | UI library |
| typescript | 5.0.3 | Type safety |
| tailwindcss | 3.3.1 | Styling |
| mermaid | 10.0.2 | Diagram rendering |
| openai | 3.2.1 | GPT API client |
| jotai | 2.0.3 | State management |
| lucide-react | 0.130.1 | Icons |
| pako | 2.1.0 | Compression |
| js-base64 | 3.7.5 | Base64 encoding |
| @radix-ui/* | Various | UI primitives |
| class-variance-authority | 0.5.1 | Component variants |
| clsx | 1.2.1 | Class merging |
| tailwind-merge | 1.11.0 | Tailwind merging |
| endent | 2.1.0 | Template formatting |
| eventsource-parser | 1.0.0 | SSE parsing |

---

## Security & Considerations

### Security Practices
✓ API key stored in browser localStorage (user's responsibility)
✓ API key sent to backend only when needed
✓ No server-side key storage
✓ Mermaid rendered with `securityLevel: "loose"` (allows interactive diagrams)

### Potential Concerns
⚠ API key exposed in browser (standard SPA pattern but user must trust)
⚠ `document.execCommand` deprecated for clipboard (could use Clipboard API)
⚠ No request validation on API endpoints
⚠ No rate limiting
⚠ No authentication mechanism

### Recommendations
1. Implement backend API key proxy for production
2. Add request validation
3. Add rate limiting
4. Update clipboard implementation to use modern Clipboard API
5. Add error handling for API failures

---

## Code Quality & Standards

### TypeScript
- Strict mode enabled
- Proper type definitions for all major interfaces
- Compound component pattern in UI library

### Code Organization
- Clear separation of concerns (components, lib, types, config)
- Utility functions isolated in `/lib`
- Type definitions in `/types`
- Configuration centralized in `/config`

### Styling Approach
- Utility-first with Tailwind CSS
- Component variants with class-variance-authority
- Responsive design with md breakpoint

### Accessibility
- Radix UI components (WCAG compliant)
- Proper label associations
- Keyboard navigation support (Shift+Enter in ChatInput)

---

## Recent Changes (Commit History)

Latest commits show active development:
1. **Merge PR #2**: Edit on mermaid-live feature
2. **parse code**: Code parsing improvements
3. **install buffer**: Added buffer polyfill for browser
4. **add edit button**: Mermaid Live integration
5. **small space updates**: UI spacing adjustments

---

## Known Features & Limitations

### Supported Diagram Types (via Mermaid)
- Flowchart
- Sequence Diagram
- Class Diagram
- User Journey
- Gantt Chart
- C4 Diagram

### Current Limitations
- No conversation history persistence (messages reset on page reload)
- No cost tracking or usage limits
- No error recovery for failed API calls
- Limited to OpenAI's gpt-3.5-turbo and gpt-4 models
- Single-session use (no multi-user support)
- No export formats beyond SVG/code

---

## Getting Started for Development

### Prerequisites
- Node.js 16+
- npm or yarn
- OpenAI API key

### Installation
```bash
git clone https://github.com/fraserxu/diagram-gpt.git
cd diagram-gpt
npm install
```

### Configuration
1. No environment variables required for development
2. Add your OpenAI API key via UI when running

### Running
```bash
npm run dev
# Open http://localhost:3000
```

### Building
```bash
npm run build
npm start
```

---

## File Size & Performance

### Key Metrics
- Small footprint with focused dependencies
- Next.js 13 provides automatic code splitting
- Mermaid.js is client-side (no server rendering)
- Streaming responses enable progressive rendering
- Edge Runtime for minimal latency

### Optimization Opportunities
1. Lazy load Mermaid.js
2. Implement diagram caching
3. Add response compression
4. Optimize image sizes (opengraph-image.png)

---

## Contributing Guidelines

Based on git history, the project accepts:
- Feature additions (edit button, themes)
- Bug fixes (code parsing, spacing)
- Dependencies (buffer module)

---

## License

Not explicitly stated in codebase. Check repository for LICENSE file.

---

## Related Resources

### External Links Integrated
- **Mermaid Documentation**: https://mermaid.js.org/
- **Mermaid Live Editor**: https://mermaid.live/
- **Next.js Docs**: https://nextjs.org/
- **Tailwind CSS**: https://tailwindcss.com/
- **Shadcn UI**: https://ui.shadcn.com/
- **OpenAI API**: https://platform.openai.com/

---

## Conclusion

Diagram GPT is a well-structured, modern web application demonstrating:
- Proper React/Next.js patterns
- Clean component architecture
- TypeScript best practices
- Effective state management with Jotai
- Responsive UI with Tailwind CSS and Radix UI
- Streaming API integration with OpenAI

The codebase is maintainable, extensible, and follows current web development standards. It's suitable for both learning modern web development and as a foundation for diagram generation applications.

