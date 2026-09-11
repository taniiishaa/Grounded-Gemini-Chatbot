# 🔎 Grounded Gemini Chat

> **A conversational AI that doesn't have to rely on memory alone.**

Most chatbots can generate fluent answers.
The real challenge is answering questions that depend on **what is happening right now**.

**Grounded Gemini Chat** explores that idea by connecting Google's Gemini model with **Google Search grounding**, allowing the chatbot to look up relevant web information while maintaining a continuous conversation.

Instead of treating every prompt as an isolated question, the system combines:

**Conversation context + Generative AI + Web Search**

---

## 💭 Why Grounding?

Imagine asking a normal LLM:

```text
"What happened in the latest football match?"
```

The model may know historical information, but it cannot magically know events that happened after its knowledge cutoff.

With web grounding, the flow becomes:

```text
User Question
      │
      ▼
Gemini
      │
      │  Needs current information?
      ▼
Google Search
      │
      ▼
Relevant Web Results
      │
      ▼
Gemini interprets the information
      │
      ▼
Grounded Response
```

The goal of this project is simple:

> **Let the model generate the answer, but give it access to information from the web when needed.**

---

## 🧠 The Interesting Part

This isn't just a chatbot calling an LLM API.

The project combines two different capabilities:

| Capability            | Role                                        |
| --------------------- | ------------------------------------------- |
| 🤖 Gemini 2.5 Flash   | Understands prompts and generates responses |
| 🌐 Google Search      | Provides up-to-date web information         |
| 💬 Chat Session       | Maintains conversational context            |
| 🔗 Grounding Metadata | Exposes the searches used for a response    |

That combination turns a basic question-answering program into a small experiment in **grounded generative AI**.

---

## 🔄 How a Conversation Works

The application creates a Gemini chat session and attaches Google's search tool to the model configuration.

Once the user enters a question:

```text
┌──────────────────────────┐
│          USER            │
│ "What's happening today?"│
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│       GEMINI 2.5 FLASH   │
│      Understand prompt   │
└────────────┬─────────────┘
             │
       ┌─────┴─────┐
       │           │
       ▼           ▼
   No Search    Need Search
       │           │
       │           ▼
       │    ┌──────────────┐
       │    │ Google Search│
       │    └──────┬───────┘
       │           │
       │           ▼
       │    Web Information
       │           │
       └─────┬─────┘
             ▼
      Generated Response
             │
             ▼
        User sees answer
```

The model decides when web grounding is useful through the configured Google Search tool.

---

## 💬 It Remembers the Conversation

A useful chatbot shouldn't force the user to repeat everything.

The project uses a Gemini **chat session**, so messages are sent through the same conversational context.

For example:

```text
You: Who won the latest Grand Prix?

Gemini: ...

You: Where was it held?

Gemini: ...
```

The second question can be interpreted in relation to the first instead of being treated as a completely unrelated prompt.

This makes the interaction feel much closer to an actual conversation.

---

## 🔍 Grounding Visibility

One detail I particularly wanted to explore was:

**What did the model actually search for?**

When grounding metadata is available, the application extracts the search queries used by Gemini and displays them alongside the response.

Conceptually:

```text
Gemini (Grounded):
[Generated response...]

Used Google Search with queries:
["latest Grand Prix winner", ...]
```

This provides a small window into the model's grounding process instead of hiding the search step completely.

---

## 🏗️ Under the Hood

The implementation is intentionally small.

There is no large framework stack hiding the core idea.

```text
Python
  │
  ├── google-genai
  │       │
  │       ├── Gemini 2.5 Flash
  │       │
  │       └── Google Search Tool
  │
  ├── Chat Session
  │
  └── Terminal Interface
```

### Core flow

```python
client
  ↓
Gemini model
  ↓
Google Search tool configured
  ↓
Chat session created
  ↓
User prompt
  ↓
send_message()
  ↓
Response + grounding metadata
```

The project therefore stays focused on understanding **how grounded generation works**, rather than adding unnecessary application complexity.

---

## 📁 Project Anatomy

```text
grounded-gemini-chat/
│
├── chatbot.py
├── requirements.txt
└── README.md
```

### `chatbot.py`

Contains the complete chatbot implementation:

* Gemini client initialization
* Google Search tool configuration
* Chat session creation
* Interactive prompt loop
* Response generation
* Grounding metadata inspection
* Search-query display
* Error handling

### `requirements.txt`

Contains the Python packages required by the project.

---

## ⚙️ Getting It Running

### 1. Clone the repository

```bash
git clone https://github.com/taniiishaa/grounded-gemini-chat.git
cd grounded-gemini-chat
```

### 2. Create a virtual environment

```bash
python -m venv venv
```

Activate it on Windows:

```bash
venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure your Gemini API access

The Google GenAI client reads the required API configuration from the environment.

For example:

```text
GEMINI_API_KEY=your_api_key
```

Keep API keys out of the repository.

### 5. Start chatting

```bash
python chatbot.py
```

You'll see:

```text
--- Gemini Chatbot Initialized (with Google Search) ---
Start chatting. Ask a current events question.
```

Then simply start asking questions.

To stop:

```text
quit
```

or

```text
exit
```

---

## 🧪 Try Questions Like These

The most interesting prompts are questions where **fresh information matters**.

```text
What are the latest developments in AI?

What happened in today's major news?

Who won the latest Formula 1 race?

What are the newest features announced by Google?

What's happening in the technology industry today?
```

You can also continue the conversation naturally with follow-up questions.

---

## 🛠️ Technology Used

**Language**

`Python`

**AI Model**

`Gemini 2.5 Flash`

**AI SDK**

`Google GenAI SDK`

**Grounding**

`Google Search`

**Interface**

`Terminal / CLI`

**Environment**

`Python virtual environment + environment variables`

---

## 🔐 A Small but Important Detail

API credentials should **never** be hard-coded into source files.

Instead of:

```python
api_key = "MY_SECRET_KEY"
```

use environment-based configuration.

This keeps credentials outside the source code and makes the project safer to share publicly on GitHub.

---

## 📌 What This Project Taught Me

Building this project helped me understand several concepts that are easy to overlook when working with LLMs:

* How to initialize and interact with the Google GenAI SDK
* How tool-enabled generation works
* What **grounding** means in a generative AI system
* How Google Search can provide external context to an LLM
* How multi-turn chat sessions preserve conversation context
* How grounding metadata can be inspected
* Why current-information questions require more than model knowledge
* How environment variables should be used for API credentials

---

## 🚀 Where This Could Go Next

This project is intentionally lightweight, but the architecture opens the door to a much larger system.

```text
Current
   │
   ▼
Gemini + Google Search
   │
   ├── Web citations
   ├── Streamlit UI
   ├── Conversation history
   ├── Source previews
   ├── Document upload
   ├── RAG pipeline
   └── Agentic tool calling
```

A future version could evolve from a terminal chatbot into a complete **grounded AI research assistant** capable of searching, comparing sources, processing documents, and presenting evidence alongside its answers.

---

## 🧩 The Bigger Idea

The most important takeaway from this project isn't the chatbot itself.

It's the idea of **grounding**.

Generative AI is powerful at reasoning and language generation, but useful AI systems often need access to information outside the model.

That leads to a broader architecture:

```text
        ┌─────────────┐
        │     User    │
        └──────┬──────┘
               │
               ▼
        ┌─────────────┐
        │     LLM     │
        └──────┬──────┘
               │
        ┌──────┴──────┐
        │             │
        ▼             ▼
   Model Knowledge   Tools
                      │
                      ▼
                External Data
                      │
                      ▼
              Grounded Answer
```

This project is my small exploration of that architecture — moving from **“an LLM that generates”** toward **“an AI system that can retrieve and generate.”**

---

<p align="center">
  Built while exploring <b>Generative AI, LLM tools, grounding and conversational systems.</b>
</p>
