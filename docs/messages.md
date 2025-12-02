# MessagesBeta

Create messages within threads

Related guide: [Assistants](https://platform.openai.com/docs/assistants/overview)

## Create messageBeta

Create a message.

### Example request

```bash
curl https://api.openai.com/v1/threads/thread_abc123/messages \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "OpenAI-Beta: assistants=v2" \
  -d '{
      "role": "user",
      "content": "How does AI work? Explain it in simple terms."
    }'
```

### Response

```json
{
  "id": "msg_abc123",
  "object": "thread.message",
  "created_at": 1713226573,
  "assistant_id": null,
  "thread_id": "thread_abc123",
  "run_id": null,
  "role": "user",
  "content": [
    {
      "type": "text",
      "text": {
        "value": "How does AI work? Explain it in simple terms.",
        "annotations": []
      }
    }
  ],
  "attachments": [],
  "metadata": {}
}
```

## List messagesBeta

Returns a list of messages for a given thread.

### Example request

```bash
curl https://api.openai.com/v1/threads/thread_abc123/messages \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "OpenAI-Beta: assistants=v2"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "id": "msg_abc123",
      "object": "thread.message",
      "created_at": 1699016383,
      "assistant_id": null,
      "thread_id": "thread_abc123",
      "run_id": null,
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": {
            "value": "How does AI work? Explain it in simple terms.",
            "annotations": []
          }
        }
      ],
      "attachments": [],
      "metadata": {}
    },
    {
      "id": "msg_abc456",
      "object": "thread.message",
      "created_at": 1699016383,
      "assistant_id": null,
      "thread_id": "thread_abc123",
      "run_id": null,
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": {
            "value": "Hello, what is AI?",
            "annotations": []
          }
        }
      ],
      "attachments": [],
      "metadata": {}
    }
  ],
  "first_id": "msg_abc123",
  "last_id": "msg_abc456",
  "has_more": false
}
```

## Retrieve messageBeta

Retrieve a message.

### Example request

```bash
curl https://api.openai.com/v1/threads/thread_abc123/messages/msg_abc123 \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "OpenAI-Beta: assistants=v2"
```

### Response

```json
{
  "id": "msg_abc123",
  "object": "thread.message",
  "created_at": 1699017614,
  "assistant_id": null,
  "thread_id": "thread_abc123",
  "run_id": null,
  "role": "user",
  "content": [
    {
      "type": "text",
      "text": {
        "value": "How does AI work? Explain it in simple terms.",
        "annotations": []
      }
    }
  ],
  "attachments": [],
  "metadata": {}
}
```

## Modify messageBeta

Modifies a message.

### Example request

```bash
curl https://api.openai.com/v1/threads/thread_abc123/messages/msg_abc123 \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "OpenAI-Beta: assistants=v2" \
  -d '{
      "metadata": {
        "modified": "true",
        "user": "abc123"
      }
    }'
```

### Response

```json
{
  "id": "msg_abc123",
  "object": "thread.message",
  "created_at": 1699017614,
  "assistant_id": null,
  "thread_id": "thread_abc123",
  "run_id": null,
  "role": "user",
  "content": [
    {
      "type": "text",
      "text": {
        "value": "How does AI work? Explain it in simple terms.",
        "annotations": []
      }
    }
  ],
  "file_ids": [],
  "metadata": {
    "modified": "true",
    "user": "abc123"
  }
}
```

## Delete messageBeta

Deletes a message.

### Example request

```bash
curl -X DELETE https://api.openai.com/v1/threads/thread_abc123/messages/msg_abc123 \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "OpenAI-Beta: assistants=v2"
```

### Response

```json
{
  "id": "msg_abc123",
  "object": "thread.message.deleted",
  "deleted": true
}
```

## The message objectBeta

Represents a message within a [thread](https://platform.openai.com/docs/api-reference/threads).

### Parameters

#### assistant_id - string
If applicable, the ID of the [assistant](https://platform.openai.com/docs/api-reference/assistants) that authored this message.

#### attachments - array
A list of files attached to the message, and the tools they were added to.

- **file_id - string**
  The ID of the file to attach to the message.

- **tools - array**
  The tools to add this file to.

  - **Code interpreter tool - object**
    The type of tool being defined: `code_interpreter`

    - **type - string**
      The type of tool being defined: `code_interpreter`

  - **FileSearch tool - object**
    The type of tool being defined: `file_search`

    - **type - string**
      The type of tool being defined: `file_search`

#### completed_at - integer
The Unix timestamp (in seconds) for when the message was completed.

#### content - array
The content of the message in array of text and/or images.

- **Image file - object**
  References an image [File](https://platform.openai.com/docs/api-reference/files) in the content of a message.

  - **image_file - object**
    The [File](https://platform.openai.com/docs/api-reference/files) ID of the image in the message content. Set `purpose="vision"` when uploading the File if you need to later display the file content.

    - **file_id - string**
      The [File](https://platform.openai.com/docs/api-reference/files) ID of the image in the message content. Set `purpose="vision"` when uploading the File if you need to later display the file content.

    - **detail - string**
      Specifies the detail level of the image if specified by the user. `low` uses fewer tokens, you can opt in to high resolution using `high`.

  - **type - string**
    Always `image_file`.

- **Image URL - object**
  References an image URL in the content of a message.

  - **image_url - object**
    The external URL of the image, must be a supported image types: jpeg, jpg, png, gif, webp.

    - **url - string**
      The external URL of the image, must be a supported image types: jpeg, jpg, png, gif, webp.

    - **detail - string**
      Specifies the detail level of the image. `low` uses fewer tokens, you can opt in to high resolution using `high`. Default value is `auto`

  - **type - string**
    The type of the content part.

- **Text - object**
  The text content that is part of a message.

  - **text - object**
    A citation within the message that points to a specific quote from a specific File associated with the assistant or the message. Generated when the assistant uses the "file_search" tool to search files.

    - **annotations - array**
      A citation within the message that points to a specific quote from a specific File associated with the assistant or the message. Generated when the assistant uses the "file_search" tool to search files.

      - **File citation - object**
        A citation within the message that points to a specific quote from a specific File associated with the assistant or the message. Generated when the assistant uses the "file_search" tool to search files.

        - **end_index - integer**

        - **file_citation - object**
          The ID of the specific File the citation is from.

          - **file_id - string**
            The ID of the specific File the citation is from.

        - **start_index - integer**

        - **text - string**
          The text in the message content that needs to be replaced.

        - **type - string**
          Always `file_citation`.

      - **File path - object**
        A URL for the file that's generated when the assistant used the `code_interpreter` tool to generate a file.

        - **end_index - integer**

        - **file_path - object**
          The ID of the file that was generated.

          - **file_id - string**
            The ID of the file that was generated.

        - **start_index - integer**

        - **text - string**
          The text in the message content that needs to be replaced.

        - **type - string**
          Always `file_path`.

    - **value - string**
      The data that makes up the text.

  - **type - string**
    Always `text`.

- **Refusal - object**
  The refusal content generated by the assistant.

  - **refusal - string**

  - **type - string**
    Always `refusal`.

#### created_at - integer
The Unix timestamp (in seconds) for when the message was created.

#### id - string
The identifier, which can be referenced in API endpoints.

#### incomplete_at - integer
The Unix timestamp (in seconds) for when the message was marked as incomplete.

#### incomplete_details - object
On an incomplete message, details about why the message is incomplete.

- **reason - string**
  The reason the message is incomplete.

#### metadata - map
Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard.

Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters.

#### object - string
The object type, which is always `thread.message`.

#### role - string
The entity that produced the message. One of `user` or `assistant`.

#### run_id - string
The ID of the [run](https://platform.openai.com/docs/api-reference/runs) associated with the creation of this message. Value is `null` when messages are created manually using the create message or create thread endpoints.

#### status - string
The status of the message, which can be either `in_progress`, `incomplete`, or `completed`.

#### thread_id - string
The [thread](https://platform.openai.com/docs/api-reference/threads) ID that this message belongs to.

### OBJECT The message object

```json
{
  "id": "msg_abc123",
  "object": "thread.message",
  "created_at": 1698983503,
  "thread_id": "thread_abc123",
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": {
        "value": "Hi! How can I help you today?",
        "annotations": []
      }
    }
  ],
  "assistant_id": "asst_abc123",
  "run_id": "run_abc123",
  "attachments": [],
  "metadata": {}
}
```