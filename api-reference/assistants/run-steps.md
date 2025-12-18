# Run stepsBeta

Represents the steps (model and tool calls) taken during the run.

Related guide: [Assistants](https://platform.openai.com/docs/assistants/overview)

## List run stepsBeta

Returns a list of run steps belonging to a run.

### Example request

```bash
curl https://api.openai.com/v1/threads/thread_abc123/runs/run_abc123/steps \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -H "OpenAI-Beta: assistants=v2"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "id": "step_abc123",
      "object": "thread.run.step",
      "created_at": 1699063291,
      "run_id": "run_abc123",
      "assistant_id": "asst_abc123",
      "thread_id": "thread_abc123",
      "type": "message_creation",
      "status": "completed",
      "cancelled_at": null,
      "completed_at": 1699063291,
      "expired_at": null,
      "failed_at": null,
      "last_error": null,
      "step_details": {
        "type": "message_creation",
        "message_creation": {
          "message_id": "msg_abc123"
        }
      },
      "usage": {
        "prompt_tokens": 123,
        "completion_tokens": 456,
        "total_tokens": 579
      }
    }
  ],
  "first_id": "step_abc123",
  "last_id": "step_abc456",
  "has_more": false
}
```

## Retrieve run stepBeta

Retrieves a run step.

### Example request

```bash
curl https://api.openai.com/v1/threads/thread_abc123/runs/run_abc123/steps/step_abc123 \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -H "OpenAI-Beta: assistants=v2"
```

### Response

```json
{
  "id": "step_abc123",
  "object": "thread.run.step",
  "created_at": 1699063291,
  "run_id": "run_abc123",
  "assistant_id": "asst_abc123",
  "thread_id": "thread_abc123",
  "type": "message_creation",
  "status": "completed",
  "cancelled_at": null,
  "completed_at": 1699063291,
  "expired_at": null,
  "failed_at": null,
  "last_error": null,
  "step_details": {
    "type": "message_creation",
    "message_creation": {
      "message_id": "msg_abc123"
    }
  },
  "usage": {
    "prompt_tokens": 123,
    "completion_tokens": 456,
    "total_tokens": 579
  }
}
```

## The run step objectBeta

Represents a step in execution of a run.

### Parameters

#### assistant_id - string
The ID of the [assistant](https://platform.openai.com/docs/api-reference/assistants) associated with the run step.

#### cancelled_at - integer
The Unix timestamp (in seconds) for when the run step was cancelled.

#### completed_at - integer
The Unix timestamp (in seconds) for when the run step completed.

#### created_at - integer
The Unix timestamp (in seconds) for when the run step was created.

#### expired_at - integer
The Unix timestamp (in seconds) for when the run step expired. A step is considered expired if the parent run is expired.

#### failed_at - integer
The Unix timestamp (in seconds) for when the run step failed.

#### id - string
The identifier of the run step, which can be referenced in API endpoints.

#### last_error - object
The last error associated with this run step. Will be `null` if there are no errors.

- **code - string**
  One of `server_error` or `rate_limit_exceeded`.

- **message - string**
  A human-readable description of the error.

#### metadata - map
Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard.

Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters.

#### object - string
The object type, which is always `thread.run.step`.

#### run_id - string
The ID of the [run](https://platform.openai.com/docs/api-reference/runs) that this run step is a part of.

#### status - string
The status of the run step, which can be either `in_progress`, `cancelled`, `failed`, `completed`, or `expired`.

#### step_details - object
The details of the run step.

- **Message creation - object**
  Details of the message creation by the run step.

  - **message_creation - object**
    The ID of the message that was created by this run step.

    - **message_id - string**
      The ID of the message that was created by this run step.

  - **type - string**
    Always `message_creation`.

- **Tool calls - object**
  Details of the tool call.

  - **tool_calls - array**
    An array of tool calls the run step was involved in. These can be associated with one of three types of tools: `code_interpreter`, `file_search`, or `function`.

    - **Code Interpreter tool call - object**
      Details of the Code Interpreter tool call the run step was involved in.

      - **code_interpreter - object**
        The Code Interpreter tool call definition.

        - **input - string**
          The input to the Code Interpreter tool call.

        - **outputs - array**
          The outputs from the Code Interpreter tool call. Code Interpreter can output one or more items, including text (`logs`) or images (`image`). Each of these are represented by a different object type.

          - **Code Interpreter log output - object**
            Text output from the Code Interpreter tool call as part of a run step.

            - **logs - string**
              The text output from the Code Interpreter tool call.

            - **type - string**
              Always `logs`.

          - **Code Interpreter image output - object**
            The [file](https://platform.openai.com/docs/api-reference/files) ID of the image.

            - **image - object**
              The [file](https://platform.openai.com/docs/api-reference/files) ID of the image.

              - **file_id - string**
                The [file](https://platform.openai.com/docs/api-reference/files) ID of the image.

            - **type - string**
              Always `image`.

      - **id - string**
        The ID of the tool call.

      - **type - string**
        The type of tool call. This is always going to be `code_interpreter` for this type of tool call.

    - **File search tool call - object**
      For now, this is always going to be an empty object.

      - **file_search - map**
        For now, this is always going to be an empty object.

        - **ranking_options - object**
          The ranking options for the file search.

          - **ranker - string**
            The ranker to use for the file search. If not specified will use the `auto` ranker.

          - **score_threshold - number**
            The score threshold for the file search. All values must be a floating point number between 0 and 1.

        - **results - array**
          The results of the file search.

          - **file_id - string**
            The ID of the file that result was found in.

          - **file_name - string**
            The name of the file that result was found in.

          - **score - number**
            The score of the result. All values must be a floating point number between 0 and 1.

          - **content - array**
            The content of the result that was found. The content is only included if requested via the include query parameter.

            - **text - string**
              The text content of the file.

            - **type - string**
              The type of the content.

      - **id - string**
        The ID of the tool call object.

      - **type - string**
        The type of tool call. This is always going to be `file_search` for this type of tool call.

    - **Function tool call - object**
      The definition of the function that was called.

      - **function - object**
        The definition of the function that was called.

        - **arguments - string**
          The arguments passed to the function.

        - **name - string**
          The name of the function.

        - **output - string**
          The output of the function. This will be `null` if the outputs have not been [submitted](https://platform.openai.com/docs/api-reference/runs/submitToolOutputs) yet.

      - **id - string**
        The ID of the tool call object.

      - **type - string**
        The type of tool call. This is always going to be `function` for this type of tool call.

  - **type - string**
    Always `tool_calls`.

#### thread_id - string
The ID of the [thread](https://platform.openai.com/docs/api-reference/threads) that was run.

#### type - string
The type of run step, which can be either `message_creation` or `tool_calls`.

#### usage - object
Usage statistics related to the run step. This value will be `null` while the run step's status is `in_progress`.

- **completion_tokens - integer**
  Number of completion tokens used over the course of the run step.

- **prompt_tokens - integer**
  Number of prompt tokens used over the course of the run step.

- **total_tokens - integer**
  Total number of tokens used (prompt + completion).

### OBJECT The run step object

```json
{
  "id": "step_abc123",
  "object": "thread.run.step",
  "created_at": 1699063291,
  "run_id": "run_abc123",
  "assistant_id": "asst_abc123",
  "thread_id": "thread_abc123",
  "type": "message_creation",
  "status": "completed",
  "cancelled_at": null,
  "completed_at": 1699063291,
  "expired_at": null,
  "failed_at": null,
  "last_error": null,
  "step_details": {
    "type": "message_creation",
    "message_creation": {
      "message_id": "msg_abc123"
    }
  },
  "usage": {
    "prompt_tokens": 123,
    "completion_tokens": 456,
    "total_tokens": 579
  }
}
```