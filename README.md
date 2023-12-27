# Tapsage API Documentation
Tapsage serves as a platform for delivering generative AI solutions. This document provides a few examples of the services offered by OpenAI. For a comprehensive understanding of all the services and their functionalities, please refer to [OpenAI’s official documentation](https://platform.openai.com/docs/api-reference/introduction). To utilize OpenAI’s APIs via Tapsage, simply replace the URL `https://api.openai.com` with `https://api.tapsage.com` and authenticate using your Tapsage Key instead of OpenAI key.

## Embeddings

An embedding is a vector (list) of floating point numbers. The distance between two vectors measures their relatedness. Small distances suggest high relatedness and large distances suggest low relatedness.

### How to get embeddings
Get a vector representation of a given input that can be easily consumed by machine learning models and algorithms.

    POST  https://api.tapsage.com/v1/embeddings
Creates an embedding vector representing the input text.
####  Request body
```bash 
curl https://api.tapsage.com/v1/embeddings \
  -H "Authorization: Bearer $TAPSAGE_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "input": "The food was delicious and the waiter...",
    "model": "text-embedding-ada-002",
    "encoding_format": "float"
  }'
```
 - **input** string or array | <span style="color:blue">*Required*</span>: Input text to embed, encoded as a string or array of tokens. To embed multiple inputs in a single request, pass an array of strings or array of token arrays. The input must not exceed the max input tokens for the model (8192 tokens for `text-embedding-ada-002`), cannot be an empty string, and any array must be 2048 dimensions or less.
 - **model** string | <span style="color:blue">*Required*</span>: ID of the model to use. You can use the [List models](https://platform.openai.com/docs/api-reference/models/list) API to see all of your available models, or see our [Model overview](https://platform.openai.com/docs/models/overview) for descriptions of them.
 - **encoding_format** string | Optional | Defaults to  float: The format to return the embeddings in. Can be either `float` or [`base64`](https://pypi.org/project/pybase64/).
 - **user** string | Optional: A unique identifier representing your end-user, which can help OpenAI to monitor and detect abuse.

####  Response
```JSON
{
  "object": "list",
  "data": [
    {
      "object": "embedding",
      "embedding": [
        0.0023064255,
        -0.009327292,
        .... (1536 floats total for ada-002)
        -0.0028842222,
      ],
      "index": 0
    }
  ],
  "model": "text-embedding-ada-002",
  "usage": {
    "prompt_tokens": 8,
    "total_tokens": 8
  }
}
```
For more information visit [this link](https://platform.openai.com/docs/api-reference/embeddings).
___
## Chat Complition
    POST  https://api.openai.com/v1/chat/completions
  Creates a model response for the given chat conversation.
  ### Requst Body
  ```bash
  curl https://api.tapsage.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TAPSAGE_KEY" \
  -d '{
    "model": "gpt-3.5-turbo",
    "messages": [
      {
        "role": "system",
        "content": "You are a helpful assistant."
      },
      {
        "role": "user",
        "content": "Hello!"
      }
    ],
    "stream": true
  }'
  ```
### Response
```JSON
{
  "id": "chatcmpl-123",
  "object": "chat.completion",
  "created": 1677652288,
  "model": "gpt-3.5-turbo-0613",
  "system_fingerprint": "fp_44709d6fcb",
  "choices": [{
    "index": 0,
    "message": {
      "role": "assistant",
      "content": "\n\nHello there, how may I assist you today?",
    },
    "logprobs": null,
    "finish_reason": "stop"
  }],
  "usage": {
    "prompt_tokens": 9,
    "completion_tokens": 12,
    "total_tokens": 21
  }
}
```
For more information visit [this link](https://platform.openai.com/docs/api-reference/chat/create).
___
## Chat Complition(Streaming)
    POST  https://api.openai.com/v1/chat/completions
  Creates a model response in streaming mode for the given chat conversation.


```bash
curl https://api.tapsage.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TAPSAGE_KEY" \
  -d '{
    "model": "gpt-3.5-turbo",
    "messages": [
      {
        "role": "system",
        "content": "You are a helpful assistant."
      },
      {
        "role": "user",
        "content": "Hello!"
      }
    ],
    "stream": true
  }'
```
#### Response
```JSON
{"id":"chatcmpl-123","object":"chat.completion.chunk","created":1694268190,"model":"gpt-3.5-turbo-0613", "system_fingerprint": "fp_44709d6fcb", "choices":[{"index":0,"delta":{"role":"assistant","content":""},"logprobs":null,"finish_reason":null}]}

{"id":"chatcmpl-123","object":"chat.completion.chunk","created":1694268190,"model":"gpt-3.5-turbo-0613", "system_fingerprint": "fp_44709d6fcb", "choices":[{"index":0,"delta":{"content":"Hello"},"logprobs":null,"finish_reason":null}]}

{"id":"chatcmpl-123","object":"chat.completion.chunk","created":1694268190,"model":"gpt-3.5-turbo-0613", "system_fingerprint": "fp_44709d6fcb", "choices":[{"index":0,"delta":{"content":"!"},"logprobs":null,"finish_reason":null}]}

....

{"id":"chatcmpl-123","object":"chat.completion.chunk","created":1694268190,"model":"gpt-3.5-turbo-0613", "system_fingerprint": "fp_44709d6fcb", "choices":[{"index":0,"delta":{"content":" today"},"logprobs":null,"finish_reason":null}]}

{"id":"chatcmpl-123","object":"chat.completion.chunk","created":1694268190,"model":"gpt-3.5-turbo-0613", "system_fingerprint": "fp_44709d6fcb", "choices":[{"index":0,"delta":{"content":"?"},"logprobs":null,"finish_reason":null}]}

{"id":"chatcmpl-123","object":"chat.completion.chunk","created":1694268190,"model":"gpt-3.5-turbo-0613", "system_fingerprint": "fp_44709d6fcb", "choices":[{"index":0,"delta":{},"logprobs":null,"finish_reason":"stop"}]}
```
For more information visit [this link](https://platform.openai.com/docs/api-reference/chat/create).

## Finetuning
Fine-tuning lets you get more out of the models available through the API by providing:

-   Higher quality results than prompting
-   Ability to train on more examples than can fit in a prompt
-   Token savings due to shorter prompts
-   Lower latency requests

OpenAI's text generation models have been pre-trained on a vast amount of text. To use the models effectively, we include instructions and sometimes several examples in a prompt. Using demonstrations to show how to perform a task is often called "few-shot learning."

Fine-tuning improves on few-shot learning by training on many more examples than can fit in the prompt, letting you achieve better results on a wide number of tasks.  **Once a model has been fine-tuned, you won't need to provide as many examples in the prompt.**  This saves costs and enables lower-latency requests.

At a high level, fine-tuning involves the following steps:

1.  Prepare and upload training data
2.  Train a new fine-tuned model
3.  Evaluate results and go back to step 1 if needed
4.  Use your fine-tuned model

For more ifnormation visit [this link](https://platform.openai.com/docs/api-reference/fine-tuning).
