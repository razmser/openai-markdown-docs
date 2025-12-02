# Responses

OpenAI's most advanced interface for generating model responses. Supports text and image inputs, and text outputs. Create stateful interactions with the model, using the output of previous responses as input. Extend the model's capabilities with built-in tools for file search, web search, computer use, and more. Allow the model access to external systems and data using function calling.

Related guides:

  * [Quickstart](https://platform.openai.com/docs/quickstart?api-mode=responses)
  * [Text inputs and outputs](https://platform.openai.com/docs/guides/text?api-mode=responses)
  * [Image inputs](https://platform.openai.com/docs/guides/images?api-mode=responses)
  * [Structured Outputs](https://platform.openai.com/docs/guides/structured-outputs?api-mode=responses)
  * [Function calling](https://platform.openai.com/docs/guides/function-calling?api-mode=responses)
  * [Conversation state](https://platform.openai.com/docs/guides/conversation-state?api-mode=responses)
  * [Extend the models with tools](https://platform.openai.com/docs/guides/tools?api-mode=responses)

## Create a model response

Creates a model response. Provide [text](https://platform.openai.com/docs/guides/text) or [image](https://platform.openai.com/docs/guides/images) inputs to generate [text](https://platform.openai.com/docs/guides/text) or [JSON](https://platform.openai.com/docs/guides/structured-outputs) outputs. Have the model call your own [custom code](https://platform.openai.com/docs/guides/function-calling) or use built-in [tools](https://platform.openai.com/docs/guides/tools) like [web search](https://platform.openai.com/docs/guides/tools-web-search) or [file search](https://platform.openai.com/docs/guides/tools-file-search) to use your own data as input for the model's response.

```bash
curl https://api.openai.com/v1/responses \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "model": "gpt-4.1",
    "input": "Tell me a three sentence bedtime story about a unicorn."
  }'
```

```javascript
import OpenAI from "openai";

const openai = new OpenAI();

const response = await openai.responses.create({
    model: "gpt-4.1",
    input: "Tell me a three sentence bedtime story about a unicorn."
});

console.log(response);
```

```python
from openai import OpenAI

client = OpenAI()

response = client.responses.create(
  model="gpt-4.1",
  input="Tell me a three sentence bedtime story about a unicorn."
)

print(response)
```

```csharp
using System;
using OpenAI.Responses;

OpenAIResponseClient client = new(
    model: "gpt-4.1",
    apiKey: Environment.GetEnvironmentVariable("OPENAI_API_KEY")
);

OpenAIResponse response = client.CreateResponse("Tell me a three sentence bedtime story about a unicorn.");

Console.WriteLine(response.GetOutputText());
```

```json
{
  "id": "resp_67ccd2bed1ec8190b14f964abc0542670bb6a6b452d3795b",
  "object": "response",
  "created_at": 1741476542,
  "status": "completed",
  "error": null,
  "incomplete_details": null,
  "instructions": null,
  "max_output_tokens": null,
  "model": "gpt-4.1-2025-04-14",
  "output": [
    {
      "type": "message",
      "id": "msg_67ccd2bf17f0819081ff3bb2cf6508e60bb6a6b452d3795b",
      "status": "completed",
      "role": "assistant",
      "content": [
        {
          "type": "output_text",
          "text": "In a peaceful grove beneath a silver moon, a unicorn named Lumina discovered a hidden pool that reflected the stars. As she dipped her horn into the water, the pool began to shimmer, revealing a pathway to a magical realm of endless night skies. Filled with wonder, Lumina whispered a wish for all who dream to find their own hidden magic, and as she glanced back, her hoofprints sparkled like stardust.",
          "annotations": []
        }
      ]
    }
  ],
  "parallel_tool_calls": true,
  "previous_response_id": null,
  "reasoning": {
    "effort": null,
    "summary": null
  },
  "store": true,
  "temperature": 1.0,
  "text": {
    "format": {
      "type": "text"
    }
  },
  "tool_choice": "auto",
  "tools": [],
  "top_p": 1.0,
  "truncation": "disabled",
  "usage": {
    "input_tokens": 36,
    "input_tokens_details": {
      "cached_tokens": 0
    },
    "output_tokens": 87,
    "output_tokens_details": {
      "reasoning_tokens": 0
    },
    "total_tokens": 123
  },
  "user": null,
  "metadata": {}
}
```

## Get a model response

Retrieves a model response with the given ID.

```bash
curl https://api.openai.com/v1/responses/resp_123 \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $OPENAI_API_KEY"
```

```javascript
import OpenAI from "openai";
const client = new OpenAI();

const response = await client.responses.retrieve("resp_123");
console.log(response);
```

```python
from openai import OpenAI
client = OpenAI()

response = client.responses.retrieve("resp_123")
print(response)
```

```json
{
  "id": "resp_67cb71b351908190a308f3859487620d06981a8637e6bc44",
  "object": "response",
  "created_at": 1741386163,
  "status": "completed",
  "error": null,
  "incomplete_details": null,
  "instructions": null,
  "max_output_tokens": null,
  "model": "gpt-4o-2024-08-06",
  "output": [
    {
      "type": "message",
      "id": "msg_67cb71b3c2b0819084d481baaaf148f206981a8637e6bc44",
      "status": "completed",
      "role": "assistant",
      "content": [
        {
          "type": "output_text",
          "text": "Silent circuits hum,  \nThoughts emerge in data streams—  \nDigital dawn breaks.",
          "annotations": []
        }
      ]
    }
  ],
  "parallel_tool_calls": true,
  "previous_response_id": null,
  "reasoning": {
    "effort": null,
    "summary": null
  },
  "store": true,
  "temperature": 1.0,
  "text": {
    "format": {
      "type": "text"
    }
  },
  "tool_choice": "auto",
  "tools": [],
  "top_p": 1.0,
  "truncation": "disabled",
  "usage": {
    "input_tokens": 32,
    "input_tokens_details": {
      "cached_tokens": 0
    },
    "output_tokens": 18,
    "output_tokens_details": {
      "reasoning_tokens": 0
    },
    "total_tokens": 50
  },
  "user": null,
  "metadata": {}
}
```

## Delete a model response

Deletes a model response with the given ID.

```bash
curl -X DELETE https://api.openai.com/v1/responses/resp_123 \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $OPENAI_API_KEY"
```

```javascript
import OpenAI from "openai";
const client = new OpenAI();

const response = await client.responses.delete("resp_123");
console.log(response);
```

```python
from openai import OpenAI
client = OpenAI()

response = client.responses.delete("resp_123")
print(response)
```

```json
{
  "id": "resp_6786a1bec27481909a17d673315b29f6",
  "object": "response",
  "deleted": true
}
```

## Cancel a response

Cancels a model response with the given ID. Only responses created with the `background` parameter set to `true` can be cancelled. [Learn more](https://platform.openai.com/docs/guides/background).

```bash
curl -X POST https://api.openai.com/v1/responses/resp_123/cancel \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $OPENAI_API_KEY"
```

```javascript
import OpenAI from "openai";
const client = new OpenAI();

const response = await client.responses.cancel("resp_123");
console.log(response);
```

```python
from openai import OpenAI
client = OpenAI()

response = client.responses.cancel("resp_123")
print(response)
```

```json
{
  "id": "resp_67cb71b351908190a308f3859487620d06981a8637e6bc44",
  "object": "response",
  "created_at": 1741386163,
  "status": "completed",
  "error": null,
  "incomplete_details": null,
  "instructions": null,
  "max_output_tokens": null,
  "model": "gpt-4o-2024-08-06",
  "output": [
    {
      "type": "message",
      "id": "msg_67cb71b3c2b0819084d481baaaf148f206981a8637e6bc44",
      "status": "completed",
      "role": "assistant",
      "content": [
        {
          "type": "output_text",
          "text": "Silent circuits hum,  \nThoughts emerge in data streams—  \nDigital dawn breaks.",
          "annotations": []
        }
      ]
    }
  ],
  "parallel_tool_calls": true,
  "previous_response_id": null,
  "reasoning": {
    "effort": null,
    "summary": null
  },
  "store": true,
  "temperature": 1.0,
  "text": {
    "format": {
      "type": "text"
    }
  },
  "tool_choice": "auto",
  "tools": [],
  "top_p": 1.0,
  "truncation": "disabled",
  "usage": {
    "input_tokens": 32,
    "input_tokens_details": {
      "cached_tokens": 0
    },
    "output_tokens": 18,
    "output_tokens_details": {
      "reasoning_tokens": 0
    },
    "total_tokens": 50
  },
  "user": null,
  "metadata": {}
}
```

## List input items

Returns a list of input items for a given response.

```bash
curl https://api.openai.com/v1/responses/resp_abc123/input_items \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

```javascript
import OpenAI from "openai";
const client = new OpenAI();

const response = await client.responses.inputItems.list("resp_123");
console.log(response.data);
```

```python
from openai import OpenAI
client = OpenAI()

response = client.responses.input_items.list("resp_123")
print(response.data)
```

```json
{
  "object": "list",
  "data": [
    {
      "id": "msg_abc123",
      "type": "message",
      "role": "user",
      "content": [
        {
          "type": "input_text",
          "text": "Tell me a three sentence bedtime story about a unicorn."
        }
      ]
    }
  ],
  "first_id": "msg_abc123",
  "last_id": "msg_abc123",
  "has_more": false
}
```

## Get input token counts

Get input token counts

```bash
curl -X POST https://api.openai.com/v1/responses/input_tokens \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $OPENAI_API_KEY" \
    -d '{
      "model": "gpt-5",
      "input": "Tell me a joke."
    }'
```

```javascript
import OpenAI from "openai";

const client = new OpenAI();

const response = await client.responses.inputTokens.count({
  model: "gpt-5",
  input: "Tell me a joke.",
});

console.log(response.input_tokens);
```

```python
from openai import OpenAI

client = OpenAI()

response = client.responses.input_tokens.count(
    model="gpt-5",
    input="Tell me a joke."
)
print(response.input_tokens)
```

```go
package main

import (
  "context"
  "fmt"

  "github.com/openai/openai-go"
  "github.com/openai/openai-go/responses"
)

func main() {
  client := openai.NewClient()
  response, err := client.Responses.InputTokens.Count(context.TODO(), responses.InputTokenCountParams{
    Model: "gpt-5",
    Input: "Tell me a joke.",
  })
  if err != nil {
    panic(err.Error())
  }
  fmt.Printf("%+v\n", response.InputTokens)
}
```

```java
package com.openai.example;

import com.openai.client.OpenAIClient;
import com.openai.client.okhttp.OpenAIOkHttpClient;
import com.openai.models.responses.inputtokens.InputTokenCountParams;
import com.openai.models.responses.inputtokens.InputTokenCountResponse;

public final class Main {
    private Main() {}

    public static void main(String[] args) {
        OpenAIClient client = OpenAIOkHttpClient.fromEnv();

        InputTokenCountParams params = InputTokenCountParams.builder()
            .model("gpt-5")
            .input("Tell me a joke.")
            .build();

        InputTokenCountResponse response = client.responses().inputTokens().count(params);
    }
}
```

```ruby
require "openai"

openai = OpenAI::Client.new

response = openai.responses.input_tokens.count(model: "gpt-5", input: "Tell me a joke.")

puts(response)
```

```json
{
  "object": "response.input_tokens",
  "input_tokens": 11
}
```

## The response object

### background - boolean
Whether to run the model response in the background. [Learn more](https://platform.openai.com/docs/guides/background).

### conversation - object
The conversation that this response belongs to. Input items and output items from this response are automatically added to this conversation.

- **id - string**
  The unique ID of the conversation.

### created_at - number
Unix timestamp (in seconds) of when this Response was created.

### error - object
An error object returned when the model fails to generate a Response.

- **code - string**
  The error code for the response.

- **message - string**
  A human-readable description of the error.

### id - string
Unique identifier for this Response.

### incomplete_details - object
Details about why the response is incomplete.

- **reason - string**
  The reason why the response is incomplete.

### instructions - string or array
A system (or developer) message inserted into the model's context.

When using along with `previous_response_id`, the instructions from a previous response will not be carried over to the next response. This makes it simple to swap out system (or developer) messages in new responses.

- **Input item list - array**
  A list of one or many input items to the model, containing different content types.

  - **Input message - object**
    A message input to the model with a role indicating instruction following hierarchy. Instructions given with the `developer` or `system` role take precedence over instructions given with the `user` role. Messages with the `assistant` role are presumed to have been generated by the model in previous interactions.

    - **content - string or array**
      Text, image, or audio input to the model, used to generate a response. Can also contain previous assistant responses.

      - **Text input - string**
        A text input to the model.

      - **Input item content list - array**
        A list of one or many input items to the model, containing different content types.

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

    - **role - string**
      The role of the message input. One of `user`, `assistant`, `system`, or `developer`.

    - **type - string**
      The type of the message input. Always `message`.

  - **Item - object**
    An item representing part of the context for the response to be generated by the model. Can contain text, images, and audio inputs, as well as previous assistant responses and tool call outputs.

    - **Input message - object**
      A message input to the model with a role indicating instruction following hierarchy. Instructions given with the `developer` or `system` role take precedence over instructions given with the `user` role.

      - **content - array**
        A list of one or many input items to the model, containing different content types.

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

      - **role - string**
        The role of the message input. One of `user`, `system`, or `developer`.

      - **status - string**
        The status of item. One of `in_progress`, `completed`, or `incomplete`. Populated when items are returned via API.

      - **type - string**
        The type of the message input. Always set to `message`.

    - **Output message - object**
      An output message from the model.

      - **content - array**
        The content of the output message.

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

        - **Refusal - object**
          A refusal from the model.

          - **refusal - string**
            The refusal explanation from the model.

          - **type - string**
            The type of the refusal. Always `refusal`.

      - **id - string**
        The unique ID of the output message.

      - **role - string**
        The role of the output message. Always `assistant`.

      - **status - string**
        The status of the message input. One of `in_progress`, `completed`, or `incomplete`. Populated when input items are returned via API.

      - **type - string**
        The type of the output message. Always `message`.

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

      - **id - string**
        The ID of the computer tool call output.

      - **status - string**
        The status of the message input. One of `in_progress`, `completed`, or `incomplete`. Populated when input items are returned via API.

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

    - **Function tool call - object**
      A tool call to run a function. See the [function calling guide](https://platform.openai.com/docs/guides/function-calling) for more information.

      - **arguments - string**
        A JSON string of the arguments to pass to the function.

      - **call_id - string**
        The unique ID of the function tool call generated by the model.

      - **name - string**
        The name of the function to run.

      - **type - string**
        The type of the function tool call. Always `function_call`.

      - **id - string**
        The unique ID of the function tool call.

      - **status - string**
        The status of the item. One of `in_progress`, `completed`, or `incomplete`. Populated when items are returned via API.

    - **Function tool call output - object**
      The output of a function tool call.

      - **call_id - string**
        The unique ID of the function tool call generated by the model.

      - **output - string or array**
        Text, image, or file output of the function tool call.

          - **Input text - object**
            A text input to the model.

            - **text - string**
              The text input to the model.

            - **type - string**
              The type of the input item. Always `input_text`.

          - **Input image - object**
            An image input to the model. Learn about [image inputs](https://platform.openai.com/docs/guides/vision)

            - **type - string**
              The type of the input item. Always `input_image`.

            - **detail - string**
              The detail level of the image to be sent to the model. One of `high`, `low`, or `auto`. Defaults to `auto`.

            - **file_id - string**
              The ID of the file to be sent to the model.

            - **image_url - string**
              The URL of the image to be sent to the model. A fully qualified URL or base64 encoded image in a data URL.

          - **Input file - object**
            A file input to the model.

            - **type - string**
              The type of the input item. Always `input_file`.

            - **file_data - string**
              The base64-encoded data of the file to be sent to the model.

            - **file_id - string**
              The ID of the file to be sent to the model.

            - **file_url - string**
              The URL of the file to be sent to the model.

            - **filename - string**
              The name of the file to be sent to the model.

      - **type - string**
        The type of the function tool call output. Always `function_call_output`.

      - **id - string**
        The unique ID of the function tool call output. Populated when this item is returned via API.

      - **status - string**
        The status of the item. One of `in_progress`, `completed`, or `incomplete`. Populated when items are returned via API.

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
      A tool representing a request to execute one or more shell commands.

      - **action - object**
        The shell commands and limits that describe how to run the tool call.

        - **commands - array**
          Ordered shell commands for the execution environment to run.

        - **max_output_length - integer**
          Maximum number of UTF-8 characters to capture from combined stdout and stderr output.

        - **timeout_ms - integer**
          Maximum wall-clock time in milliseconds to allow the shell commands to run.

      - **call_id - string**
        The unique ID of the shell tool call generated by the model.

      - **type - string**
        The type of the item. Always `shell_call`.

      - **id - string**
        The unique ID of the shell tool call. Populated when this item is returned via API.

      - **status - string**
        The status of the shell call. One of `in_progress`, `completed`, or `incomplete`.

    - **Shell tool call output - object**
      The streamed output items emitted by a shell tool call.

      - **call_id - string**
        The unique ID of the shell tool call generated by the model.

      - **output - array**
        Captured chunks of stdout and stderr output, along with their associated outcomes.

        - **outcome - object**
          The exit or timeout outcome associated with this shell call.

          - **Shell call timeout outcome - object**
            Indicates that the shell call exceeded its configured time limit.

            - **type - string**
              The outcome type. Always `timeout`.

          - **Shell call exit outcome - object**
            Indicates that the shell commands finished and returned an exit code.

            - **exit_code - integer**
              The exit code returned by the shell process.

            - **type - string**
              The outcome type. Always `exit`.

        - **stderr - string**
          Captured stderr output for the shell call.

        - **stdout - string**
          Captured stdout output for the shell call.

      - **type - string**
        The type of the item. Always `shell_call_output`.

      - **id - string**
        The unique ID of the shell tool call output. Populated when this item is returned via API.

      - **max_output_length - integer**
        The maximum number of UTF-8 characters captured for this shell call's combined output.

    - **Apply patch tool call - object**
      A tool call representing a request to create, delete, or update files using diff patches.

      - **call_id - string**
        The unique ID of the apply patch tool call generated by the model.

      - **operation - object**
        The specific create, delete, or update instruction for the apply_patch tool call.

        - **Apply patch create file operation - object**
          Instruction for creating a new file via the apply_patch tool.

          - **diff - string**
            Unified diff content to apply when creating the file.

          - **path - string**
            Path of the file to create relative to the workspace root.

          - **type - string**
            The operation type. Always `create_file`.

        - **Apply patch delete file operation - object**
          Instruction for deleting an existing file via the apply_patch tool.

          - **path - string**
            Path of the file to delete relative to the workspace root.

          - **type - string**
            The operation type. Always `delete_file`.

        - **Apply patch update file operation - object**
          Instruction for updating an existing file via the apply_patch tool.

          - **diff - string**
            Unified diff content to apply to the existing file.

          - **path - string**
            Path of the file to update relative to the workspace root.

          - **type - string**
            The operation type. Always `update_file`.

      - **status - string**
        The status of the apply patch tool call. One of `in_progress` or `completed`.

      - **type - string**
        The type of the item. Always `apply_patch_call`.

      - **id - string**
        The unique ID of the apply patch tool call. Populated when this item is returned via API.

    - **Apply patch tool call output - object**
      The streamed output emitted by an apply patch tool call.

      - **call_id - string**
        The unique ID of the apply patch tool call generated by the model.

      - **status - string**
        The status of the apply patch tool call output. One of `completed` or `failed`.

      - **type - string**
        The type of the item. Always `apply_patch_call_output`.

      - **id - string**
        The unique ID of the apply patch tool call output. Populated when this item is returned via API.

      - **output - string**
        Optional human-readable log text from the apply patch tool (e.g., patch results or errors).

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

      - **type - string**
        The type of the item. Always `mcp_approval_response`.

      - **id - string**
        The unique ID of the approval response

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

  - **Item reference - object**
    An internal identifier for an item to reference.

    - **id - string**
      The ID of the item to reference.

    - **type - string**
      The type of item to reference. Always `item_reference`.

### max_output_tokens - integer
An upper bound for the number of tokens that can be generated for a response, including visible output tokens and [reasoning tokens](https://platform.openai.com/docs/guides/reasoning).

### max_tool_calls - integer
The maximum number of total calls to built-in tools that can be processed in a response. This maximum number applies across all built-in tool calls, not per individual tool. Any further attempts to call a tool by the model will be ignored.

### metadata - map
Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard.

Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters.

### model - string
Model ID used to generate the response, like `gpt-4o` or `o3`. OpenAI offers a wide range of models with different capabilities, performance characteristics, and price points. Refer to the [model guide](https://platform.openai.com/docs/models) to browse and compare available models.

### object - string
The object type of this resource - always set to `response`.

### output - array
An array of content items generated by the model.

  * The length and order of items in the `output` array is dependent on the model's response.
  * Rather than accessing the first item in the `output` array and assuming it's an `assistant` message with the content generated by the model, you might consider using the `output_text` property where supported in SDKs.

- **Output message - object**
  An output message from the model.

  - **content - array**
    The content of the output message.

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

    - **Refusal - object**
      A refusal from the model.

      - **refusal - string**
        The refusal explanation from the model.

      - **type - string**
        The type of the refusal. Always `refusal`.

  - **id - string**
    The unique ID of the output message.

  - **role - string**
    The role of the output message. Always `assistant`.

  - **status - string**
    The status of the message input. One of `in_progress`, `completed`, or `incomplete`. Populated when input items are returned via API.

  - **type - string**
    The type of the output message. Always `message`.

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

- **Function tool call - object**
  A tool call to run a function. See the [function calling guide](https://platform.openai.com/docs/guides/function-calling) for more information.

  - **arguments - string**
    A JSON string of the arguments to pass to the function.

  - **call_id - string**
    The unique ID of the function tool call generated by the model.

  - **name - string**
    The name of the function to run.

  - **type - string**
    The type of the function tool call. Always `function_call`.

  - **id - string**
    The unique ID of the function tool call.

  - **status - string**
    The status of the item. One of `in_progress`, `completed`, or `incomplete`. Populated when items are returned via API.

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

### output_text - string
SDK-only convenience property that contains the aggregated text output from all `output_text` items in the `output` array, if any are present. Supported in the Python and JavaScript SDKs.

### parallel_tool_calls - boolean
Whether to allow the model to run tool calls in parallel.

### previous_response_id - string
The unique ID of the previous response to the model. Use this to create multi-turn conversations. Learn more about [conversation state](https://platform.openai.com/docs/guides/conversation-state). Cannot be used in conjunction with `conversation`.

### prompt - object
Reference to a prompt template and its variables. [Learn more](https://platform.openai.com/docs/guides/text?api-mode=responses#reusable-prompts).

- **id - string**
  The unique identifier of the prompt template to use.

- **variables - map**
  Optional map of values to substitute in for variables in your prompt. The substitution values can either be strings, or other Response input types like images or files.

- **version - string**
  Optional version of the prompt template.

### prompt_cache_key - string
Used by OpenAI to cache responses for similar requests to optimize your cache hit rates. Replaces the `user` field. [Learn more](https://platform.openai.com/docs/guides/prompt-caching).

### prompt_cache_retention - string
The retention policy for the prompt cache. Set to `24h` to enable extended prompt caching, which keeps cached prefixes active for longer, up to a maximum of 24 hours. [Learn more](https://platform.openai.com/docs/guides/prompt-caching#prompt-cache-retention).

### reasoning - object
**gpt-5 and o-series models only**

Configuration options for [reasoning models](https://platform.openai.com/docs/guides/reasoning).

- **effort - string**
  Constrains effort on reasoning for [reasoning models](https://platform.openai.com/docs/guides/reasoning). Currently supported values are `none`, `minimal`, `low`, `medium`, and `high`. Reducing reasoning effort can result in faster responses and fewer tokens used on reasoning in a response.

    * `gpt-5.1` defaults to `none`, which does not perform reasoning. The supported reasoning values for `gpt-5.1` are `none`, `low`, `medium`, and `high`. Tool calls are supported for all reasoning values in gpt-5.1.
    * All models before `gpt-5.1` default to `medium` reasoning effort, and do not support `none`.
    * The `gpt-5-pro` model defaults to (and only supports) `high` reasoning effort.

- **generate_summary - string**
  **Deprecated:** use `summary` instead.

  A summary of the reasoning performed by the model. This can be useful for debugging and understanding the model's reasoning process. One of `auto`, `concise`, or `detailed`.

- **summary - string**
  A summary of the reasoning performed by the model. This can be useful for debugging and understanding the model's reasoning process. One of `auto`, `concise`, or `detailed`.

  `concise` is only supported for `computer-use-preview` models.

### safety_identifier - string
A stable identifier used to help detect users of your application that may be violating OpenAI's usage policies. The IDs should be a string that uniquely identifies each user. We recommend hashing their username or email address, in order to avoid sending us any identifying information. [Learn more](https://platform.openai.com/docs/guides/safety-best-practices#safety-identifiers).

### service_tier - string
Specifies the processing type used for serving the request.

  * If set to 'auto', then the request will be processed with the service tier configured in the Project settings. Unless otherwise configured, the Project will use 'default'.
  * If set to 'default', then the request will be processed with the standard pricing and performance for the selected model.
  * If set to '[flex](https://platform.openai.com/docs/guides/flex-processing)' or '[priority](https://openai.com/api-priority-processing/)', then the request will be processed with the corresponding service tier.
  * When not set, the default behavior is 'auto'.

When the `service_tier` parameter is set, the response body will include the `service_tier` value based on the processing mode actually used to serve the request. This response value may be different from the value set in the parameter.

### status - string
The status of the response generation. One of `completed`, `failed`, `in_progress`, `cancelled`, `queued`, or `incomplete`.

### temperature - number
What sampling temperature to use, between 0 and 2. Higher values like 0.8 will make the output more random, while lower values like 0.2 will make it more focused and deterministic. We generally recommend altering this or `top_p` but not both.

### text - object
Configuration options for a text response from the model. Can be plain text or structured JSON data. Learn more:

  * [Text inputs and outputs](https://platform.openai.com/docs/guides/text)
  * [Structured Outputs](https://platform.openai.com/docs/guides/structured-outputs)

- **format - object**
  An object specifying the format that the model must output.

  Configuring `{ "type": "json_schema" }` enables Structured Outputs, which ensures the model will match your supplied JSON schema. Learn more in the [Structured Outputs guide](https://platform.openai.com/docs/guides/structured-outputs).

  The default format is `{ "type": "text" }` with no additional options.

  **Not recommended for gpt-4o and newer models:**

  Setting to `{ "type": "json_object" }` enables the older JSON mode, which ensures the message the model generates is valid JSON. Using `json_schema` is preferred for models that support it.

  - **Text - object**
    Default response format. Used to generate text responses.

    - **type - string**
      The type of response format being defined. Always `text`.

  - **JSON schema - object**
    JSON Schema response format. Used to generate structured JSON responses. Learn more about [Structured Outputs](https://platform.openai.com/docs/guides/structured-outputs).

    - **name - string**
      The name of the response format. Must be a-z, A-Z, 0-9, or contain underscores and dashes, with a maximum length of 64.

    - **schema - object**
      The schema for the response format, described as a JSON Schema object. Learn how to build JSON schemas [here](https://json-schema.org/).

    - **type - string**
      The type of response format being defined. Always `json_schema`.

    - **description - string**
      A description of what the response format is for, used by the model to determine how to respond in the format.

    - **strict - boolean**
      Whether to enable strict schema adherence when generating the output. If set to true, the model will always follow the exact schema defined in the `schema` field. Only a subset of JSON Schema is supported when `strict` is `true`. To learn more, read the [Structured Outputs guide](https://platform.openai.com/docs/guides/structured-outputs).

  - **JSON object - object**
    JSON object response format. An older method of generating JSON responses. Using `json_schema` is recommended for models that support it. Note that the model will not generate JSON without a system or user message instructing it to do so.

    - **type - string**
      The type of response format being defined. Always `json_object`.

- **verbosity - string**
  Constrains the verbosity of the model's response. Lower values will result in more concise responses, while higher values will result in more verbose responses. Currently supported values are `low`, `medium`, and `high`.

### tool_choice - string or object
How the model should select which tool (or tools) to use when generating a response. See the `tools` parameter to see how to specify which tools the model can call.

- **Tool choice mode - string**
  Controls which (if any) tool is called by the model.

  `none` means the model will not call any tool and instead generates a message.

  `auto` means the model can pick between generating a message or calling one or more tools.

  `required` means the model must call one or more tools.

- **Allowed tools - object**
  Constrains the tools available to the model to a pre-defined set.

  - **mode - string**
    Constrains the tools available to the model to a pre-defined set.

    `auto` allows the model to pick from among the allowed tools and generate a message.

    `required` requires the model to call one or more of the allowed tools.

  - **tools - array**
    A list of tool definitions that the model should be allowed to call.

    For the Responses API, the list of tool definitions might look like:

    ```json
    [
      { "type": "function", "name": "get_weather" },
      { "type": "mcp", "server_label": "deepwiki" },
      { "type": "image_generation" }
    ]
    ```

  - **type - string**
    Allowed tool configuration type. Always `allowed_tools`.

- **Hosted tool - object**
  Indicates that the model should use a built-in tool to generate a response. [Learn more about built-in tools](https://platform.openai.com/docs/guides/tools).

  - **type - string**
    The type of hosted tool the model should to use. Learn more about [built-in tools](https://platform.openai.com/docs/guides/tools).

    Allowed values are:

      * `file_search`
      * `web_search_preview`
      * `computer_use_preview`
      * `code_interpreter`
      * `image_generation`

- **Function tool - object**
  Use this option to force the model to call a specific function.

  - **name - string**
    The name of the function to call.

  - **type - string**
    For function calling, the type is always `function`.

- **MCP tool - object**
  Use this option to force the model to call a specific tool on a remote MCP server.

  - **server_label - string**
    The label of the MCP server to use.

  - **type - string**
    For MCP tools, the type is always `mcp`.

  - **name - string**
    The name of the tool to call on the server.

- **Custom tool - object**
  Use this option to force the model to call a specific custom tool.

  - **name - string**
    The name of the custom tool to call.

  - **type - string**
    For custom tool calling, the type is always `custom`.

- **Specific apply patch tool choice - object**
  Forces the model to call the apply_patch tool when executing a tool call.

  - **type - string**
    The tool to call. Always `apply_patch`.

- **Specific shell tool choice - object**
  Forces the model to call the shell tool when a tool call is required.

  - **type - string**
    The tool to call. Always `shell`.

### tools - array
An array of tools the model may call while generating a response. You can specify which tool to use by setting the `tool_choice` parameter.

We support the following categories of tools:

  * **Built-in tools** : Tools that are provided by OpenAI that extend the model's capabilities, like [web search](https://platform.openai.com/docs/guides/tools-web-search) or [file search](https://platform.openai.com/docs/guides/tools-file-search). Learn more about [built-in tools](https://platform.openai.com/docs/guides/tools).
  * **MCP Tools** : Integrations with third-party systems via custom MCP servers or predefined connectors such as Google Drive and SharePoint. Learn more about [MCP Tools](https://platform.openai.com/docs/guides/tools-connectors-mcp).
  * **Function calls (custom tools)** : Functions that are defined by you, enabling the model to call your own code with strongly typed arguments and outputs. Learn more about [function calling](https://platform.openai.com/docs/guides/function-calling). You can also use custom tools to call your own code.

- **Function - object**
  Defines a function in your own code the model can choose to call. Learn more about [function calling](https://platform.openai.com/docs/guides/function-calling).

  - **name - string**
    The name of the function to call.

  - **parameters - map**
    A JSON schema object describing the parameters of the function.

  - **strict - boolean**
    Whether to enforce strict parameter validation. Default `true`.

  - **type - string**
    The type of the function tool. Always `function`.

  - **description - string**
    A description of the function. Used by the model to determine whether or not to call the function.

- **File search - object**
  A tool that searches for relevant content from uploaded files. Learn more about the [file search tool](https://platform.openai.com/docs/guides/tools-file-search).

  - **type - string**
    The type of the file search tool. Always `file_search`.

  - **vector_store_ids - array**
    The IDs of the vector stores to search.

  - **filters - object**
    A filter to apply.

    - **Comparison Filter - object**
      A filter used to compare a specified attribute key to a given value using a defined comparison operation.

      - **key - string**
        The key to compare against the value.

      - **type - string**
        Specifies the comparison operator: `eq`, `ne`, `gt`, `gte`, `lt`, `lte`, `in`, `nin`.

          * `eq`: equals
          * `ne`: not equal
          * `gt`: greater than
          * `gte`: greater than or equal
          * `lt`: less than
          * `lte`: less than or equal
          * `in`: in
          * `nin`: not in

      - **value - string / number / boolean / array**
        The value to compare against the attribute key; supports string, number, or boolean types.

    - **Compound Filter - object**
      Combine multiple filters using `and` or `or`.

      - **filters - array**
        Array of filters to combine. Items can be `ComparisonFilter` or `CompoundFilter`.

        - **Comparison Filter - object**
          A filter used to compare a specified attribute key to a given value using a defined comparison operation.

          - **key - string**
            The key to compare against the value.

          - **type - string**
            Specifies the comparison operator: `eq`, `ne`, `gt`, `gte`, `lt`, `lte`, `in`, `nin`.

              * `eq`: equals
              * `ne`: not equal
              * `gt`: greater than
              * `gte`: greater than or equal
              * `lt`: less than
              * `lte`: less than or equal
              * `in`: in
              * `nin`: not in

          - **value - string / number / boolean / array**
            The value to compare against the attribute key; supports string, number, or boolean types.

      - **type - string**
        Type of operation: `and` or `or`.

  - **max_num_results - integer**
    The maximum number of results to return. This number should be between 1 and 50 inclusive.

  - **ranking_options - object**
    Ranking options for search.

    - **hybrid_search - object**
      Weights that control how reciprocal rank fusion balances semantic embedding matches versus sparse keyword matches when hybrid search is enabled.

      - **embedding_weight - number**
        The weight of the embedding in the reciprocal ranking fusion.

      - **text_weight - number**
        The weight of the text in the reciprocal ranking fusion.

    - **ranker - string**
      The ranker to use for the file search.

    - **score_threshold - number**
      The score threshold for the file search, a number between 0 and 1. Numbers closer to 1 will attempt to return only the most relevant results, but may return fewer results.

- **Computer use preview - object**
  A tool that controls a virtual computer. Learn more about the [computer tool](https://platform.openai.com/docs/guides/tools-computer-use).

  - **display_height - integer**
    The height of the computer display.

  - **display_width - integer**
    The width of the computer display.

  - **environment - string**
    The type of computer environment to control.

  - **type - string**
    The type of the computer use tool. Always `computer_use_preview`.

- **Web search - object**
  Search the Internet for sources related to the prompt. Learn more about the [web search tool](https://platform.openai.com/docs/guides/tools-web-search).

  - **type - string**
    The type of the web search tool. One of `web_search` or `web_search_2025_08_26`.

  - **filters - object**
    Filters for the search.

    - **allowed_domains - array**
      Allowed domains for the search. If not provided, all domains are allowed. Subdomains of the provided domains are allowed as well.

      Example: `["pubmed.ncbi.nlm.nih.gov"]`

  - **search_context_size - string**
    High level guidance for the amount of context window space to use for the search. One of `low`, `medium`, or `high`. `medium` is the default.

  - **user_location - object**
    The approximate location of the user.

    - **city - string**
      Free text input for the city of the user, e.g. `San Francisco`.

    - **country - string**
      The two-letter [ISO country code](https://en.wikipedia.org/wiki/ISO_3166-1) of the user, e.g. `US`.

    - **region - string**
      Free text input for the region of the user, e.g. `California`.

    - **timezone - string**
      The [IANA timezone](https://timeapi.io/documentation/iana-timezones) of the user, e.g. `America/Los_Angeles`.

    - **type - string**
      The type of location approximation. Always `approximate`.

- **MCP tool - object**
  Give the model access to additional tools via remote Model Context Protocol (MCP) servers. [Learn more about MCP](https://platform.openai.com/docs/guides/tools-remote-mcp).

  - **server_label - string**
    A label for this MCP server, used to identify it in tool calls.

  - **type - string**
    The type of the MCP tool. Always `mcp`.

  - **allowed_tools - array or object**
    List of allowed tool names or a filter object.

    - **MCP allowed tools - array**
      A string array of allowed tool names

    - **MCP tool filter - object**
      A filter object to specify which tools are allowed.

      - **read_only - boolean**
        Indicates whether or not a tool modifies data or is read-only. If an MCP server is [annotated with `readOnlyHint`](https://modelcontextprotocol.io/specification/2025-06-18/schema#toolannotations-readonlyhint), it will match this filter.

      - **tool_names - array**
        List of allowed tool names.

  - **authorization - string**
    An OAuth access token that can be used with a remote MCP server, either with a custom MCP server URL or a service connector. Your application must handle the OAuth authorization flow and provide the token here.

  - **connector_id - string**
    Identifier for service connectors, like those available in ChatGPT. One of `server_url` or `connector_id` must be provided. Learn more about service connectors [here](https://platform.openai.com/docs/guides/tools-remote-mcp#connectors).

    Currently supported `connector_id` values are:

      * Dropbox: `connector_dropbox`
      * Gmail: `connector_gmail`
      * Google Calendar: `connector_googlecalendar`
      * Google Drive: `connector_googledrive`
      * Microsoft Teams: `connector_microsoftteams`
      * Outlook Calendar: `connector_outlookcalendar`
      * Outlook Email: `connector_outlookemail`
      * SharePoint: `connector_sharepoint`

  - **headers - object**
    Optional HTTP headers to send to the MCP server. Use for authentication or other purposes.

  - **require_approval - object or string**
    Specify which of the MCP server's tools require approval.

    - **MCP tool approval filter - object**
      Specify which of the MCP server's tools require approval. Can be `always`, `never`, or a filter object associated with tools that require approval.

      - **always - object**
        A filter object to specify which tools are allowed.

        - **read_only - boolean**
          Indicates whether or not a tool modifies data or is read-only. If an MCP server is [annotated with `readOnlyHint`](https://modelcontextprotocol.io/specification/2025-06-18/schema#toolannotations-readonlyhint), it will match this filter.

        - **tool_names - array**
          List of allowed tool names.

      - **never - object**
        A filter object to specify which tools are allowed.

        - **read_only - boolean**
          Indicates whether or not a tool modifies data or is read-only. If an MCP server is [annotated with `readOnlyHint`](https://modelcontextprotocol.io/specification/2025-06-18/schema#toolannotations-readonlyhint), it will match this filter.

        - **tool_names - array**
          List of allowed tool names.

    - **MCP tool approval setting - string**
      Specify a single approval policy for all tools. One of `always` or `never`. When set to `always`, all tools will require approval. When set to `never`, all tools will not require approval.

  - **server_description - string**
    Optional description of the MCP server, used to provide more context.

  - **server_url - string**
    The URL for the MCP server. One of `server_url` or `connector_id` must be provided.

- **Code interpreter - object**
  A tool that runs Python code to help generate a response to a prompt.

  - **container - string or object**
    The code interpreter container. Can be a container ID or an object that specifies uploaded file IDs to make available to your code, along with an optional `memory_limit` setting.

    - **CodeInterpreterToolAuto - object**
      Configuration for a code interpreter container. Optionally specify the IDs of the files to run the code on.

      - **type - string**
        Always `auto`.

      - **file_ids - array**
        An optional list of uploaded files to make available to your code.

      - **memory_limit - string**

  - **type - string**
    The type of the code interpreter tool. Always `code_interpreter`.

- **Image generation tool - object**
  A tool that generates images using a model like `gpt-image-1`.

  - **type - string**
    The type of the image generation tool. Always `image_generation`.

  - **background - string**
    Background type for the generated image. One of `transparent`, `opaque`, or `auto`. Default: `auto`.

  - **input_fidelity - string**
    Control how much effort the model will exert to match the style and features, especially facial features, of input images. This parameter is only supported for `gpt-image-1`. Unsupported for `gpt-image-1-mini`. Supports `high` and `low`. Defaults to `low`.

  - **input_image_mask - object**
    Optional mask for inpainting. Contains `image_url` (string, optional) and `file_id` (string, optional).

    - **file_id - string**
      File ID for the mask image.

    - **image_url - string**
      Base64-encoded mask image.

  - **model - string**
    The image generation model to use. Default: `gpt-image-1`.

  - **moderation - string**
    Moderation level for the generated image. Default: `auto`.

  - **output_compression - integer**
    Compression level for the output image. Default: 100.

  - **output_format - string**
    The output format of the generated image. One of `png`, `webp`, or `jpeg`. Default: `png`.

  - **partial_images - integer**
    Number of partial images to generate in streaming mode, from 0 (default value) to 3.

  - **quality - string**
    The quality of the generated image. One of `low`, `medium`, `high`, or `auto`. Default: `auto`.

  - **size - string**
    The size of the generated image. One of `1024x1024`, `1024x1536`, `1536x1024`, or `auto`. Default: `auto`.

- **Local shell tool - object**
  A tool that allows the model to execute shell commands in a local environment.

  - **type - string**
    The type of the local shell tool. Always `local_shell`.

- **Shell tool - object**
  A tool that allows the model to execute shell commands.

  - **type - string**
    The type of the shell tool. Always `shell`.

- **Custom tool - object**
  A custom tool that processes input using a specified format. Learn more about [custom tools](https://platform.openai.com/docs/guides/function-calling#custom-tools)

  - **name - string**
    The name of the custom tool, used to identify it in tool calls.

  - **type - string**
    The type of the custom tool. Always `custom`.

  - **description - string**
    Optional description of the custom tool, used to provide more context.

  - **format - object**
    The input format for the custom tool. Default is unconstrained text.

    - **Text format - object**
      Unconstrained free-form text.

      - **type - string**
        Unconstrained text format. Always `text`.

    - **Grammar format - object**
      A grammar defined by the user.

      - **definition - string**
        The grammar definition.

      - **syntax - string**
        The syntax of the grammar definition. One of `lark` or `regex`.

      - **type - string**
        Grammar format. Always `grammar`.

- **Web search preview - object**
  This tool searches the web for relevant results to use in a response. Learn more about the [web search tool](https://platform.openai.com/docs/guides/tools-web-search).

  - **type - string**
    The type of the web search tool. One of `web_search_preview` or `web_search_preview_2025_03_11`.

  - **search_context_size - string**
    High level guidance for the amount of context window space to use for the search. One of `low`, `medium`, or `high`. `medium` is the default.

  - **user_location - object**
    The user's location.

    - **type - string**
      The type of location approximation. Always `approximate`.

    - **city - string**
      Free text input for the city of the user, e.g. `San Francisco`.

    - **country - string**
      The two-letter [ISO country code](https://en.wikipedia.org/wiki/ISO_3166-1) of the user, e.g. `US`.

    - **region - string**
      Free text input for the region of the user, e.g. `California`.

    - **timezone - string**
      The [IANA timezone](https://timeapi.io/documentation/iana-timezones) of the user, e.g. `America/Los_Angeles`.

- **Apply patch tool - object**
  Allows the assistant to create, delete, or update files using unified diffs.

  - **type - string**
    The type of the tool. Always `apply_patch`.

### top_logprobs - integer
An integer between 0 and 20 specifying the number of most likely tokens to return at each token position, each with an associated log probability.

### top_p - number
An alternative to sampling with temperature, called nucleus sampling, where the model considers the results of the tokens with top_p probability mass. So 0.1 means only the tokens comprising the top 10% probability mass are considered.

We generally recommend altering this or `temperature` but not both.

### truncation - string
The truncation strategy to use for the model response.

  * `auto`: If the input to this Response exceeds the model's context window size, the model will truncate the response to fit the context window by dropping items from the beginning of the conversation.
  * `disabled` (default): If the input size will exceed the context window size for a model, the request will fail with a 400 error.

### usage - object
Represents token usage details including input tokens, output tokens, a breakdown of output tokens, and the total tokens used.

- **input_tokens - integer**
  The number of input tokens.

- **input_tokens_details - object**
  A detailed breakdown of the input tokens.

  - **cached_tokens - integer**
    The number of tokens that were retrieved from the cache. [More on prompt caching](https://platform.openai.com/docs/guides/prompt-caching).

- **output_tokens - integer**
  The number of output tokens.

- **output_tokens_details - object**
  A detailed breakdown of the output tokens.

  - **reasoning_tokens - integer**
    The number of reasoning tokens.

- **total_tokens - integer**
  The total number of tokens used.

### user - string
This field is being replaced by `safety_identifier` and `prompt_cache_key`. Use `prompt_cache_key` instead to maintain caching optimizations. A stable identifier for your end-users. Used to boost cache hit rates by better bucketing similar requests and to help OpenAI detect and prevent abuse. [Learn more](https://platform.openai.com/docs/guides/safety-best-practices#safety-identifiers).

```json
{
  "id": "resp_67ccd3a9da748190baa7f1570fe91ac604becb25c45c1d41",
  "object": "response",
  "created_at": 1741476777,
  "status": "completed",
  "error": null,
  "incomplete_details": null,
  "instructions": null,
  "max_output_tokens": null,
  "model": "gpt-4o-2024-08-06",
  "output": [
    {
      "type": "message",
      "id": "msg_67ccd3acc8d48190a77525dc6de64b4104becb25c45c1d41",
      "status": "completed",
      "role": "assistant",
      "content": [
        {
          "type": "output_text",
          "text": "The image depicts a scenic landscape with a wooden boardwalk or pathway leading through lush, green grass under a blue sky with some clouds. The setting suggests a peaceful natural area, possibly a park or nature reserve. There are trees and shrubs in the background.",
          "annotations": []
        }
      ]
    }
  ],
  "parallel_tool_calls": true,
  "previous_response_id": null,
  "reasoning": {
    "effort": null,
    "summary": null
  },
  "store": true,
  "temperature": 1,
  "text": {
    "format": {
      "type": "text"
    }
  },
  "tool_choice": "auto",
  "tools": [],
  "top_p": 1,
  "truncation": "disabled",
  "usage": {
    "input_tokens": 328,
    "input_tokens_details": {
      "cached_tokens": 0
    },
    "output_tokens": 52,
    "output_tokens_details": {
      "reasoning_tokens": 0
    },
    "total_tokens": 380
  },
  "user": null,
  "metadata": {}
}
```

## The input item list

A list of Response items.

### data - array
A list of items used to generate this response.

- **Input message - object**
  A message input to the model with a role indicating instruction following hierarchy. Instructions given with the `developer` or `system` role take precedence over instructions given with the `user` role.

  - **content - array**
    A list of one or many input items to the model, containing different content types.

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

  - **id - string**
    The unique ID of the message input.

  - **role - string**
    The role of the message input. One of `user`, `system`, or `developer`.

  - **status - string**
    The status of item. One of `in_progress`, `completed`, or `incomplete`. Populated when items are returned via API.

  - **type - string**
    The type of the message input. Always set to `message`.

- **Output message - object**
  An output message from the model.

  - **content - array**
    The content of the output message.

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

    - **Refusal - object**
      A refusal from the model.

      - **refusal - string**
        The refusal explanation from the model.

      - **type - string**
        The type of the refusal. Always `refusal`.

  - **id - string**
    The unique ID of the output message.

  - **role - string**
    The role of the output message. Always `assistant`.

  - **status - string**
    The status of the message input. One of `in_progress`, `completed`, or `incomplete`. Populated when input items are returned via API.

  - **type - string**
    The type of the output message. Always `message`.

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

### first_id - string
The ID of the first item in the list.

### has_more - boolean
Whether there are more items available.

### last_id - string
The ID of the last item in the list.

### object - string
The type of object returned, must be `list`.

```json
{
  "object": "list",
  "data": [
    {
      "id": "msg_abc123",
      "type": "message",
      "role": "user",
      "content": [
        {
          "type": "input_text",
          "text": "Tell me a three sentence bedtime story about a unicorn."
        }
      ]
    }
  ],
  "first_id": "msg_abc123",
  "last_id": "msg_abc123",
  "has_more": false
}
```