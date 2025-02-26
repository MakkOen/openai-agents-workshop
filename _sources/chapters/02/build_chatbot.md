# Build a chatbot!

It's time for our first exercise! 

We now have all the tools needed to actually build our first simple chatbot that will assist tourists visiting Namibia. We have a semi-woking chatbot already in `chatbot.py` using streamlit but it's currently missing few things:

* There is no system prompt specifying the system instructions, make the model only answer questions related to Namibia and answer the following otherwise:
    ```python
    "I'm sorry user, i'm afraid i can't do that"
    ```
* Make sure to limit the model's response to 1000 tokens and set and gracefully handle a timeout of 10 seconds.
* The chatbot is just one-shot without history, make sure it can follow a conversation.

Only modify the parts needed, you don't have to tinker with the with the simple streamlit app.

If you're struggling or want to compare your solution, check out `chatbot_done.py`

## Running the chatbot
With the workshop environment activated, run the chatbot with simple:
```bash
streamlit run chatbot.py
```

:::{admonition}
:class: basic streamlit tip
* Use `st.markdown()` to print out the assistant and user strings
* There is no need for our own loop to handle the long conversation, the main body of the streamlit script runs in a loop and clicking the `Send` button launches a new iteration. Use `st.sesssion
* Use `st.error(body: str)` to notify the user of a timeout.
:::