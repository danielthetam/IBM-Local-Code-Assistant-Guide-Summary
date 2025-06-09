# Summary
## Step 1
Install [Ollama](https://ollama.com/download) and run the setup.
## Step 2
Access your command prompt, run the following commands one after another.
```
ollama pull granite3.3:8b
ollama pull granite-code:3b
ollama pull granite-embedding:30m
```

These are three separate models that will each play a specific role in our code assistant.
> ### Granite 3.3 (Chat Role)  
> * This chat model is used to generate appropriate responses to user prompts.
> ### Granite-Code (Autocomplete Role)
> * This model is trained specifically to predict the code that goes between the prefix and suffix of a code file. This refers to the code before your cursor (prefix), and the code following your cursor (suffix).
> ### Granite-Embedding (Embed Role)
> * The purpose of an embedding model has been explained in the Notes section. Continue uses this model to index your codebase. We need not dive into the details of what goes on here. However, for the curious bunch interested in a deeper delve into Continue's inner workings, [this](https://docs.continue.dev/customize/deep-dives/codebase) will prove useful.

## Step 3
Next, run this command to install continue. This is given under the assumption that Visual Studio Code has been installed.
```
code --install-extension continue.continue
```

## Step 4
Next, access your `config.yaml` file, located in `$HOME/.continue/config.yaml`.
> For Windows users, do take a look into your `C:/Users/Windows/your_current_user/` directory. You should find a `.continue` folder there by default. Access it, and open your `config.yaml` file.

Now, let's specify the models we want to use. Insert this snippet of code into the config.yaml file.
```
models:
  - name: Granite 3.3 8b
    provider: ollama
    model: granite3.3:8b

  - name: Granite Embedding 30m
    provider: ollama
    model: granite-embedding:30m
    embedOptions:
      maxChunkSize: 512
    roles:
      - embed

  - name: Granite Code 3b
    provider: ollama
    model: granite-code:3b
    roles:
      - autocomplete
```

## Step 5
Test it out! Look into your VS code sidebar, you should see a newly available button rocking continue's icon. Click on it and give it a whirl.
> If you can't find the button, try restarting VS code!

## Step 6 (Optional from here on out)
Here, we will set up a custom slash command that we can use via the chat interface. A custom command will allow us to write a one-time prompt for certain repetitive tasks, such as refactoring our code to make it more readable!

In our config.yaml file, insert the following code.
```
prompts:
  - name: list-comprehension
    description: Refactor code to use list comprehensions
    prompt: |
      Refactor the selected Python code to utilise list comprehensions wherever applicable.
      Present the output as a Python code snippet.
```
This way, whenever we want to refactor our code to use list comprehensions, we need not type out this lengthy prompt again.
Simply:
1. Highlight the snippet of code that you want to perform the command on
2. Use the shortcut `Ctrl + L` to add it to our prompt window.
3. Type out `/list-comprehension`, and press `SPACE` or `TAB` to use the custom command.


# Notes
## Ollama
- Ollama will ensure data privacy by allowing developers to run LLMs on their local workstation. Thus removing the need to send company source code to a remote service.
- Utilises quantization (a process that uses low-precision data types to reduce memory and overall computational costs)
- Provides a level of abstraction through its API, making it easier to interact with a range of well-known models.

## Embedding models
Embedding models are trained to allow data (such as images, text, audio, etc.) to be expressed mathematically using vector representations. Word embeddings are a method of representing words using dense real-valued vectors in a multi-dimensional space such that it captures the semantic information of a word, as well as its relations to other words. These relations can be measured by observing the vectors (e.g. their distance, direction, etc.).

## Continue
Continue is a tool used to create a personalised AI code assisstant. This allows us to interact with our Ollama models through within the comfort of our editor. In this case we use the available VS Code extension to integrate our granite model (run locally using ollama) with VS code.

## My config.yaml file for reference
```
name: Local Assistant
version: 1.0.0
schema: v1
models:
  - name: Granite 3.3 8b
    provider: ollama
    model: granite3.3:8b
  - name: Granite Embedding 30m
    provider: ollama
    model: granite-embedding:30m
    embedOptions:
      maxChunkSize: 512
    roles:
      - embed
  - name: Granite Code 3b
    provider: ollama
    model: granite-code:3b
    roles:
      - autocomplete

prompts:
  - name: list-comprehension
    description: Refactor code to use list comprehensions
    prompt: |
      Refactor the selected Python code to utilise list comprehensions wherever applicable.
      Present the output as a Python code snippet.

context:
  - provider: code
  - provider: docs
  - provider: diff
  - provider: terminal
  - provider: problems
  - provider: folder
  - provider: codebase
```

## References
* [Continue Documentation](https://docs.continue.dev/reference)
* [IBM Guide](https://developer.ibm.com/tutorials/awb-local-ai-copilot-ibm-granite-code-ollama-continue/)
> This guide is slightly outdated as it uses a config.json file for the configuration of continue. This method is now deprecated. Continue now uses yaml files, which makes the configuration much cleaner and readable. The changes aren't too drastic, hence with the Continue documentation as a reference, you can easily convert the json code in the IBM guide to yaml code. 