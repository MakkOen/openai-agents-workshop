# Tools

Tools are nothing but a python functions and any python function we're capable of describing executing can be a tool for our agent.

Lets star with giving the LLM a simple but powerful tool, a calculator for simple arithmetics:
```python
def calculator(expression):
    """
    Evaluate a mathematical expression containing only basic arithmetic operators.

    Args:
        expression (str): The expression to evaluate.
    
    Returns:
        float: The result of the expression.
    
    """
    return eval(expression, {"__builtins__": None})
```
Note the verbose docstrings, we will use them later to describe the tool to the model.

Now we need to describe the tool to our model, so that it knows it's available, what it does and how to use it. 
We do that by creating a list `tools`, where each element is dictionary describing the tool:

```python
tools = [{
    "type": "function",
    "function": {
        "name": calculator.__name__,
        "description": calculator.__doc__,
        "parameters": {
            "type": "object",
            "properties": {
                "expression": {"type": "number"},
            },
            "required": ["expression"],
            "additionalProperties": False
        },
        "strict": True
    }
}]
```

Few things to note here:
* The name and description do not have to be identical to our function's `__name__` and `__doc__`. 
* `properties`: is a dictionary with the function's arguments
    * Types are not necessarily Python types but rather JSON Schema types:
    * `string`, `number`, `integer`, `object`, `array`, `boolean`, `null`
* `required`: list of necessary parameters
* `additionalProperties`: If we want to allow the model to add extra, undefined parameters. We don't.
* `strict`: Makes sure the output of the model follows the defined schema.

We can now finally give the model our tools. We do that using a new parameter, `tools` of the chat.completions.create() call:
```python
client.chat.completions.create(
    model="gpt-4o-mini",
    messages=messages,
    tools=tools,
)
```

Let's try giving the model our calculator and seeing what happens!

...

The response was empty?!

It wasn't really. The OpenAI API just catches the tool request server side and formats the response so that we can easily find without inpsecting the string output itself. We can check if the model requires a tool call but looking inspecing `response.choices[0].finish_reason`, if it's equal to `"tool_calls"` we need to now call the tool ourselves.

But we need to know what tool and which parameters to use for its call:
* `response.choices[0].message.tool_calls[0].function.name` contains the function name
* `response.choices[0].message.tool_calls[0].function.arguments` contains the dictionary of the tool's arguments and their values

With this information we can just call the tool ourselves:
```python
expression = completion.message.tool_calls[0].function.arguments
tool_call_result = calculator(expression)
```

Now we need to send the result back to the model so that it can use it and formulate a new output. We will do it by using a new role for a message, `"tool"`.
We also need to provide an additional argument with this message: `"tool_call_id"`, so the API can pair the tool call answer to the request properly. We can find it in:
```python
completion.message.tool_calls[0].id
```

So the message looks like this:
```python
{
    "role": "tool",
    "tool_call_id": response.choices[0].message.tool_calls[0].id,
    "content": tool_call_result
}
```

Let's now try sending the result to the model. But we must not forget to add the response, the tool call request to the conversation so the model knows what the tool call answer was for, we can do that with:
```python
messages.append(response.choices[0].message)
```
And after we can append our response message.
```{admonition} Tip:
Compare the result to a call without any tools at all.
```

## Multiple tool calls
What happens when the model wants to perform several tool calls at once? Try asking the model `"What is 857*423? What is 934*123"`.
We can see the usual tool call request `response.choices[0].message.tool_calls[0]` but there's another one `response.choices[0].message.tool_calls[1]`!
`response.choices[0].message.tool_calls` is actually a list of tool calls that we need to all process!

Once we do that we have to send back a separate response for each tool call request. So our code will have to look something like:
```python
for tool_call in response.choices[0].message.tool_calls:
    #process the tool call
    tool_call_result=...

    messages.append(
        {
            "role": "tool",
            "tool_call_id": tool_call.id,
            "content": tool_call_result
        }
    )
```


## Exercise:
Using the `api.open-meteo.com` to create a `get_weather` tool to get the weather given latitude and longitude. Use
```
https://api.open-meteo.com/v1/forecast?latitude={latitude}&longitude={longitude}&current=temperature_2m,wind_speed_10m
```

