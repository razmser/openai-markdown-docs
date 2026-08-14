# Realtime translation server events

> For the complete documentation index, see [llms.txt](https://developers.openai.com/llms.txt). Markdown versions of documentation pages are available by appending `.md` to the page URL.

These are events emitted from the OpenAI Realtime Translation WebSocket server to the client.

## error

Returned when an error occurs, which could be a client problem or a server
problem. Most errors are recoverable and the session will stay open, we
recommend to implementors to monitor and log error messages by default.

### Schema

Schema name: `RealtimeServerEventError`

### Example

```json
{
    "event_id": "event_890",
    "type": "error",
    "error": {
        "type": "invalid_request_error",
        "code": "invalid_event",
        "message": "The 'type' field is missing.",
        "param": null,
        "event_id": "event_567"
    }
}
```

## session.created

Returned when a translation session is created. Emitted automatically when a
new connection is established as the first server event. This event contains
the default translation session configuration.

### Schema

Schema name: `RealtimeTranslationServerEventSessionCreated`

### Example

```json
{
  "type": "session.created",
  "event_id": "event_123",
  "session": {
    "id": "sess_123",
    "type": "translation",
    "model": "gpt-realtime-translate",
    "expires_at": 1714857600,
    "audio": {
      "input": {
        "transcription": {
          "model": "gpt-realtime-whisper",
          "language": "en"
        },
        "noise_reduction": {
          "type": "near_field"
        }
      },
      "output": {
        "language": "fr"
      }
    }
  }
}
```

## session.updated

Returned when a translation session is updated with a `session.update` event,
unless there is an error.

### Schema

Schema name: `RealtimeTranslationServerEventSessionUpdated`

### Example

```json
{
  "type": "session.updated",
  "event_id": "event_124",
  "session": {
    "id": "sess_123",
    "type": "translation",
    "model": "gpt-realtime-translate",
    "expires_at": 1714857600,
    "audio": {
      "input": {
        "transcription": {
          "model": "gpt-realtime-whisper",
          "language": "en"
        },
        "noise_reduction": {
          "type": "near_field"
        }
      },
      "output": {
        "language": "es"
      }
    }
  }
}
```

## session.closed

Returned when a realtime translation session is closed.

### Schema

Schema name: `RealtimeTranslationServerEventSessionClosed`

### Example

```json
{
  "event_id": "event_987",
  "type": "session.closed"
}
```

## session.input_transcript.delta

Returned when optional source-language transcript text is available. This event
is emitted only when `audio.input.transcription` is configured.

Transcript deltas are append-only text fragments. Clients should not insert
unconditional spaces between deltas.

### Schema

Schema name: `RealtimeTranslationServerEventSessionInputTranscriptDelta`

### Example

```json
{
  "event_id": "event_125",
  "type": "session.input_transcript.delta",
  "delta": " hear",
  "elapsed_ms": 1200
}
```

## session.output_transcript.delta

Returned when translated transcript text is available.

Transcript deltas are append-only text fragments. Clients should not insert
unconditional spaces between deltas.

### Schema

Schema name: `RealtimeTranslationServerEventSessionOutputTranscriptDelta`

### Example

```json
{
  "event_id": "event_124",
  "type": "session.output_transcript.delta",
  "delta": " escuch",
  "elapsed_ms": 1200
}
```

## session.output_audio.delta

Returned when translated output audio is available. Output audio deltas are
200 ms frames of PCM16 audio.

### Schema

Schema name: `RealtimeTranslationServerEventSessionOutputAudioDelta`

### Example

```json
{
  "event_id": "event_123",
  "type": "session.output_audio.delta",
  "delta": "Base64EncodedAudioDelta",
  "sample_rate": 24000,
  "channels": 1,
  "format": "pcm16",
  "elapsed_ms": 1200
}
```
