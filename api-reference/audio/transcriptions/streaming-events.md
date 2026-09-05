# Transcription streaming events

> For the complete documentation index, see [llms.txt](https://developers.openai.com/llms.txt). Markdown versions of documentation pages are available by appending `.md` to the page URL.

## transcript.text.segment

Emitted when a diarized transcription returns a completed segment with speaker information. Only emitted when you [create a transcription](https://developers.openai.com/docs/api-reference/audio/create-transcription) with `stream` set to `true` and `response_format` set to `diarized_json`.

### Schema

Schema name: `TranscriptTextSegmentEvent`

### Example

```json
{
  "type": "transcript.text.segment",
  "id": "seg_002",
  "start": 5.2,
  "end": 12.8,
  "text": "Hi, I need help with diarization.",
  "speaker": "A"
}
```

## transcript.text.delta

Emitted when there is an additional text delta. This is also the first event emitted when the transcription starts. Only emitted when you [create a transcription](https://developers.openai.com/docs/api-reference/audio/create-transcription) with the `Stream` parameter set to `true`.

### Schema

Schema name: `TranscriptTextDeltaEvent`

### Example

```json
{
  "type": "transcript.text.delta",
  "delta": " wonderful"
}
```

## transcript.text.done

Emitted when the transcription is complete. Contains the complete transcription text. Only emitted when you [create a transcription](https://developers.openai.com/docs/api-reference/audio/create-transcription) with the `Stream` parameter set to `true`.

### Schema

Schema name: `TranscriptTextDoneEvent`

### Example

```json
{
  "type": "transcript.text.done",
  "text": "I see skies of blue and clouds of white, the bright blessed days, the dark sacred nights, and I think to myself, what a wonderful world.",
  "usage": {
    "type": "tokens",
    "input_tokens": 14,
    "input_token_details": {
      "text_tokens": 10,
      "audio_tokens": 4
    },
    "output_tokens": 31,
    "total_tokens": 45
  }
}
```
