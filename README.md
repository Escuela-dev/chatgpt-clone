_draft 1_

# Step‑by‑Step Tutorial: Build a ChatGPT Clone (Next.js + TypeScript + OpenAI API)

## 0) Prerequisites
- Node.js (v20+ recommended)
- npm (or pnpm/yarn)
- A terminal
- OpenAI API key

---

## 1) Create the Next.js + TypeScript project
```bash
npx create-next-app@latest chatgpt-clone --typescript
cd chatgpt-clone
```

When prompted:
- Use **App Router** (Yes)
- ESLint (Yes)

---

## 2) Project structure overview
You will mainly use:
```
app/
  page.tsx
  api/
    chat/
      route.ts
```

---

## 3) Add environment variable
Create a file:

```
.env.local
```

Add your key:

```
OPENAI_API_KEY=sk-xxx
```

---

## 4) Build the backend API route
Create file:

```
app/api/chat/route.ts
```

Add this code:

```ts
import { NextRequest, NextResponse } from "next/server";

export async function POST(req: NextRequest) {
  const { messages } = await req.json();

  const response = await fetch("https://api.openai.com/v1/chat/completions", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      Authorization: `Bearer ${process.env.OPENAI_API_KEY}`,
    },
    body: JSON.stringify({
      model: "gpt-4o-mini",
      messages,
    }),
  });

  const data = await response.json();
  return NextResponse.json(data);
}
```

---

## 5) Build the UI (chat screen)
Edit:

```
app/page.tsx
```

Replace with:

```tsx
"use client";

import { useState } from "react";

type Message = {
  role: "user" | "assistant";
  content: string;
};

export default function Home() {
  const [input, setInput] = useState("");
  const [messages, setMessages] = useState<Message[]>([]);

  const sendMessage = async () => {
    if (!input.trim()) return;

    const newMessages = [...messages, { role: "user", content: input }];
    setMessages(newMessages);
    setInput("");

    const res = await fetch("/api/chat", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ messages: newMessages }),
    });

    const data = await res.json();
    const assistantMessage = data.choices[0].message;

    setMessages([...newMessages, assistantMessage]);
  };

  return (
    <main className="chat-container">
      <h1>ChatGPT Clone</h1>

      <div className="chat-box">
        {messages.map((m, i) => (
          <div key={i} className={`message ${m.role}`}>
            {m.content}
          </div>
        ))}
      </div>

      <div className="input-area">
        <textarea
          placeholder="Type your message..."
          value={input}
          onChange={(e) => setInput(e.target.value)}
        />
        <button onClick={sendMessage}>Send</button>
      </div>
    </main>
  );
}
```

---

## 6) Add native CSS styling
Edit:

```
app/globals.css
```

Replace with:

```css
* {
  box-sizing: border-box;
  font-family: Arial, sans-serif;
}

body {
  margin: 0;
  background: #f3f3f3;
}

.chat-container {
  max-width: 800px;
  margin: 30px auto;
  padding: 20px;
}

.chat-box {
  background: #fff;
  padding: 20px;
  height: 400px;
  overflow-y: auto;
  border-radius: 8px;
  margin-bottom: 20px;
  border: 1px solid #ddd;
}

.message {
  padding: 10px 14px;
  margin-bottom: 10px;
  border-radius: 6px;
  white-space: pre-wrap;
}

.message.user {
  background: #d1e7ff;
  text-align: right;
}

.message.assistant {
  background: #e5e5e5;
  text-align: left;
}

.input-area {
  display: flex;
  gap: 10px;
}

textarea {
  flex: 1;
  padding: 10px;
  border-radius: 6px;
  border: 1px solid #ccc;
  resize: none;
  height: 60px;
}

button {
  padding: 10px 20px;
  border: none;
  background: #0070f3;
  color: white;
  border-radius: 6px;
  cursor: pointer;
}
```

---

## 7) Run the app
```bash
npm run dev
```

Open:
```
http://localhost:3000
```

---

# ✅ Result
You now have:
- A working ChatGPT‑like UI
- API integration with OpenAI
- Typed React/TypeScript code
- Pure HTML/CSS (no UI library)

---

# Next Steps (Optional Improvements)
- Add loading state (spinner)
- Show errors when API fails
- Add “Enter to send”
- Save chat history in localStorage
- Add system prompts

---

## FUTURE dev

- A **streaming response** version  
- A **multi‑message chat history** version with persistence
