# Fine-tuning

Manage fine-tuning jobs to tailor a model to your specific training data. Related guide: [Fine-tune models](https://platform.openai.com/docs/guides/fine-tuning)

## Create fine-tuning job

Creates a fine-tuning job which begins the process of creating a new model from a given dataset.

Response includes details of the enqueued job including job status and the name of the fine-tuned models once complete.

[Learn more about fine-tuning](https://platform.openai.com/docs/guides/model-optimization)

### Example request

```bash
curl https://api.openai.com/v1/fine_tuning/jobs \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "training_file": "file-BK7bzQj3FfZFXr7DbL6xJwfo",
    "model": "gpt-4o-mini"
  }'
```

### Response

```json
{
  "object": "fine_tuning.job",
  "id": "ftjob-abc123",
  "model": "gpt-4o-mini-2024-07-18",
  "created_at": 1721764800,
  "fine_tuned_model": null,
  "organization_id": "org-123",
  "result_files": [],
  "status": "queued",
  "validation_file": null,
  "training_file": "file-abc123",
  "method": {
    "type": "supervised",
    "supervised": {
      "hyperparameters": {
        "batch_size": "auto",
        "learning_rate_multiplier": "auto",
        "n_epochs": "auto",
      }
    }
  },
  "metadata": null
}
```

## List fine-tuning jobs

List your organization's fine-tuning jobs

### Example request

```bash
curl https://api.openai.com/v1/fine_tuning/jobs?limit=2&metadata[key]=value \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "object": "fine_tuning.job",
      "id": "ftjob-abc123",
      "model": "gpt-4o-mini-2024-07-18",
      "created_at": 1721764800,
      "fine_tuned_model": null,
      "organization_id": "org-123",
      "result_files": [],
      "status": "queued",
      "validation_file": null,
      "training_file": "file-abc123",
      "metadata": {
        "key": "value"
      }
    },
    { ... },
    { ... }
  ], "has_more": true
}
```

## List fine-tuning events

Get status updates for a fine-tuning job.

### Example request

```bash
curl https://api.openai.com/v1/fine_tuning/jobs/ftjob-abc123/events \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "object": "fine_tuning.job.event",
      "id": "ft-event-ddTJfwuMVpfLXseO0Am0Gqjm",
      "created_at": 1721764800,
      "level": "info",
      "message": "Fine tuning job successfully completed",
      "data": null,
      "type": "message"
    },
    {
      "object": "fine_tuning.job.event",
      "id": "ft-event-tyiGuB72evQncpH87xe505Sv",
      "created_at": 1721764800,
      "level": "info",
      "message": "New fine-tuned model created: ft:gpt-4o-mini:openai::7p4lURel",
      "data": null,
      "type": "message"
    }
  ],
  "has_more": true
}
```

## List fine-tuning checkpoints

List checkpoints for a fine-tuning job.

### Example request

```bash
curl https://api.openai.com/v1/fine_tuning/jobs/ftjob-abc123/checkpoints \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "object": "fine_tuning.job.checkpoint",
      "id": "ftckpt_zc4Q7MP6XxulcVzj4MZdwsAB",
      "created_at": 1721764867,
      "fine_tuned_model_checkpoint": "ft:gpt-4o-mini-2024-07-18:my-org:custom-suffix:96olL566:ckpt-step-2000",
      "metrics": {
        "full_valid_loss": 0.134,
        "full_valid_mean_token_accuracy": 0.874
      },
      "fine_tuning_job_id": "ftjob-abc123",
      "step_number": 2000
    },
    {
      "object": "fine_tuning.job.checkpoint",
      "id": "ftckpt_enQCFmOTGj3syEpYVhBRLTSy",
      "created_at": 1721764800,
      "fine_tuned_model_checkpoint": "ft:gpt-4o-mini-2024-07-18:my-org:custom-suffix:7q8mpxmy:ckpt-step-1000",
      "metrics": {
        "full_valid_loss": 0.167,
        "full_valid_mean_token_accuracy": 0.781
      },
      "fine_tuning_job_id": "ftjob-abc123",
      "step_number": 1000
    }
  ],
  "first_id": "ftckpt_zc4Q7MP6XxulcVzj4MZdwsAB",
  "last_id": "ftckpt_enQCFmOTGj3syEpYVhBRLTSy",
  "has_more": true
}
```

## List checkpoint permissions

**NOTE:** This endpoint requires an [admin API key]().

Organization owners can use this endpoint to view all permissions for a fine-tuned model checkpoint.

### Example request

```bash
curl https://api.openai.com/v1/fine_tuning/checkpoints/ft:gpt-4o-mini-2024-07-18:org:weather:B7R9VjQd/permissions \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "object": "checkpoint.permission",
      "id": "cp_zc4Q7MP6XxulcVzj4MZdwsAB",
      "created_at": 1721764867,
      "project_id": "proj_abGMw1llN8IrBb6SvvY5A1iH"
    },
    {
      "object": "checkpoint.permission",
      "id": "cp_enQCFmOTGj3syEpYVhBRLTSy",
      "created_at": 1721764800,
      "project_id": "proj_iqGMw1llN8IrBb6SvvY5A1oF"
    },
  ],
  "first_id": "cp_zc4Q7MP6XxulcVzj4MZdwsAB",
  "last_id": "cp_enQCFmOTGj3syEpYVhBRLTSy",
  "has_more": false
}
```

## Create checkpoint permissions

**NOTE:** Calling this endpoint requires an [admin API key]().

This enables organization owners to share fine-tuned models with other projects in their organization.

### Example request

```bash
curl https://api.openai.com/v1/fine_tuning/checkpoints/ft:gpt-4o-mini-2024-07-18:org:weather:B7R9VjQd/permissions \
  -H "Authorization: Bearer $OPENAI_API_KEY"
  -d '{"project_ids": ["proj_abGMw1llN8IrBb6SvvY5A1iH"]}'
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "object": "checkpoint.permission",
      "id": "cp_zc4Q7MP6XxulcVzj4MZdwsAB",
      "created_at": 1721764867,
      "project_id": "proj_abGMw1llN8IrBb6SvvY5A1iH"
    }
  ],
  "first_id": "cp_zc4Q7MP6XxulcVzj4MZdwsAB",
  "last_id": "cp_zc4Q7MP6XxulcVzj4MZdwsAB",
  "has_more": false
}
```

## Delete checkpoint permission

**NOTE:** This endpoint requires an [admin API key]().

Organization owners can use this endpoint to delete a permission for a fine-tuned model checkpoint.

### Example request

```bash
curl https://api.openai.com/v1/fine_tuning/checkpoints/ft:gpt-4o-mini-2024-07-18:org:weather:B7R9VjQd/permissions/cp_zc4Q7MP6XxulcVzj4MZdwsAB \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "object": "checkpoint.permission",
  "id": "cp_zc4Q7MP6XxulcVzj4MZdwsAB",
  "deleted": true
}
```

## Retrieve fine-tuning job

Get info about a fine-tuning job.

[Learn more about fine-tuning](https://platform.openai.com/docs/guides/model-optimization)

### Example request

```bash
curl https://api.openai.com/v1/fine_tuning/jobs/ft-AF1WoRqd3aJAHsqc9NY7iL8F \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "object": "fine_tuning.job",
  "id": "ftjob-abc123",
  "model": "davinci-002",
  "created_at": 1692661014,
  "finished_at": 1692661190,
  "fine_tuned_model": "ft:davinci-002:my-org:custom_suffix:7q8mpxmy",
  "organization_id": "org-123",
  "result_files": [
      "file-abc123"
  ],
  "status": "succeeded",
  "validation_file": null,
  "training_file": "file-abc123",
  "hyperparameters": {
      "n_epochs": 4,
      "batch_size": 1,
      "learning_rate_multiplier": 1.0
  },
  "trained_tokens": 5768,
  "integrations": [],
  "seed": 0,
  "estimated_finish": 0,
  "method": {
    "type": "supervised",
    "supervised": {
      "hyperparameters": {
        "n_epochs": 4,
        "batch_size": 1,
        "learning_rate_multiplier": 1.0
      }
    }
  }
}
```

## Cancel fine-tuning

Immediately cancel a fine-tune job.

### Example request

```bash
curl -X POST https://api.openai.com/v1/fine_tuning/jobs/ftjob-abc123/cancel \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "object": "fine_tuning.job",
  "id": "ftjob-abc123",
  "model": "gpt-4o-mini-2024-07-18",
  "created_at": 1721764800,
  "fine_tuned_model": null,
  "organization_id": "org-123",
  "result_files": [],
  "status": "cancelled",
  "validation_file": "file-abc123",
  "training_file": "file-abc123"
}
```

## Resume fine-tuning

Resume a fine-tune job.

### Example request

```bash
curl -X POST https://api.openai.com/v1/fine_tuning/jobs/ftjob-abc123/resume \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "object": "fine_tuning.job",
  "id": "ftjob-abc123",
  "model": "gpt-4o-mini-2024-07-18",
  "created_at": 1721764800,
  "fine_tuned_model": null,
  "organization_id": "org-123",
  "result_files": [],
  "status": "queued",
  "validation_file": "file-abc123",
  "training_file": "file-abc123"
}
```

## Pause fine-tuning

Pause a fine-tune job.

### Example request

```bash
curl -X POST https://api.openai.com/v1/fine_tuning/jobs/ftjob-abc123/pause \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "object": "fine_tuning.job",
  "id": "ftjob-abc123",
  "model": "gpt-4o-mini-2024-07-18",
  "created_at": 1721764800,
  "fine_tuned_model": null,
  "organization_id": "org-123",
  "result_files": [],
  "status": "paused",
  "validation_file": "file-abc123",
  "training_file": "file-abc123"
}
```

## Training format for chat models using the supervised method

The per-line training example of a fine-tuning input file for chat models using the supervised method. Input messages may contain text or image content only. Audio and file input messages are not currently supported for fine-tuning.

### Parameters

#### functions - array - Deprecated
A list of functions the model may generate JSON inputs for.

- **name - string**
  The name of the function to be called. Must be a-z, A-Z, 0-9, or contain underscores and dashes, with a maximum length of 64.

- **description - string**
  A description of what the function does, used by the model to choose when and how to call the function.

- **parameters - object**
  The parameters the functions accepts, described as a JSON Schema object. See the [guide](https://platform.openai.com/docs/guides/function-calling) for examples, and the [JSON Schema reference](https://json-schema.org/understanding-json-schema/) for documentation about the format.

  Omitting `parameters` defines a function with an empty parameter list.

#### messages - array
Developer-provided instructions that the model should follow, regardless of messages sent by the user. With o1 models and newer, use `developer` messages for this purpose instead.

- **System message - object**
  Developer-provided instructions that the model should follow, regardless of messages sent by the user. With o1 models and newer, use `developer` messages for this purpose instead.

  - **content - string or array**
    The contents of the system message.

    - **Text content - string**
      The contents of the system message.

    - **Array of content parts - array**
      An array of content parts with a defined type. For system messages, only type `text` is supported.

      - **Text content part - object**
        Learn about [text inputs](https://platform.openai.com/docs/guides/text-generation).

        - **text - string**
          The text content.

        - **type - string**
          The type of the content part.

  - **role - string**
    The role of the messages author, in this case `system`.

  - **name - string**
    An optional name for the participant. Provides the model information to differentiate between participants of the same role.

- **User message - object**
  Messages sent by an end user, containing prompts or additional context information.

  - **content - string or array**
    The contents of the user message.

    - **Text content - string**
      The text contents of the message.

    - **Array of content parts - array**
      An array of content parts with a defined type. Supported options differ based on the [model](https://platform.openai.com/docs/models) being used to generate the response. Can contain text, image, or audio inputs.

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

      - **Audio content part - object**
        Learn about [audio inputs](https://platform.openai.com/docs/guides/audio).

        - **input_audio - object**
          Base64 encoded audio data.

          - **data - string**
            Base64 encoded audio data.

          - **format - string**
            The format of the encoded audio data. Currently supports "wav" and "mp3".

        - **type - string**
          The type of the content part. Always `input_audio`.

      - **File content part - object**
        Learn about [file inputs](https://platform.openai.com/docs/guides/text) for text generation.

        - **file - object**
          The base64 encoded file data, used when passing the file to the model as a string.

          - **file_data - string**
            The base64 encoded file data, used when passing the file to the model as a string.

          - **file_id - string**
            The ID of an uploaded file to use as input.

          - **filename - string**
            The name of the file, used when passing the file to the model as a string.

        - **type - string**
          The type of the content part. Always `file`.

  - **role - string**
    The role of the messages author, in this case `user`.

  - **name - string**
    An optional name for the participant. Provides the model information to differentiate between participants of the same role.

- **Assistant message - object**
  Messages sent by the model in response to user messages.

  - **role - string**
    The role of the messages author, in this case `assistant`.

  - **audio - object**
    Data about a previous audio response from the model. [Learn more](https://platform.openai.com/docs/guides/audio).

    - **id - string**
      Unique identifier for a previous audio response from the model.

  - **content - string or array**
    The contents of the assistant message. Required unless `tool_calls` or `function_call` is specified.

    - **Text content - string**
      The contents of the assistant message.

    - **Array of content parts - array**
      An array of content parts with a defined type. Can be one or more of type `text`, or exactly one of type `refusal`.

      - **Text content part - object**
        Learn about [text inputs](https://platform.openai.com/docs/guides/text-generation).

        - **text - string**
          The text content.

        - **type - string**
          The type of the content part.

      - **Refusal content part - object**
        The refusal message generated by the model.

        - **refusal - string**
          The refusal message generated by the model.

        - **type - string**
          The type of the content part.

  - **function_call - object - Deprecated**
    Deprecated and replaced by `tool_calls`. The name and arguments of a function that should be called, as generated by the model.

    - **arguments - string**
      The arguments to call the function with, as generated by the model in JSON format. Note that the model does not always generate valid JSON, and may hallucinate parameters not defined by your function schema. Validate the arguments in your code before calling your function.

    - **name - string**
      The name of the function to call.

  - **name - string**
    An optional name for the participant. Provides the model information to differentiate between participants of the same role.

  - **refusal - string**
    The refusal message by the assistant.

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

  - **weight - integer**
    Controls whether the assistant message is trained against (0 or 1)

- **Tool message - object**
  The contents of the tool message.

  - **content - string or array**
    The contents of the tool message.

    - **Text content - string**
      The contents of the tool message.

    - **Array of content parts - array**
      An array of content parts with a defined type. For tool messages, only type `text` is supported.

      - **Text content part - object**
        Learn about [text inputs](https://platform.openai.com/docs/guides/text-generation).

        - **text - string**
          The text content.

        - **type - string**
          The type of the content part.

  - **role - string**
    The role of the messages author, in this case `tool`.

  - **tool_call_id - string**
    Tool call that this message is responding to.

- **Function message - object - Deprecated**
  The contents of the function message.

  - **content - string**
    The contents of the function message.

  - **name - string**
    The name of the function to call.

  - **role - string**
    The role of the messages author, in this case `function`.

#### parallel_tool_calls - boolean
Whether to enable [parallel function calling](https://platform.openai.com/docs/guides/function-calling#configuring-parallel-function-calling) during tool use.

#### tools - array
A list of tools the model may generate JSON inputs for.

- **function - object**
  The name of the function to be called. Must be a-z, A-Z, 0-9, or contain underscores and dashes, with a maximum length of 64.

  - **name - string**
    The name of the function to be called. Must be a-z, A-Z, 0-9, or contain underscores and dashes, with a maximum length of 64.

  - **description - string**
    A description of what the function does, used by the model to choose when and how to call the function.

  - **parameters - object**
    The parameters the functions accepts, described as a JSON Schema object. See the [guide](https://platform.openai.com/docs/guides/function-calling) for examples, and the [JSON Schema reference](https://json-schema.org/understanding-json-schema/) for documentation about the format.

    Omitting `parameters` defines a function with an empty parameter list.

  - **strict - boolean**
    Whether to enable strict schema adherence when generating the function call. If set to true, the model will follow the exact schema defined in the `parameters` field. Only a subset of JSON Schema is supported when `strict` is `true`. Learn more about Structured Outputs in the [function calling guide](https://platform.openai.com/docs/guides/function-calling).

- **type - string**
  The type of the tool. Currently, only `function` is supported.

### OBJECT Training format for chat models using the supervised method

```json
{
  "messages": [
    { "role": "user", "content": "What is the weather in San Francisco?" },
    {
      "role": "assistant",
      "tool_calls": [
        {
          "id": "call_id",
          "type": "function",
          "function": {
            "name": "get_current_weather",
            "arguments": "{\"location\": \"San Francisco, USA\", \"format\": \"celsius\"}"
          }
        }
      ]
    }
  ],
  "parallel_tool_calls": false,
  "tools": [
    {
      "type": "function",
      "function": {
        "name": "get_current_weather",
        "description": "Get the current weather",
        "parameters": {
          "type": "object",
          "properties": {
            "location": {
                "type": "string",
                "description": "The city and country, eg. San Francisco, USA"
            },
            "format": { "type": "string", "enum": ["celsius", "fahrenheit"] }
          },
          "required": ["location", "format"]
        }
      }
    }
  ]
}
```

## Training format for chat models using the preference method

The per-line training example of a fine-tuning input file for chat models using the dpo method. Input messages may contain text or image content only. Audio and file input messages are not currently supported for fine-tuning.

### Parameters

#### input - object
Developer-provided instructions that the model should follow, regardless of messages sent by the user. With o1 models and newer, use `developer` messages for this purpose instead.

- **messages - array**
  Developer-provided instructions that the model should follow, regardless of messages sent by the user. With o1 models and newer, use `developer` messages for this purpose instead.

  - **System message - object**
    Developer-provided instructions that the model should follow, regardless of messages sent by the user. With o1 models and newer, use `developer` messages for this purpose instead.

    - **content - string or array**
      The contents of the system message.

      - **Text content - string**
        The contents of the system message.

      - **Array of content parts - array**
        An array of content parts with a defined type. For system messages, only type `text` is supported.

        - **Text content part - object**
          Learn about [text inputs](https://platform.openai.com/docs/guides/text-generation).

          - **text - string**
            The text content.

          - **type - string**
            The type of the content part.

    - **role - string**
      The role of the messages author, in this case `system`.

    - **name - string**
      An optional name for the participant. Provides the model information to differentiate between participants of the same role.

  - **User message - object**
    Messages sent by an end user, containing prompts or additional context information.

    - **content - string or array**
      The contents of the user message.

      - **Text content - string**
        The text contents of the message.

      - **Array of content parts - array**
        An array of content parts with a defined type. Supported options differ based on the [model](https://platform.openai.com/docs/models) being used to generate the response. Can contain text, image, or audio inputs.

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

        - **Audio content part - object**
          Learn about [audio inputs](https://platform.openai.com/docs/guides/audio).

          - **input_audio - object**
            Base64 encoded audio data.

            - **data - string**
              Base64 encoded audio data.

            - **format - string**
              The format of the encoded audio data. Currently supports "wav" and "mp3".

          - **type - string**
            The type of the content part. Always `input_audio`.

        - **File content part - object**
          Learn about [file inputs](https://platform.openai.com/docs/guides/text) for text generation.

          - **file - object**
            The base64 encoded file data, used when passing the file to the model as a string.

            - **file_data - string**
              The base64 encoded file data, used when passing the file to the model as a string.

            - **file_id - string**
              The ID of an uploaded file to use as input.

            - **filename - string**
              The name of the file, used when passing the file to the model as a string.

          - **type - string**
            The type of the content part. Always `file`.

    - **role - string**
      The role of the messages author, in this case `user`.

    - **name - string**
      An optional name for the participant. Provides the model information to differentiate between participants of the same role.

  - **Assistant message - object**
    Messages sent by the model in response to user messages.

    - **role - string**
      The role of the messages author, in this case `assistant`.

    - **audio - object**
      Data about a previous audio response from the model. [Learn more](https://platform.openai.com/docs/guides/audio).

      - **id - string**
        Unique identifier for a previous audio response from the model.

    - **content - string or array**
      The contents of the assistant message. Required unless `tool_calls` or `function_call` is specified.

      - **Text content - string**
        The contents of the assistant message.

      - **Array of content parts - array**
        An array of content parts with a defined type. Can be one or more of type `text`, or exactly one of type `refusal`.

        - **Text content part - object**
          Learn about [text inputs](https://platform.openai.com/docs/guides/text-generation).

          - **text - string**
            The text content.

          - **type - string**
            The type of the content part.

        - **Refusal content part - object**
          The refusal message generated by the model.

          - **refusal - string**
            The refusal message generated by the model.

          - **type - string**
            The type of the content part.

    - **function_call - object - Deprecated**
      Deprecated and replaced by `tool_calls`. The name and arguments of a function that should be called, as generated by the model.

      - **arguments - string**
        The arguments to call the function with, as generated by the model in JSON format. Note that the model does not always generate valid JSON, and may hallucinate parameters not defined by your function schema. Validate the arguments in your code before calling your function.

      - **name - string**
        The name of the function to call.

    - **name - string**
      An optional name for the participant. Provides the model information to differentiate between participants of the same role.

    - **refusal - string**
      The refusal message by the assistant.

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

    - **weight - integer**
      Controls whether the assistant message is trained against (0 or 1)

  - **Tool message - object**
    The contents of the tool message.

    - **content - string or array**
      The contents of the tool message.

      - **Text content - string**
        The contents of the tool message.

      - **Array of content parts - array**
        An array of content parts with a defined type. For tool messages, only type `text` is supported.

        - **Text content part - object**
          Learn about [text inputs](https://platform.openai.com/docs/guides/text-generation).

          - **text - string**
            The text content.

          - **type - string**
            The type of the content part.

    - **role - string**
      The role of the messages author, in this case `tool`.

    - **tool_call_id - string**
      Tool call that this message is responding to.

  - **Function message - object - Deprecated**
    The contents of the function message.

    - **content - string**
      The contents of the function message.

    - **name - string**
      The name of the function to call.

    - **role - string**
      The role of the messages author, in this case `function`.

- **parallel_tool_calls - boolean**
  Whether to enable [parallel function calling](https://platform.openai.com/docs/guides/function-calling#configuring-parallel-function-calling) during tool use.

- **tools - array**
  A list of tools the model may generate JSON inputs for.

  - **function - object**
    The name of the function to be called. Must be a-z, A-Z, 0-9, or contain underscores and dashes, with a maximum length of 64.

    - **name - string**
      The name of the function to be called. Must be a-z, A-Z, 0-9, or contain underscores and dashes, with a maximum length of 64.

    - **description - string**
      A description of what the function does, used by the model to choose when and how to call the function.

    - **parameters - object**
      The parameters the functions accepts, described as a JSON Schema object. See the [guide](https://platform.openai.com/docs/guides/function-calling) for examples, and the [JSON Schema reference](https://json-schema.org/understanding-json-schema/) for documentation about the format.

      Omitting `parameters` defines a function with an empty parameter list.

    - **strict - boolean**
      Whether to enable strict schema adherence when generating the function call. If set to true, the model will follow the exact schema defined in the `parameters` field. Only a subset of JSON Schema is supported when `strict` is `true`. Learn more about Structured Outputs in the [function calling guide](https://platform.openai.com/docs/guides/function-calling).

  - **type - string**
    The type of the tool. Currently, only `function` is supported.

#### non_preferred_output - array
The non-preferred completion message for the output.

- **Assistant message - object**
  Messages sent by the model in response to user messages.

  - **role - string**
    The role of the messages author, in this case `assistant`.

  - **audio - object**
    Data about a previous audio response from the model. [Learn more](https://platform.openai.com/docs/guides/audio).

    - **id - string**
      Unique identifier for a previous audio response from the model.

  - **content - string or array**
    The contents of the assistant message. Required unless `tool_calls` or `function_call` is specified.

    - **Text content - string**
      The contents of the assistant message.

    - **Array of content parts - array**
      An array of content parts with a defined type. Can be one or more of type `text`, or exactly one of type `refusal`.

      - **Text content part - object**
        Learn about [text inputs](https://platform.openai.com/docs/guides/text-generation).

        - **text - string**
          The text content.

        - **type - string**
          The type of the content part.

      - **Refusal content part - object**
        The refusal message generated by the model.

        - **refusal - string**
          The refusal message generated by the model.

        - **type - string**
          The type of the content part.

  - **function_call - object - Deprecated**
    Deprecated and replaced by `tool_calls`. The name and arguments of a function that should be called, as generated by the model.

    - **arguments - string**
      The arguments to call the function with, as generated by the model in JSON format. Note that the model does not always generate valid JSON, and may hallucinate parameters not defined by your function schema. Validate the arguments in your code before calling your function.

    - **name - string**
      The name of the function to call.

  - **name - string**
    An optional name for the participant. Provides the model information to differentiate between participants of the same role.

  - **refusal - string**
    The refusal message by the assistant.

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

#### preferred_output - array
The preferred completion message for the output.

- **Assistant message - object**
  Messages sent by the model in response to user messages.

  - **role - string**
    The role of the messages author, in this case `assistant`.

  - **audio - object**
    Data about a previous audio response from the model. [Learn more](https://platform.openai.com/docs/guides/audio).

    - **id - string**
      Unique identifier for a previous audio response from the model.

  - **content - string or array**
    The contents of the assistant message. Required unless `tool_calls` or `function_call` is specified.

    - **Text content - string**
      The contents of the assistant message.

    - **Array of content parts - array**
      An array of content parts with a defined type. Can be one or more of type `text`, or exactly one of type `refusal`.

      - **Text content part - object**
        Learn about [text inputs](https://platform.openai.com/docs/guides/text-generation).

        - **text - string**
          The text content.

        - **type - string**
          The type of the content part.

      - **Refusal content part - object**
        The refusal message generated by the model.

        - **refusal - string**
          The refusal message generated by the model.

        - **type - string**
          The type of the content part.

  - **function_call - object - Deprecated**
    Deprecated and replaced by `tool_calls`. The name and arguments of a function that should be called, as generated by the model.

    - **arguments - string**
      The arguments to call the function with, as generated by the model in JSON format. Note that the model does not always generate valid JSON, and may hallucinate parameters not defined by your function schema. Validate the arguments in your code before calling your function.

    - **name - string**
      The name of the function to call.

  - **name - string**
    An optional name for the participant. Provides the model information to differentiate between participants of the same role.

  - **refusal - string**
    The refusal message by the assistant.

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

### OBJECT Training format for chat models using the preference method

```json
{
  "input": {
    "messages": [
      { "role": "user", "content": "What is the weather in San Francisco?" }
    ]
  },
  "preferred_output": [
    {
      "role": "assistant",
      "content": "The weather in San Francisco is 70 degrees Fahrenheit."
    }
  ],
  "non_preferred_output": [
    {
      "role": "assistant",
      "content": "The weather in San Francisco is 21 degrees Celsius."
    }
  ]
}
```

## Training format for reasoning models using the reinforcement method

Per-line training example for reinforcement fine-tuning. Note that `messages` and `tools` are the only reserved keywords. Any other arbitrary key-value data can be included on training datapoints and will be available to reference during grading under the `{{ item.XXX }}` template variable. Input messages may contain text or image content only. Audio and file input messages are not currently supported for fine-tuning.

### Parameters

#### messages - array
Developer-provided instructions that the model should follow, regardless of messages sent by the user. With o1 models and newer, `developer` messages replace the previous `system` messages.

- **Developer message - object**
  Developer-provided instructions that the model should follow, regardless of messages sent by the user. With o1 models and newer, `developer` messages replace the previous `system` messages.

  - **content - string or array**
    The contents of the developer message.

    - **Text content - string**
      The contents of the developer message.

    - **Array of content parts - array**
      An array of content parts with a defined type. For developer messages, only type `text` is supported.

      - **text - string**
        The text content.

      - **type - string**
        The type of the content part.

  - **role - string**
    The role of the messages author, in this case `developer`.

  - **name - string**
    An optional name for the participant. Provides the model information to differentiate between participants of the same role.

- **User message - object**
  Messages sent by an end user, containing prompts or additional context information.

  - **content - string or array**
    The contents of the user message.

    - **Text content - string**
      The text contents of the message.

    - **Array of content parts - array**
      An array of content parts with a defined type. Supported options differ based on the [model](https://platform.openai.com/docs/models) being used to generate the response. Can contain text, image, or audio inputs.

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

      - **Audio content part - object**
        Learn about [audio inputs](https://platform.openai.com/docs/guides/audio).

        - **input_audio - object**
          Base64 encoded audio data.

          - **data - string**
            Base64 encoded audio data.

          - **format - string**
            The format of the encoded audio data. Currently supports "wav" and "mp3".

        - **type - string**
          The type of the content part. Always `input_audio`.

      - **File content part - object**
        Learn about [file inputs](https://platform.openai.com/docs/guides/text) for text generation.

        - **file - object**
          The base64 encoded file data, used when passing the file to the model as a string.

          - **file_data - string**
            The base64 encoded file data, used when passing the file to the model as a string.

          - **file_id - string**
            The ID of an uploaded file to use as input.

          - **filename - string**
            The name of the file, used when passing the file to the model as a string.

        - **type - string**
          The type of the content part. Always `file`.

  - **role - string**
    The role of the messages author, in this case `user`.

  - **name - string**
    An optional name for the participant. Provides the model information to differentiate between participants of the same role.

- **Assistant message - object**
  Messages sent by the model in response to user messages.

  - **role - string**
    The role of the messages author, in this case `assistant`.

  - **audio - object**
    Data about a previous audio response from the model. [Learn more](https://platform.openai.com/docs/guides/audio).

    - **id - string**
      Unique identifier for a previous audio response from the model.

  - **content - string or array**
    The contents of the assistant message. Required unless `tool_calls` or `function_call` is specified.

    - **Text content - string**
      The contents of the assistant message.

    - **Array of content parts - array**
      An array of content parts with a defined type. Can be one or more of type `text`, or exactly one of type `refusal`.

      - **Text content part - object**
        Learn about [text inputs](https://platform.openai.com/docs/guides/text-generation).

        - **text - string**
          The text content.

        - **type - string**
          The type of the content part.

      - **Refusal content part - object**
        The refusal message generated by the model.

        - **refusal - string**
          The refusal message generated by the model.

        - **type - string**
          The type of the content part.

  - **function_call - object - Deprecated**
    Deprecated and replaced by `tool_calls`. The name and arguments of a function that should be called, as generated by the model.

    - **arguments - string**
      The arguments to call the function with, as generated by the model in JSON format. Note that the model does not always generate valid JSON, and may hallucinate parameters not defined by your function schema. Validate the arguments in your code before calling your function.

    - **name - string**
      The name of the function to call.

  - **name - string**
    An optional name for the participant. Provides the model information to differentiate between participants of the same role.

  - **refusal - string**
    The refusal message by the assistant.

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

  - **weight - integer**
    Controls whether the assistant message is trained against (0 or 1)

- **Tool message - object**
  The contents of the tool message.

  - **content - string or array**
    The contents of the tool message.

    - **Text content - string**
      The contents of the tool message.

    - **Array of content parts - array**
      An array of content parts with a defined type. For tool messages, only type `text` is supported.

      - **Text content part - object**
        Learn about [text inputs](https://platform.openai.com/docs/guides/text-generation).

        - **text - string**
          The text content.

        - **type - string**
          The type of the content part.

  - **role - string**
    The role of the messages author, in this case `tool`.

  - **tool_call_id - string**
    Tool call that this message is responding to.

#### tools - array
A list of tools the model may generate JSON inputs for.

- **function - object**
  The name of the function to be called. Must be a-z, A-Z, 0-9, or contain underscores and dashes, with a maximum length of 64.

  - **name - string**
    The name of the function to be called. Must be a-z, A-Z, 0-9, or contain underscores and dashes, with a maximum length of 64.

  - **description - string**
    A description of what the function does, used by the model to choose when and how to call the function.

  - **parameters - object**
    The parameters the functions accepts, described as a JSON Schema object. See the [guide](https://platform.openai.com/docs/guides/function-calling) for examples, and the [JSON Schema reference](https://json-schema.org/understanding-json-schema/) for documentation about the format.

    Omitting `parameters` defines a function with an empty parameter list.

  - **strict - boolean**
    Whether to enable strict schema adherence when generating the function call. If set to true, the model will follow the exact schema defined in the `parameters` field. Only a subset of JSON Schema is supported when `strict` is `true`. Learn more about Structured Outputs in the [function calling guide](https://platform.openai.com/docs/guides/function-calling).

- **type - string**
  The type of the tool. Currently, only `function` is supported.

### OBJECT Training format for reasoning models using the reinforcement method

```json
{
  "messages": [
    {
      "role": "user",
      "content": "Your task is to take a chemical in SMILES format and predict the number of hydrobond bond donors and acceptors according to Lipinkski's rule. CCN(CC)CCC(=O)c1sc(N)nc1C"
    },
  ],
  # Any other JSON data can be inserted into an example and referenced during RFT grading
  "reference_answer": {
    "donor_bond_counts": 5,
    "acceptor_bond_counts": 7
  }
}
```

## The fine-tuning job object

The `fine_tuning.job` object represents a fine-tuning job that has been created through the API.

### Parameters

#### created_at - integer
The Unix timestamp (in seconds) for when the fine-tuning job was created.

#### error - object
For fine-tuning jobs that have `failed`, this will contain more information on the cause of the failure.

- **code - string**
  A machine-readable error code.

- **message - string**
  A human-readable error message.

- **param - string**
  The parameter that was invalid, usually `training_file` or `validation_file`. This field will be null if the failure was not parameter-specific.

#### estimated_finish - integer
The Unix timestamp (in seconds) for when the fine-tuning job is estimated to finish. The value will be null if the fine-tuning job is not running.

#### fine_tuned_model - string
The name of the fine-tuned model that is being created. The value will be null if the fine-tuning job is still running.

#### finished_at - integer
The Unix timestamp (in seconds) for when the fine-tuning job was finished. The value will be null if the fine-tuning job is still running.

#### hyperparameters - object
The hyperparameters used for the fine-tuning job. This value will only be returned when running `supervised` jobs.

- **batch_size - "auto" or integer**
  Number of examples in each batch. A larger batch size means that model parameters are updated less frequently, but with lower variance.

- **learning_rate_multiplier - "auto" or number**
  Scaling factor for the learning rate. A smaller learning rate may be useful to avoid overfitting.

- **n_epochs - "auto" or integer**
  The number of epochs to train the model for. An epoch refers to one full cycle through the training dataset.

#### id - string
The object identifier, which can be referenced in the API endpoints.

#### integrations - array
A list of integrations to enable for this fine-tuning job.

- **Fine-Tuning Job Integration - object**
  The type of the integration being enabled for the fine-tuning job

  - **type - string**
    The type of the integration being enabled for the fine-tuning job

  - **wandb - object**
    The settings for your integration with Weights and Biases. This payload specifies the project that metrics will be sent to. Optionally, you can set an explicit display name for your run, add tags to your run, and set a default entity (team, username, etc) to be associated with your run.

    - **project - string**
      The name of the project that the new run will be created under.

    - **entity - string**
      The entity to use for the run. This allows you to set the team or username of the WandB user that you would like associated with the run. If not set, the default entity for the registered WandB API key is used.

    - **name - string**
      A display name to set for the run. If not set, we will use the Job ID as the name.

    - **tags - array**
      A list of tags to be attached to the newly created run. These tags are passed through directly to WandB. Some default tags are generated by OpenAI: "openai/finetune", "openai/\{base-model\}", "openai/\{ftjob-abcdef\}".

#### metadata - map
Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard.

Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters.

#### method - object
The method used for fine-tuning.

- **type - string**
  The type of method. Is either `supervised`, `dpo`, or `reinforcement`.

- **dpo - object**
  Configuration for the DPO fine-tuning method.

  - **hyperparameters - object**
    The hyperparameters used for the DPO fine-tuning job.

    - **batch_size - "auto" or integer**
      Number of examples in each batch. A larger batch size means that model parameters are updated less frequently, but with lower variance.

    - **beta - "auto" or number**
      The beta value for the DPO method. A higher beta value will increase the weight of the penalty between the policy and reference model.

    - **learning_rate_multiplier - "auto" or number**
      Scaling factor for the learning rate. A smaller learning rate may be useful to avoid overfitting.

    - **n_epochs - "auto" or integer**
      The number of epochs to train the model for. An epoch refers to one full cycle through the training dataset.

- **reinforcement - object**
  Configuration for the reinforcement fine-tuning method.

  - **grader - object**
    The grader used for the fine-tuning job.

    - **StringCheckGrader - object**
      A StringCheckGrader object that performs a string comparison between input and reference using a specified operation.

      - **input - string**
        The input text. This may include template strings.

      - **name - string**
        The name of the grader.

      - **operation - string**
        The string check operation to perform. One of `eq`, `ne`, `like`, or `ilike`.

      - **reference - string**
        The reference text. This may include template strings.

      - **type - string**
        The object type, which is always `string_check`.

    - **TextSimilarityGrader - object**
      A TextSimilarityGrader object which grades text based on similarity metrics.

      - **evaluation_metric - string**
        The evaluation metric to use. One of `cosine`, `fuzzy_match`, `bleu`, `gleu`, `meteor`, `rouge_1`, `rouge_2`, `rouge_3`, `rouge_4`, `rouge_5`, or `rouge_l`.

      - **input - string**
        The text being graded.

      - **name - string**
        The name of the grader.

      - **reference - string**
        The text being graded against.

      - **type - string**
        The type of grader.

    - **PythonGrader - object**
      A PythonGrader object that runs a python script on the input.

      - **name - string**
        The name of the grader.

      - **source - string**
        The source code of the python script.

      - **type - string**
        The object type, which is always `python`.

      - **image_tag - string**
        The image tag to use for the python script.

    - **ScoreModelGrader - object**
      A ScoreModelGrader object that uses a model to assign a score to the input.

      - **input - array**
        The input text. This may include template strings.

        - **content - string / object / array**
          Inputs to the model - can contain template strings.

          - **Text input - string**
            A text input to the model.

          - **Input text - object**
            A text input to the model.

            - **text - string**
              The text input to the model.

            - **type - string**
              The type of the input item. Always `input_text`.

          - **Output text - object**
            A text output from the model.

            - **text - string**
              The text output from the model.

            - **type - string**
              The type of the output text. Always `output_text`.

          - **Input image - object**
            An image input to the model.

            - **image_url - string**
              The URL of the image input.

            - **type - string**
              The type of the image input. Always `input_image`.

            - **detail - string**
              The detail level of the image to be sent to the model. One of `high`, `low`, or `auto`. Defaults to `auto`.

          - **Input audio - object**
            An audio input to the model.

            - **input_audio - object**
              Base64-encoded audio data.

              - **data - string**
                Base64-encoded audio data.

              - **format - string**
                The format of the audio data. Currently supported formats are `mp3` and `wav`.

            - **type - string**
              The type of the input item. Always `input_audio`.

          - **An array of Input text, Input image, and Input audio - array**
            A list of inputs, each of which may be either an input text, input image, or input audio object.

        - **role - string**
          The role of the message input. One of `user`, `assistant`, `system`, or `developer`.

        - **type - string**
          The type of the message input. Always `message`.

      - **model - string**
        The model to use for the evaluation.

      - **name - string**
        The name of the grader.

      - **type - string**
        The object type, which is always `score_model`.

      - **range - array**
        The range of the score. Defaults to `[0, 1]`.

      - **sampling_params - object**
        The sampling parameters for the model.

        - **max_completions_tokens - integer**
          The maximum number of tokens the grader model may generate in its response.

        - **reasoning_effort - string**
          Constrains effort on reasoning for [reasoning models](https://platform.openai.com/docs/guides/reasoning). Currently supported values are `none`, `minimal`, `low`, `medium`, and `high`. Reducing reasoning effort can result in faster responses and fewer tokens used on reasoning in a response.

            * `gpt-5.1` defaults to `none`, which does not perform reasoning. The supported reasoning values for `gpt-5.1` are `none`, `low`, `medium`, and `high`. Tool calls are supported for all reasoning values in gpt-5.1.
            * All models before `gpt-5.1` default to `medium` reasoning effort, and do not support `none`.
            * The `gpt-5-pro` model defaults to (and only supports) `high` reasoning effort.

        - **seed - integer**
          A seed value to initialize the randomness, during sampling.

        - **temperature - number**
          A higher temperature increases randomness in the outputs.

        - **top_p - number**
          An alternative to temperature for nucleus sampling; 1.0 includes all tokens.

    - **MultiGrader - object**
      A MultiGrader object combines the output of multiple graders to produce a single score.

      - **calculate_output - string**
        A formula to calculate the output based on grader results.

      - **graders - object**
        A StringCheckGrader object that performs a string comparison between input and reference using a specified operation.

        - **StringCheckGrader - object**
          A StringCheckGrader object that performs a string comparison between input and reference using a specified operation.

          - **input - string**
            The input text. This may include template strings.

          - **name - string**
            The name of the grader.

          - **operation - string**
            The string check operation to perform. One of `eq`, `ne`, `like`, or `ilike`.

          - **reference - string**
            The reference text. This may include template strings.

          - **type - string**
            The object type, which is always `string_check`.

        - **TextSimilarityGrader - object**
          A TextSimilarityGrader object which grades text based on similarity metrics.

          - **evaluation_metric - string**
            The evaluation metric to use. One of `cosine`, `fuzzy_match`, `bleu`, `gleu`, `meteor`, `rouge_1`, `rouge_2`, `rouge_3`, `rouge_4`, `rouge_5`, or `rouge_l`.

          - **input - string**
            The text being graded.

          - **name - string**
            The name of the grader.

          - **reference - string**
            The text being graded against.

          - **type - string**
            The type of grader.

        - **PythonGrader - object**
          A PythonGrader object that runs a python script on the input.

          - **name - string**
            The name of the grader.

          - **source - string**
            The source code of the python script.

          - **type - string**
            The object type, which is always `python`.

          - **image_tag - string**
            The image tag to use for the python script.

        - **ScoreModelGrader - object**
          A ScoreModelGrader object that uses a model to assign a score to the input.

          - **input - array**
            The input text. This may include template strings.

            - **content - string / object / array**
              Inputs to the model - can contain template strings.

              - **Text input - string**
                A text input to the model.

              - **Input text - object**
                A text input to the model.

                - **text - string**
                  The text input to the model.

                - **type - string**
                  The type of the input item. Always `input_text`.

              - **Output text - object**
                A text output from the model.

                - **text - string**
                  The text output from the model.

                - **type - string**
                  The type of the output text. Always `output_text`.

              - **Input image - object**
                An image input to the model.

                - **image_url - string**
                  The URL of the image input.

                - **type - string**
                  The type of the image input. Always `input_image`.

                - **detail - string**
                  The detail level of the image to be sent to the model. One of `high`, `low`, or `auto`. Defaults to `auto`.

              - **Input audio - object**
                An audio input to the model.

                - **input_audio - object**
                  Base64-encoded audio data.

                  - **data - string**
                    Base64-encoded audio data.

                  - **format - string**
                    The format of the audio data. Currently supported formats are `mp3` and `wav`.

                - **type - string**
                  The type of the input item. Always `input_audio`.

              - **An array of Input text, Input image, and Input audio - array**
                A list of inputs, each of which may be either an input text, input image, or input audio object.

            - **role - string**
              The role of the message input. One of `user`, `assistant`, `system`, or `developer`.

            - **type - string**
              The type of the message input. Always `message`.

          - **model - string**
            The model to use for the evaluation.

          - **name - string**
            The name of the grader.

          - **type - string**
            The object type, which is always `score_model`.

          - **range - array**
            The range of the score. Defaults to `[0, 1]`.

          - **sampling_params - object**
            The sampling parameters for the model.

            - **max_completions_tokens - integer**
              The maximum number of tokens the grader model may generate in its response.

            - **reasoning_effort - string**
              Constrains effort on reasoning for [reasoning models](https://platform.openai.com/docs/guides/reasoning). Currently supported values are `none`, `minimal`, `low`, `medium`, and `high`. Reducing reasoning effort can result in faster responses and fewer tokens used on reasoning in a response.

                * `gpt-5.1` defaults to `none`, which does not perform reasoning. The supported reasoning values for `gpt-5.1` are `none`, `low`, `medium`, and `high`. Tool calls are supported for all reasoning values in gpt-5.1.
                * All models before `gpt-5.1` default to `medium` reasoning effort, and do not support `none`.
                * The `gpt-5-pro` model defaults to (and only supports) `high` reasoning effort.

            - **seed - integer**
              A seed value to initialize the randomness, during sampling.

            - **temperature - number**
              A higher temperature increases randomness in the outputs.

            - **top_p - number**
              An alternative to temperature for nucleus sampling; 1.0 includes all tokens.

        - **LabelModelGrader - object**
          A LabelModelGrader object which uses a model to assign labels to each item in the evaluation.

          - **input - array**
            Inputs to the model - can contain template strings.

            - **content - string / object / array**
              Inputs to the model - can contain template strings.

              - **Text input - string**
                A text input to the model.

              - **Input text - object**
                A text input to the model.

                - **text - string**
                  The text input to the model.

                - **type - string**
                  The type of the input item. Always `input_text`.

              - **Output text - object**
                A text output from the model.

                - **text - string**
                  The text output from the model.

                - **type - string**
                  The type of the output text. Always `output_text`.

              - **Input image - object**
                An image input to the model.

                - **image_url - string**
                  The URL of the image input.

                - **type - string**
                  The type of the image input. Always `input_image`.

                - **detail - string**
                  The detail level of the image to be sent to the model. One of `high`, `low`, or `auto`. Defaults to `auto`.

              - **Input audio - object**
                An audio input to the model.

                - **input_audio - object**
                  Base64-encoded audio data.

                  - **data - string**
                    Base64-encoded audio data.

                  - **format - string**
                    The format of the audio data. Currently supported formats are `mp3` and `wav`.

                - **type - string**
                  The type of the input item. Always `input_audio`.

              - **An array of Input text, Input image, and Input audio - array**
                A list of inputs, each of which may be either an input text, input image, or input audio object.

            - **role - string**
              The role of the message input. One of `user`, `assistant`, `system`, or `developer`.

            - **type - string**
              The type of the message input. Always `message`.

          - **labels - array**
            The labels to assign to each item in the evaluation.

          - **model - string**
            The model to use for the evaluation. Must support structured outputs.

          - **name - string**
            The name of the grader.

          - **passing_labels - array**
            The labels that indicate a passing result. Must be a subset of labels.

          - **type - string**
            The object type, which is always `label_model`.

      - **name - string**
        The name of the grader.

      - **type - string**
        The object type, which is always `multi`.

  - **hyperparameters - object**
    The hyperparameters used for the reinforcement fine-tuning job.

    - **batch_size - "auto" or integer**
      Number of examples in each batch. A larger batch size means that model parameters are updated less frequently, but with lower variance.

    - **compute_multiplier - "auto" or number**
      Multiplier on amount of compute used for exploring search space during training.

    - **eval_interval - "auto" or integer**
      The number of training steps between evaluation runs.

    - **eval_samples - "auto" or integer**
      Number of evaluation samples to generate per training step.

    - **learning_rate_multiplier - "auto" or number**
      Scaling factor for the learning rate. A smaller learning rate may be useful to avoid overfitting.

    - **n_epochs - "auto" or integer**
      The number of epochs to train the model for. An epoch refers to one full cycle through the training dataset.

    - **reasoning_effort - string**
      Level of reasoning effort.

- **supervised - object**
  Configuration for the supervised fine-tuning method.

  - **hyperparameters - object**
    The hyperparameters used for the fine-tuning job.

    - **batch_size - "auto" or integer**
      Number of examples in each batch. A larger batch size means that model parameters are updated less frequently, but with lower variance.

    - **learning_rate_multiplier - "auto" or number**
      Scaling factor for the learning rate. A smaller learning rate may be useful to avoid overfitting.

    - **n_epochs - "auto" or integer**
      The number of epochs to train the model for. An epoch refers to one full cycle through the training dataset.

#### model - string
The base model that is being fine-tuned.

#### object - string
The object type, which is always "fine_tuning.job".

#### organization_id - string
The organization that owns the fine-tuning job.

#### result_files - array
The compiled results file ID(s) for the fine-tuning job. You can retrieve the results with the [Files API](https://platform.openai.com/docs/api-reference/files/retrieve-contents).

#### seed - integer
The seed used for the fine-tuning job.

#### status - string
The current status of the fine-tuning job, which can be either `validating_files`, `queued`, `running`, `succeeded`, `failed`, or `cancelled`.

#### trained_tokens - integer
The total number of billable tokens processed by this fine-tuning job. The value will be null if the fine-tuning job is still running.

#### training_file - string
The file ID used for training. You can retrieve the training data with the [Files API](https://platform.openai.com/docs/api-reference/files/retrieve-contents).

#### validation_file - string
The file ID used for validation. You can retrieve the validation results with the [Files API](https://platform.openai.com/docs/api-reference/files/retrieve-contents).

### OBJECT The fine-tuning job object

```json
{
  "object": "fine_tuning.job",
  "id": "ftjob-abc123",
  "model": "davinci-002",
  "created_at": 1692661014,
  "finished_at": 1692661190,
  "fine_tuned_model": "ft:davinci-002:my-org:custom_suffix:7q8mpxmy",
  "organization_id": "org-123",
  "result_files": [
      "file-abc123"
  ],
  "status": "succeeded",
  "validation_file": null,
  "training_file": "file-abc123",
  "hyperparameters": {
      "n_epochs": 4,
      "batch_size": 1,
      "learning_rate_multiplier": 1.0
  },
  "trained_tokens": 5768,
  "integrations": [],
  "seed": 0,
  "estimated_finish": 0,
  "method": {
    "type": "supervised",
    "supervised": {
      "hyperparameters": {
        "n_epochs": 4,
        "batch_size": 1,
        "learning_rate_multiplier": 1.0
      }
    }
  },
  "metadata": {
    "key": "value"
  }
}
```

## The fine-tuning job event object

Fine-tuning job event object

### Parameters

#### created_at - integer
The Unix timestamp (in seconds) for when the fine-tuning job was created.

#### data - object
The data associated with the event.

#### id - string
The object identifier.

#### level - string
The log level of the event.

#### message - string
The message of the event.

#### object - string
The object type, which is always "fine_tuning.job.event".

#### type - string
The type of event.

### OBJECT The fine-tuning job event object

```json
{
  "object": "fine_tuning.job.event",
  "id": "ftevent-abc123"
  "created_at": 1677610602,
  "level": "info",
  "message": "Created fine-tuning job",
  "data": {},
  "type": "message"
}
```

## The fine-tuning job checkpoint object

The `fine_tuning.job.checkpoint` object represents a model checkpoint for a fine-tuning job that is ready to use.

### Parameters

#### created_at - integer
The Unix timestamp (in seconds) for when the checkpoint was created.

#### fine_tuned_model_checkpoint - string
The name of the fine-tuned checkpoint model that is created.

#### fine_tuning_job_id - string
The name of the fine-tuning job that this checkpoint was created from.

#### id - string
The checkpoint identifier, which can be referenced in the API endpoints.

#### metrics - object
Metrics at the step number during the fine-tuning job.

- **full_valid_loss - number**

- **full_valid_mean_token_accuracy - number**

- **step - number**

- **train_loss - number**

- **train_mean_token_accuracy - number**

- **valid_loss - number**

- **valid_mean_token_accuracy - number**

#### object - string
The object type, which is always "fine_tuning.job.checkpoint".

#### step_number - integer
The step number that the checkpoint was created at.

### OBJECT The fine-tuning job checkpoint object

```json
{
  "object": "fine_tuning.job.checkpoint",
  "id": "ftckpt_qtZ5Gyk4BLq1SfLFWp3RtO3P",
  "created_at": 1712211699,
  "fine_tuned_model_checkpoint": "ft:gpt-4o-mini-2024-07-18:my-org:custom_suffix:9ABel2dg:ckpt-step-88",
  "fine_tuning_job_id": "ftjob-fpbNQ3H1GrMehXRf8cO97xTN",
  "metrics": {
    "step": 88,
    "train_loss": 0.478,
    "train_mean_token_accuracy": 0.924,
    "valid_loss": 10.112,
    "valid_mean_token_accuracy": 0.145,
    "full_valid_loss": 0.567,
    "full_valid_mean_token_accuracy": 0.944
  },
  "step_number": 88
}
```

## The fine-tuned model checkpoint permission object

The `checkpoint.permission` object represents a permission for a fine-tuned model checkpoint.

### Parameters

#### created_at - integer
The Unix timestamp (in seconds) for when the permission was created.

#### id - string
The permission identifier, which can be referenced in the API endpoints.

#### object - string
The object type, which is always "checkpoint.permission".

#### project_id - string
The project identifier that the permission is for.

### OBJECT The fine-tuned model checkpoint permission object

```json
{
  "object": "checkpoint.permission",
  "id": "cp_zc4Q7MP6XxulcVzj4MZdwsAB",
  "created_at": 1712211699,
  "project_id": "proj_abGMw1llN8IrBb6SvvY5A1iH"
}
```