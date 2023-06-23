# Switch between OpenAI and Azure OpenAI endpoints

## API key
### OpenAI
```Python 
import openai
openai.api_key = "sk-..."
```
### Azure OpenAI

```Python
import openai
openai.api_type = "azure"
openai.api_key = token.token
openai.api_base = "https://example-endpoint.openai.azure.com"
openai.api_version = "2023-05-15"
```

Preferer to use `dotenv` to load API key from `.env` file, so that you don't expose the API key in the code.
Keep the API key in the `~/.env` file in your local environment in this format:
```shell
OPENAI_API_TYPE = azure
OPENAI_API_BASE = https://your-endpoint.openai.azure.com/
OPENAI_API_VERSION = yyyy-mm-dd-xyz
OPENAI_API_KEY = xyz
```
Then load the `.env` parameters in the code:
```python
# pip install python-dotenv
import openai
import os

from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv())

openai.api_type = os.getenv("OPENAI_API_TYPE")
openai.api_base = os.getenv("OPENAI_API_BASE")
openai.api_version = os.getenv("OPENAI_API_VERSION")
openai.api_key = os.getenv("OPENAI_API_KEY")
openai_deployment_id = os.getenv("OPENAI_DEPLOYMENT_ID")
```

## Helper Function
OpenAI uses the `model` keyword argument to specify what model to use. 

Azure OpenAI uses the `deployment_id` keyword argument to describe which model deployment to use. Azure OpenAI also supports the use of `engine` interchangeably with deployment_id.

### OpenAI
```python
completion = openai.Completion.create(
    prompt="<prompt>",
    model="text-davinci-003"
)
  
chat_completion = openai.ChatCompletion.create(
    messages="<messages>",
    model="gpt-4"
)

embedding = openai.Embedding.create(
  input="<input>",
  model="text-embedding-ada-002"
)
```
### Azure OpenAI
```python
completion = openai.Completion.create(
    prompt="<prompt>",
    deployment_id="text-davinci-003"
    #engine="text-davinci-003" 
)
  
chat_completion = openai.ChatCompletion.create(
    messages="<messages>",
    deployment_id="gpt-4"
    #engine="gpt-4"
)

embedding = openai.Embedding.create(
  input="<input>",
  deployment_id="text-embedding-ada-002"
  #engine="text-embedding-ada-002"
)
```

## Azure OpenAI embeddings doesn't support multiple inputs
### OpenAI
```python
inputs = ["A", "B", "C"]

embedding = openai.Embedding.create(
  input=inputs,
  model="text-embedding-ada-002"
)
```

### Azure OpenAI
```python
inputs = ["A", "B", "C"]

for text in inputs:
    embedding = openai.Embedding.create(
        input=text,
        deployment_id="text-embedding-ada-002"
        #engine="text-embedding-ada-002"
    )
```

## Example

```python
prompt = f"""what is the answer to life, the universe, and everything?"""

def get_completion(prompt):
    messages=[
            {"role": "system", "content": "You are an AI assistant that helps people find information."},
            {"role": "user", "content": prompt}
        ]

    response = openai.ChatCompletion.create(
        deployment_id="gpt-35-turbo",
        messages = messages,
        temperature=0.7,
        max_tokens=800,
        top_p=0.95,
        frequency_penalty=0,
        presence_penalty=0,
        stop=None)

    return response.choices[0].message["content"]

response = get_completion(prompt)
print(response)

```
The GPT answer:

According to Douglas Adams' book "The Hitchhiker's Guide to the Galaxy", the answer to life, the universe, and everything is 42. However, this is a fictional answer and its significance is never fully explained in the book. In reality, the meaning of life and the universe is a philosophical question that has been debated for centuries with no definitive answer.


## Reference
[How to switch between OpenAI and Azure OpenAI endpoints with Python](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/how-to/switching-endpoints)

[Learn how to work with the ChatGPT and GPT-4 models](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/how-to/chatgpt?pivots=programming-language-chat-completions)