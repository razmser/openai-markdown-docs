# Chat Completions

The Chat Completions API endpoint will generate a model response from a list of messages comprising a conversation.

Related guides:

  * [Quickstart](https://platform.openai.com/docs/quickstart?api-mode=chat)
  * [Text inputs and outputs](https://platform.openai.com/docs/guides/text?api-mode=chat)
  * [Image inputs](https://platform.openai.com/docs/guides/images?api-mode=chat)
  * [Audio inputs and outputs](https://platform.openai.com/docs/guides/audio?api-mode=chat)
  * [Structured Outputs](https://platform.openai.com/docs/guides/structured-outputs?api-mode=chat)
  * [Function calling](https://platform.openai.com/docs/guides/function-calling?api-mode=chat)
  * [Conversation state](https://platform.openai.com/docs/guides/conversation-state?api-mode=chat)

**Starting a new project?** We recommend trying [Responses](https://platform.openai.com/docs/api-reference/responses) to take advantage of the latest OpenAI platform features. Compare [Chat Completions with Responses](https://platform.openai.com/docs/guides/responses-vs-chat-completions?api-mode=responses).

## Create chat completion

**Starting a new project?** We recommend trying [Responses](https://platform.openai.com/docs/api-reference/responses) to take advantage of the latest OpenAI platform features. Compare [Chat Completions with Responses](https://platform.openai.com/docs/guides/responses-vs-chat-completions?api-mode=responses).

* * *

Creates a model response for the given chat conversation. Learn more in the [text generation](https://platform.openai.com/docs/guides/text-generation), [vision](https://platform.openai.com/docs/guides/vision), and [audio](https://platform.openai.com/docs/guides/audio) guides.

Parameter support can differ depending on the model used to generate the response, particularly for newer reasoning models. Parameters that are only supported for reasoning models are noted below. For the current state of unsupported parameters in reasoning models, [refer to the reasoning guide](https://platform.openai.com/docs/guides/reasoning).

### Example request

```bash
curl https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "model": "gpt-5.1",
    "messages": [
      {
        "role": "developer",
        "content": "You are a helpful assistant."
      },
      {
        "role": "user",
        "content": "Hello!"
      }
    ]
  }'
```

### Response

```json
{
  "id": "chatcmpl-B9MBs8CjcvOU2jLn4n570S5qMJKcT",
  "object": "chat.completion",
  "created": 1741569952,
  "model": "gpt-4.1-2025-04-14",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "Hello! How can I assist you today?",
        "refusal": null,
        "annotations": []
      },
      "logprobs": null,
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 19,
    "completion_tokens": 10,
    "total_tokens": 29,
    "prompt_tokens_details": {
      "cached_tokens": 0,
      "audio_tokens": 0
    },
    "completion_tokens_details": {
      "reasoning_tokens": 0,
      "audio_tokens": 0,
      "accepted_prediction_tokens": 0,
      "rejected_prediction_tokens": 0
    }
  },
  "service_tier": "default"
}
```

## Get chat completion

Get a stored chat completion. Only Chat Completions that have been created with the `store` parameter set to `true` will be returned.

### Example request

```bash
curl https://api.openai.com/v1/chat/completions/chatcmpl-abc123 \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "object": "chat.completion",
  "id": "chatcmpl-abc123",
  "model": "gpt-4o-2024-08-06",
  "created": 1738960610,
  "request_id": "req_ded8ab984ec4bf840f37566c1011c417",
  "tool_choice": null,
  "usage": {
    "total_tokens": 31,
    "completion_tokens": 18,
    "prompt_tokens": 13
  },
  "seed": 4944116822809979520,
  "top_p": 1.0,
  "temperature": 1.0,
  "presence_penalty": 0.0,
  "frequency_penalty": 0.0,
  "system_fingerprint": "fp_50cad350e4",
  "input_user": null,
  "service_tier": "default",
  "tools": null,
  "metadata": {},
  "choices": [
    {
      "index": 0,
      "message": {
        "content": "Mind of circuits hum,  \nLearning patterns in silence—  \nFuture's quiet spark.",
        "role": "assistant",
        "tool_calls": null,
        "function_call": null
      },
      "finish_reason": "stop",
      "logprobs": null
    }
  ],
  "response_format": null
}
```

## Get chat messages

Get the messages in a stored chat completion. Only Chat Completions that have been created with the `store` parameter set to `true` will be returned.

### Example request

```bash
curl https://api.openai.com/v1/chat/completions/chat_abc123/messages \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "id": "chatcmpl-AyPNinnUqUDYo9SAdA52NobMflmj2-0",
      "role": "user",
      "content": "write a haiku about ai",
      "name": null,
      "content_parts": null
    }
  ],
  "first_id": "chatcmpl-AyPNinnUqUDYo9SAdA52NobMflmj2-0",
  "last_id": "chatcmpl-AyPNinnUqUDYo9SAdA52NobMflmj2-0",
  "has_more": false
}
```

## List Chat Completions

List stored Chat Completions. Only Chat Completions that have been stored with the `store` parameter set to `true` will be returned.

### Example request

```bash
curl https://api.openai.com/v1/chat/completions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "object": "chat.completion",
      "id": "chatcmpl-AyPNinnUqUDYo9SAdA52NobMflmj2",
      "model": "gpt-4.1-2025-04-14",
      "created": 1738960610,
      "request_id": "req_ded8ab984ec4bf840f37566c1011c417",
      "tool_choice": null,
      "usage": {
        "total_tokens": 31,
        "completion_tokens": 18,
        "prompt_tokens": 13
      },
      "seed": 4944116822809979520,
      "top_p": 1.0,
      "temperature": 1.0,
      "presence_penalty": 0.0,
      "frequency_penalty": 0.0,
      "system_fingerprint": "fp_50cad350e4",
      "input_user": null,
      "service_tier": "default",
      "tools": null,
      "metadata": {},
      "choices": [
        {
          "index": 0,
          "message": {
            "content": "Mind of circuits hum,  \nLearning patterns in silence—  \nFuture's quiet spark.",
            "role": "assistant",
            "tool_calls": null,
            "function_call": null
          },
          "finish_reason": "stop",
          "logprobs": null
        }
      ],
      "response_format": null
    }
  ],
  "first_id": "chatcmpl-AyPNinnUqUDYo9SAdA52NobMflmj2",
  "last_id": "chatcmpl-AyPNinnUqUDYo9SAdA52NobMflmj2",
  "has_more": false
}
```

## Update chat completion

Modify a stored chat completion. Only Chat Completions that have been created with the `store` parameter set to `true` can be modified. Currently, the only supported modification is to update the `metadata` field.

### Example request

```bash
curl -X POST https://api.openai.com/v1/chat/completions/chat_abc123 \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"metadata": {"foo": "bar"}}'
```

### Response

```json
{
  "object": "chat.completion",
  "id": "chatcmpl-AyPNinnUqUDYo9SAdA52NobMflmj2",
  "model": "gpt-4o-2024-08-06",
  "created": 1738960610,
  "request_id": "req_ded8ab984ec4bf840f37566c1011c417",
  "tool_choice": null,
  "usage": {
    "total_tokens": 31,
    "completion_tokens": 18,
    "prompt_tokens": 13
  },
  "seed": 4944116822809979520,
  "top_p": 1.0,
  "temperature": 1.0,
  "presence_penalty": 0.0,
  "frequency_penalty": 0.0,
  "system_fingerprint": "fp_50cad350e4",
  "input_user": null,
  "service_tier": "default",
  "tools": null,
  "metadata": {
    "foo": "bar"
  },
  "choices": [
    {
      "index": 0,
      "message": {
        "content": "Mind of circuits hum,  \nLearning patterns in silence—  \nFuture's quiet spark.",
        "role": "assistant",
        "tool_calls": null,
        "function_call": null
      },
      "finish_reason": "stop",
      "logprobs": null
    }
  ],
  "response_format": null
}
```

## Delete chat completion

Delete a stored chat completion. Only Chat Completions that have been created with the `store` parameter set to `true` can be deleted.

### Example request

```bash
curl -X DELETE https://api.openai.com/v1/chat/completions/chat_abc123 \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "object": "chat.completion.deleted",
  "id": "chatcmpl-AyPNinnUqUDYo9SAdA52NobMflmj2",
  "deleted": true
}
```

## The chat completion object

Represents a chat completion response returned by model, based on the provided input.

### Parameters

#### choices - array
A list of chat completion choices. Can be more than one if `n` is greater than 1.

- **finish_reason - string**
  The reason the model stopped generating tokens. This will be `stop` if the model hit a natural stop point or a provided stop sequence, `length` if the maximum number of tokens specified in the request was reached, `content_filter` if content was omitted due to a flag from our content filters, `tool_calls` if the model called a tool, or `function_call` (deprecated) if the model called a function.

- **index - integer**
  The index of the choice in the list of choices.

- **logprobs - object**
  Log probability information for the choice.

  - **content - array**
    A list of message content tokens with log probability information.

    - **bytes - array**
      A list of integers representing the UTF-8 bytes representation of the token. Useful in instances where characters are represented by multiple tokens and their byte representations must be combined to generate the correct text representation. Can be `null` if there is no bytes representation for the token.

    - **logprob - number**
      The log probability of this token, if it is within the top 20 most likely tokens. Otherwise, the value `-9999.0` is used to signify that the token is very unlikely.

    - **token - string**
      The token.

    - **top_logprobs - array**
      List of the most likely tokens and their log probability, at this token position. In rare cases, there may be fewer than the number of requested `top_logprobs` returned.

      - **bytes - array**
        A list of integers representing the UTF-8 bytes representation of the token. Useful in instances where characters are represented by multiple tokens and their byte representations must be combined to generate the correct text representation. Can be `null` if there is no bytes representation for the token.

      - **logprob - number**
        The log probability of this token, if it is within the top 20 most likely tokens. Otherwise, the value `-9999.0` is used to signify that the token is very unlikely.

      - **token - string**
        The token.

  - **refusal - array**
    A list of message refusal tokens with log probability information.

    - **bytes - array**
      A list of integers representing the UTF-8 bytes representation of the token. Useful in instances where characters are represented by multiple tokens and their byte representations must be combined to generate the correct text representation. Can be `null` if there is no bytes representation for the token.

    - **logprob - number**
      The log probability of this token, if it is within the top 20 most likely tokens. Otherwise, the value `-9999.0` is used to signify that the token is very unlikely.

    - **token - string**
      The token.

    - **top_logprobs - array**
      List of the most likely tokens and their log probability, at this token position. In rare cases, there may be fewer than the number of requested `top_logprobs` returned.

      - **bytes - array**
        A list of integers representing the UTF-8 bytes representation of the token. Useful in instances where characters are represented by multiple tokens and their byte representations must be combined to generate the correct text representation. Can be `null` if there is no bytes representation for the token.

      - **logprob - number**
        The log probability of this token, if it is within the top 20 most likely tokens. Otherwise, the value `-9999.0` is used to signify that the token is very unlikely.

      - **token - string**
        The token.

- **message - object**
  A chat completion message generated by the model.

  - **content - string**
    The contents of the message.

  - **refusal - string**
    The refusal message generated by the model.

  - **role - string**
    The role of the author of this message.

  - **annotations - array**
    Annotations for the message, when applicable, as when using the [web search tool](https://platform.openai.com/docs/guides/tools-web-search?api-mode=chat).

    - **type - string**
      The type of the URL citation. Always `url_citation`.

    - **url_citation - object**
      A URL citation when using web search.

      - **end_index - integer**
        The index of the last character of the URL citation in the message.

      - **start_index - integer**
        The index of the first character of the URL citation in the message.

      - **title - string**
        The title of the web resource.

      - **url - string**
        The URL of the web resource.

  - **audio - object**
    If the audio output modality is requested, this object contains data about the audio response from the model. [Learn more](https://platform.openai.com/docs/guides/audio).

    - **data - string**
      Base64 encoded audio bytes generated by the model, in the format specified in the request.

    - **expires_at - integer**
      The Unix timestamp (in seconds) for when this audio response will no longer be accessible on the server for use in multi-turn conversations.

    - **id - string**
      Unique identifier for this audio response.

    - **transcript - string**
      Transcript of the audio generated by the model.

  - **function_call - object - Deprecated**
    Deprecated and replaced by `tool_calls`. The name and arguments of a function that should be called, as generated by the model.

    - **arguments - string**
      The arguments to call the function with, as generated by the model in JSON format. Note that the model does not always generate valid JSON, and may hallucinate parameters not defined by your function schema. Validate the arguments in your code before calling your function.

    - **name - string**
      The name of the function to call.

  - **tool_calls - array**
    The tool calls generated by the model, such as function calls.

    - **Function tool call - object**
      A call to a function tool created by the model.

      - **function - object**
        The function that the model called.

        - **arguments - string**
          The arguments to call the function with, as generated by the model in JSON format. Note that the model does not always generate valid JSON, and may hallucinate parameters not defined by your function schema. Validate the arguments in your code before calling your function.

        - **name - string**
          The name of the function to call.

      - **id - string**
        The ID of the tool call.

      - **type - string**
        The type of the tool. Currently, only `function` is supported.

    - **Custom tool call - object**
      A call to a custom tool created by the model.

      - **custom - object**
        The custom tool that the model called.

        - **input - string**
          The input for the custom tool call generated by the model.

        - **name - string**
          The name of the custom tool to call.

      - **id - string**
        The ID of the tool call.

      - **type - string**
        The type of the tool. Always `custom`.

#### created - integer
The Unix timestamp (in seconds) of when the chat completion was created.

#### id - string
A unique identifier for the chat completion.

#### model - string
The model used for the chat completion.

#### object - string
The object type, which is always `chat.completion`.

#### service_tier - string
Specifies the processing type used for serving the request.

  * If set to 'auto', then the request will be processed with the service tier configured in the Project settings. Unless otherwise configured, the Project will use 'default'.
  * If set to 'default', then the request will be processed with the standard pricing and performance for the selected model.
  * If set to '[flex](https://platform.openai.com/docs/guides/flex-processing)' or '[priority](https://openai.com/api-priority-processing/)', then the request will be processed with the corresponding service tier.
  * When not set, the default behavior is 'auto'.

When the `service_tier` parameter is set, the response body will include the `service_tier` value based on the processing mode actually used to serve the request. This response value may be different from the value set in the parameter.

#### system_fingerprint - string - Deprecated
This fingerprint represents the backend configuration that the model runs with.

Can be used in conjunction with the `seed` request parameter to understand when backend changes have been made that might impact determinism.

#### usage - object
Usage statistics for the completion request.

- **completion_tokens - integer**
  Number of tokens in the generated completion.

- **prompt_tokens - integer**
  Number of tokens in the prompt.

- **total_tokens - integer**
  Total number of tokens used in the request (prompt + completion).

- **completion_tokens_details - object**
  Breakdown of tokens used in a completion.

  - **accepted_prediction_tokens - integer**
    When using Predicted Outputs, the number of tokens in the prediction that appeared in the completion.

  - **audio_tokens - integer**
    Audio input tokens generated by the model.

  - **reasoning_tokens - integer**
    Tokens generated by the model for reasoning.

  - **rejected_prediction_tokens - integer**
    When using Predicted Outputs, the number of tokens in the prediction that did not appear in the completion. However, like reasoning tokens, these tokens are still counted in the total completion tokens for purposes of billing, output, and context window limits.

- **prompt_tokens_details - object**
  Breakdown of tokens used in the prompt.

  - **audio_tokens - integer**
    Audio input tokens present in the prompt.

  - **cached_tokens - integer**
    Cached tokens present in the prompt.

### OBJECT The chat completion object

```json
{
  "id": "chatcmpl-B9MHDbslfkBeAs8l4bebGdFOJ6PeG",
  "object": "chat.completion",
  "created": 1741570283,
  "model": "gpt-4o-2024-08-06",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "The image shows a wooden boardwalk path running through a lush green field or meadow. The sky is bright blue with some scattered clouds, giving the scene a serene and peaceful atmosphere. Trees and shrubs are visible in the background.",
        "refusal": null,
        "annotations": []
      },
      "logprobs": null,
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 1117,
    "completion_tokens": 46,
    "total_tokens": 1163,
    "prompt_tokens_details": {
      "cached_tokens": 0,
      "audio_tokens": 0
    },
    "completion_tokens_details": {
      "reasoning_tokens": 0,
      "audio_tokens": 0,
      "accepted_prediction_tokens": 0,
      "rejected_prediction_tokens": 0
    }
  },
  "service_tier": "default",
  "system_fingerprint": "fp_fc9f1d7035"
}
```

## The chat completion list object

An object representing a list of Chat Completions.

### Parameters

#### data - array
An array of chat completion objects.

- **choices - array**
  A list of chat completion choices. Can be more than one if `n` is greater than 1.

  - **finish_reason - string**
    The reason the model stopped generating tokens. This will be `stop` if the model hit a natural stop point or a provided stop sequence, `length` if the maximum number of tokens specified in the request was reached, `content_filter` if content was omitted due to a flag from our content filters, `tool_calls` if the model called a tool, or `function_call` (deprecated) if the model called a function.

  - **index - integer**
    The index of the choice in the list of choices.

  - **logprobs - object**
    Log probability information for the choice.

    - **content - array**
      A list of message content tokens with log probability information.

      - **bytes - array**
        A list of integers representing the UTF-8 bytes representation of the token. Useful in instances where characters are represented by multiple tokens and their byte representations must be combined to generate the correct text representation. Can be `null` if there is no bytes representation for the token.

      - **logprob - number**
        The log probability of this token, if it is within the top 20 most likely tokens. Otherwise, the value `-9999.0` is used to signify that the token is very unlikely.

      - **token - string**
        The token.

      - **top_logprobs - array**
        List of the most likely tokens and their log probability, at this token position. In rare cases, there may be fewer than the number of requested `top_logprobs` returned.

        - **bytes - array**
          A list of integers representing the UTF-8 bytes representation of the token. Useful in instances where characters are represented by multiple tokens and their byte representations must be combined to generate the correct text representation. Can be `null` if there is no bytes representation for the token.

        - **logprob - number**
          The log probability of this token, if it is within the top 20 most likely tokens. Otherwise, the value `-9999.0` is used to signify that the token is very unlikely.

        - **token - string**
          The token.

    - **refusal - array**
      A list of message refusal tokens with log probability information.

      - **bytes - array**
        A list of integers representing the UTF-8 bytes representation of the token. Useful in instances where characters are represented by multiple tokens and their byte representations must be combined to generate the correct text representation. Can be `null` if there is no bytes representation for the token.

      - **logprob - number**
        The log probability of this token, if it is within the top 20 most likely tokens. Otherwise, the value `-9999.0` is used to signify that the token is very unlikely.

      - **token - string**
        The token.

      - **top_logprobs - array**
        List of the most likely tokens and their log probability, at this token position. In rare cases, there may be fewer than the number of requested `top_logprobs` returned.

        - **bytes - array**
          A list of integers representing the UTF-8 bytes representation of the token. Useful in instances where characters are represented by multiple tokens and their byte representations must be combined to generate the correct text representation. Can be `null` if there is no bytes representation for the token.

        - **logprob - number**
          The log probability of this token, if it is within the top 20 most likely tokens. Otherwise, the value `-9999.0` is used to signify that the token is very unlikely.

        - **token - string**
          The token.

  - **message - object**
    A chat completion message generated by the model.

    - **content - string**
      The contents of the message.

    - **refusal - string**
      The refusal message generated by the model.

    - **role - string**
      The role of the author of this message.

    - **annotations - array**
      Annotations for the message, when applicable, as when using the [web search tool](https://platform.openai.com/docs/guides/tools-web-search?api-mode=chat).

      - **type - string**
        The type of the URL citation. Always `url_citation`.

      - **url_citation - object**
        A URL citation when using web search.

        - **end_index - integer**
          The index of the last character of the URL citation in the message.

        - **start_index - integer**
          The index of the first character of the URL citation in the message.

        - **title - string**
          The title of the web resource.

        - **url - string**
          The URL of the web resource.

    - **audio - object**
      If the audio output modality is requested, this object contains data about the audio response from the model. [Learn more](https://platform.openai.com/docs/guides/audio).

      - **data - string**
        Base64 encoded audio bytes generated by the model, in the format specified in the request.

      - **expires_at - integer**
        The Unix timestamp (in seconds) for when this audio response will no longer be accessible on the server for use in multi-turn conversations.

      - **id - string**
        Unique identifier for this audio response.

      - **transcript - string**
        Transcript of the audio generated by the model.

    - **function_call - object - Deprecated**
      Deprecated and replaced by `tool_calls`. The name and arguments of a function that should be called, as generated by the model.

      - **arguments - string**
        The arguments to call the function with, as generated by the model in JSON format. Note that the model does not always generate valid JSON, and may hallucinate parameters not defined by your function schema. Validate the arguments in your code before calling your function.

      - **name - string**
        The name of the function to call.

    - **tool_calls - array**
      The tool calls generated by the model, such as function calls.

      - **Function tool call - object**
        A call to a function tool created by the model.

        - **function - object**
          The function that the model called.

          - **arguments - string**
            The arguments to call the function with, as generated by the model in JSON format. Note that the model does not always generate valid JSON, and may hallucinate parameters not defined by your function schema. Validate the arguments in your code before calling your function.

          - **name - string**
            The name of the function to call.

        - **id - string**
          The ID of the tool call.

        - **type - string**
          The type of the tool. Currently, only `function` is supported.

      - **Custom tool call - object**
        A call to a custom tool created by the model.

        - **custom - object**
          The custom tool that the model called.

          - **input - string**
            The input for the custom tool call generated by the model.

          - **name - string**
            The name of the custom tool to call.

        - **id - string**
          The ID of the tool call.

        - **type - string**
          The type of the tool. Always `custom`.

- **created - integer**
  The Unix timestamp (in seconds) of when the chat completion was created.

- **id - string**
  A unique identifier for the chat completion.

- **model - string**
  The model used for the chat completion.

- **object - string**
  The object type, which is always `chat.completion`.

- **service_tier - string**
  Specifies the processing type used for serving the request.

    * If set to 'auto', then the request will be processed with the service tier configured in the Project settings. Unless otherwise configured, the Project will use 'default'.
    * If set to 'default', then the request will be processed with the standard pricing and performance for the selected model.
    * If set to '[flex](https://platform.openai.com/docs/guides/flex-processing)' or '[priority](https://openai.com/api-priority-processing/)', then the request will be processed with the corresponding service tier.
    * When not set, the default behavior is 'auto'.

  When the `service_tier` parameter is set, the response body will include the `service_tier` value based on the processing mode actually used to serve the request. This response value may be different from the value set in the parameter.

- **system_fingerprint - string - Deprecated**
  This fingerprint represents the backend configuration that the model runs with.

  Can be used in conjunction with the `seed` request parameter to understand when backend changes have been made that might impact determinism.

- **usage - object**
  Usage statistics for the completion request.

  - **completion_tokens - integer**
    Number of tokens in the generated completion.

  - **prompt_tokens - integer**
    Number of tokens in the prompt.

  - **total_tokens - integer**
    Total number of tokens used in the request (prompt + completion).

  - **completion_tokens_details - object**
    Breakdown of tokens used in a completion.

    - **accepted_prediction_tokens - integer**
      When using Predicted Outputs, the number of tokens in the prediction that appeared in the completion.

    - **audio_tokens - integer**
      Audio input tokens generated by the model.

    - **reasoning_tokens - integer**
      Tokens generated by the model for reasoning.

    - **rejected_prediction_tokens - integer**
      When using Predicted Outputs, the number of tokens in the prediction that did not appear in the completion. However, like reasoning tokens, these tokens are still counted in the total completion tokens for purposes of billing, output, and context window limits.

  - **prompt_tokens_details - object**
    Breakdown of tokens used in the prompt.

    - **audio_tokens - integer**
      Audio input tokens present in the prompt.

    - **cached_tokens - integer**
      Cached tokens present in the prompt.

#### first_id - string
The identifier of the first chat completion in the data array.

#### has_more - boolean
Indicates whether there are more Chat Completions available.

#### last_id - string
The identifier of the last chat completion in the data array.

#### object - string
The type of this object. It is always set to "list".

### OBJECT The chat completion list object

```json
{
  "object": "list",
  "data": [
    {
      "object": "chat.completion",
      "id": "chatcmpl-AyPNinnUqUDYo9SAdA52NobMflmj2",
      "model": "gpt-4o-2024-08-06",
      "created": 1738960610,
      "request_id": "req_ded8ab984ec4bf840f37566c1011c417",
      "tool_choice": null,
      "usage": {
        "total_tokens": 31,
        "completion_tokens": 18,
        "prompt_tokens": 13
      },
      "seed": 4944116822809979520,
      "top_p": 1.0,
      "temperature": 1.0,
      "presence_penalty": 0.0,
      "frequency_penalty": 0.0,
      "system_fingerprint": "fp_50cad350e4",
      "input_user": null,
      "service_tier": "default",
      "tools": null,
      "metadata": {},
      "choices": [
        {
          "index": 0,
          "message": {
            "content": "Mind of circuits hum,  \nLearning patterns in silence—  \nFuture's quiet spark.",
            "role": "assistant",
            "tool_calls": null,
            "function_call": null
          },
          "finish_reason": "stop",
          "logprobs": null
        }
      ],
      "response_format": null
    }
  ],
  "first_id": "chatcmpl-AyPNinnUqUDYo9SAdA52NobMflmj2",
  "last_id": "chatcmpl-AyPNinnUqUDYo9SAdA52NobMflmj2",
  "has_more": false
}
```

## The chat completion message list object

An object representing a list of chat completion messages.

### Parameters

#### data - array
An array of chat completion message objects.

- **content - string**
  The contents of the message.

- **id - string**
  The identifier of the chat message.

- **refusal - string**
  The refusal message generated by the model.

- **role - string**
  The role of the author of this message.

- **annotations - array**
  Annotations for the message, when applicable, as when using the [web search tool](https://platform.openai.com/docs/guides/tools-web-search?api-mode=chat).

  - **type - string**
    The type of the URL citation. Always `url_citation`.

  - **url_citation - object**
    A URL citation when using web search.

    - **end_index - integer**
      The index of the last character of the URL citation in the message.

    - **start_index - integer**
      The index of the first character of the URL citation in the message.

    - **title - string**
      The title of the web resource.

    - **url - string**
      The URL of the web resource.

- **audio - object**
  If the audio output modality is requested, this object contains data about the audio response from the model. [Learn more](https://platform.openai.com/docs/guides/audio).

  - **data - string**
    Base64 encoded audio bytes generated by the model, in the format specified in the request.

  - **expires_at - integer**
    The Unix timestamp (in seconds) for when this audio response will no longer be accessible on the server for use in multi-turn conversations.

  - **id - string**
    Unique identifier for this audio response.

  - **transcript - string**
    Transcript of the audio generated by the model.

- **content_parts - array**
  If a content parts array was provided, this is an array of `text` and `image_url` parts. Otherwise, null.

  - **Text content part - object**
    Learn about [text inputs](https://platform.openai.com/docs/guides/text-generation).

    - **text - string**
      The text content.

    - **type - string**
      The type of the content part.

  - **Image content part - object**
    Learn about [image inputs](https://platform.openai.com/docs/guides/vision).

    - **image_url - object**
      Either a URL of the image or the base64 encoded image data.

      - **url - string**
        Either a URL of the image or the base64 encoded image data.

      - **detail - string**
        Specifies the detail level of the image. Learn more in the [Vision guide](https://platform.openai.com/docs/guides/vision#low-or-high-fidelity-image-understanding).

    - **type - string**
      The type of the content part.

- **function_call - object - Deprecated**
  Deprecated and replaced by `tool_calls`. The name and arguments of a function that should be called, as generated by the model.

  - **arguments - string**
    The arguments to call the function with, as generated by the model in JSON format. Note that the model does not always generate valid JSON, and may hallucinate parameters not defined by your function schema. Validate the arguments in your code before calling your function.

  - **name - string**
    The name of the function to call.

- **tool_calls - array**
  The tool calls generated by the model, such as function calls.

  - **Function tool call - object**
    A call to a function tool created by the model.

    - **function - object**
      The function that the model called.

      - **arguments - string**
        The arguments to call the function with, as generated by the model in JSON format. Note that the model does not always generate valid JSON, and may hallucinate parameters not defined by your function schema. Validate the arguments in your code before calling your function.

      - **name - string**
        The name of the function to call.

    - **id - string**
      The ID of the tool call.

    - **type - string**
      The type of the tool. Currently, only `function` is supported.

  - **Custom tool call - object**
    A call to a custom tool created by the model.

    - **custom - object**
      The custom tool that the model called.

      - **input - string**
        The input for the custom tool call generated by the model.

      - **name - string**
        The name of the custom tool to call.

    - **id - string**
      The ID of the tool call.

    - **type - string**
      The type of the tool. Always `custom`.

#### first_id - string
The identifier of the first chat message in the data array.

#### has_more - boolean
Indicates whether there are more chat messages available.

#### last_id - string
The identifier of the last chat message in the data array.

#### object - string
The type of this object. It is always set to "list".

### OBJECT The chat completion message list object

```json
{
  "object": "list",
  "data": [
    {
      "id": "chatcmpl-AyPNinnUqUDYo9SAdA52NobMflmj2-0",
      "role": "user",
      "content": "write a haiku about ai",
      "name": null,
      "content_parts": null
    }
  ],
  "first_id": "chatcmpl-AyPNinnUqUDYo9SAdA52NobMflmj2-0",
  "last_id": "chatcmpl-AyPNinnUqUDYo9SAdA52NobMflmj2-0",
  "has_more": false
}
```