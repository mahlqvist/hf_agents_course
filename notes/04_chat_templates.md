# Chat Templates

Let's have a look at how **LLM**s structure their generations through chat templates.

Ever wondered how you can have a back-and-forth conversation with ChatGPT? The AI isn't really chatting, the model is just looking at a pile of text and predicting the next word.  

So how does it **keep track of a conversation**?  

That's where **Chat Templates** come in. They structure the conversation, making sure every message gets formatted in a way the AI understands.  

When you talk to an AI, your words don't just float into the ether. Instead, the system **packages everything up** into a structured format, ensuring that every model, despite its unique **special tokens**, receives the correctly formatted prompt.

Example:  

```python
messages = [
    {"role": "system", "content": "You are a math tutor."},
    {"role": "user", "content": "What is calculus?"},
    {"role": "assistant", "content": "Calculus is a branch of mathematics..."},
]
```

This is what’s **actually** happening behind the scenes.  

- The **system message** tells the AI how to behave (like "You are a math tutor").  
- The **user message** is what you type in, your query.  
- The **assistant message** is what the AI responds with.  

The AI doesn't "*remember*" past messages in the way humans do, it just gets fed **a single big text chunk** containing the conversation history, every time you send a message.  

### Base Models vs Instruct Models  

There are **two kinds of models**:  

**Base Models** are trained to predict the next word based on raw text, they don't naturally follow instructions.  

**Instruct Models** are fine-tuned specifically to follow instructions and engage in conversations. 

Think of it like this:  
- A **Base Model** is like a student who has read a million books but was never told *how* to answer a question.  
- An **Instruct Model** is that same student, but now they've had a tutor guiding them on how to respond properly.  

Each instruct model **expects** its prompts in a specific format, so we use **Chat Templates** to make sure the conversation is structured **exactly** the way the model expects.  

### Format Conversations

Since every model has its own quirks, we don't want to manually format messages every time. Instead, we use the model’s **tokenizer** to apply the right chat template automatically.  

Here's how you do it in Python:  

```python
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained(
    "HuggingFaceTB/SmolLM2-1.7B-Instruct"
)

rendered_prompt = tokenizer.apply_chat_template(
    messages, 
    tokenize=False, 
    add_generation_prompt=True
)
```

This `apply_chat_template()` function **formats everything** so that the AI gets exactly what it needs to generate a proper response.  

If you’re building anything with AI, whether it's a chatbot, an assistant or an API, you **need** chat templates to make sure your AI:  
- Understands the full conversation.  
- Knows what role it’s supposed to play.  
- Follows the right structure for its specific model type.  

Without chat templates, your AI might **misinterpret** messages, forget context or just output garbage. With them, it becomes a **structured**, **intelligent** conversationalist.  

So in summary:
- **Chat Templates** organize conversations so AI models can understand and respond correctly.  
- **Messages** are structured with **system**, **user** and **assistant** roles.  
- **Base models** predict text, while **Instruct models** are trained to follow commands.  
- **Each model has different formatting rules** and chat templates apply the correct one automatically.  
- **Use `apply_chat_template()`** to make sure your chatbot talks the way it should!  