# Prompts
In our first OpenAI API call we sent just the user query
```python
{"role": "user", "content": "What's the capital of Namibia"}
```
but what if we want to add our system instructions in a system prompt along with the user query? To do that we have to use the `role` keyword.

Generally the `messages` parameter of the client.chat.completions.create() call is a list of messages, where each message is a dictionary with keys `role` and `content`.
* `role`: Specifies the role of the message in the conversation
* `content`: The actual string content of the message itself.

## Roles
* `"system"`
Role to give the model a general system instructions, system prompt. Eg:
```python
{"role": "system", "content": "Your are a helpful assistant knowledgeable about Namibia, please answer tourist queries in the most helpful manner."}
```

* `"user"`
The user input query to the model. Eg:
```python
{"role": "user", "content": "What's the capital of Namibia?"}
```

* `"assistant"`
The role used to denote the model's responses so it can orient itself in a multi-step conversations. Eg:
```python
{"role": "assistant", "content": "The capital of Namibia is Windhoek."}
```

There is also another role, `"tools"`, which we will learn about and use in later chapters when building our agent.

## Chat history
Currently, in our simple model call, the model has no sense of any other calls we have made and so we can't really have an ongoing conversation with it.

:::{admonition}
:class: tip
Test this in the notebook yourself!
:::

To fix this, we need to add the current chat history with every call:
```python
messages = [
    {"role": "user", "content": "Give me a random number"},
]

response_1 = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=messages,
)

messages.extend([
    {"role": "assistant", "content": response_1.choices[0].message.content},
    {"role": "user", "content": "Now add 10 to that number"},
])

response_2 = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=messages,
)

print(f"First response: {response_1.choices[0].message.content}")
print(f"Second response: {response_2.choices[0].message.content}")
```

```{admonition} Bonus question:
The length of the model is limited (128,000 tokens for gpt-4o-mini-2024-07-18). How can we handle a long conversation extending beyond this limit?
```
