# DeepSeek Chatbot Demo Setup Guide

`npx create-next-app@latest deepseek-chatbot-demo`

## 2. Shadcn UI Configuration

### 2.1 Initialize Shadcn

```bash
cd deepseek-chatbot-demo
npx shadcn-ui@latest init
```

### 2.2 Add Chat Components

```bash
npx shadcn-ui@latest add https://shadcn-chatbot-kit.vercel.app/r/chat.json
```

## 3. Dependency Installation

### 3.1 Core Dependencies

```bash
npm install ai @ai-sdk/deepseek
```

### 3.2 Environment Variables

Create `.env` file:

```env
DEEPSEEK_API_KEY=your_api_key_here
```

## 4. Code Modifications

### 4.1 Fix Markdown Renderer (ui/markdown-renderer.tsx)

```diff
export function MarkdownRenderer({ children }: MarkdownRendererProps) {
  return (
•   <div className="space-y-3">
      <Markdown
        remarkPlugins={[remarkGfm]}
        components={COMPONENTS}
•       className="space-y-3"
      >
        {children}
      </Markdown>
•   </div>
  )
}
```

4.2 Build Chat Demo Component (components/chat-demo.tsx)

```typescript
"use client";

import { useChat, type UseChatOptions } from "@ai-sdk/react";

import { Chat } from "@/components/ui/chat";

type ChatDemoProps = {
  initialMessages?: UseChatOptions["initialMessages"];
};

export function ChatDemo(props: ChatDemoProps) {
  const {
    messages,
    input,
    handleInputChange,
    handleSubmit,
    append,
    stop,
    isLoading,
  } = useChat(props);

  return (
    <div className="flex h-[90vh] w-full">
      <Chat
        className="grow"
        messages={messages}
        handleSubmit={handleSubmit}
        input={input}
        handleInputChange={handleInputChange}
        isGenerating={isLoading}
        stop={stop}
        append={append}
        suggestions={[
          "Generate a tasty vegan lasagna recipe for 3 people.",
          "Generate a list of 5 questions for a job interview for a software engineer.",
          "Who won the 2022 FIFA World Cup?",
        ]}
      />
    </div>
  );
}
```

4.3 Chat Interface Integration (app/page.tsx)

```tsx
import { ChatDemo } from "@/components/chat-demo";

export default function Home() {
  return (
    <main className="flex min-h-screen flex-col items-center justify-center max-w-4xl mx-auto">
      <div className="border rounded-md p-4">
        <ChatDemo />
      </div>
    </main>
  );
}
```

## 5. Backend Implementation

### 5.1 API Route (app/api/chat/route.ts)

```ts
import { deepseek } from "@ai-sdk/deepseek";
import { streamText } from "ai";

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = streamText({
    model: deepseek("deepseek-reasoner"),
    messages,
  });

  return result.toDataStreamResponse({
    sendReasoning: true,
  });
}
```

## 6. Running the Project

### 6.1 Development Mode

```bash
npm dev
```

Access: http://localhost:3000

### 6.2 Production Build

```bash
npm build && npm start
```

---

> **Repository Template**: [deepseek-chatbot-demo](https://github.com/liuyuelintop/deepseek-chatbot-demo) > **Live Demo**: https://deepseek-chatbot-demo.vercel.app
