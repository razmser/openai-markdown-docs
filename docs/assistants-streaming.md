# StreamingBeta

Stream the result of executing a Run or resuming a Run after submitting tool outputs. You can stream events from the [Create Thread and Run](https://platform.openai.com/docs/api-reference/runs/createThreadAndRun), [Create Run](https://platform.openai.com/docs/api-reference/runs/createRun), and [Submit Tool Outputs](https://platform.openai.com/docs/api-reference/runs/submitToolOutputs) endpoints by passing `"stream": true`. The response will be a [Server-Sent events](https://html.spec.whatwg.org/multipage/server-sent-events.html#server-sent-events) stream. Our Node and Python SDKs provide helpful utilities to make streaming easy. Reference the [Assistants API quickstart](https://platform.openai.com/docs/assistants/overview) to learn more.

## The message delta objectBeta

Represents a message delta i.e. any changed fields on a message during streaming.

### Parameters

#### delta - object
The delta containing the fields that have changed on the Message.

- **content - array**
  The content of the message in array of text and/or images.

  - **Image file - object**
    References an image [File](https://platform.openai.com/docs/api-reference/files) in the content of a message.

    - **index - integer**
      The index of the content part in the message.

    - **type - string**
      Always `image_file`.

    - **image_file - object**
      Specifies the detail level of the image if specified by the user. `low` uses fewer tokens, you can opt in to high resolution using `high`.

      - **detail - string**
        Specifies the detail level of the image if specified by the user. `low` uses fewer tokens, you can opt in to high resolution using `high`.

      - **file_id - string**
        The [File](https://platform.openai.com/docs/api-reference/files) ID of the image in the message content. Set `purpose="vision"` when uploading the File if you need to later display the file content.

  - **Text - object**
    The text content that is part of a message.

    - **index - integer**
      The index of the content part in the message.

    - **type - string**
      Always `text`.

    - **text - object**
      A citation within the message that points to a specific quote from a specific File associated with the assistant or the message. Generated when the assistant uses the "file_search" tool to search files.

      - **annotations - array**
        A citation within the message that points to a specific quote from a specific File associated with the assistant or the message. Generated when the assistant uses the "file_search" tool to search files.

        - **File citation - object**
          A citation within the message that points to a specific quote from a specific File associated with the assistant or the message. Generated when the assistant uses the "file_search" tool to search files.

          - **index - integer**
            The index of the annotation in the text content part.

          - **type - string**
            Always `file_citation`.

          - **end_index - integer**

          - **file_citation - object**
            The ID of the specific File the citation is from.

            - **file_id - string**
              The ID of the specific File the citation is from.

            - **quote - string**
              The specific quote in the file.

          - **start_index - integer**

          - **text - string**
            The text in the message content that needs to be replaced.

        - **File path - object**
          A URL for the file that's generated when the assistant used the `code_interpreter` tool to generate a file.

          - **index - integer**
            The index of the annotation in the text content part.

          - **type - string**
            Always `file_path`.

          - **end_index - integer**

          - **file_path - object**
            The ID of the file that was generated.

            - **file_id - string**
              The ID of the file that was generated.

          - **start_index - integer**

          - **text - string**
            The text in the message content that needs to be replaced.

      - **value - string**
        The data that makes up the text.

  - **Refusal - object**
    The refusal content that is part of a message.

    - **index - integer**
      The index of the refusal part in the message.

    - **type - string**
      Always `refusal`.

    - **refusal - string**

  - **Image URL - object**
    References an image URL in the content of a message.

    - **index - integer**
      The index of the content part in the message.

    - **type - string**
      Always `image_url`.

    - **image_url - object**
      Specifies the detail level of the image. `low` uses fewer tokens, you can opt in to high resolution using `high`.

      - **detail - string**
        Specifies the detail level of the image. `low` uses fewer tokens, you can opt in to high resolution using `high`.

      - **url - string**
        The URL of the image, must be a supported image types: jpeg, jpg, png, gif, webp.

- **role - string**
  The entity that produced the message. One of `user` or `assistant`.

#### id - string
The identifier of the message, which can be referenced in API endpoints.

#### object - string
The object type, which is always `thread.message.delta`.

### OBJECT The message delta object

```json
{
  "id": "msg_123",
  "object": "thread.message.delta",
  "delta": {
    "content": [
      {
        "index": 0,
        "type": "text",
        "text": { "value": "Hello", "annotations": [] }
      }
    ]
  }
}
```

## The run step delta objectBeta

Represents a run step delta i.e. any changed fields on a run step during streaming.

### Parameters

#### delta - object
The delta containing the fields that have changed on the run step.

- **step_details - object**
  The details of the run step.

  - **Message creation - object**
    Details of the message creation by the run step.

    - **type - string**
      Always `message_creation`.

    - **message_creation - object**
      The ID of the message that was created by this run step.

      - **message_id - string**
        The ID of the message that was created by this run step.

  - **Tool calls - object**
    Details of the tool call.

    - **type - string**
      Always `tool_calls`.

    - **tool_calls - array**
      An array of tool calls the run step was involved in. These can be associated with one of three types of tools: `code_interpreter`, `file_search`, or `function`.

      - **Code interpreter tool call - object**
        Details of the Code Interpreter tool call the run step was involved in.

        - **index - integer**
          The index of the tool call in the tool calls array.

        - **type - string**
          The type of tool call. This is always going to be `code_interpreter` for this type of tool call.

        - **code_interpreter - object**
          The Code Interpreter tool call definition.

          - **input - string**
            The input to the Code Interpreter tool call.

          - **outputs - array**
            The outputs from the Code Interpreter tool call. Code Interpreter can output one or more items, including text (`logs`) or images (`image`). Each of these are represented by a different object type.

            - **Code interpreter log output - object**
              Text output from the Code Interpreter tool call as part of a run step.

              - **index - integer**
                The index of the output in the outputs array.

              - **type - string**
                Always `logs`.

              - **logs - string**
                The text output from the Code Interpreter tool call.

            - **Code interpreter image output - object**
              The index of the output in the outputs array.

              - **index - integer**
                The index of the output in the outputs array.

              - **type - string**
                Always `image`.

              - **image - object**
                The [file](https://platform.openai.com/docs/api-reference/files) ID of the image.

                - **file_id - string**
                  The [file](https://platform.openai.com/docs/api-reference/files) ID of the image.

        - **id - string**
          The ID of the tool call.

      - **File search tool call - object**
        For now, this is always going to be an empty object.

        - **file_search - map**
          For now, this is always going to be an empty object.

        - **index - integer**
          The index of the tool call in the tool calls array.

        - **type - string**
          The type of tool call. This is always going to be `file_search` for this type of tool call.

        - **id - string**
          The ID of the tool call object.

      - **Function tool call - object**
        The index of the tool call in the tool calls array.

        - **index - integer**
          The index of the tool call in the tool calls array.

        - **type - string**
          The type of tool call. This is always going to be `function` for this type of tool call.

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

#### id - string
The identifier of the run step, which can be referenced in API endpoints.

#### object - string
The object type, which is always `thread.run.step.delta`.

### OBJECT The run step delta object

```json
{
  "id": "step_123",
  "object": "thread.run.step.delta",
  "delta": {
    "step_details": {
      "type": "tool_calls",
      "tool_calls": [
        {
          "index": 0,
          "id": "call_123",
          "type": "code_interpreter",
          "code_interpreter": { "input": "", "outputs": [] }
        }
      ]
    }
  }
}
```

## Assistant stream eventsBeta

Represents an event emitted when streaming a Run.

Each event in a server-sent events stream has an `event` and `data` property:
    
    
    event: thread.created
    data: {"id": "thread_123", "object": "thread", ...}

We emit events whenever a new object is created, transitions to a new state, or is being streamed in parts (deltas). For example, we emit `thread.run.created` when a new run is created, `thread.run.completed` when a run completes, and so on. When an Assistant chooses to create a message during a run, we emit a `thread.message.created event`, a `thread.message.in_progress` event, many `thread.message.delta` events, and finally a `thread.message.completed` event.

We may add additional events over time, so we recommend handling unknown events gracefully in your code. See the [Assistants API quickstart](https://platform.openai.com/docs/assistants/overview) to learn how to integrate the Assistants API with streaming.

### Parameters

#### done - datais[DONE]
Occurs when a stream ends.

#### error - datais anerror
Occurs when an [error](https://platform.openai.com/docs/guides/error-codes#api-errors) occurs. This can happen due to an internal server error or a timeout.

#### thread.created - datais athread
Occurs when a new [thread](https://platform.openai.com/docs/api-reference/threads/object) is created.

#### thread.message.completed - datais amessage
Occurs when a [message](https://platform.openai.com/docs/api-reference/messages/object) is completed.

#### thread.message.created - datais amessage
Occurs when a [message](https://platform.openai.com/docs/api-reference/messages/object) is created.

#### thread.message.delta - datais amessage delta
Occurs when parts of a [Message](https://platform.openai.com/docs/api-reference/messages/object) are being streamed.

#### thread.message.in_progress - datais amessage
Occurs when a [message](https://platform.openai.com/docs/api-reference/messages/object) moves to an `in_progress` state.

#### thread.message.incomplete - datais amessage
Occurs when a [message](https://platform.openai.com/docs/api-reference/messages/object) ends before it is completed.

#### thread.run.cancelled - datais arun
Occurs when a [run](https://platform.openai.com/docs/api-reference/runs/object) is cancelled.

#### thread.run.cancelling - datais arun
Occurs when a [run](https://platform.openai.com/docs/api-reference/runs/object) moves to a `cancelling` status.

#### thread.run.completed - datais arun
Occurs when a [run](https://platform.openai.com/docs/api-reference/runs/object) is completed.

#### thread.run.created - datais arun
Occurs when a new [run](https://platform.openai.com/docs/api-reference/runs/object) is created.

#### thread.run.expired - datais arun
Occurs when a [run](https://platform.openai.com/docs/api-reference/runs/object) expires.

#### thread.run.failed - datais arun
Occurs when a [run](https://platform.openai.com/docs/api-reference/runs/object) fails.

#### thread.run.in_progress - datais arun
Occurs when a [run](https://platform.openai.com/docs/api-reference/runs/object) moves to an `in_progress` status.

#### thread.run.incomplete - datais arun
Occurs when a [run](https://platform.openai.com/docs/api-reference/runs/object) ends with status `incomplete`.

#### thread.run.queued - datais arun
Occurs when a [run](https://platform.openai.com/docs/api-reference/runs/object) moves to a `queued` status.

#### thread.run.requires_action - datais arun
Occurs when a [run](https://platform.openai.com/docs/api-reference/runs/object) moves to a `requires_action` status.

#### thread.run.step.cancelled - datais arun step
Occurs when a [run step](https://platform.openai.com/docs/api-reference/run-steps/step-object) is cancelled.

#### thread.run.step.completed - datais arun step
Occurs when a [run step](https://platform.openai.com/docs/api-reference/run-steps/step-object) is completed.

#### thread.run.step.created - datais arun step
Occurs when a [run step](https://platform.openai.com/docs/api-reference/run-steps/step-object) is created.

#### thread.run.step.delta - datais arun step delta
Occurs when parts of a [run step](https://platform.openai.com/docs/api-reference/run-steps/step-object) are being streamed.

#### thread.run.step.expired - datais arun step
Occurs when a [run step](https://platform.openai.com/docs/api-reference/run-steps/step-object) expires.

#### thread.run.step.failed - datais arun step
Occurs when a [run step](https://platform.openai.com/docs/api-reference/run-steps/step-object) fails.

#### thread.run.step.in_progress - datais arun step
Occurs when a [run step](https://platform.openai.com/docs/api-reference/run-steps/step-object) moves to an `in_progress` state.