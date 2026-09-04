# Responses streaming events

> For the complete documentation index, see [llms.txt](https://developers.openai.com/llms.txt). Markdown versions of documentation pages are available by appending `.md` to the page URL.

When you [create a Response](https://developers.openai.com/docs/api-reference/responses/create) with
`stream` set to `true`, the server will emit server-sent events to the
client as the Response is generated. This section contains the events that
are emitted by the server.

[Learn more about streaming responses](https://developers.openai.com/docs/guides/streaming-responses?api-mode=responses).

## response.created

An event that is emitted when a response is created.

### Schema

Schema name: `ResponseCreatedEvent`

### Example

```json
{
  "type": "response.created",
  "response": {
    "id": "resp_67ccfcdd16748190a91872c75d38539e09e4d4aac714747c",
    "object": "response",
    "created_at": 1741487325,
    "status": "in_progress",
    "completed_at": null,
    "error": null,
    "incomplete_details": null,
    "instructions": null,
    "max_output_tokens": null,
    "model": "gpt-6-astra",
    "output": [],
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
    "usage": null,
    "user": null,
    "metadata": {}
  },
  "sequence_number": 1
}
```

## response.in_progress

Emitted when the response is in progress.

### Schema

Schema name: `ResponseInProgressEvent`

### Example

```json
{
  "type": "response.in_progress",
  "response": {
    "id": "resp_67ccfcdd16748190a91872c75d38539e09e4d4aac714747c",
    "object": "response",
    "created_at": 1741487325,
    "status": "in_progress",
    "completed_at": null,
    "error": null,
    "incomplete_details": null,
    "instructions": null,
    "max_output_tokens": null,
    "model": "gpt-6-astra",
    "output": [],
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
    "usage": null,
    "user": null,
    "metadata": {}
  },
  "sequence_number": 1
}
```

## response.completed

Emitted when the model response is complete.

### Schema

Schema name: `ResponseCompletedEvent`

### Example

```json
{
  "type": "response.completed",
  "response": {
    "id": "resp_123",
    "object": "response",
    "created_at": 1740855869,
    "status": "completed",
    "completed_at": 1740855870,
    "error": null,
    "incomplete_details": null,
    "input": [],
    "instructions": null,
    "max_output_tokens": null,
    "model": "gpt-6-astra",
    "output": [
      {
        "id": "msg_123",
        "type": "message",
        "role": "assistant",
        "content": [
          {
            "type": "output_text",
            "text": "In a shimmering forest under a sky full of stars, a lonely unicorn named Lila discovered a hidden pond that glowed with moonlight. Every night, she would leave sparkling, magical flowers by the water's edge, hoping to share her beauty with others. One enchanting evening, she woke to find a group of friendly animals gathered around, eager to be friends and share in her magic.",
            "annotations": []
          }
        ]
      }
    ],
    "previous_response_id": null,
    "reasoning_effort": null,
    "store": false,
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
      "input_tokens": 0,
      "output_tokens": 0,
      "output_tokens_details": {
        "reasoning_tokens": 0
      },
      "total_tokens": 0
    },
    "user": null,
    "metadata": {}
  },
  "sequence_number": 1
}
```

## response.failed

An event that is emitted when a response fails.

### Schema

Schema name: `ResponseFailedEvent`

### Example

```json
{
  "type": "response.failed",
  "response": {
    "id": "resp_123",
    "object": "response",
    "created_at": 1740855869,
    "status": "failed",
    "completed_at": null,
    "error": {
      "code": "server_error",
      "message": "The model failed to generate a response."
    },
    "incomplete_details": null,
    "instructions": null,
    "max_output_tokens": null,
    "model": "gpt-6-astra",
    "output": [],
    "previous_response_id": null,
    "reasoning_effort": null,
    "store": false,
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
    "usage": null,
    "user": null,
    "metadata": {}
  }
}
```

## response.incomplete

An event that is emitted when a response finishes as incomplete.

Over WebSocket, steering can finish a response with
`response.incomplete_details.reason` set to `steered`, followed automatically
by a successor `response.created` that commits the queued steering input.

### Schema

Schema name: `ResponseIncompleteEvent`

### Example

```json
{
  "type": "response.incomplete",
  "response": {
    "id": "resp_123",
    "object": "response",
    "created_at": 1740855869,
    "status": "incomplete",
    "completed_at": null,
    "error": null,
    "incomplete_details": {
      "reason": "max_tokens"
    },
    "instructions": null,
    "max_output_tokens": null,
    "model": "gpt-6-astra",
    "output": [],
    "previous_response_id": null,
    "reasoning_effort": null,
    "store": false,
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
    "usage": null,
    "user": null,
    "metadata": {}
  },
  "sequence_number": 1
}
```

## response.output_item.added

Emitted when a new output item is added.

### Schema

Schema name: `ResponseOutputItemAddedEvent`

### Example

```json
{
  "type": "response.output_item.added",
  "output_index": 0,
  "item": {
    "id": "msg_123",
    "status": "in_progress",
    "type": "message",
    "role": "assistant",
    "content": []
  },
  "sequence_number": 1
}
```

## response.output_item.done

Emitted when an output item is marked done.

### Schema

Schema name: `ResponseOutputItemDoneEvent`

### Example

```json
{
  "type": "response.output_item.done",
  "output_index": 0,
  "item": {
    "id": "msg_123",
    "status": "completed",
    "type": "message",
    "role": "assistant",
    "content": [
      {
        "type": "output_text",
        "text": "In a shimmering forest under a sky full of stars, a lonely unicorn named Lila discovered a hidden pond that glowed with moonlight. Every night, she would leave sparkling, magical flowers by the water's edge, hoping to share her beauty with others. One enchanting evening, she woke to find a group of friendly animals gathered around, eager to be friends and share in her magic.",
        "annotations": []
      }
    ]
  },
  "sequence_number": 1
}
```

## response.content_part.added

Emitted when a new content part is added.

### Schema

Schema name: `ResponseContentPartAddedEvent`

### Example

```json
{
  "type": "response.content_part.added",
  "item_id": "msg_123",
  "output_index": 0,
  "content_index": 0,
  "part": {
    "type": "output_text",
    "text": "",
    "annotations": []
  },
  "sequence_number": 1
}
```

## response.content_part.done

Emitted when a content part is done.

### Schema

Schema name: `ResponseContentPartDoneEvent`

### Example

```json
{
  "type": "response.content_part.done",
  "item_id": "msg_123",
  "output_index": 0,
  "content_index": 0,
  "sequence_number": 1,
  "part": {
    "type": "output_text",
    "text": "In a shimmering forest under a sky full of stars, a lonely unicorn named Lila discovered a hidden pond that glowed with moonlight. Every night, she would leave sparkling, magical flowers by the water's edge, hoping to share her beauty with others. One enchanting evening, she woke to find a group of friendly animals gathered around, eager to be friends and share in her magic.",
    "annotations": []
  }
}
```

## response.output_text.delta

Emitted when there is an additional text delta.

### Schema

Schema name: `ResponseTextDeltaEvent`

### Example

```json
{
  "type": "response.output_text.delta",
  "item_id": "msg_123",
  "output_index": 0,
  "content_index": 0,
  "delta": "In",
  "sequence_number": 1
}
```

## response.output_text.done

Emitted when text content is finalized.

### Schema

Schema name: `ResponseTextDoneEvent`

### Example

```json
{
  "type": "response.output_text.done",
  "item_id": "msg_123",
  "output_index": 0,
  "content_index": 0,
  "text": "In a shimmering forest under a sky full of stars, a lonely unicorn named Lila discovered a hidden pond that glowed with moonlight. Every night, she would leave sparkling, magical flowers by the water's edge, hoping to share her beauty with others. One enchanting evening, she woke to find a group of friendly animals gathered around, eager to be friends and share in her magic.",
  "sequence_number": 1
}
```

## response.refusal.delta

Emitted when there is a partial refusal text.

### Schema

Schema name: `ResponseRefusalDeltaEvent`

### Example

```json
{
  "type": "response.refusal.delta",
  "item_id": "msg_123",
  "output_index": 0,
  "content_index": 0,
  "delta": "refusal text so far",
  "sequence_number": 1
}
```

## response.refusal.done

Emitted when refusal text is finalized.

### Schema

Schema name: `ResponseRefusalDoneEvent`

### Example

```json
{
  "type": "response.refusal.done",
  "item_id": "item-abc",
  "output_index": 1,
  "content_index": 2,
  "refusal": "final refusal text",
  "sequence_number": 1
}
```

## response.function_call_arguments.delta

Emitted when there is a partial function-call arguments delta.

### Schema

Schema name: `ResponseFunctionCallArgumentsDeltaEvent`

### Example

```json
{
  "type": "response.function_call_arguments.delta",
  "item_id": "item-abc",
  "output_index": 0,
  "delta": "{ \"arg\":"
  "sequence_number": 1
}
```

## response.function_call_arguments.done

Emitted when function-call arguments are finalized.

### Schema

Schema name: `ResponseFunctionCallArgumentsDoneEvent`

### Example

```json
{
  "type": "response.function_call_arguments.done",
  "item_id": "item-abc",
  "name": "get_weather",
  "output_index": 1,
  "arguments": "{ \"arg\": 123 }",
  "sequence_number": 1
}
```

## response.file_search_call.in_progress

Emitted when a file search call is initiated.

### Schema

Schema name: `ResponseFileSearchCallInProgressEvent`

### Example

```json
{
  "type": "response.file_search_call.in_progress",
  "output_index": 0,
  "item_id": "fs_123",
  "sequence_number": 1
}
```

## response.file_search_call.searching

Emitted when a file search is currently searching.

### Schema

Schema name: `ResponseFileSearchCallSearchingEvent`

### Example

```json
{
  "type": "response.file_search_call.searching",
  "output_index": 0,
  "item_id": "fs_123",
  "sequence_number": 1
}
```

## response.file_search_call.completed

Emitted when a file search call is completed (results found).

### Schema

Schema name: `ResponseFileSearchCallCompletedEvent`

### Example

```json
{
  "type": "response.file_search_call.completed",
  "output_index": 0,
  "item_id": "fs_123",
  "sequence_number": 1
}
```

## response.web_search_call.in_progress

Emitted when a web search call is initiated.

### Schema

Schema name: `ResponseWebSearchCallInProgressEvent`

### Example

```json
{
  "type": "response.web_search_call.in_progress",
  "output_index": 0,
  "item_id": "ws_123",
  "sequence_number": 0
}
```

## response.web_search_call.searching

Emitted when a web search call is executing.

### Schema

Schema name: `ResponseWebSearchCallSearchingEvent`

### Example

```json
{
  "type": "response.web_search_call.searching",
  "output_index": 0,
  "item_id": "ws_123",
  "sequence_number": 0
}
```

## response.web_search_call.completed

Emitted when a web search call is completed.

### Schema

Schema name: `ResponseWebSearchCallCompletedEvent`

### Example

```json
{
  "type": "response.web_search_call.completed",
  "output_index": 0,
  "item_id": "ws_123",
  "sequence_number": 0
}
```

## response.reasoning_summary_part.added

Emitted when a new reasoning summary part is added.

### Schema

Schema name: `ResponseReasoningSummaryPartAddedEvent`

### Example

```json
{
  "type": "response.reasoning_summary_part.added",
  "item_id": "rs_6806bfca0b2481918a5748308061a2600d3ce51bdffd5476",
  "output_index": 0,
  "summary_index": 0,
  "part": {
    "type": "summary_text",
    "text": ""
  },
  "sequence_number": 1
}
```

## response.reasoning_summary_part.done

Emitted when a reasoning summary part is completed.

### Schema

Schema name: `ResponseReasoningSummaryPartDoneEvent`

### Example

```json
{
  "type": "response.reasoning_summary_part.done",
  "item_id": "rs_6806bfca0b2481918a5748308061a2600d3ce51bdffd5476",
  "output_index": 0,
  "summary_index": 0,
  "part": {
    "type": "summary_text",
    "text": "**Responding to a greeting**\n\nThe user just said, \"Hello!\" So, it seems I need to engage. I'll greet them back and offer help since they're looking to chat. I could say something like, \"Hello! How can I assist you today?\" That feels friendly and open. They didn't ask a specific question, so this approach will work well for starting a conversation. Let's see where it goes from there!"
  },
  "sequence_number": 1
}
```

## response.reasoning_summary_text.delta

Emitted when a delta is added to a reasoning summary text.

### Schema

Schema name: `ResponseReasoningSummaryTextDeltaEvent`

### Example

```json
{
  "type": "response.reasoning_summary_text.delta",
  "item_id": "rs_6806bfca0b2481918a5748308061a2600d3ce51bdffd5476",
  "output_index": 0,
  "summary_index": 0,
  "delta": "**Responding to a greeting**\n\nThe user just said, \"Hello!\" So, it seems I need to engage. I'll greet them back and offer help since they're looking to chat. I could say something like, \"Hello! How can I assist you today?\" That feels friendly and open. They didn't ask a specific question, so this approach will work well for starting a conversation. Let's see where it goes from there!",
  "sequence_number": 1
}
```

## response.reasoning_summary_text.done

Emitted when a reasoning summary text is completed.

### Schema

Schema name: `ResponseReasoningSummaryTextDoneEvent`

### Example

```json
{
  "type": "response.reasoning_summary_text.done",
  "item_id": "rs_6806bfca0b2481918a5748308061a2600d3ce51bdffd5476",
  "output_index": 0,
  "summary_index": 0,
  "text": "**Responding to a greeting**\n\nThe user just said, \"Hello!\" So, it seems I need to engage. I'll greet them back and offer help since they're looking to chat. I could say something like, \"Hello! How can I assist you today?\" That feels friendly and open. They didn't ask a specific question, so this approach will work well for starting a conversation. Let's see where it goes from there!",
  "sequence_number": 1
}
```

## response.reasoning_text.delta

Emitted when a delta is added to a reasoning text.

### Schema

Schema name: `ResponseReasoningTextDeltaEvent`

### Example

```json
{
  "type": "response.reasoning_text.delta",
  "item_id": "rs_123",
  "output_index": 0,
  "content_index": 0,
  "delta": "The",
  "sequence_number": 1
}
```

## response.reasoning_text.done

Emitted when a reasoning text is completed.

### Schema

Schema name: `ResponseReasoningTextDoneEvent`

### Example

```json
{
  "type": "response.reasoning_text.done",
  "item_id": "rs_123",
  "output_index": 0,
  "content_index": 0,
  "text": "The user is asking...",
  "sequence_number": 4
}
```

## response.image_generation_call.completed

Emitted when an image generation tool call has completed and the final image is available.

### Schema

Schema name: `ResponseImageGenCallCompletedEvent`

### Example

```json
{
  "type": "response.image_generation_call.completed",
  "output_index": 0,
  "item_id": "item-123",
  "sequence_number": 1
}
```

## response.image_generation_call.generating

Emitted when an image generation tool call is actively generating an image (intermediate state).

### Schema

Schema name: `ResponseImageGenCallGeneratingEvent`

### Example

```json
{
  "type": "response.image_generation_call.generating",
  "output_index": 0,
  "item_id": "item-123",
  "sequence_number": 0
}
```

## response.image_generation_call.in_progress

Emitted when an image generation tool call is in progress.

### Schema

Schema name: `ResponseImageGenCallInProgressEvent`

### Example

```json
{
  "type": "response.image_generation_call.in_progress",
  "output_index": 0,
  "item_id": "item-123",
  "sequence_number": 0
}
```

## response.image_generation_call.partial_image

Emitted when a partial image is available during image generation streaming.

### Schema

Schema name: `ResponseImageGenCallPartialImageEvent`

### Example

```json
{
  "type": "response.image_generation_call.partial_image",
  "output_index": 0,
  "item_id": "item-123",
  "sequence_number": 0,
  "partial_image_index": 0,
  "partial_image_b64": "..."
}
```

## response.mcp_call_arguments.delta

Emitted when there is a delta (partial update) to the arguments of an MCP tool call.

### Schema

Schema name: `ResponseMCPCallArgumentsDeltaEvent`

### Example

```json
{
  "type": "response.mcp_call_arguments.delta",
  "output_index": 0,
  "item_id": "item-abc",
  "delta": "{",
  "sequence_number": 1
}
```

## response.mcp_call_arguments.done

Emitted when the arguments for an MCP tool call are finalized.

### Schema

Schema name: `ResponseMCPCallArgumentsDoneEvent`

### Example

```json
{
  "type": "response.mcp_call_arguments.done",
  "output_index": 0,
  "item_id": "item-abc",
  "arguments": "{\"arg1\": \"value1\", \"arg2\": \"value2\"}",
  "sequence_number": 1
}
```

## response.mcp_call.completed

Emitted when an MCP  tool call has completed successfully.

### Schema

Schema name: `ResponseMCPCallCompletedEvent`

### Example

```json
{
  "type": "response.mcp_call.completed",
  "sequence_number": 1,
  "item_id": "mcp_682d437d90a88191bf88cd03aae0c3e503937d5f622d7a90",
  "output_index": 0
}
```

## response.mcp_call.failed

Emitted when an MCP  tool call has failed.

### Schema

Schema name: `ResponseMCPCallFailedEvent`

### Example

```json
{
  "type": "response.mcp_call.failed",
  "sequence_number": 1,
  "item_id": "mcp_682d437d90a88191bf88cd03aae0c3e503937d5f622d7a90",
  "output_index": 0
}
```

## response.mcp_call.in_progress

Emitted when an MCP  tool call is in progress.

### Schema

Schema name: `ResponseMCPCallInProgressEvent`

### Example

```json
{
  "type": "response.mcp_call.in_progress",
  "sequence_number": 1,
  "output_index": 0,
  "item_id": "mcp_682d437d90a88191bf88cd03aae0c3e503937d5f622d7a90"
}
```

## response.mcp_list_tools.completed

Emitted when the list of available MCP tools has been successfully retrieved.

### Schema

Schema name: `ResponseMCPListToolsCompletedEvent`

### Example

```json
{
  "type": "response.mcp_list_tools.completed",
  "sequence_number": 1,
  "output_index": 0,
  "item_id": "mcpl_682d4379df088191886b70f4ec39f90403937d5f622d7a90"
}
```

## response.mcp_list_tools.failed

Emitted when the attempt to list available MCP tools has failed.

### Schema

Schema name: `ResponseMCPListToolsFailedEvent`

### Example

```json
{
  "type": "response.mcp_list_tools.failed",
  "sequence_number": 1,
  "output_index": 0,
  "item_id": "mcpl_682d4379df088191886b70f4ec39f90403937d5f622d7a90"
}
```

## response.mcp_list_tools.in_progress

Emitted when the system is in the process of retrieving the list of available MCP tools.

### Schema

Schema name: `ResponseMCPListToolsInProgressEvent`

### Example

```json
{
  "type": "response.mcp_list_tools.in_progress",
  "sequence_number": 1,
  "output_index": 0,
  "item_id": "mcpl_682d4379df088191886b70f4ec39f90403937d5f622d7a90"
}
```

## response.code_interpreter_call.in_progress

Emitted when a code interpreter call is in progress.

### Schema

Schema name: `ResponseCodeInterpreterCallInProgressEvent`

### Example

```json
{
  "type": "response.code_interpreter_call.in_progress",
  "output_index": 0,
  "item_id": "ci_12345",
  "sequence_number": 1
}
```

## response.code_interpreter_call.interpreting

Emitted when the code interpreter is actively interpreting the code snippet.

### Schema

Schema name: `ResponseCodeInterpreterCallInterpretingEvent`

### Example

```json
{
  "type": "response.code_interpreter_call.interpreting",
  "output_index": 4,
  "item_id": "ci_12345",
  "sequence_number": 1
}
```

## response.code_interpreter_call.completed

Emitted when the code interpreter call is completed.

### Schema

Schema name: `ResponseCodeInterpreterCallCompletedEvent`

### Example

```json
{
  "type": "response.code_interpreter_call.completed",
  "output_index": 5,
  "item_id": "ci_12345",
  "sequence_number": 1
}
```

## response.code_interpreter_call_code.delta

Emitted when a partial code snippet is streamed by the code interpreter.

### Schema

Schema name: `ResponseCodeInterpreterCallCodeDeltaEvent`

### Example

```json
{
  "type": "response.code_interpreter_call_code.delta",
  "output_index": 0,
  "item_id": "ci_12345",
  "delta": "print('Hello, world')",
  "sequence_number": 1
}
```

## response.code_interpreter_call_code.done

Emitted when the code snippet is finalized by the code interpreter.

### Schema

Schema name: `ResponseCodeInterpreterCallCodeDoneEvent`

### Example

```json
{
  "type": "response.code_interpreter_call_code.done",
  "output_index": 3,
  "item_id": "ci_12345",
  "code": "print('done')",
  "sequence_number": 1
}
```

## response.output_text.annotation.added

Emitted when an annotation is added to output text content.

### Schema

Schema name: `ResponseOutputTextAnnotationAddedEvent`

### Example

```json
{
  "type": "response.output_text.annotation.added",
  "item_id": "item-abc",
  "output_index": 0,
  "content_index": 0,
  "annotation_index": 0,
  "annotation": {
    "type": "file_citation",
    "file_id": "file-abc",
    "index": 0,
    "filename": "example.txt"
  },
  "sequence_number": 1
}
```

## response.queued

Emitted when a response is queued and waiting to be processed.

### Schema

Schema name: `ResponseQueuedEvent`

### Example

```json
{
  "type": "response.queued",
  "response": {
    "id": "res_123",
    "status": "queued",
    "created_at": "2021-01-01T00:00:00Z",
    "updated_at": "2021-01-01T00:00:00Z"
  },
  "sequence_number": 1
}
```

## response.custom_tool_call_input.delta

Event representing a delta (partial update) to the input of a custom tool call.

### Schema

Schema name: `ResponseCustomToolCallInputDeltaEvent`

### Example

```json
{
  "type": "response.custom_tool_call_input.delta",
  "output_index": 0,
  "item_id": "ctc_1234567890abcdef",
  "delta": "partial input text"
}
```

## response.custom_tool_call_input.done

Event indicating that input for a custom tool call is complete.

### Schema

Schema name: `ResponseCustomToolCallInputDoneEvent`

### Example

```json
{
  "type": "response.custom_tool_call_input.done",
  "output_index": 0,
  "item_id": "ctc_1234567890abcdef",
  "input": "final complete input text"
}
```

## error

Emitted when an error occurs.

### Schema

Schema name: `ResponseErrorEvent`

### Example

```json
{
  "type": "error",
  "code": "ERR_SOMETHING",
  "message": "Something went wrong",
  "param": null,
  "sequence_number": 1
}
```

## response.audio.delta

Emitted when there is a partial audio response.

### Schema

Schema name: `ResponseAudioDeltaEvent`

### Example

```json
{
  "type": "response.audio.delta",
  "response_id": "resp_123",
  "delta": "base64encoded...",
  "sequence_number": 1
}
```

## response.audio.done

Emitted when the audio response is complete.

### Schema

Schema name: `ResponseAudioDoneEvent`

### Example

```json
{
  "type": "response.audio.done",
  "response_id": "resp-123",
  "sequence_number": 1
}
```

## response.audio.transcript.delta

Emitted when there is a partial transcript of audio.

### Schema

Schema name: `ResponseAudioTranscriptDeltaEvent`

### Example

```json
{
  "type": "response.audio.transcript.delta",
  "response_id": "resp_123",
  "delta": " ... partial transcript ... ",
  "sequence_number": 1
}
```

## response.audio.transcript.done

Emitted when the full audio transcript is completed.

### Schema

Schema name: `ResponseAudioTranscriptDoneEvent`

### Example

```json
{
  "type": "response.audio.transcript.done",
  "response_id": "resp_123",
  "sequence_number": 1
}
```

## response.shell_call_command.added

A streaming event that indicated a shell command was added to a tool call.

### Schema

Schema name: `ResponseShellCallCommandAddedStreamingEvent`

### Example

```json
{
  "type": "response.shell_call_command.added",
  "sequence_number": 0,
  "output_index": 0,
  "command_index": 0,
  "command": "command"
}
```

## response.shell_call_command.delta

A streaming event that indicated a shell command was incrementally updated.

### Schema

Schema name: `ResponseShellCallCommandDeltaStreamingEvent`

### Example

```json
{
  "type": "response.shell_call_command.delta",
  "sequence_number": 0,
  "output_index": 0,
  "command_index": 0,
  "delta": "delta",
  "obfuscation": "obfuscation"
}
```

## response.shell_call_command.done

A streaming event that indicated a shell command was completed.

### Schema

Schema name: `ResponseShellCallCommandDoneStreamingEvent`

### Example

```json
{
  "type": "response.shell_call_command.done",
  "sequence_number": 0,
  "output_index": 0,
  "command_index": 0,
  "command": "command"
}
```

## response.shell_call_output_content.delta

A streaming event that indicated shell call output was incrementally added.

### Schema

Schema name: `ResponseShellCallOutputContentDeltaStreamingEvent`

### Example

```json
{
  "type": "response.shell_call_output_content.delta",
  "sequence_number": 0,
  "item_id": "item_id",
  "output_index": 0,
  "command_index": 0,
  "delta": {
    "stdout": "stdout",
    "stderr": "stderr"
  }
}
```

## response.shell_call_output_content.done

A streaming event that indicated shell call output was completed.

### Schema

Schema name: `ResponseShellCallOutputContentDoneStreamingEvent`

### Example

```json
{
  "type": "response.shell_call_output_content.done",
  "sequence_number": 0,
  "item_id": "item_id",
  "output_index": 0,
  "command_index": 0,
  "output": [
    {
      "stdout": "stdout",
      "stderr": "stderr",
      "outcome": {
        "type": "timeout"
      },
      "created_by": "created_by"
    }
  ]
}
```
