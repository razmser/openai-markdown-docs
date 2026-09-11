# Sideband WebSocket

> For the complete documentation index, see [llms.txt](https://developers.openai.com/llms.txt). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Attach your backend server to an existing Live session with your OpenAI API key. For example, when your frontend connects over WebRTC, use this WebSocket to handle events and control the session from your backend.

WS `/v1/live/sessions/{session_id}/attach`

## Connection

`wss://api.openai.com/v1/live/sessions/{session_id}/attach`

Authenticate from your backend with your OpenAI API key in the `Authorization: Bearer $OPENAI_API_KEY` header. Keep the key on your server.

`session_id` (required path parameter): The ID of the existing session to attach to.

Attaching does not create a session or replay earlier events. Audio stays on the primary connection. Do not send session.start or session.input_audio.append on this WebSocket.

## Inputs

For a session using Responses delegation, update its tool choice. The delegation type cannot change. There is no required first message after attaching.

### Example client event: session.update

```json
{
  "type": "session.update",
  "event_id": "update_1",
  "session": {
    "delegation": {
      "type": "responses",
      "responses": {
        "tool_choice": "required"
      }
    }
  }
}
```

[All client events](#client-events)

## Outputs

A configuration update is acknowledged with session.updated. You also receive subsequent session events; attaching alone does not trigger this event.

### Example server event · excerpt: session.updated

```json
{
  "type": "session.updated",
  "event_id": "event_updated_1",
  "client_event_id": "update_1",
  "session": {
    "id": "live_123",
    "expires_at": 1788307200,
    "status": "active",
    "model": "gpt-live-1"
  }
}
```

[All server events](#server-events)

[Need to start a session and stream audio from your backend? Use a primary WebSocket.](https://developers.openai.com/api/reference/resources/live/primary-websocket)

## Client events

### session.update

Update the delegation settings of an active Live session. The server acknowledges accepted changes with `session.updated`.

#### Schema

Schema name: `LiveSessionUpdateParam`

#### Example

```json
{
  "type": "session.update",
  "event_id": "evt_update_001",
  "session": {
    "delegation": {
      "type": "responses",
      "responses": {
        "instructions": "Check restaurant availability. Ask before confirming a booking.",
        "max_output_tokens": 1024
      }
    }
  }
}
```

### session.input_audio.mute

Mute audio input to the Live model without closing the session. The server acknowledges with `session.input_audio.muted`.

#### Schema

Schema name: `LiveInputAudioMuteParam`

#### Example

```json
{
  "type": "session.input_audio.mute",
  "event_id": "evt_mute_001"
}
```

### session.input_audio.unmute

Resume audio input to a Live model after muting it. The server acknowledges with `session.input_audio.unmuted`.

#### Schema

Schema name: `LiveInputAudioUnmuteParam`

#### Example

```json
{
  "type": "session.input_audio.unmute",
  "event_id": "evt_unmute_001"
}
```

### session.instructions.append

Append instructions to the Live conversation while it is running, optionally associating them with an existing client delegation.

#### Schema

Schema name: `LiveInstructionsAppendParam`

#### Example

```json
{
  "type": "session.instructions.append",
  "event_id": "evt_instructions_001",
  "delegation_id": null,
  "content": "The caller prefers outdoor seating."
}
```

### session.thinking.append

Provide silent reasoning or progress context to the Live model, optionally for an existing client delegation.

#### Schema

Schema name: `LiveThinkingAppendParam`

#### Example

```json
{
  "type": "session.thinking.append",
  "event_id": "evt_thinking_001",
  "delegation_id": "del_abc123",
  "content": "Checking availability for two guests at 7 PM."
}
```

### session.commentary.append

Provide context the Live model can communicate to the user, optionally for an existing client delegation.

#### Schema

Schema name: `LiveCommentaryAppendParam`

#### Example

```json
{
  "type": "session.commentary.append",
  "event_id": "evt_commentary_001",
  "delegation_id": "del_abc123",
  "content": "There is an outdoor table for two at 7 PM. Ask whether to reserve it."
}
```

### response.item.create

Add an input item to the Live session’s Responses backend. Requires Responses delegation; use `response.create` to request a response.

#### Schema

Schema name: `LiveResponseItemCreateParam`

#### Example

```json
{
  "type": "response.item.create",
  "event_id": "evt_item_001",
  "item": {
    "type": "message",
    "role": "user",
    "content": [
      {
        "type": "input_text",
        "text": "Please check for a table for two at 7 PM."
      }
    ]
  }
}
```

### response.create

Request a response from the Live session’s Responses backend, or continue a delegated response waiting for tool results. Requires Responses delegation.

#### Schema

Schema name: `LiveResponseCreateParam`

#### Example

```json
{
  "type": "response.create",
  "event_id": "evt_response_001"
}
```

### session.close

Request that the Live session close. The terminal `session.closed` event contains the close reason and final usage.

#### Schema

Schema name: `LiveSessionCloseParam`

#### Example

```json
{
  "type": "session.close",
  "event_id": "evt_close_001"
}
```

## Server events

### session.started

Returned when a Live session has started. Contains the resolved session configuration, including server defaults.

#### Schema

Schema name: `LiveSessionStarted`

#### Example

```json
{
  "type": "session.started",
  "event_id": "evt_started_001",
  "client_event_id": "evt_start_001",
  "session": {
    "id": "live_abc123",
    "model": "gpt-live-1",
    "status": "active",
    "expires_at": 1788555600,
    "instructions": "Help the caller plan a restaurant reservation. Confirm details before booking.",
    "input": [],
    "audio": {
      "format": {
        "type": "audio/pcm",
        "rate": 24000
      },
      "output": {
        "voice": "marin"
      }
    },
    "delegation": {
      "type": "client"
    }
  }
}
```

### session.updated

Returned when a Live session update is accepted. Contains the resolved session configuration after the update.

#### Schema

Schema name: `LiveSessionUpdated`

#### Example

```json
{
  "type": "session.updated",
  "event_id": "evt_updated_001",
  "client_event_id": "evt_update_001",
  "session": {
    "id": "live_def456",
    "model": "gpt-live-1",
    "status": "active",
    "expires_at": 1788555600,
    "instructions": "Help the caller plan a restaurant reservation. Confirm details before booking.",
    "input": [],
    "audio": {
      "format": {
        "type": "audio/pcm",
        "rate": 24000
      },
      "output": {
        "voice": "marin"
      }
    },
    "delegation": {
      "type": "responses",
      "responses": {
        "model": "gpt-6-astra",
        "instructions": "Check restaurant availability. Ask before confirming a booking.",
        "max_output_tokens": 1024,
        "tools": []
      }
    }
  }
}
```

### session.input_audio.muted

Returned when a session.input_audio.mute command is accepted. Input audio is no longer sent to the model; sideband audio reflection continues.

#### Schema

Schema name: `LiveInputAudioMuted`

#### Example

```json
{
  "type": "session.input_audio.muted",
  "event_id": "evt_muted_001",
  "client_event_id": "evt_mute_001"
}
```

### session.input_audio.unmuted

Returned when a session.input_audio.unmute command is accepted. Input audio is sent to the model again.

#### Schema

Schema name: `LiveInputAudioUnmuted`

#### Example

```json
{
  "type": "session.input_audio.unmuted",
  "event_id": "evt_unmuted_001",
  "client_event_id": "evt_unmute_001"
}
```

### session.instructions.appended

Returned when a session.instructions.append command is accepted into the Live session timeline. Acknowledges the appended instructions without guaranteeing that the model has acted on them.

#### Schema

Schema name: `LiveInstructionsAppended`

#### Example

```json
{
  "type": "session.instructions.appended",
  "event_id": "evt_instructions_002",
  "client_event_id": "evt_instructions_001",
  "start_ms": 1200,
  "end_ms": 1400
}
```

### session.thinking.appended

Returned when a session.thinking.append command is accepted into the Live session timeline. Acknowledges the added reasoning context without guaranteeing any spoken output.

#### Schema

Schema name: `LiveThinkingAppended`

#### Example

```json
{
  "type": "session.thinking.appended",
  "event_id": "evt_thinking_002",
  "client_event_id": "evt_thinking_001",
  "start_ms": 4600,
  "end_ms": 4800
}
```

### session.commentary.appended

Returned when a session.commentary.append command is accepted into the Live session timeline. Acknowledges the added commentary without guaranteeing exact wording or completed audio playback.

#### Schema

Schema name: `LiveCommentaryAppended`

#### Example

```json
{
  "type": "session.commentary.appended",
  "event_id": "evt_commentary_002",
  "client_event_id": "evt_commentary_001",
  "start_ms": 5200,
  "end_ms": 5400
}
```

### session.input_transcript.delta

A transcript fragment for user input audio in the Live session. Accumulate fragments in delivery order; these events do not define complete turns or include a transcript-done event.

#### Schema

Schema name: `LiveInputTranscriptDelta`

#### Example

```json
{
  "type": "session.input_transcript.delta",
  "event_id": "evt_input_transcript_001",
  "delta": "A table for two at seven, please.",
  "start_ms": 1600,
  "end_ms": 3400
}
```

### session.output_transcript.delta

A transcript fragment for assistant output audio in the Live session. Accumulate fragments in delivery order; these events do not define complete turns or include a transcript-done event.

#### Schema

Schema name: `LiveOutputTranscriptDelta`

#### Example

```json
{
  "type": "session.output_transcript.delta",
  "event_id": "evt_output_transcript_001",
  "delta": "Would you like me to reserve that table?",
  "start_ms": 5400,
  "end_ms": 7200
}
```

### session.delegation.created

Returned when the Live model delegates work to your application or a Responses backend. Contains delegation metadata and the position on the session timeline where the work was delegated.

#### Schema

Schema name: `LiveDelegationCreated`

#### Example

```json
{
  "type": "session.delegation.created",
  "event_id": "evt_delegation_001",
  "offset_ms": 3600,
  "delegation": {
    "id": "del_abc123",
    "type": "delegation",
    "target": "client"
  }
}
```

### response.event

A streaming Responses API event from a backend delegated to by the Live session. Use the outer delegation_id to associate the nested stream with its Live delegation.

#### Schema

Schema name: `LiveResponseEvent`

#### Example

```json
{
  "type": "response.event",
  "event_id": "evt_response_002",
  "delegation_id": "del_responses123",
  "event": {
    "type": "response.output_text.delta",
    "item_id": "msg_abc123",
    "output_index": 0,
    "content_index": 0,
    "delta": "An outdoor table is available at 7 PM.",
    "sequence_number": 3,
    "logprobs": []
  }
}
```

### session.usage.updated

Reports cumulative Live audio usage and, when available, the most recent context-window usage. Delegated Responses token usage is reported separately in response.event events.

#### Schema

Schema name: `LiveSessionUsageUpdated`

#### Example

```json
{
  "type": "session.usage.updated",
  "event_id": "evt_usage_001",
  "usage": {
    "seconds": 32.5
  },
  "context_window": {
    "usage_ratio": 0.12
  }
}
```

### session.closed

Returned after the Live session finishes finalizing, with the close reason, final session snapshot, and cumulative audio usage. A connection closing without this event does not confirm successful finalization.

#### Schema

Schema name: `LiveSessionClosed`

#### Example

```json
{
  "type": "session.closed",
  "event_id": "evt_closed_001",
  "client_event_id": "evt_close_001",
  "reason": "close_requested",
  "session": {
    "id": "live_abc123",
    "model": "gpt-live-1",
    "status": "active",
    "expires_at": 1788555600,
    "instructions": "Help the caller plan a restaurant reservation. Confirm details before booking.",
    "input": [],
    "audio": {
      "format": {
        "type": "audio/pcm",
        "rate": 24000
      },
      "output": {
        "voice": "marin"
      }
    },
    "delegation": {
      "type": "client"
    }
  },
  "usage": {
    "seconds": 45.8
  }
}
```

### error

Reports an error in the Live session, such as an invalid client command. Use error.client_event_id, when present, to identify the command that caused the error.

#### Schema

Schema name: `LiveErrorEvent`

#### Example

```json
{
  "type": "error",
  "event_id": "evt_error_001",
  "error": {
    "type": "invalid_request_error",
    "code": "unknown_parameter",
    "message": "Unknown parameter: 'session.voice'.",
    "param": "session.voice",
    "client_event_id": "evt_invalid_001"
  }
}
```

### info

An informational notice about the Live session, such as the event permissions applied to a frontend data channel.

#### Schema

Schema name: `LiveInfoEvent`

#### Example

```json
{
  "type": "info",
  "event_id": "evt_info_001",
  "code": "data_channel_permissions",
  "message": "The frontend data channel is configured with restricted event permissions."
}
```

### session.input_audio.append

Input audio received from the primary transport and reflected to a Live sideband connection before model-input muting.

#### Schema

Schema name: `LiveInputAudioAppend`

#### Example

```json
{
  "type": "session.input_audio.append",
  "audio": "AACAAIAAAIAAAP9/AIAAgA=="
}
```

### transport.dtmf.received

A SIP DTMF keypress received from the caller. Delivered only to sideband observers.

#### Schema

Schema name: `LiveTransportDTMFReceived`

#### Example

```json
{
  "type": "transport.dtmf.received",
  "event_id": "event_dtmf_1",
  "event": "5"
}
```

### transport.dtmf.send

A SIP DTMF keypress successfully sent by the hosted tool. Delivered only to sideband observers; this is not a client command.

#### Schema

Schema name: `LiveTransportDTMFSend`

#### Example

```json
{
  "type": "transport.dtmf.send",
  "event_id": "event_dtmf_2",
  "event": "#"
}
```

### transport.ringing

The outbound SIP provider leg is ringing or providing early media. Delivered only to sideband observers.

#### Schema

Schema name: `LiveTransportRinging`

#### Example

```json
{
  "type": "transport.ringing",
  "event_id": "event_call_1",
  "session_id": "live_u0_123"
}
```

### transport.answered

The outbound SIP provider leg answered and media is established. Delivered only to sideband observers.

#### Schema

Schema name: `LiveTransportAnswered`

#### Example

```json
{
  "type": "transport.answered",
  "event_id": "event_call_2",
  "session_id": "live_u0_123"
}
```

### transport.failed

An asynchronous outbound SIP setup failure. Delivered only to sideband observers.

#### Schema

Schema name: `LiveTransportFailed`

#### Example

```json
{
  "type": "transport.failed",
  "event_id": "event_call_4",
  "session_id": "live_u0_123",
  "error": {
    "type": "call_error",
    "code": "provider_invite_failed",
    "message": "provider rejected the call",
    "param": ""
  }
}
```
