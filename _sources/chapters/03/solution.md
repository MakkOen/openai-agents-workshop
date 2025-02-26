# Finished Agent

Here is a simple complete working agent to help you if you get stuck at any point:
```python
class Agent():
    def __init__(
        self,
        system_prompt: str,
        model: str,
        tools: list
    ):

        self._system_prompt = system_prompt
        self._messages = []
        if system_prompt is not None:
            self._messages.append({
                "role": "system",
                "content": system_prompt
            })
        self._model = model
        self._tools = tools

    def _parse_response(self, response: ChatCompletion) -> dict:
        # check if we have a tool call
        if response.choices[0].finish_reason == 'tool_calls':
            tool_calls =  response.choices[0].message.tool_calls
            next_step = {
                'target': 'llm',
                'messages': [],
            }

            for tool_call in tool_calls:
                args = json.loads(tool_call.function.arguments)
                # check what function to call
                if tool_call.function.name == 'calculator':
                    result = calculator(**args)
                    next_step["messages"].append({
                        'id': tool_call.id,
                        'name': tool_call.function.name,
                        'args': args,
                        'content': str(result)
                    })
                elif tool_call.function.name == 'get_weather':
                    result = get_weather(**args)
                    next_step["messages"].append({
                        'id': tool_call.id,
                        'name': tool_call.function.name,
                        'args': args,
                        'content': str(result)
                    })
                
                else:
                    next_step["messages"].append({
                        'id': tool_call.id,
                        'name': tool_call.function.name,
                        'args': args,
                        'content': "Unsupported function"
                    })

        else:
            next_step = {
                "target": "user",
                "messages": [response.choices[0].message.content]
            }

        return next_step

    def flush(self):
        self._messages = []
        if self._system_prompt is not None:
            self._messages.append({
                "role": "system",
                "content": self._system_prompt
            })

    def run(self, query: str) -> str:
        # add the new user query to our messages
        self._messages.append({
            "role": "user",
            "content": query
        })
        for step in range(10):
            print(f"Agent step: {step}")
            try:
                response = client.chat.completions.create(
                    model=self._model,
                    messages=self._messages,
                    tools=self._tools,
                    timeout=10,
                    max_completion_tokens=100
                )
            except Exception as e:
                print(f"Request failed: {e}")
                print(f"Trying again")
                continue

            next_step = self._parse_response(response)

            if next_step["target"] == "user":
                return next_step['messages'][0]
            
            self._messages.append(response.choices[0].message)
            for tool_call in next_step['messages']:
                print(f"Called tool: {tool_call['name']}, with arguments: {tool_call['args']}")

                print(f"Tool output: {tool_call['content']}\n")

                self._messages.append({
                    "role": "tool",
                    "tool_call_id": tool_call["id"],
                    "content": tool_call["content"]
                })
        
        # The agent kept looping
        return "The agent could not come up with an answer to the query"

```
