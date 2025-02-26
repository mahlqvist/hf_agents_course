# Interact with its Environment 

An AI agent isn’t just sitting around thinking all day, it has to **do** something!

Let’s say you’ve got an AI helping with customer support. What can it **do**?  
- Pull up customer records.  
- Suggest help articles.  
- Hand off tricky cases to a human.  

Every action the agent takes is **deliberate**, nothing happens by accident. Now, not all agents act the same way. Let’s look at a few types.  

|Type of Agent         | How It Works                                                         |  
|:---------------------|:---------------------------------------------------------------------|  
|JSON Agent            |Describes actions in JSON format. Clear, structured and easy to parse.|  
|Code Agent            |Writes a block of code (usually Python) that gets executed externally.|  
|Function-calling Agent|A JSON Agent that formats output so functions can be called directly. |

So, what do these agents actually **do** with their actions?

|Type of Action         |What It Does                                            |  
|:----------------------|:-------------------------------------------------------|  
|Information Gathering  |Searches the web, queries databases, pulls up documents.|  
|Tool Usage             |Calls APIs, runs calculations, executes code.           |  
|Environment Interaction|Controls apps, devices or even robots.                  |  
|Communication          |Chats with users, coordinates with other agents.        |  

Now, here's something important: once an agent finishes an action, it has to **stop**. If it just keeps rambling, it could generate useless or incorrect output. Stopping at the right moment keeps everything clean and precise.  

### The "Stop and Parse" Approach  

Alright, so how does an agent actually **tell** the outside world what action to take? The answer: it **outputs structured text**, then stops. 

Here’s how it works:  
1. **Generate a structured action**: The agent describes exactly what it wants to do in a predictable format (like JSON or code).  
2. **Stop at the right time**: No extra junk—just the action.  
3. **Parse the action**: An external system reads the output, figures out which tool to call, and extracts the necessary details.  

This keeps everything **structured and machine-readable**, reducing errors and making sure the right action gets executed.  

Now, function-calling agents work the same way. They don't just generate text randomly, they format everything so the right function gets called with the right arguments.  

### Code Agents 

Some agents don't just output **JSON**, they generate entire **code snippets**. Why? Because code can:  
- Handle complex logic (loops, conditionals, calculations).  
- Be **modular**—reuse functions instead of repeating steps.  
- Be **easier to debug** (errors are easier to spot in structured code).  
- Directly integrate with external APIs, libraries and systems.  

Let's say an agent wants to find the weather. Instead of outputting JSON, it generates Python code like this:  

```python
import requests

response = requests.get("https://api.weather.com/temp?city=NewYork")
data = response.json()
print("The temperature is:", data["temp"])
```

What’s happening here?  
1. The agent **fetches weather data** using an API.  
2. It **processes the response** to extract the temperature.  
3. It **outputs the final answer**.  

Just like before, the agent **stops at the right place**, so the output stays clean.  

At the end of the day, **actions** are what turn AI from a **thinker** into a **doer**. Whether it's structured JSON, function calls or full-on code generation, the key is:  
- **Be precise**: Every action is structured and clear.  
- **Be deliberate**: The agent takes an action for a reason.  
- **Know when to stop**: No unnecessary output.  