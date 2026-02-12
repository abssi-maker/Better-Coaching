# Technical Documentation: BetterCoaching

## Overview
BetterCoaching is a high-performance coaching platform built with a terminal-inspired interface. It leverages advanced AI orchestration and a service-oriented architecture to provide a seamless, low-friction user experience.

---

## 1. Tech Stack
The application is built using a modern, reactive mobile stack:

- **Core**: React Native (0.81.5) with Expo (SDK 54).
- **Language**: TypeScript for type-safe business logic.
- **Styling**: NativeWind (Tailwind CSS for React Native) v4.
- **Animations**: React Native Reanimated for smooth, high-frame-rate interactions.
- **State Management**: Zustand for lightweight, centralized global state.
- **AI Engine**: Google Generative AI (@google/generative-ai) integrating **Gemini 2.5**.
- **Monetization**: RevenueCat (react-native-purchases) for cross-platform subscription management.

---

## 2. Architecture

### Service-Oriented Logic
The application logic is decoupled from the UI, residing in `src/services/`:
- **`GeminiService`**: Orchestrates AI interactions, manages conversation history, and handles dynamic action parsing.
- **`RevenueCatService`**: Manages entitlements, offerings, and purchase flows.
- **`StorageService`**: Handles persistence using `@react-native-async-storage/async-storage`.

### State Management
We use a centralized `useAppStore` (Zustand) to manage:
- Active coach and conversation thread.
- User context and goals.
- Onboarding status and premium entitlement state.

### High-Agency UI Components
BetterCoaching uses an "Inline Action" system. The AI can inject specialized UI components directly into the chat stream via custom tags:
- `[TIMER:min]`: Spawns an `InlineTimer`.
- `[TASK_LIST:t1|t2]`: Spawns an `InlineTaskList`.
- `[BREATHING:in,hold,out]`: Spawns a `BreathingAnimation` with specific rhythms.

---

## 3. RevenueCat Implementation
The monetization strategy is built around a secure, server-side verified entitlement system.

### Configuration
RevenueCat is initialized in `App.tsx` during the app startup sequence.
```typescript
await revenueCatService.initialize();
```

### Key Logic
- **Entitlement Check**: We check for active entitlements to gate premium coaches (e.g., Mindset, Learn) and advanced features.
- **Offering Fetching**: Standard offerings are fetched dynamically from the RevenueCat dashboard.
- **Purchase Flow**: The `revenuecat.ts` service handles the complexity of platform-specific purchasing and error handling (including legacy `ITEM_UNAVAILABLE` workarounds).

---

## 4. AI Orchestration (Gemini 2.5)
The AI doesn't just "chat"; it operates the UI.

### Prompt Engineering
Each coach has a unique `systemPrompt` that defines its personality and limits. We inject the `UserContext` into every request to ensure high relevance.

### Action Parsing
The `GeminiService` uses regular expressions to scan AI responses for functional tokens. These tokens are stripped from the text and converted into structured action objects, which are then rendered as interactive components by the `ChatScreen`.

---

## 5. Development & Deployment
- **Bundler**: Metro with custom config for NativeWind.
- **Build System**: EAS (Expo Application Services) for internal and production builds.
- **Environment**: Managed through `.env` files with Expo Public prefixing for client-side accessibility.
