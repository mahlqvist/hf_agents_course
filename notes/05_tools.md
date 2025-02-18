# Tools

AI Agents aren't magic, they don't actually "do" anything on their own. They sit there, crunching numbers and spitting out text. But what if we want them to act? That's where **Tools** come in.  

A **Tool** is like handing your AI a calculator, a search engine or even a robot arm. The AI itself doesn't know how to calculate 235 × 57 any better than you do without a calculator (if the AI have been trained on that kind of data it will ofc be able to give you an answer). 

If we give the AI a **tool**, a function that does multiplication, it suddenly seems much smarter.  

### Give AI a Tool  

You don't just throw a toolbox at it and hope for the best. You have to **teach it** which tools exist and how they can be used.  

- First, we describe the tool in plain language.  
- Then, we give it a function it can call (something that actually does the work).  
- We specify the input (what the tool needs) and the output (what it returns).  

Think of it like telling an intern, "*Hey, when you need to multiply numbers, use this calculator. Just type in two numbers and hit enter*".  

A simple **calculator tool**:  

```python
def calculator(a: int, b: int) -> int:
    """Multiply two integers."""
    return a * b
```

So now, instead of the AI struggling with arithmetic, it **knows** that when it sees a math problem, it should call the **calculator tool** rather than trying to guess the answer from memory.  

### How Do AI Agents Use Tools?

The key thing to remember, **LLMs can only generate text. That’s it!** They don't run code, they don't press buttons and they don't browse the web. But they can **write the right text** that tells someone (or some system) to use a tool.  

Imagine we give an AI a **weather tool** that fetches real-time weather data. If we ask, "*Hey, what's the weather like in Paris?*" The AI doesn't know the answer off the top of its head. But it **knows about the tool** and can respond with something like:  

```ini
Use weather_tool("Paris") to get the answer.
```

The AI itself isn't fetching the data, it's **suggesting the right tool**. The **Agent**, which is the system running the AI, recognizes this and says, "*Ah, I see! The AI wants to use the weather tool. Let's call it!*" The result (let's say, "14&deg;C and cloudy") gets passed back to the AI, which then **forms a proper response**:  

> "The weather in Paris is currently 14&deg;C and cloudy."  

So the AI **looks smart**, but really, it's just writing down instructions. The Agent is the one actually running the tools.  

### Automating Tool Discovery

Wouldn't it be nice if we didn't have to manually describe every tool? Well, we can make Python do the heavy lifting!  

Python has **introspection**, which is just a fancy way of saying,  **it can look at its own code and figure things out**. If we write our functions properly, with type hints and docstrings, Python can generate tool descriptions for us.  

Example:  

```python
@tool
def calculator(a: int, b: int) -> int:
    """Multiply two integers."""
    return a * b

print(calculator.to_string())  
```

Boom! The AI now understands that there's a **calculator tool** that multiplies numbers, so no need to write out a full description manually, just awesome.  

Without tools, an AI is like a robot without arms, it can **think**, but it can't **act**. 

If we add tools, it can do real-time calculations, fetch live data from the web, interact with external systems and solve problems that require more than just text generation.

Tools take AI from being just a **chatbot** to being an **agent that can act in the world**.  

And **that's the real magic**, not that the AI is all-knowing, but that it can learn to **use the right tools at the right time**.  

**Tools** are functions that give AI **extra abilities** beyond just generating text and we **define** them by giving them a **clear description, inputs, outputs and a callable function**.  