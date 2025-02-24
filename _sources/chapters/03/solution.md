# Finished Agent

Here is a simple complete working agent to help you if you get stuck at any point:
```python
class Agent():
    def __init__(
        system_prompt: str=None,
        model: str,
        tools: list
    ):

    self._system_prompt = system_prompt
    self._messages = []
    if system_prompt is not None:
        self._messages.append([{
            "role": "system",
            "content": system_prompt
        }])
    self._model = model
    self._tools = tools

    def _parse_response(response: ChatCompletion) -> dict:
    # check if we have a tool call
    if response.choices[0].finish_reason == 'tool_calls':
        tool_call =  completion.choices[0].message.tool_calls[0]
        args = json.loads(tool_call.function.arguments)
        # check what function to call
        if tool_call.function.name == 'calculator':
            result = calculator(**args)
            return {
                'target': 'llm',
                'id': tool_call.id,
                'content': str(result)
            }
        elif tool_call.function.name == 'get_weather':
        
        else:
            return {
                'target': 'llm',
                'id': tool_call.id,
                'content': "Unsupported function"
            }

    else:
        return {
            'target': 'user',
            'content': response.choices[0].message.content
        }

    def flush():
        self._messages = []
        if self._system_prompt is not None:
            self._messages.append([{
                "role": "system",
                "content": system_prompt
            }])

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
                tools=self._tools,
                timeout=10,
                max_completion_token=100
            )

            self._messages.append(response.choices[0].message)
            next_step = parse_output(response)

            if next_step['target'] == 'user':
                return next_step['content']

            self._messages.append({
                "role": "tool",
                "tool_call_id": next_step['id'],
                "content": next_step['content']
            })
        
        # The agent kept looping
        return "The agent could not come up with an answer to the query"
```
