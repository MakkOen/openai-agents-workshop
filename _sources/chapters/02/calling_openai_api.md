# OpenAI API
```{admonition} Tip:
Use the playground.ipynb notebook to play around with all the code presented in this workshop.
```

OpenAI API offers RESTful, streaming, and real-time APIs to interact with the OpenAI platform or any local LLM you set up with it.

We are going to focus on the `chat/completions/` endpoint that allows us to send prompts and receive responses in a RESTful way.

Our first LLM call:

```python
response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[{"role": "user", "content": "What's the capital of Namibia"}],
)
```

But what did we get from the API as a response? A `ChatCompletion` object. Use
```python
print(json.dumps(response.model_dump(), indent=2))
```
to inspect it.

`ChatCompletion` gives us the id of the response, the reason for the response generation being stopped, and other useful info, but most importantly we get the actual response text as:
```python
response.choices[0].message.content
```