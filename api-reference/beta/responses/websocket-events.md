# WebSocket events

> For the complete documentation index, see [llms.txt](https://developers.openai.com/llms.txt). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Send client events and receive server events over a persistent Responses API WebSocket connection. [Learn more about WebSocket mode.](https://developers.openai.com/api/docs/guides/websocket-mode)

## Client events

Events sent by the client over a Responses API WebSocket connection.

### response.create

Client event for creating a response over a persistent WebSocket connection.
This payload uses the same top-level fields as `POST /v1/responses`, plus
WebSocket-only envelope metadata.

Notes:
- `stream` is implicit over WebSocket and should not be sent.
- `background` is not supported over WebSocket.
- `stream_id` is WebSocket-only and is not part of `POST /v1/responses`.

#### Schema

Schema name: `BetaResponsesClientEventResponseCreate`

#### Example

```json
{
  "type": "response.create",
  "stream_id": "agent_1",
  "model": "gpt-6-astra",
  "input": "Say hello."
}
```

### response.steer

Queues user input to steer a response on this WebSocket connection. Input
can contain text, images, and files. Steering is supported only for
single-agent responses on models and execution modes that support steering.
Responses bound to a conversation or using automatic compaction do not
support steering.

A `response.steer.accepted` event acknowledges that the server owns the
queued input, not that it has been applied. The successor's `response.created`
event is the commit point. Input that cannot be committed is returned in
`response.steer.failed`.

Steering may cause the active response to finish at a safe output boundary
with `response.incomplete` and `incomplete_details.reason` set to `steered`,
followed automatically by a successor `response.created`. Normal completion
can also be followed by an automatic successor. Automatic successors inherit
the previous response's settings and continue from it with the queued input.

If the response stops for client-owned tool output or approval, accepted
steering input remains queued and `response.steer.pending` is emitted after
`response.completed`. Fill the `required_input` stubs from that event with
saved tool results or approval decisions, and send one explicit
`response.create` per parent with the same `previous_response_id` and
WebSocket lane. Do not rerun tools or resend accepted steering input. The
queued input is prepended in submission order to that request's input, and
the explicit request retains its own settings.

This event accepts only `type`, `previous_response_id`, and `input`. Do not
send `stream_id`; the target response determines the WebSocket lane.

#### Schema

Schema name: `BetaResponseSteerEvent`

#### Example

```json
{
  "type": "response.steer",
  "previous_response_id": "resp_123",
  "input": [
    {
      "type": "message",
      "role": "user",
      "content": [
        {
          "type": "input_text",
          "text": "Prioritize the database rollout."
        }
      ]
    }
  ]
}
```

### response.inject

Injects input items into an active response over a WebSocket connection.
The items are validated and committed atomically. Currently, the server
accepts client-owned tool outputs that resume a waiting agent.

#### Schema

Schema name: `BetaResponseInjectEvent`

#### Example

```json
{
  "type": "response.inject",
  "response_id": "resp_123",
  "input": [
    {
      "type": "function_call_output",
      "call_id": "call_123",
      "output": "{\"temperature\":72}"
    }
  ]
}
```

## Server events (WebSocket only)

Events emitted only over a Responses API WebSocket connection.

### error

Emitted when an error occurs while processing a Responses WebSocket request.

#### Schema

Schema name: `BetaResponseWsError`

#### Example

```json
{
  "type": "error",
  "status": 400,
  "stream_id": "agent_1",
  "error": {
    "type": "invalid_request_error",
    "code": "websocket_stream_limit_reached",
    "message": "This WebSocket connection has reached its stream limit.",
    "param": "stream_id"
  }
}
```

### response.steer.accepted

Emitted when steering input has been validated and queued. Acceptance means
the server owns the input, not that it has been applied. The successor's
`response.created` event is the commit point. If accepted input cannot be
committed, `response.steer.failed` returns it with the same steering ID.

When the response stops for client-owned tool output or approval, the input
remains queued and `response.steer.pending` is emitted after
`response.completed`. Fill the pending event's `required_input` stubs with
saved results and send one matching explicit `response.create` per parent.
Do not resend accepted input while it is still queued.

#### Schema

Schema name: `BetaResponseSteerAcceptedEvent`

#### Example

```json
{
  "type": "response.steer.accepted",
  "sequence_number": 2,
  "steer": {
    "id": "steer_456",
    "previous_response_id": "resp_123"
  }
}
```

### response.steer.pending

Emitted when accepted steering input remains queued after the target
response completes. The server still owns the input. Do not resend it.
The successor's `response.created` event is the commit point.

When `reason` is `waiting_for_required_input`, this event follows
`response.completed` while the response waits for the tool results or
approval decisions identified by `required_input`. Copy those stubs, fill
their result fields using the ordinary `response.create` input schemas,
and submit one continuation per parent with the same `previous_response_id`
and WebSocket lane. Use saved results without rerunning tools. The queued
steering input is prepended in submission order to the continuation's
input. That explicit request retains its own settings.

This notification is emitted at most once per steering submission. Multiple
submissions for the same parent can report the same required inputs; they
do not each require a separate continuation.

#### Schema

Schema name: `BetaResponseSteerPendingEvent`

#### Example

```json
{
  "type": "response.steer.pending",
  "sequence_number": 10,
  "steer": {
    "id": "steer_456",
    "previous_response_id": "resp_123"
  },
  "reason": "waiting_for_required_input",
  "required_input": [
    {
      "type": "function_call_output",
      "call_id": "call_789",
      "name": "lookup"
    }
  ]
}
```

### response.steer.failed

Emitted when steering input is rejected or cannot be committed to a
successor response. Returns the original, uncommitted input so the client
can carry it into `response.create` when appropriate. Invalid input must
be corrected before retrying.

Failures after acceptance include the same steering ID. Failures before an
ID is allocated omit `steer.id`. A lost connection or missing acknowledgement
leaves the outcome unknown; it is not proof that the input was rejected.

#### Schema

Schema name: `BetaResponseSteerFailedEvent`

#### Example

```json
{
  "type": "response.steer.failed",
  "sequence_number": 5,
  "steer": {
    "id": "steer_456",
    "previous_response_id": "resp_123",
    "input": [
      {
        "type": "message",
        "role": "user",
        "content": [
          {
            "type": "input_text",
            "text": "Prioritize the database rollout."
          }
        ]
      }
    ]
  },
  "error": {
    "type": "invalid_request_error",
    "code": "successor_creation_failed",
    "message": "We couldn't start the next response. Send this steering input again with response.create."
  }
}
```

### response.inject.created

Emitted when all injected input items were validated and committed to the
active response.

#### Schema

Schema name: `BetaResponseInjectCreatedEvent`

#### Example

```json
{
  "type": "response.inject.created",
  "response_id": "resp_123",
  "sequence_number": 8
}
```

### response.inject.failed

Emitted when injected input could not be committed to a response. The event
returns the uncommitted raw input so the client can retry it in another
response when appropriate.

#### Schema

Schema name: `BetaResponseInjectFailedEvent`

#### Example

```json
{
  "type": "response.inject.failed",
  "response_id": "resp_123",
  "input": [
    {
      "type": "function_call_output",
      "call_id": "call_123",
      "output": "{\"temperature\":72}"
    }
  ],
  "error": {
    "code": "response_already_completed",
    "message": "Response 'resp_123' has already completed."
  },
  "sequence_number": 9
}
```

## Server events

These events use the same payloads over WebSocket and
[HTTP streaming](https://developers.openai.com/api/reference/resources/beta/subresources/responses/streaming-events).

### response.created

An event that is emitted when a response is created.

#### Schema

Schema name: `BetaResponseCreatedEvent`

#### Example

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

### response.in_progress

Emitted when the response is in progress.

#### Schema

Schema name: `BetaResponseInProgressEvent`

#### Example

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

### response.completed

Emitted when the model response is complete.

#### Schema

Schema name: `BetaResponseCompletedEvent`

#### Example

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

### response.failed

An event that is emitted when a response fails.

#### Schema

Schema name: `BetaResponseFailedEvent`

#### Example

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

### response.incomplete

An event that is emitted when a response finishes as incomplete.

Over WebSocket, steering can finish a response with
`response.incomplete_details.reason` set to `steered`, followed automatically
by a successor `response.created` that commits the queued steering input.

#### Schema

Schema name: `BetaResponseIncompleteEvent`

#### Example

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

### response.output_item.added

Emitted when a new output item is added.

#### Schema

Schema name: `BetaResponseOutputItemAddedEvent`

#### Example

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

### response.output_item.done

Emitted when an output item is marked done.

#### Schema

Schema name: `BetaResponseOutputItemDoneEvent`

#### Example

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

### response.content_part.added

Emitted when a new content part is added.

#### Schema

Schema name: `BetaResponseContentPartAddedEvent`

#### Example

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

### response.content_part.done

Emitted when a content part is done.

#### Schema

Schema name: `BetaResponseContentPartDoneEvent`

#### Example

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

### response.output_text.delta

Emitted when there is an additional text delta.

#### Schema

Schema name: `BetaResponseTextDeltaEvent`

#### Example

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

### response.output_text.done

Emitted when text content is finalized.

#### Schema

Schema name: `BetaResponseTextDoneEvent`

#### Example

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

### response.refusal.delta

Emitted when there is a partial refusal text.

#### Schema

Schema name: `BetaResponseRefusalDeltaEvent`

#### Example

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

### response.refusal.done

Emitted when refusal text is finalized.

#### Schema

Schema name: `BetaResponseRefusalDoneEvent`

#### Example

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

### response.function_call_arguments.delta

Emitted when there is a partial function-call arguments delta.

#### Schema

Schema name: `BetaResponseFunctionCallArgumentsDeltaEvent`

#### Example

```json
{
  "type": "response.function_call_arguments.delta",
  "item_id": "item-abc",
  "output_index": 0,
  "delta": "{ \"arg\":"
  "sequence_number": 1
}
```

### response.function_call_arguments.done

Emitted when function-call arguments are finalized.

#### Schema

Schema name: `BetaResponseFunctionCallArgumentsDoneEvent`

#### Example

```json
{
  "type": "response.function_call_arguments.done",
  "item_id": "item-abc",
  "output_index": 1,
  "arguments": "{ \"arg\": 123 }",
  "sequence_number": 1
}
```

### response.file_search_call.in_progress

Emitted when a file search call is initiated.

#### Schema

Schema name: `BetaResponseFileSearchCallInProgressEvent`

#### Example

```json
{
  "type": "response.file_search_call.in_progress",
  "output_index": 0,
  "item_id": "fs_123",
  "sequence_number": 1
}
```

### response.file_search_call.searching

Emitted when a file search is currently searching.

#### Schema

Schema name: `BetaResponseFileSearchCallSearchingEvent`

#### Example

```json
{
  "type": "response.file_search_call.searching",
  "output_index": 0,
  "item_id": "fs_123",
  "sequence_number": 1
}
```

### response.file_search_call.completed

Emitted when a file search call is completed (results found).

#### Schema

Schema name: `BetaResponseFileSearchCallCompletedEvent`

#### Example

```json
{
  "type": "response.file_search_call.completed",
  "output_index": 0,
  "item_id": "fs_123",
  "sequence_number": 1
}
```

### response.web_search_call.in_progress

Emitted when a web search call is initiated.

#### Schema

Schema name: `BetaResponseWebSearchCallInProgressEvent`

#### Example

```json
{
  "type": "response.web_search_call.in_progress",
  "output_index": 0,
  "item_id": "ws_123",
  "sequence_number": 0
}
```

### response.web_search_call.searching

Emitted when a web search call is executing.

#### Schema

Schema name: `BetaResponseWebSearchCallSearchingEvent`

#### Example

```json
{
  "type": "response.web_search_call.searching",
  "output_index": 0,
  "item_id": "ws_123",
  "sequence_number": 0
}
```

### response.web_search_call.completed

Emitted when a web search call is completed.

#### Schema

Schema name: `BetaResponseWebSearchCallCompletedEvent`

#### Example

```json
{
  "type": "response.web_search_call.completed",
  "output_index": 0,
  "item_id": "ws_123",
  "sequence_number": 0
}
```

### response.reasoning_summary_part.added

Emitted when a new reasoning summary part is added.

#### Schema

Schema name: `BetaResponseReasoningSummaryPartAddedEvent`

#### Example

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

### response.reasoning_summary_part.done

Emitted when a reasoning summary part is completed.

#### Schema

Schema name: `BetaResponseReasoningSummaryPartDoneEvent`

#### Example

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

### response.reasoning_summary_text.delta

Emitted when a delta is added to a reasoning summary text.

#### Schema

Schema name: `BetaResponseReasoningSummaryTextDeltaEvent`

#### Example

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

### response.reasoning_summary_text.done

Emitted when a reasoning summary text is completed.

#### Schema

Schema name: `BetaResponseReasoningSummaryTextDoneEvent`

#### Example

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

### response.reasoning_text.delta

Emitted when a delta is added to a reasoning text.

#### Schema

Schema name: `BetaResponseReasoningTextDeltaEvent`

#### Example

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

### response.reasoning_text.done

Emitted when a reasoning text is completed.

#### Schema

Schema name: `BetaResponseReasoningTextDoneEvent`

#### Example

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

### response.image_generation_call.completed

Emitted when an image generation tool call has completed and the final image is available.

#### Schema

Schema name: `BetaResponseImageGenCallCompletedEvent`

#### Example

```json
{
  "type": "response.image_generation_call.completed",
  "output_index": 0,
  "item_id": "item-123",
  "sequence_number": 1
}
```

### response.image_generation_call.generating

Emitted when an image generation tool call is actively generating an image (intermediate state).

#### Schema

Schema name: `BetaResponseImageGenCallGeneratingEvent`

#### Example

```json
{
  "type": "response.image_generation_call.generating",
  "output_index": 0,
  "item_id": "item-123",
  "sequence_number": 0
}
```

### response.image_generation_call.in_progress

Emitted when an image generation tool call is in progress.

#### Schema

Schema name: `BetaResponseImageGenCallInProgressEvent`

#### Example

```json
{
  "type": "response.image_generation_call.in_progress",
  "output_index": 0,
  "item_id": "item-123",
  "sequence_number": 0
}
```

### response.image_generation_call.partial_image

Emitted when a partial image is available during image generation streaming.

#### Schema

Schema name: `BetaResponseImageGenCallPartialImageEvent`

#### Example

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

### response.mcp_call_arguments.delta

Emitted when there is a delta (partial update) to the arguments of an MCP tool call.

#### Schema

Schema name: `BetaResponseMCPCallArgumentsDeltaEvent`

#### Example

```json
{
  "type": "response.mcp_call_arguments.delta",
  "output_index": 0,
  "item_id": "item-abc",
  "delta": "{",
  "sequence_number": 1
}
```

### response.mcp_call_arguments.done

Emitted when the arguments for an MCP tool call are finalized.

#### Schema

Schema name: `BetaResponseMCPCallArgumentsDoneEvent`

#### Example

```json
{
  "type": "response.mcp_call_arguments.done",
  "output_index": 0,
  "item_id": "item-abc",
  "arguments": "{\"arg1\": \"value1\", \"arg2\": \"value2\"}",
  "sequence_number": 1
}
```

### response.mcp_call.completed

Emitted when an MCP  tool call has completed successfully.

#### Schema

Schema name: `BetaResponseMCPCallCompletedEvent`

#### Example

```json
{
  "type": "response.mcp_call.completed",
  "sequence_number": 1,
  "item_id": "mcp_682d437d90a88191bf88cd03aae0c3e503937d5f622d7a90",
  "output_index": 0
}
```

### response.mcp_call.failed

Emitted when an MCP  tool call has failed.

#### Schema

Schema name: `BetaResponseMCPCallFailedEvent`

#### Example

```json
{
  "type": "response.mcp_call.failed",
  "sequence_number": 1,
  "item_id": "mcp_682d437d90a88191bf88cd03aae0c3e503937d5f622d7a90",
  "output_index": 0
}
```

### response.mcp_call.in_progress

Emitted when an MCP  tool call is in progress.

#### Schema

Schema name: `BetaResponseMCPCallInProgressEvent`

#### Example

```json
{
  "type": "response.mcp_call.in_progress",
  "sequence_number": 1,
  "output_index": 0,
  "item_id": "mcp_682d437d90a88191bf88cd03aae0c3e503937d5f622d7a90"
}
```

### response.mcp_list_tools.completed

Emitted when the list of available MCP tools has been successfully retrieved.

#### Schema

Schema name: `BetaResponseMCPListToolsCompletedEvent`

#### Example

```json
{
  "type": "response.mcp_list_tools.completed",
  "sequence_number": 1,
  "output_index": 0,
  "item_id": "mcpl_682d4379df088191886b70f4ec39f90403937d5f622d7a90"
}
```

### response.mcp_list_tools.failed

Emitted when the attempt to list available MCP tools has failed.

#### Schema

Schema name: `BetaResponseMCPListToolsFailedEvent`

#### Example

```json
{
  "type": "response.mcp_list_tools.failed",
  "sequence_number": 1,
  "output_index": 0,
  "item_id": "mcpl_682d4379df088191886b70f4ec39f90403937d5f622d7a90"
}
```

### response.mcp_list_tools.in_progress

Emitted when the system is in the process of retrieving the list of available MCP tools.

#### Schema

Schema name: `BetaResponseMCPListToolsInProgressEvent`

#### Example

```json
{
  "type": "response.mcp_list_tools.in_progress",
  "sequence_number": 1,
  "output_index": 0,
  "item_id": "mcpl_682d4379df088191886b70f4ec39f90403937d5f622d7a90"
}
```

### response.code_interpreter_call.in_progress

Emitted when a code interpreter call is in progress.

#### Schema

Schema name: `BetaResponseCodeInterpreterCallInProgressEvent`

#### Example

```json
{
  "type": "response.code_interpreter_call.in_progress",
  "output_index": 0,
  "item_id": "ci_12345",
  "sequence_number": 1
}
```

### response.code_interpreter_call.interpreting

Emitted when the code interpreter is actively interpreting the code snippet.

#### Schema

Schema name: `BetaResponseCodeInterpreterCallInterpretingEvent`

#### Example

```json
{
  "type": "response.code_interpreter_call.interpreting",
  "output_index": 4,
  "item_id": "ci_12345",
  "sequence_number": 1
}
```

### response.code_interpreter_call.completed

Emitted when the code interpreter call is completed.

#### Schema

Schema name: `BetaResponseCodeInterpreterCallCompletedEvent`

#### Example

```json
{
  "type": "response.code_interpreter_call.completed",
  "output_index": 5,
  "item_id": "ci_12345",
  "sequence_number": 1
}
```

### response.code_interpreter_call_code.delta

Emitted when a partial code snippet is streamed by the code interpreter.

#### Schema

Schema name: `BetaResponseCodeInterpreterCallCodeDeltaEvent`

#### Example

```json
{
  "type": "response.code_interpreter_call_code.delta",
  "output_index": 0,
  "item_id": "ci_12345",
  "delta": "print('Hello, world')",
  "sequence_number": 1
}
```

### response.code_interpreter_call_code.done

Emitted when the code snippet is finalized by the code interpreter.

#### Schema

Schema name: `BetaResponseCodeInterpreterCallCodeDoneEvent`

#### Example

```json
{
  "type": "response.code_interpreter_call_code.done",
  "output_index": 3,
  "item_id": "ci_12345",
  "code": "print('done')",
  "sequence_number": 1
}
```

### response.output_text.annotation.added

Emitted when an annotation is added to output text content.

#### Schema

Schema name: `BetaResponseOutputTextAnnotationAddedEvent`

#### Example

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

### response.queued

Emitted when a response is queued and waiting to be processed.

#### Schema

Schema name: `BetaResponseQueuedEvent`

#### Example

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

### response.custom_tool_call_input.delta

Event representing a delta (partial update) to the input of a custom tool call.

#### Schema

Schema name: `BetaResponseCustomToolCallInputDeltaEvent`

#### Example

```json
{
  "type": "response.custom_tool_call_input.delta",
  "output_index": 0,
  "item_id": "ctc_1234567890abcdef",
  "delta": "partial input text"
}
```

### response.custom_tool_call_input.done

Event indicating that input for a custom tool call is complete.

#### Schema

Schema name: `BetaResponseCustomToolCallInputDoneEvent`

#### Example

```json
{
  "type": "response.custom_tool_call_input.done",
  "output_index": 0,
  "item_id": "ctc_1234567890abcdef",
  "input": "final complete input text"
}
```

### response.audio.delta

Emitted when there is a partial audio response.

#### Schema

Schema name: `BetaResponseAudioDeltaEvent`

#### Example

```json
{
  "type": "response.audio.delta",
  "response_id": "resp_123",
  "delta": "base64encoded...",
  "sequence_number": 1
}
```

### response.audio.done

Emitted when the audio response is complete.

#### Schema

Schema name: `BetaResponseAudioDoneEvent`

#### Example

```json
{
  "type": "response.audio.done",
  "response_id": "resp-123",
  "sequence_number": 1
}
```

### response.audio.transcript.delta

Emitted when there is a partial transcript of audio.

#### Schema

Schema name: `BetaResponseAudioTranscriptDeltaEvent`

#### Example

```json
{
  "type": "response.audio.transcript.delta",
  "response_id": "resp_123",
  "delta": " ... partial transcript ... ",
  "sequence_number": 1
}
```

### response.audio.transcript.done

Emitted when the full audio transcript is completed.

#### Schema

Schema name: `BetaResponseAudioTranscriptDoneEvent`

#### Example

```json
{
  "type": "response.audio.transcript.done",
  "response_id": "resp_123",
  "sequence_number": 1
}
```

### response.shell_call_command.added

A streaming event that indicated a shell command was added to a tool call.

#### Schema

Schema name: `BetaResponseShellCallCommandAddedStreamingEvent`

#### Example

```json
{
  "type": "response.shell_call_command.added",
  "sequence_number": 0,
  "agent": {
    "agent_name": "agent_name"
  },
  "output_index": 0,
  "command_index": 0,
  "command": "command"
}
```

### response.shell_call_command.delta

A streaming event that indicated a shell command was incrementally updated.

#### Schema

Schema name: `BetaResponseShellCallCommandDeltaStreamingEvent`

#### Example

```json
{
  "type": "response.shell_call_command.delta",
  "sequence_number": 0,
  "agent": {
    "agent_name": "agent_name"
  },
  "output_index": 0,
  "command_index": 0,
  "delta": "delta",
  "obfuscation": "obfuscation"
}
```

### response.shell_call_command.done

A streaming event that indicated a shell command was completed.

#### Schema

Schema name: `BetaResponseShellCallCommandDoneStreamingEvent`

#### Example

```json
{
  "type": "response.shell_call_command.done",
  "sequence_number": 0,
  "agent": {
    "agent_name": "agent_name"
  },
  "output_index": 0,
  "command_index": 0,
  "command": "command"
}
```

### response.shell_call_output_content.delta

A streaming event that indicated shell call output was incrementally added.

#### Schema

Schema name: `BetaResponseShellCallOutputContentDeltaStreamingEvent`

#### Example

```json
{
  "type": "response.shell_call_output_content.delta",
  "sequence_number": 0,
  "agent": {
    "agent_name": "agent_name"
  },
  "item_id": "item_id",
  "output_index": 0,
  "command_index": 0,
  "delta": {
    "stdout": "stdout",
    "stderr": "stderr"
  }
}
```

### response.shell_call_output_content.done

A streaming event that indicated shell call output was completed.

#### Schema

Schema name: `BetaResponseShellCallOutputContentDoneStreamingEvent`

#### Example

```json
{
  "type": "response.shell_call_output_content.done",
  "sequence_number": 0,
  "agent": {
    "agent_name": "agent_name"
  },
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
