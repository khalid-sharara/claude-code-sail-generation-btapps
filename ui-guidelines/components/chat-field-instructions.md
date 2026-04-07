# SAIL AI Chat Component Instructions

## Overview

Appian provides two core functions for building AI-powered chat interfaces:
- `a!chatField()` — The UI component that renders an interactive chat interface
- `a!callLanguageModel()` — Calls Appian's built-in language model
- `a!chatMessage()` — Constructs individual message objects for the chat

These components work together to create conversational AI experiences. The chat field handles UX (auto-scrolling, Enter-to-send, message display), while you control the conversation logic, history, and AI model configuration.

---

## a!chatField() Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `title` | Text or Component | Content above the chat. Plain text or components (e.g., sideBySideLayout with avatar). |
| `helpTooltip` | Text | Tooltip on help icon (only shows with plain text title). |
| `messages` | List of a!chatMessage | Messages displayed in the chat. Each must be an `a!chatMessage()`. |
| `height` | Text | `"EXTRA_SHORT"`, `"SHORT"`, `"SHORT_PLUS"`, `"MEDIUM"`, `"MEDIUM_PLUS"`, `"TALL"`, `"TALL_PLUS"`, `"EXTRA_TALL"`, `"AUTO"` (default). |
| `shape` | Text | `"SQUARED"` (default), `"SEMI_ROUNDED"`, `"ROUNDED"`. |
| `saveInto` | List of Save | Updated when user sends a message. `save!value` contains the user's text. |
| `showWhen` | Boolean | Whether component is displayed. Default: true. |
| `placeholder` | Text | Placeholder text in the input field. |
| `showBorder` | Boolean | Whether component has outer border. Default: false. |
| `streamingUuid` | Text | UUID from `a!callLanguageModel()` for streaming mode. |
| `streamingSaveInto` | List of Save | Updated with partial streaming responses. `save!value` is a map with `.done` and `.text`. |
| `backgroundColor` | Text | `"WHITE"` (default), `"STANDARD"`, `"TRANSPARENT"`, `"CHARCOAL_SCHEME"`, `"NAVY_SCHEME"`, `"PLUM_SCHEME"`, or hex color. |

---

## a!chatMessage() Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `role` | Text | `"USER"` or `"ASSISTANT"`. Required. |
| `messageContent` | Any | Plain text (default styling) or any Appian component for custom rendering. |

---

## a!callLanguageModel() Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `prompt` | Text | System prompt / instructions passed to the language model. |
| `messages` | List of Map | Conversation history. Each map must have `messageContent` (Text) and `role` (Text: `"USER"` or `"ASSISTANT"`). |
| `isStreaming` | Boolean | When true, returns a UUID for streaming. Default: false. |

### Return Values
- **Non-streaming (default):** Returns the model's complete response as Text.
- **Streaming:** Returns a UUID (Text) to link with `a!chatField(streamingUuid: ...)`.

---

## ⚠️ CRITICAL: save!value Scoping Rules

`save!value` is ONLY available inside `a!save()` expressions. It is NOT a general-purpose variable.

### ❌ WRONG — save!value outside a!save():
```sail
saveInto: {
  if(save!value = "hello", /* ERROR: save!value not in scope here */
    a!save(local!x, "matched"),
    {}
  )
}
```

### ❌ WRONG — save!value in streamingSaveInto if() block:
```sail
streamingSaveInto: {
  if(
    save!value.done, /* ERROR: save!value not available here */
    a!save(local!messages, append(local!messages, save!value.text)),
    {}
  )
}
```

### ✅ CORRECT — save!value inside a!save():
```sail
saveInto: {
  a!save(local!userMessage, save!value), /* save!value valid here */
  a!save(local!messages, append(local!messages, save!value)) /* valid here too */
}
```

### ✅ CORRECT — streamingSaveInto with a!localVariables capture:
```sail
streamingSaveInto: a!localVariables(
  local!event,
  {
    a!save(local!event, save!value), /* Capture into local */
    /* Now use local!event (not save!value) for branching */
    a!save(
      local!messages,
      if(local!event.done, append(local!messages, a!map(role: "ASSISTANT", messageContent: local!event.text)), local!messages)
    ),
    a!save(
      local!streamedResponse,
      if(local!event.done, a!map(done: true(), text: ""), local!event)
    )
  }
)
```

---

## Pattern 1: Non-Streaming Chat (Recommended for Simplicity)

This is the most reliable pattern. The LLM response appears all at once after processing.

```sail
a!localVariables(
  local!chatMessages_list: {
    a!map(role: "ASSISTANT", messageContent: "Hello! How can I help you today?")
  },

  a!chatField(
    title: "AI Assistant",
    height: "TALL",
    shape: "SEMI_ROUNDED",
    showBorder: true,
    placeholder: "Type your message...",

    messages: a!forEach(
      items: local!chatMessages_list,
      expression: a!chatMessage(
        role: fv!item.role,
        messageContent: fv!item.messageContent
      )
    ),

    saveInto: a!localVariables(
      local!modelResponse_txt,
      {
        /* 1. Append user message */
        a!save(
          local!chatMessages_list,
          append(local!chatMessages_list, a!map(role: "USER", messageContent: save!value))
        ),

        /* 2. Call LLM with full history */
        a!save(
          local!modelResponse_txt,
          a!callLanguageModel(
            prompt: "You are a helpful assistant.",
            messages: local!chatMessages_list
          )
        ),

        /* 3. Append AI response */
        a!save(
          local!chatMessages_list,
          append(local!chatMessages_list, a!map(role: "ASSISTANT", messageContent: local!modelResponse_txt))
        )
      }
    )
  )
)
```

### Key Points:
- `a!localVariables` inside `saveInto` scopes `local!modelResponse_txt` to the save chain
- SAIL evaluates `a!save()` calls sequentially — step 2 sees the updated `local!chatMessages_list` from step 1
- `a!callLanguageModel()` receives the full conversation history via `messages`, giving the LLM context
- The `prompt` parameter acts as a system prompt / instruction set
- Seed `local!chatMessages_list` with an initial ASSISTANT message so the chat is never blank

---

## Pattern 2: Streaming Chat (Real-Time Token Display)

Responses appear progressively as the LLM generates them. More complex but better UX.

```sail
a!localVariables(
  local!messages_list: {
    a!map(role: "ASSISTANT", messageContent: "Hello! How can I help?")
  },
  local!streamingUuid_txt,
  local!streamedResponse_map: a!map(done: true(), text: ""),

  a!chatField(
    title: "AI Assistant",
    height: "TALL",
    shape: "SEMI_ROUNDED",
    showBorder: true,
    placeholder: "Type your message...",

    /* Show history + any partial streaming response */
    messages: if(
      a!isNotNullOrEmpty(local!streamedResponse_map.text),
      append(
        a!forEach(
          items: local!messages_list,
          expression: a!chatMessage(role: fv!item.role, messageContent: fv!item.messageContent)
        ),
        a!chatMessage(role: "ASSISTANT", messageContent: local!streamedResponse_map.text)
      ),
      a!forEach(
        items: local!messages_list,
        expression: a!chatMessage(role: fv!item.role, messageContent: fv!item.messageContent)
      )
    ),

    saveInto: {
      a!save(
        local!messages_list,
        append(local!messages_list, a!map(role: "USER", messageContent: save!value))
      ),
      a!save(
        local!streamingUuid_txt,
        a!callLanguageModel(
          prompt: "You are a helpful assistant.",
          isStreaming: true(),
          messages: local!messages_list
        )
      )
    },

    streamingUuid: local!streamingUuid_txt,

    streamingSaveInto: a!localVariables(
      local!event,
      {
        a!save(local!event, save!value),
        a!save(
          local!messages_list,
          if(
            local!event.done,
            append(local!messages_list, a!map(role: "ASSISTANT", messageContent: local!event.text)),
            local!messages_list
          )
        ),
        a!save(
          local!streamedResponse_map,
          if(local!event.done, a!map(done: true(), text: ""), local!event)
        )
      }
    )
  )
)
```

### Streaming Event Map Keys:
- `.done` (Boolean) — true when streaming is complete
- `.text` (Text) — the accumulated response text so far

### ⚠️ Streaming Caveats:
- Custom component messages do NOT support streaming — only plain text
- The streaming event map key names may vary between Appian versions (25.4 used `Content`/`isDone`/`Error`; 26.1 uses `text`/`done`)
- If streaming causes issues, fall back to non-streaming pattern

---

## Pattern 3: Using a!callLanguageModel() for Data Generation

You can use `a!callLanguageModel()` outside of chat to generate structured data (e.g., job descriptions, summaries, recommendations).

```sail
a!buttonWidget(
  label: "Generate Report",
  style: "SOLID",
  color: "ACCENT",
  loadingIndicator: true,
  saveInto: a!localVariables(
    local!rawResponse_txt,
    {
      a!save(
        local!rawResponse_txt,
        a!callLanguageModel(
          prompt: "Generate a summary report. Use these exact section headers: TITLE: ..., SUMMARY: ..., RECOMMENDATIONS: - item1, - item2",
          messages: local!contextMessages_list
        )
      ),
      /* Parse the response using search(), mid(), trim(), split() */
      a!save(local!parsedResult_map, /* parsing logic here */ )
    }
  )
)
```

### Parsing LLM Text Responses:
Since `a!callLanguageModel()` returns plain text (not JSON), use this pattern to extract structured sections:

```sail
/* Extract a section between two headers */
local!sectionText: if(
  search("HEADER_A:", local!response) > 0,
  trim(mid(
    local!response,
    search("HEADER_A:", local!response) + len("HEADER_A:"),
    if(
      search("HEADER_B:", local!response) > search("HEADER_A:", local!response),
      search("HEADER_B:", local!response) - search("HEADER_A:", local!response) - len("HEADER_A:"),
      500 /* fallback length for last section */
    )
  )),
  "default value"
)

/* Parse bullet lists from a text block */
local!bulletItems: reject(
  fn!isnull,
  a!forEach(
    items: split(local!sectionText, char(10)),
    expression: if(
      search("- ", fv!item) > 0,
      trim(mid(fv!item, search("- ", fv!item) + 2, len(fv!item))),
      null
    )
  )
)
```

---

## Message History Format

Messages stored in local variables should use `a!map()` with these keys:
- `role`: `"USER"` or `"ASSISTANT"` (uppercase)
- `messageContent`: The message text

This format is compatible with both `a!chatMessage()` for display and `a!callLanguageModel(messages:)` for context.

```sail
local!chatMessages_list: {
  a!map(role: "ASSISTANT", messageContent: "Welcome! How can I help?"),
  a!map(role: "USER", messageContent: "I need help with a report."),
  a!map(role: "ASSISTANT", messageContent: "Sure! What kind of report?")
}
```

---

## Common Mistakes

### ❌ Empty initial messages
```sail
/* Chat starts blank — bad UX */
local!messages: {}
```
### ✅ Seed with greeting
```sail
local!messages: {
  a!map(role: "ASSISTANT", messageContent: "Hello! How can I help you today?")
}
```

### ❌ Using plain strings instead of a!chatMessage
```sail
/* 25.4 allowed plain strings; 26.1+ requires a!chatMessage */
messages: local!messages
```
### ✅ Convert to a!chatMessage objects
```sail
messages: a!forEach(
  items: local!messages,
  expression: a!chatMessage(role: fv!item.role, messageContent: fv!item.messageContent)
)
```

### ❌ Forgetting to pass history to LLM
```sail
/* LLM has no context of prior conversation */
a!callLanguageModel(prompt: "Answer: " & save!value)
```
### ✅ Pass full message history
```sail
a!callLanguageModel(
  prompt: "You are a helpful assistant.",
  messages: local!chatMessages_list
)
```

---

## Validation Checklist

- [ ] `messages` parameter uses `a!chatMessage()` objects (not plain strings or maps)
- [ ] Message history stored as `a!map(role: "...", messageContent: "...")` 
- [ ] `role` values are uppercase: `"USER"` and `"ASSISTANT"`
- [ ] `save!value` only used inside `a!save()` expressions
- [ ] Initial messages list seeded with at least one ASSISTANT greeting
- [ ] `a!callLanguageModel()` receives `messages` parameter with full conversation history
- [ ] `prompt` parameter contains system instructions (not user message)
- [ ] If using streaming: `streamingUuid` and `streamingSaveInto` both configured
- [ ] If using streaming: `streamingSaveInto` uses `a!localVariables` to capture `save!value`
- [ ] `height` is a valid enum value (not a pixel value or custom string)
- [ ] `shape` is `"SQUARED"`, `"SEMI_ROUNDED"`, or `"ROUNDED"`
