# Agents Introduction
Sor far, we have everything to make a little chatbot. But all he can do is just accept and output text. But it's rather limited. No matter how powerful LLM we use, it always has only information about the past up to a certain point and there are things, like simple arithmetics, that are very hard for language models but very easy with some simple tools, like a calculator.

What we're going to need to learn and do:

1. Write our own tools
    * Tools are nothing but our python functions but we have to document them properly.
2. Recognize when the model wants us to call the tool and how
    * OpenAI API helps us with that.
3. Call the tool ourselves
    * The model can't run anything so it's fully our responsibility
4. Send the result back to the model
    * We already know how to keep a long conversation going
