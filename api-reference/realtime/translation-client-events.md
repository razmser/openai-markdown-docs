# Realtime translation client events

> For the complete documentation index, see [llms.txt](https://developers.openai.com/llms.txt). Markdown versions of documentation pages are available by appending `.md` to the page URL.

These are events that the OpenAI Realtime Translation WebSocket server will accept from the client.

## session.update

Send this event to update the translation session configuration. Translation
sessions support updates to `audio.output.language`, `audio.input.transcription`,
and `audio.input.noise_reduction`.

### Schema

Schema name: `RealtimeTranslationClientEventSessionUpdate`

### Example

```json
{
  "type": "session.update",
  "session": {
    "audio": {
      "input": {
        "transcription": {
          "model": "gpt-realtime-whisper"
        },
        "noise_reduction": null
      },
      "output": {
        "language": "es"
      }
    }
  }
}
```

## session.input_audio_buffer.append

Send this event to append audio bytes to the translation session input audio buffer.

WebSocket translation sessions accept base64-encoded 24 kHz PCM16 mono
little-endian raw audio bytes. Unsupported websocket audio formats return a
validation error because lower-quality audio materially degrades translation
quality.

Translation consumes 200 ms engine frames. For best realtime behavior, append
audio in 200 ms chunks. If a chunk is shorter, the server buffers it until it
has enough audio for one frame. If a chunk is longer, the server splits it into
200 ms frames and enqueues them back-to-back.

Keep appending silence while the session is active. If a client stops sending
audio and later resumes, model time treats the resumed audio as contiguous with
the previous audio rather than as a real-world pause.

### Schema

Schema name: `RealtimeTranslationClientEventInputAudioBufferAppend`

### Example

```json
{
  "event_id": "event_456",
  "type": "session.input_audio_buffer.append",
  "audio": "Base64EncodedAudioData"
}
```

## session.close

Gracefully close the realtime translation session. The server flushes pending
input audio and emits any remaining translated output before closing the
session.

### Schema

Schema name: `RealtimeTranslationClientEventSessionClose`

### Example

```json
{
  "event_id": "event_789",
  "type": "session.close"
}
```
