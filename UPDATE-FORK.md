# LM Studio Support — Fork Updates

All changes to add **LM Studio** as an LLM provider in the AI Settings panel.

## Files Changed

### `gitnexus-web/src/config/ui-constants.ts`
Added default LM Studio base URL constant:
```ts
export const DEFAULT_LM_STUDIO_BASE_URL = 'http://localhost:1234';
```

### `gitnexus-web/src/core/llm/types.ts`
- Added `'lm-studio'` to `LLMProvider` union type
- Added `LMStudioConfig` interface (`baseUrl?`, `model`)
- Added `LMStudioConfig` to `ProviderConfig` union
- Added `lmStudio` field to `LLMSettings`
- Added defaults in `DEFAULT_LLM_SETTINGS`

### `gitnexus-web/src/core/llm/settings-service.ts`
- Added `LMStudioConfig` import
- Added `lmStudio` to `mergeWithDefaults`
- Added `'lm-studio'` branch in `updateProviderSettings` (type + switch)
- Added `'lm-studio'` builder in `providerBuilders` (no API key required, like Ollama)
- Added display name: `'LM Studio (Local)'` in `getProviderDisplayName`
- Added empty model list in `getAvailableModels` (user types model name manually)

### `gitnexus-web/src/core/llm/agent.ts`
- Added `LMStudioConfig` import + `DEFAULT_LM_STUDIO_BASE_URL` import
- Added `'lm-studio'` case in `createChatModel`:
  - Uses `ChatOpenAI` (OpenAI-compatible)
  - Passes `apiKey: 'lm-studio'` dummy key to satisfy LangChain validation
  - In dev mode: uses Vite proxy (`/lm-studio-proxy/v1`) to avoid CORS issues
  - In production: uses direct LM Studio URL

### `gitnexus-web/src/components/SettingsPanel.tsx`
- Added `DEFAULT_LM_STUDIO_BASE_URL` import
- Added `checkLMStudioStatus` function (uses `no-cors` mode to avoid CORS preflight)
- Added LM Studio connection state (`lmStudioError`, `isCheckingLmStudio`)
- Added `checkLmStudioConnection` callback + auto-check effect
- Added `'lm-studio'` to the providers array (shown as 10th provider)
- Added LM Studio emoji (`🤗`) in provider button
- Added full LM Studio settings section with:
  - Quick-start instructions box (download from lmstudio.ai)
  - Base URL input (default `http://localhost:1234`) with connection refresh button
  - Model name input (user types the loaded model name)
  - Connection error display

### `gitnexus-web/vite.config.ts`
Added dev server proxy for LM Studio (avoids CORS):
```ts
proxy: {
  '/lm-studio-proxy': {
    target: 'http://localhost:1234',
    changeOrigin: true,
    rewrite: (path) => path.replace(/^\/lm-studio-proxy/, ''),
  },
}
```

### `gitnexus-web/vitest.config.ts`
Added missing `gitnexus-shared` alias so tests can resolve the workspace dependency.

### `gitnexus-web/src/locales/en/settings.json`
Added LM Studio locale strings:
- `quickStart`, `installFrom`, `thenRun`, `runCommand`
- `modelPlaceholder`, `modelHint`

### `gitnexus-web/src/locales/zh-CN/settings.json`
Added Chinese translations for all LM Studio UI strings.

# Simple run
### Run backend:
cd gitnexus
npm run serve

### Run frontend:
cd gitnexus-web
npm run build (if first time maybe need to build gitnexus-shared too)
npm run dev 