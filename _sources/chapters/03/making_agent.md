# Controling the agent workflow

So we can provide the LLM with our defined tools, we can call the tools when needed and send the result back to the model. But so far we've done that all manually, step by step. This is far from a model with agency to do stuff itself. We need to setup a loop in which we can control the flow of the agent and do all this automatically.

Lets setup an object `Agent` which will have the method `run(query: str) -> str` which will take the initial user input and output the final answer to the user query.

We will first give the agent the ability to parse the output from the model, to decide whether to call a tool or pass the final answer back to the user. It could look something like:
```python
def _parse_response(response: ChatCompletion) -> dict:
    # check if we have a tool call
    if response.choices[0].finish_reason == 'tool_calls':
        tool_call =  response.choices[0].message.tool_calls[0]
        args = json.loads(tool_call.function.arguments)
        # check what function to call
        if tool_call.function.name == 'calculator':
            result = calculator(**args)
            next_step = {
                'target': 'llm',
                'message': {
                    'id': tool_call.id,
                    'content': str(result)
                }
            }

        return next_step
```

## Exercise:
Finish the `_parse_response()` function by adding the following:
* Handling the weather tool call.
* Handling any other tool calls.
* Make sure you can handle multiple calls at once.
* Handling the case if the model doesn't need any new further tool calls.

```{admonition} Tip:
If you are struggling with at any point when putting the agent together, you can check out the next section for a solution.
```

We want the agent to have a "persistent" (at least for the life of the `Agent` object) memory so we can continue a conversation with it through separate `run()` calls.
We will do it with a property `Agent._messages` which we can fill with the system prompt, if we use any, during initialization. For simplicity, let's have the system prompt, along side with the model and the tools (the list of descriptions) be `__init__` arguments. Let's also give the agent a method that flushes the memory so we can start over.

## Exercise:
Complete the `__init__` and `flush` agent methods.

Now we can go and finish our agent by writing the `Agent.run` method. It should contain a loop that will run until the model decides to give the final answer. But let's not use just a `while True:` loop, let's give the agent a maximum, let's say 10, amounts of iterations to make sure it does not get stuck. Then the method could start like
```python
def run(self, query: str) -> str:
    # add the new user query to our messages
    self._messages.append({
        "role": "user",
        "content": query
    })
    for _ in range(10):
        response = client.chat.completions.create(
            model=self._model,
            messages=self._messages,
            tools=tools,
        )

        self._messages.append(response.choices[0].message)
        next_step = parse_output(response)
```

Finish the agent by completing the `run` method. Add the following:
* Limit the length of the answers with `max_completion_tokens`.
* Set a timeout length so we don't get stuck.
* Based on `next_step` decide if the agent should keep going or we should end.
* Handle the case when the agent just keeps going and our `for` loop ends.
* Add some simple `print`s so we can inspect what's happening while the agent is working.
* Experiment with system prompts to make sure the agent can work in multiple steps to solve your problem.









