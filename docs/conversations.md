# Conversations

Create and manage conversations to store and retrieve conversation state across Response API calls.

## Create a conversation

Create a conversation.

### Example request

```bash
curl https://api.openai.com/v1/conversations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "metadata": {"topic": "demo"},
    "items": [
      {
        "type": "message",
        "role": "user",
        "content": "Hello!"
      }
    ]
  }'
```

### Response

```json
{
  "id": "conv_123",
  "object": "conversation",
  "created_at": 1741900000,
  "metadata": {"topic": "demo"}
}
```

## Retrieve a conversation

Get a conversation

### Example request

```bash
curl https://api.openai.com/v1/conversations/conv_123 \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "id": "conv_123",
  "object": "conversation",
  "created_at": 1741900000,
  "metadata": {"topic": "demo"}
}
```

## Update a conversation

Update a conversation

### Example request

```bash
curl https://api.openai.com/v1/conversations/conv_123 \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "metadata": {"topic": "project-x"}
  }'
```

### Response

```json
{
  "id": "conv_123",
  "object": "conversation",
  "created_at": 1741900000,
  "metadata": {"topic": "project-x"}
}
```

## Delete a conversation

Delete a conversation. Items in the conversation will not be deleted.

### Example request

```bash
curl -X DELETE https://api.openai.com/v1/conversations/conv_123 \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "id": "conv_123",
  "object": "conversation.deleted",
  "deleted": true
}
```

## List items

List all items for a conversation with the given ID.

### Example request

```bash
curl "https://api.openai.com/v1/conversations/conv_123/items?limit=10" \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "type": "message",
      "id": "msg_abc",
      "status": "completed",
      "role": "user",
      "content": [
        {"type": "input_text", "text": "Hello!"}
      ]
    }
  ],
  "first_id": "msg_abc",
  "last_id": "msg_abc",
  "has_more": false
}
```

## Create items

Create items in a conversation with the given ID.

### Example request

```bash
curl https://api.openai.com/v1/conversations/conv_123/items \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "items": [
      {
        "type": "message",
        "role": "user",
        "content": [
          {"type": "input_text", "text": "Hello!"}
        ]
      },
      {
        "type": "message",
        "role": "user",
        "content": [
          {"type": "input_text", "text": "How are you?"}
        ]
      }
    ]
  }'
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "type": "message",
      "id": "msg_abc",
      "status": "completed",
      "role": "user",
      "content": [
        {"type": "input_text", "text": "Hello!"}
      ]
    },
    {
      "type": "message",
      "id": "msg_def",
      "status": "completed",
      "role": "user",
      "content": [
        {"type": "input_text", "text": "How are you?"}
      ]
    }
  ],
  "first_id": "msg_abc",
  "last_id": "msg_def",
  "has_more": false
}
```

## Retrieve an item

Get a single item from a conversation with the given IDs.

### Example request

```bash
curl https://api.openai.com/v1/conversations/conv_123/items/msg_abc \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "type": "message",
  "id": "msg_abc",
  "status": "completed",
  "role": "user",
  "content": [
    {"type": "input_text", "text": "Hello!"}
  ]
}
```

## Delete an item

Delete an item from a conversation with the given IDs.

### Example request

```bash
curl -X DELETE https://api.openai.com/v1/conversations/conv_123/items/msg_abc \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "id": "conv_123",
  "object": "conversation",
  "created_at": 1741900000,
  "metadata": {"topic": "demo"}
}
```

## The conversation object

### Parameters

#### created_at - integer
The time at which the conversation was created, measured in seconds since the Unix epoch.

#### id - string
The unique ID of the conversation.

#### metadata
Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard. Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters.

#### object - string
The object type, which is always `conversation`.

## The item list

A list of Conversation items.

### Parameters

#### data - array
A list of conversation items.

- **Message - object**
  A message to or from the model.

  - **content - array**
    The content of the message

    - **Input text - object**
      A text input to the model.

      - **text - string**
        The text input to the model.

      - **type - string**
        The type of the input item. Always `input_text`.

    - **Output text - object**
      A text output from the model.

      - **annotations - array**
        The annotations of the text output.

        - **File citation - object**
          A citation to a file.

          - **file_id - string**
            The ID of the file.

          - **filename - string**
            The filename of the file cited.

          - **index - integer**
            The index of the file in the list of files.

          - **type - string**
            The type of the file citation. Always `file_citation`.

        - **URL citation - object**
          A citation for a web resource used to generate a model response.

          - **end_index - integer**
            The index of the last character of the URL citation in the message.

          - **start_index - integer**
            The index of the first character of the URL citation in the message.

          - **title - string**
            The title of the web resource.

          - **type - string**
            The type of the URL citation. Always `url_citation`.

          - **url - string**
            The URL of the web resource.

        - **Container file citation - object**
          A citation for a container file used to generate a model response.

          - **container_id - string**
            The ID of the container file.

          - **end_index - integer**
            The index of the last character of the container file citation in the message.

          - **file_id - string**
            The ID of the file.

          - **filename - string**
            The filename of the container file cited.

          - **start_index - integer**
            The index of the first character of the container file citation in the message.

          - **type - string**
            The type of the container file citation. Always `container_file_citation`.

        - **File path - object**
          A path to a file.

          - **file_id - string**
            The ID of the file.

          - **index - integer**
            The index of the file in the list of files.

          - **type - string**
            The type of the file path. Always `file_path`.

      - **logprobs - array**

        - **bytes - array**

        - **logprob - number**

        - **token - string**

        - **top_logprobs - array**

          - **bytes - array**

          - **logprob - number**

          - **token - string**

      - **text - string**
        The text output from the model.

      - **type - string**
        The type of the output text. Always `output_text`.

    - **Text Content - object**
      A text content.

      - **text - string**

      - **type - string**

    - **Summary text - object**
      A summary text from the model.

      - **text - string**
        A summary of the reasoning output from the model so far.

      - **type - string**
        The type of the object. Always `summary_text`.

    - **Reasoning text - object**
      Reasoning text from the model.

      - **text - string**
        The reasoning text from the model.

      - **type - string**
        The type of the reasoning text. Always `reasoning_text`.

    - **Refusal - object**
      A refusal from the model.

      - **refusal - string**
        The refusal explanation from the model.

      - **type - string**
        The type of the refusal. Always `refusal`.

    - **Input image - object**
      An image input to the model. Learn about [image inputs](https://platform.openai.com/docs/guides/vision).

      - **detail - string**
        The detail level of the image to be sent to the model. One of `high`, `low`, or `auto`. Defaults to `auto`.

      - **type - string**
        The type of the input item. Always `input_image`.

      - **file_id - string**
        The ID of the file to be sent to the model.

      - **image_url - string**
        The URL of the image to be sent to the model. A fully qualified URL or base64 encoded image in a data URL.

    - **Computer screenshot - object**
      A screenshot of a computer.

      - **file_id - string**
        The identifier of an uploaded file that contains the screenshot.

      - **image_url - string**
        The URL of the screenshot image.

      - **type - string**
        Specifies the event type. For a computer screenshot, this property is always set to `computer_screenshot`.

    - **Input file - object**
      A file input to the model.

      - **type - string**
        The type of the input item. Always `input_file`.

      - **file_data - string**
        The content of the file to be sent to the model.

      - **file_id - string**
        The ID of the file to be sent to the model.

      - **file_url - string**
        The URL of the file to be sent to the model.

      - **filename - string**
        The name of the file to be sent to the model.

  - **id - string**
    The unique ID of the message.

  - **role - string**
    The role of the message. One of `unknown`, `user`, `assistant`, `system`, `critic`, `discriminator`, `developer`, or `tool`.

  - **status - string**
    The status of item. One of `in_progress`, `completed`, or `incomplete`. Populated when items are returned via API.

  - **type - string**
    The type of the message. Always set to `message`.

- **Function tool call - object**
  A tool call to run a function. See the [function calling guide](https://platform.openai.com/docs/guides/function-calling) for more information.

  - **arguments - string**
    A JSON string of the arguments to pass to the function.

  - **call_id - string**
    The unique ID of the function tool call generated by the model.

  - **id - string**
    The unique ID of the function tool call.

  - **name - string**
    The name of the function to run.

  - **type - string**
    The type of the function tool call. Always `function_call`.

  - **status - string**
    The status of the item. One of `in_progress`, `completed`, or `incomplete`. Populated when items are returned via API.

- **Function tool call output - object**
  The output of a function tool call.

  - **call_id - string**
    The unique ID of the function tool call generated by the model.

  - **id - string**
    The unique ID of the function call tool output.

  - **output - string or array**
    The output from the function call generated by your code. Can be a string or an list of output content.

    - **string output - string**
      A string of the output of the function call.

    - **output content list - array**
      Text, image, or file output of the function call.

      - **Input text - object**
        A text input to the model.

        - **text - string**
          The text input to the model.

        - **type - string**
          The type of the input item. Always `input_text`.

      - **Input image - object**
        An image input to the model. Learn about [image inputs](https://platform.openai.com/docs/guides/vision).

        - **detail - string**
          The detail level of the image to be sent to the model. One of `high`, `low`, or `auto`. Defaults to `auto`.

        - **type - string**
          The type of the input item. Always `input_image`.

        - **file_id - string**
          The ID of the file to be sent to the model.

        - **image_url - string**
          The URL of the image to be sent to the model. A fully qualified URL or base64 encoded image in a data URL.

      - **Input file - object**
        A file input to the model.

        - **type - string**
          The type of the input item. Always `input_file`.

        - **file_data - string**
          The content of the file to be sent to the model.

        - **file_id - string**
          The ID of the file to be sent to the model.

        - **file_url - string**
          The URL of the file to be sent to the model.

        - **filename - string**
          The name of the file to be sent to the model.

  - **type - string**
    The type of the function tool call output. Always `function_call_output`.

  - **status - string**
    The status of the item. One of `in_progress`, `completed`, or `incomplete`. Populated when items are returned via API.

- **File search tool call - object**
  The results of a file search tool call. See the [file search guide](https://platform.openai.com/docs/guides/tools-file-search) for more information.

  - **id - string**
    The unique ID of the file search tool call.

  - **queries - array**
    The queries used to search for files.

  - **status - string**
    The status of the file search tool call. One of `in_progress`, `searching`, `incomplete` or `failed`,

  - **type - string**
    The type of the file search tool call. Always `file_search_call`.

  - **results - array**
    The results of the file search tool call.

    - **attributes - map**
      Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard. Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters, booleans, or numbers.

    - **file_id - string**
      The unique ID of the file.

    - **filename - string**
      The name of the file.

    - **score - number**
      The relevance score of the file - a value between 0 and 1.

    - **text - string**
      The text that was retrieved from the file.

- **Web search tool call - object**
  The results of a web search tool call. See the [web search guide](https://platform.openai.com/docs/guides/tools-web-search) for more information.

  - **action - object**
    An object describing the specific action taken in this web search call. Includes details on how the model used the web (search, open_page, find).

    - **Search action - object**
      Action type "search" - Performs a web search query.

      - **query - string**
        The search query.

      - **type - string**
        The action type.

      - **sources - array**
        The sources used in the search.

        - **type - string**
          The type of source. Always `url`.

        - **url - string**
          The URL of the source.

    - **Open page action - object**
      Action type "open_page" - Opens a specific URL from search results.

      - **type - string**
        The action type.

      - **url - string**
        The URL opened by the model.

    - **Find action - object**
      Action type "find": Searches for a pattern within a loaded page.

      - **pattern - string**
        The pattern or text to search for within the page.

      - **type - string**
        The action type.

      - **url - string**
        The URL of the page searched for the pattern.

  - **id - string**
    The unique ID of the web search tool call.

  - **status - string**
    The status of the web search tool call.

  - **type - string**
    The type of the web search tool call. Always `web_search_call`.

- **Image generation call - object**
  An image generation request made by the model.

  - **id - string**
    The unique ID of the image generation call.

  - **result - string**
    The generated image encoded in base64.

  - **status - string**
    The status of the image generation call.

  - **type - string**
    The type of the image generation call. Always `image_generation_call`.

- **Computer tool call - object**
  A tool call to a computer use tool. See the [computer use guide](https://platform.openai.com/docs/guides/tools-computer-use) for more information.

  - **action - object**
    A click action.

    - **Click - object**
      A click action.

      - **button - string**
        Indicates which mouse button was pressed during the click. One of `left`, `right`, `wheel`, `back`, or `forward`.

      - **type - string**
        Specifies the event type. For a click action, this property is always `click`.

      - **x - integer**
        The x-coordinate where the click occurred.

      - **y - integer**
        The y-coordinate where the click occurred.

    - **DoubleClick - object**
      A double click action.

      - **type - string**
        Specifies the event type. For a double click action, this property is always set to `double_click`.

      - **x - integer**
        The x-coordinate where the double click occurred.

      - **y - integer**
        The y-coordinate where the double click occurred.

    - **Drag - object**
      A drag action.

      - **path - array**
        An array of coordinates representing the path of the drag action. Coordinates will appear as an array of objects, eg

        ```text
        [
          { x: 100, y: 200 },
          { x: 200, y: 300 }
        ]
        ```

        - **x - integer**
          The x-coordinate.

        - **y - integer**
          The y-coordinate.

      - **type - string**
        Specifies the event type. For a drag action, this property is always set to `drag`.

    - **KeyPress - object**
      A collection of keypresses the model would like to perform.

      - **keys - array**
        The combination of keys the model is requesting to be pressed. This is an array of strings, each representing a key.

      - **type - string**
        Specifies the event type. For a keypress action, this property is always set to `keypress`.

    - **Move - object**
      A mouse move action.

      - **type - string**
        Specifies the event type. For a move action, this property is always set to `move`.

      - **x - integer**
        The x-coordinate to move to.

      - **y - integer**
        The y-coordinate to move to.

    - **Screenshot - object**
      A screenshot action.

      - **type - string**
        Specifies the event type. For a screenshot action, this property is always set to `screenshot`.

    - **Scroll - object**
      A scroll action.

      - **scroll_x - integer**
        The horizontal scroll distance.

      - **scroll_y - integer**
        The vertical scroll distance.

      - **type - string**
        Specifies the event type. For a scroll action, this property is always set to `scroll`.

      - **x - integer**
        The x-coordinate where the scroll occurred.

      - **y - integer**
        The y-coordinate where the scroll occurred.

    - **Type - object**
      An action to type in text.

      - **text - string**
        The text to type.

      - **type - string**
        Specifies the event type. For a type action, this property is always set to `type`.

    - **Wait - object**
      A wait action.

      - **type - string**
        Specifies the event type. For a wait action, this property is always set to `wait`.

  - **call_id - string**
    An identifier used when responding to the tool call with output.

  - **id - string**
    The unique ID of the computer call.

  - **pending_safety_checks - array**
    The pending safety checks for the computer call.

    - **id - string**
      The ID of the pending safety check.

    - **code - string**
      The type of the pending safety check.

    - **message - string**
      Details about the pending safety check.

  - **status - string**
    The status of the item. One of `in_progress`, `completed`, or `incomplete`. Populated when items are returned via API.

  - **type - string**
    The type of the computer call. Always `computer_call`.

- **Computer tool call output - object**
  The output of a computer tool call.

  - **call_id - string**
    The ID of the computer tool call that produced the output.

  - **id - string**
    The unique ID of the computer call tool output.

  - **output - object**
    A computer screenshot image used with the computer use tool.

    - **type - string**
      Specifies the event type. For a computer screenshot, this property is always set to `computer_screenshot`.

    - **file_id - string**
      The identifier of an uploaded file that contains the screenshot.

    - **image_url - string**
      The URL of the screenshot image.

  - **type - string**
    The type of the computer tool call output. Always `computer_call_output`.

  - **acknowledged_safety_checks - array**
    The safety checks reported by the API that have been acknowledged by the developer.

    - **id - string**
      The ID of the pending safety check.

    - **code - string**
      The type of the pending safety check.

    - **message - string**
      Details about the pending safety check.

  - **status - string**
    The status of the message input. One of `in_progress`, `completed`, or `incomplete`. Populated when input items are returned via API.

- **Reasoning - object**
  A description of the chain of thought used by a reasoning model while generating a response. Be sure to include these items in your `input` to the Responses API for subsequent turns of a conversation if you are manually [managing context](https://platform.openai.com/docs/guides/conversation-state).

  - **id - string**
    The unique identifier of the reasoning content.

  - **summary - array**
    Reasoning summary content.

    - **text - string**
      A summary of the reasoning output from the model so far.

    - **type - string**
      The type of the object. Always `summary_text`.

  - **type - string**
    The type of the object. Always `reasoning`.

  - **content - array**
    Reasoning text content.

    - **text - string**
      The reasoning text from the model.

    - **type - string**
      The type of the reasoning text. Always `reasoning_text`.

  - **encrypted_content - string**
    The encrypted content of the reasoning item - populated when a response is generated with `reasoning.encrypted_content` in the `include` parameter.

  - **status - string**
    The status of the item. One of `in_progress`, `completed`, or `incomplete`. Populated when items are returned via API.

- **Code interpreter tool call - object**
  A tool call to run code.

  - **code - string**
    The code to run, or null if not available.

  - **container_id - string**
    The ID of the container used to run the code.

  - **id - string**
    The unique ID of the code interpreter tool call.

  - **outputs - array**
    The outputs generated by the code interpreter, such as logs or images. Can be null if no outputs are available.

    - **Code interpreter output logs - object**
      The logs output from the code interpreter.

      - **logs - string**
        The logs output from the code interpreter.

      - **type - string**
        The type of the output. Always `logs`.

    - **Code interpreter output image - object**
      The image output from the code interpreter.

      - **type - string**
        The type of the output. Always `image`.

      - **url - string**
        The URL of the image output from the code interpreter.

  - **status - string**
    The status of the code interpreter tool call. Valid values are `in_progress`, `completed`, `incomplete`, `interpreting`, and `failed`.

  - **type - string**
    The type of the code interpreter tool call. Always `code_interpreter_call`.

- **Local shell call - object**
  A tool call to run a command on the local shell.

  - **action - object**
    Execute a shell command on the server.

    - **command - array**
      The command to run.

    - **env - map**
      Environment variables to set for the command.

    - **type - string**
      The type of the local shell action. Always `exec`.

    - **timeout_ms - integer**
      Optional timeout in milliseconds for the command.

    - **user - string**
      Optional user to run the command as.

    - **working_directory - string**
      Optional working directory to run the command in.

  - **call_id - string**
    The unique ID of the local shell tool call generated by the model.

  - **id - string**
    The unique ID of the local shell call.

  - **status - string**
    The status of the local shell call.

  - **type - string**
    The type of the local shell call. Always `local_shell_call`.

- **Local shell call output - object**
  The output of a local shell tool call.

  - **id - string**
    The unique ID of the local shell tool call generated by the model.

  - **output - string**
    A JSON string of the output of the local shell tool call.

  - **type - string**
    The type of the local shell tool call output. Always `local_shell_call_output`.

  - **status - string**
    The status of the item. One of `in_progress`, `completed`, or `incomplete`.

- **Shell tool call - object**
  A tool call that executes one or more shell commands in a managed environment.

  - **action - object**
    The shell commands and limits that describe how to run the tool call.

    - **commands - array**

    - **max_output_length - integer**
      Optional maximum number of characters to return from each command.

    - **timeout_ms - integer**
      Optional timeout in milliseconds for the commands.

  - **call_id - string**
    The unique ID of the shell tool call generated by the model.

  - **id - string**
    The unique ID of the shell tool call. Populated when this item is returned via API.

  - **status - string**
    The status of the shell call. One of `in_progress`, `completed`, or `incomplete`.

  - **type - string**
    The type of the item. Always `shell_call`.

  - **created_by - string**
    The ID of the entity that created this tool call.

- **Shell call output - object**
  The output of a shell tool call.

  - **call_id - string**
    The unique ID of the shell tool call generated by the model.

  - **id - string**
    The unique ID of the shell call output. Populated when this item is returned via API.

  - **max_output_length - integer**
    The maximum length of the shell command output. This is generated by the model and should be passed back with the raw output.

  - **output - array**
    An array of shell call output contents

    - **outcome - object**
      Represents either an exit outcome (with an exit code) or a timeout outcome for a shell call output chunk.

      - **Shell call timeout outcome - object**
        Indicates that the shell call exceeded its configured time limit.

        - **type - string**
          The outcome type. Always `timeout`.

      - **Shell call exit outcome - object**
        Indicates that the shell commands finished and returned an exit code.

        - **exit_code - integer**
          Exit code from the shell process.

        - **type - string**
          The outcome type. Always `exit`.

    - **stderr - string**

    - **stdout - string**

    - **created_by - string**

  - **type - string**
    The type of the shell call output. Always `shell_call_output`.

  - **created_by - string**

- **Apply patch tool call - object**
  A tool call that applies file diffs by creating, deleting, or updating files.

  - **call_id - string**
    The unique ID of the apply patch tool call generated by the model.

  - **id - string**
    The unique ID of the apply patch tool call. Populated when this item is returned via API.

  - **operation - object**
    One of the create_file, delete_file, or update_file operations applied via apply_patch.

    - **Apply patch create file operation - object**
      Instruction describing how to create a file via the apply_patch tool.

      - **diff - string**
        Diff to apply.

      - **path - string**
        Path of the file to create.

      - **type - string**
        Create a new file with the provided diff.

    - **Apply patch delete file operation - object**
      Instruction describing how to delete a file via the apply_patch tool.

      - **path - string**
        Path of the file to delete.

      - **type - string**
        Delete the specified file.

    - **Apply patch update file operation - object**
      Instruction describing how to update a file via the apply_patch tool.

      - **diff - string**
        Diff to apply.

      - **path - string**
        Path of the file to update.

      - **type - string**
        Update an existing file with the provided diff.

  - **status - string**
    The status of the apply patch tool call. One of `in_progress` or `completed`.

  - **type - string**
    The type of the item. Always `apply_patch_call`.

  - **created_by - string**
    The ID of the entity that created this tool call.

- **Apply patch tool call output - object**
  The output emitted by an apply patch tool call.

  - **call_id - string**
    The unique ID of the apply patch tool call generated by the model.

  - **id - string**
    The unique ID of the apply patch tool call output. Populated when this item is returned via API.

  - **status - string**
    The status of the apply patch tool call output. One of `completed` or `failed`.

  - **type - string**
    The type of the item. Always `apply_patch_call_output`.

  - **created_by - string**
    The ID of the entity that created this tool call output.

  - **output - string**
    Optional textual output returned by the apply patch tool.

- **MCP list tools - object**
  A list of tools available on an MCP server.

  - **id - string**
    The unique ID of the list.

  - **server_label - string**
    The label of the MCP server.

  - **tools - array**
    The tools available on the server.

    - **input_schema - object**
      The JSON schema describing the tool's input.

    - **name - string**
      The name of the tool.

    - **annotations - object**
      Additional annotations about the tool.

    - **description - string**
      The description of the tool.

  - **type - string**
    The type of the item. Always `mcp_list_tools`.

  - **error - string**
    Error message if the server could not list tools.

- **MCP approval request - object**
  A request for human approval of a tool invocation.

  - **arguments - string**
    A JSON string of arguments for the tool.

  - **id - string**
    The unique ID of the approval request.

  - **name - string**
    The name of the tool to run.

  - **server_label - string**
    The label of the MCP server making the request.

  - **type - string**
    The type of the item. Always `mcp_approval_request`.

- **MCP approval response - object**
  A response to an MCP approval request.

  - **approval_request_id - string**
    The ID of the approval request being answered.

  - **approve - boolean**
    Whether the request was approved.

  - **id - string**
    The unique ID of the approval response

  - **type - string**
    The type of the item. Always `mcp_approval_response`.

  - **reason - string**
    Optional reason for the decision.

- **MCP tool call - object**
  An invocation of a tool on an MCP server.

  - **arguments - string**
    A JSON string of the arguments passed to the tool.

  - **id - string**
    The unique ID of the tool call.

  - **name - string**
    The name of the tool that was run.

  - **server_label - string**
    The label of the MCP server running the tool.

  - **type - string**
    The type of the item. Always `mcp_call`.

  - **approval_request_id - string**
    Unique identifier for the MCP tool call approval request. Include this value in a subsequent `mcp_approval_response` input to approve or reject the corresponding tool call.

  - **error - string**
    The error from the tool call, if any.

  - **output - string**
    The output from the tool call.

  - **status - string**
    The status of the tool call. One of `in_progress`, `completed`, `incomplete`, `calling`, or `failed`.

- **Custom tool call - object**
  A call to a custom tool created by the model.

  - **call_id - string**
    An identifier used to map this custom tool call to a tool call output.

  - **input - string**
    The input for the custom tool call generated by the model.

  - **name - string**
    The name of the custom tool being called.

  - **type - string**
    The type of the custom tool call. Always `custom_tool_call`.

  - **id - string**
    The unique ID of the custom tool call in the OpenAI platform.

- **Custom tool call output - object**
  The output of a custom tool call from your code, being sent back to the model.

  - **call_id - string**
    The call ID, used to map this custom tool call output to a custom tool call.

  - **output - string or array**
    The output from the custom tool call generated by your code. Can be a string or an list of output content.

    - **string output - string**
      A string of the output of the custom tool call.

    - **output content list - array**
      Text, image, or file output of the custom tool call.

      - **Input text - object**
        A text input to the model.

        - **text - string**
          The text input to the model.

        - **type - string**
          The type of the input item. Always `input_text`.

      - **Input image - object**
        An image input to the model. Learn about [image inputs](https://platform.openai.com/docs/guides/vision).

        - **detail - string**
          The detail level of the image to be sent to the model. One of `high`, `low`, or `auto`. Defaults to `auto`.

        - **type - string**
          The type of the input item. Always `input_image`.

        - **file_id - string**
          The ID of the file to be sent to the model.

        - **image_url - string**
          The URL of the image to be sent to the model. A fully qualified URL or base64 encoded image in a data URL.

      - **Input file - object**
        A file input to the model.

        - **type - string**
          The type of the input item. Always `input_file`.

        - **file_data - string**
          The content of the file to be sent to the model.

        - **file_id - string**
          The ID of the file to be sent to the model.

        - **file_url - string**
          The URL of the file to be sent to the model.

        - **filename - string**
          The name of the file to be sent to the model.

  - **type - string**
    The type of the custom tool call output. Always `custom_tool_call_output`.

  - **id - string**
    The unique ID of the custom tool call output in the OpenAI platform.

#### first_id - string
The ID of the first item in the list.

#### has_more - boolean
Whether there are more items available.

#### last_id - string
The ID of the last item in the list.

#### object - string
The type of object returned, must be `list`.