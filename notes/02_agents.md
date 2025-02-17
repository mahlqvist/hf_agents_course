# Introduction to Agents

Imagine you have a little robot friend. 

You ask it, "*Hey, what's the weather today?*" and boom it tells you, "*It's sunny and the temperature is 25&deg;C*".  

Now, what's happening inside this robot? Let’s divide it into two parts:  

1. **The Brain (AI Model)** – This is the part that *thinks*. The AI model handles reasoning and planning, usually a *Large Language Model* (**LLM**) or a *Neural Network*. When you ask a question, it processes your words, figures out what you mean and generates a response. This is done using *Natural Language Processing* (**NLP**). Think of it like a student who has studied a lot of books and can answer based on what it has learned.  

2. **The Body (Capabilities & Tools)** – A brain alone isn't enough, it needs a body to *act*. This is where tools come in. If the AI agent has access to a weather API, it can *fetch live weather data* instead of guessing or use a web search to fetch the data and then give you the answer. If it controls a robot, it might *move an arm* or *open a door*. The "Body" is everything the AI can interact with, whether it's the internet, a database or real-world sensors.  

To tie it all together, the **Brain** figures out the plan and then use the **Body** to interact with the environment to carrie out that plan.  

Now, let’s push this idea further. If you tell an AI agent, **"Book me a flight to Paris"**, a good agent won't just respond with, *"Paris is the capital of France!"* That’s just Brain thinking. A real AI agent will:  

1. Understand your intent (**Brain**)  
2. Use a flight-booking API to find options (**Body**)  
3. Show you choices or even book a flight for you (**Action**)  

That's what makes it an *agent*, it doesn't just sit there answering questions. It **acts on your behalf** using both intelligence (Brain) and tools (Body).

An **Agent** is capable of reasoning, planning and interacting with its environment.

We call it **Agent** because it has **agency**, aka it has the *the ability to take action or to choose what action to take*.

> "An Agent is a system that leverages an AI model to interact with its environment in order to achieve a user-defined objective. It combines reasoning, planning, and the execution of actions (often via external tools) to fulfill tasks."