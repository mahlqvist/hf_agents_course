# Your First Agent

This was pretty awesome. Since DuckDuckGoSearchTool was alread imported, it became my first custom tool.

```python
@tool
def my_custom_search_tool(usr_query:str)-> str:
    """A tool to perform a web search of the user query and awnser based on that web search
    Args:
        usr_query: a string representing the users query
    """
    search_tool = DuckDuckGoSearchTool()

    results = search_tool(usr_query)

    if not results:
        return "No relevant results found!"
    return results
``` 

To use the image generation tool I guess you need a hugging face token.

Go to `Settings/Variables and secrets/New secret` and paste your token.

```python
import os

# Ensure you have a Hugging Face API token set
_ = os.getenv("HF_TOKEN") 

# Load the tool
image_generation_tool = load_tool(
    "agents-course/text-to-image", 
    trust_remote_code=True
)
``` 

Finally, just add the tools to the list of tools inside `CodeAgent()`.

```python
agent = CodeAgent(
    ...,
    tools=[
        final_answer, 
        get_current_time_in_timezone, 
        image_generation_tool, 
        my_custom_search_tool
    ],
    ...,
)
``` 

And your first agent is up and running!