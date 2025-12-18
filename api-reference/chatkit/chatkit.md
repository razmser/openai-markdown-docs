# ChatKitBeta

Manage ChatKit sessions, threads, and file uploads for internal integrations.

## Create ChatKit sessionBeta

Create a ChatKit session

### Example request

```bash
curl https://api.openai.com/v1/chatkit/sessions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "OpenAI-Beta: chatkit_beta=v1" \
  -d '{
    "workflow": {
      "id": "workflow_alpha",
      "version": "2024-10-01"
    },
    "scope": {
      "project": "alpha",
      "environment": "staging"
    },
    "expires_after": 1800,
    "max_requests_per_1_minute": 60,
    "max_requests_per_session": 500
  }'
```

### Response

```json
{
  "client_secret": "chatkit_token_123",
  "expires_after": 1800,
  "workflow": {
    "id": "workflow_alpha",
    "version": "2024-10-01"
  },
  "scope": {
    "project": "alpha",
    "environment": "staging"
  },
  "max_requests_per_1_minute": 60,
  "max_requests_per_session": 500,
  "status": "active"
}
```

## Cancel chat sessionBeta

Cancel a ChatKit session

### Example request

```bash
curl -X POST \
  https://api.openai.com/v1/chatkit/sessions/cksess_123/cancel \
  -H "OpenAI-Beta: chatkit_beta=v1" \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "id": "cksess_123",
  "object": "chatkit.session",
  "workflow": {
    "id": "workflow_alpha",
    "version": "1"
  },
  "scope": {
    "customer_id": "cust_456"
  },
  "max_requests_per_1_minute": 30,
  "ttl_seconds": 900,
  "status": "cancelled",
  "cancelled_at": 1712345678
}
```

## List ChatKit threadsBeta

List ChatKit threads

### Example request

```bash
curl "https://api.openai.com/v1/chatkit/threads?limit=2&order=desc" \
  -H "OpenAI-Beta: chatkit_beta=v1" \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "data": [
    {
      "id": "cthr_abc123",
      "object": "chatkit.thread",
      "title": "Customer escalation"
    },
    {
      "id": "cthr_def456",
      "object": "chatkit.thread",
      "title": "Demo feedback"
    }
  ],
  "has_more": false,
  "object": "list"
}
```

## Retrieve ChatKit threadBeta

Retrieve a ChatKit thread

### Example request

```bash
curl https://api.openai.com/v1/chatkit/threads/cthr_abc123 \
  -H "OpenAI-Beta: chatkit_beta=v1" \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "id": "cthr_abc123",
  "object": "chatkit.thread",
  "title": "Customer escalation",
  "items": {
    "data": [
      {
        "id": "cthi_user_001",
        "object": "chatkit.thread_item",
        "type": "user_message",
        "content": [
          {
            "type": "input_text",
            "text": "I need help debugging an onboarding issue."
          }
        ],
        "attachments": []
      },
      {
        "id": "cthi_assistant_002",
        "object": "chatkit.thread_item",
        "type": "assistant_message",
        "content": [
          {
            "type": "output_text",
            "text": "Let's start by confirming the workflow version you deployed."
          }
        ]
      }
    ],
    "has_more": false
  }
}
```

## Delete ChatKit threadBeta

Delete a ChatKit thread

### Example request

```javascript
import OpenAI from 'openai';

const client = new OpenAI();

const thread = await client.beta.chat_kit.threads.delete('cthr_123');

console.log(thread.id);
```

## List ChatKit thread itemsBeta

List ChatKit thread items

### Example request

```bash
curl "https://api.openai.com/v1/chatkit/threads/cthr_abc123/items?limit=3" \
  -H "OpenAI-Beta: chatkit_beta=v1" \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "data": [
    {
      "id": "cthi_user_001",
      "object": "chatkit.thread_item",
      "type": "user_message",
      "content": [
        {
          "type": "input_text",
          "text": "I need help debugging an onboarding issue."
        }
      ],
      "attachments": []
    },
    {
      "id": "cthi_assistant_002",
      "object": "chatkit.thread_item",
      "type": "assistant_message",
      "content": [
        {
          "type": "output_text",
          "text": "Let's start by confirming the workflow version you deployed."
        }
      ]
    }
  ],
  "has_more": false,
  "object": "list"
}
```

## The chat session object

Represents a ChatKit session and its resolved configuration.

### Parameters

#### chatkit_configuration - object
Resolved ChatKit feature configuration for the session.

- **automatic_thread_titling - object**
  Automatic thread titling preferences.

  - **enabled - boolean**
    Whether automatic thread titling is enabled.

- **file_upload - object**
  Upload settings for the session.

  - **enabled - boolean**
    Indicates if uploads are enabled for the session.

  - **max_file_size - integer**
    Maximum upload size in megabytes.

  - **max_files - integer**
    Maximum number of uploads allowed during the session.

- **history - object**
  History retention configuration.

  - **enabled - boolean**
    Indicates if chat history is persisted for the session.

  - **recent_threads - integer**
    Number of prior threads surfaced in history views. Defaults to null when all history is retained.

#### client_secret - string
Ephemeral client secret that authenticates session requests.

#### expires_at - integer
Unix timestamp (in seconds) for when the session expires.

#### id - string
Identifier for the ChatKit session.

#### max_requests_per_1_minute - integer
Convenience copy of the per-minute request limit.

#### object - string
Type discriminator that is always `chatkit.session`.

#### rate_limits - object
Resolved rate limit values.

- **max_requests_per_1_minute - integer**
  Maximum allowed requests per one-minute window.

#### status - string
Current lifecycle state of the session.

#### user - string
User identifier associated with the session.

#### workflow - object
Workflow metadata for the session.

- **id - string**
  Identifier of the workflow backing the session.

- **state_variables - map**
  State variable key-value pairs applied when invoking the workflow. Defaults to null when no overrides were provided.

- **tracing - object**
  Tracing settings applied to the workflow.

  - **enabled - boolean**
    Indicates whether tracing is enabled.

- **version - string**
  Specific workflow version used for the session. Defaults to null when using the latest deployment.

### OBJECT The chat session object

```json
{
  "id": "cksess_123",
  "object": "chatkit.session",
  "client_secret": "ek_token_123",
  "expires_at": 1712349876,
  "workflow": {
    "id": "workflow_alpha",
    "version": "2024-10-01"
  },
  "user": "user_789",
  "rate_limits": {
    "max_requests_per_1_minute": 60
  },
  "max_requests_per_1_minute": 60,
  "status": "cancelled",
  "chatkit_configuration": {
    "automatic_thread_titling": {
      "enabled": true
    },
    "file_upload": {
      "enabled": true,
      "max_file_size": 16,
      "max_files": 20
    },
    "history": {
      "enabled": true,
      "recent_threads": 10
    }
  }
}
```

## The thread object

Represents a ChatKit thread and its current status.

### Parameters

#### created_at - integer
Unix timestamp (in seconds) for when the thread was created.

#### id - string
Identifier of the thread.

#### object - string
Type discriminator that is always `chatkit.thread`.

#### status - object
Current status for the thread. Defaults to `active` for newly created threads.

- **Active thread status - object**
  Indicates that a thread is active.

  - **type - string**
    Status discriminator that is always `active`.

- **Locked thread status - object**
  Indicates that a thread is locked and cannot accept new input.

  - **reason - string**
    Reason that the thread was locked. Defaults to null when no reason is recorded.

  - **type - string**
    Status discriminator that is always `locked`.

- **Closed thread status - object**
  Indicates that a thread has been closed.

  - **reason - string**
    Reason that the thread was closed. Defaults to null when no reason is recorded.

  - **type - string**
    Status discriminator that is always `closed`.

#### title - string
Optional human-readable title for the thread. Defaults to null when no title has been generated.

#### user - string
Free-form string that identifies your end user who owns the thread.

### OBJECT The thread object

```json
{
  "id": "cthr_def456",
  "object": "chatkit.thread",
  "created_at": 1712345600,
  "title": "Demo feedback",
  "status": {
    "type": "active"
  },
  "user": "user_456"
}
```

## Thread Items

A paginated list of thread items rendered for the ChatKit API.

### Parameters

#### data - array
A list of items

- **User Message Item - object**
  User-authored messages within a thread.

  - **attachments - array**
    Attachments associated with the user message. Defaults to an empty list.

    - **id - string**
      Identifier for the attachment.

    - **mime_type - string**
      MIME type of the attachment.

    - **name - string**
      Original display name for the attachment.

    - **preview_url - string**
      Preview URL for rendering the attachment inline.

    - **type - string**
      Attachment discriminator.

  - **content - array**
    Ordered content elements supplied by the user.

    - **User message input - object**
      Text block that a user contributed to the thread.

      - **text - string**
        Plain-text content supplied by the user.

      - **type - string**
        Type discriminator that is always `input_text`.

    - **User message quoted text - object**
      Quoted snippet that the user referenced in their message.

      - **text - string**
        Quoted text content.

      - **type - string**
        Type discriminator that is always `quoted_text`.

  - **created_at - integer**
    Unix timestamp (in seconds) for when the item was created.

  - **id - string**
    Identifier of the thread item.

  - **inference_options - object**
    Inference overrides applied to the message. Defaults to null when unset.

    - **model - string**
      Model name that generated the response. Defaults to null when using the session default.

    - **tool_choice - object**
      Preferred tool to invoke. Defaults to null when ChatKit should auto-select.

      - **id - string**
        Identifier of the requested tool.

  - **object - string**
    Type discriminator that is always `chatkit.thread_item`.

  - **thread_id - string**
    Identifier of the parent thread.

  - **type - string**

- **Assistant message - object**
  Assistant-authored message within a thread.

  - **content - array**
    Ordered assistant response segments.

    - **annotations - array**
      Ordered list of annotations attached to the response text.

      - **File annotation - object**
        Annotation that references an uploaded file.

        - **source - object**
          File attachment referenced by the annotation.

          - **filename - string**
            Filename referenced by the annotation.

          - **type - string**
            Type discriminator that is always `file`.

        - **type - string**
          Type discriminator that is always `file` for this annotation.

      - **URL annotation - object**
        Annotation that references a URL.

        - **source - object**
          URL referenced by the annotation.

          - **type - string**
            Type discriminator that is always `url`.

          - **url - string**
            URL referenced by the annotation.

        - **type - string**
          Type discriminator that is always `url` for this annotation.

    - **text - string**
      Assistant generated text.

    - **type - string**
      Type discriminator that is always `output_text`.

  - **created_at - integer**
    Unix timestamp (in seconds) for when the item was created.

  - **id - string**
    Identifier of the thread item.

  - **object - string**
    Type discriminator that is always `chatkit.thread_item`.

  - **thread_id - string**
    Identifier of the parent thread.

  - **type - string**
    Type discriminator that is always `chatkit.assistant_message`.

- **Widget message - object**
  Thread item that renders a widget payload.

  - **created_at - integer**
    Unix timestamp (in seconds) for when the item was created.

  - **id - string**
    Identifier of the thread item.

  - **object - string**
    Type discriminator that is always `chatkit.thread_item`.

  - **thread_id - string**
    Identifier of the parent thread.

  - **type - string**
    Type discriminator that is always `chatkit.widget`.

  - **widget - string**
    Serialized widget payload rendered in the UI.

- **Client tool call - object**
  Record of a client side tool invocation initiated by the assistant.

  - **arguments - string**
    JSON-encoded arguments that were sent to the tool.

  - **call_id - string**
    Identifier for the client tool call.

  - **created_at - integer**
    Unix timestamp (in seconds) for when the item was created.

  - **id - string**
    Identifier of the thread item.

  - **name - string**
    Tool name that was invoked.

  - **object - string**
    Type discriminator that is always `chatkit.thread_item`.

  - **output - string**
    JSON-encoded output captured from the tool. Defaults to null while execution is in progress.

  - **status - string**
    Execution status for the tool call.

  - **thread_id - string**
    Identifier of the parent thread.

  - **type - string**
    Type discriminator that is always `chatkit.client_tool_call`.

- **Task item - object**
  Task emitted by the workflow to show progress and status updates.

  - **created_at - integer**
    Unix timestamp (in seconds) for when the item was created.

  - **heading - string**
    Optional heading for the task. Defaults to null when not provided.

  - **id - string**
    Identifier of the thread item.

  - **object - string**
    Type discriminator that is always `chatkit.thread_item`.

  - **summary - string**
    Optional summary that describes the task. Defaults to null when omitted.

  - **task_type - string**
    Subtype for the task.

  - **thread_id - string**
    Identifier of the parent thread.

  - **type - string**
    Type discriminator that is always `chatkit.task`.

- **Task group - object**
  Collection of workflow tasks grouped together in the thread.

  - **created_at - integer**
    Unix timestamp (in seconds) for when the item was created.

  - **id - string**
    Identifier of the thread item.

  - **object - string**
    Type discriminator that is always `chatkit.thread_item`.

  - **tasks - array**
    Tasks included in the group.

    - **heading - string**
      Optional heading for the grouped task. Defaults to null when not provided.

    - **summary - string**
      Optional summary that describes the grouped task. Defaults to null when omitted.

    - **type - string**
      Subtype for the grouped task.

  - **thread_id - string**
    Identifier of the parent thread.

  - **type - string**
    Type discriminator that is always `chatkit.task_group`.

#### first_id - string
The ID of the first item in the list.

#### has_more - boolean
Whether there are more items available.

#### last_id - string
The ID of the last item in the list.

#### object - string
The type of object returned, must be `list`.