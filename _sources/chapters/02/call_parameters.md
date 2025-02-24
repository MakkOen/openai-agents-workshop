# `chat.completions.create()` parameters
So far we know how to query our model but how do we make sure the model doesn't eat all our credits or compute generating a very large response or how do we handle timeouts? We can use the following parameters during the `.create()` call. For a full list of parameters check the API documentation but we will need the following ones:
* `max_completion_tokens`

    This parameter lets us setup the maximum number of tokens we allow the model to generate. In case the generation goes over this model, the model is stopped and we will be given its response at that moment, possibly incomplete.
    ```python
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "user", "content": "What is the capital of Namibia"},
        ],
        max_completion_tokens=1
    )
    ```

* `timeout`

    We're using synchronus call to the API and need to be prepared to handle the cases of the API taking forever to respond. We can do that with the `timeout` parameter which makes the call throw an exception after the specified amount of seconds elapsed.
    ```python
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[{"role": "user", "content": "What is the capital of Namibia?"}],
        timeout=0.1,
    )
    ```

    The particular exception is then `openai._exceptions.APITimeoutError` if we want to catch it.

* Bonus: `temperature`

    Is your model too boring and always giving you always the same answer? Use the `temperature` parameter. With values between 0 and 2 you can control how deterministic the model is, with 0 being (almost) deterministic one.
    ```python
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "user", "content": "Give me a random number. Output only the number."},
        ],
        temperature = 0.0,
    )
    ```