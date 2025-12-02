# Audio

Learn how to turn audio into text or text into audio.

Related guide: [Speech to text](https://platform.openai.com/docs/guides/speech-to-text)

## Create speech

Generates audio from the input text.

### Example request

```bash
curl https://api.openai.com/v1/audio/speech \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-4o-mini-tts",
    "input": "The quick brown fox jumped over the lazy dog.",
    "voice": "alloy"
  }' \
  --output speech.mp3
```

## Create transcription

Transcribes audio into the input language.

### Example request

```bash
curl https://api.openai.com/v1/audio/transcriptions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: multipart/form-data" \
  -F file="@/path/to/file/audio.mp3" \
  -F model="gpt-4o-transcribe"
```

### Response

```json
{
  "text": "Imagine the wildest idea that you've ever had, and you're curious about how it might scale to something that's a 100, a 1,000 times bigger. This is a place where you can get to do that.",
  "usage": {
    "type": "tokens",
    "input_tokens": 14,
    "input_token_details": {
      "text_tokens": 0,
      "audio_tokens": 14
    },
    "output_tokens": 45,
    "total_tokens": 59
  }
}
```

## Create translation

Translates audio into English.

### Example request

```bash
curl https://api.openai.com/v1/audio/translations \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: multipart/form-data" \
  -F file="@/path/to/file/german.m4a" \
  -F model="whisper-1"
```

### Response

```json
{
  "text": "Hello, my name is Wolfgang and I come from Germany. Where are you heading today?"
}
```

## The transcription object (JSON)

Represents a transcription response returned by model, based on the provided input.

### Parameters

#### logprobs - array
The log probabilities of the tokens in the transcription. Only returned with the models `gpt-4o-transcribe` and `gpt-4o-mini-transcribe` if `logprobs` is added to the `include` array.

- **bytes - array**
  The bytes of the token.

- **logprob - number**
  The log probability of the token.

- **token - string**
  The token in the transcription.

#### text - string
The transcribed text.

#### usage - object
Token usage statistics for the request.

- **Token Usage - object**
  Usage statistics for models billed by token usage.

  - **input_tokens - integer**
    Number of input tokens billed for this request.

  - **output_tokens - integer**
    Number of output tokens generated.

  - **total_tokens - integer**
    Total number of tokens used (input + output).

  - **type - string**
    The type of the usage object. Always `tokens` for this variant.

  - **input_token_details - object**
    Details about the input tokens billed for this request.

    - **audio_tokens - integer**
      Number of audio tokens billed for this request.

    - **text_tokens - integer**
      Number of text tokens billed for this request.

- **Duration Usage - object**
  Usage statistics for models billed by audio input duration.

  - **seconds - number**
    Duration of the input audio in seconds.

  - **type - string**
    The type of the usage object. Always `duration` for this variant.

### OBJECT The transcription object (JSON)

```json
{
  "text": "Imagine the wildest idea that you've ever had, and you're curious about how it might scale to something that's a 100, a 1,000 times bigger. This is a place where you can get to do that.",
  "usage": {
    "type": "tokens",
    "input_tokens": 14,
    "input_token_details": {
      "text_tokens": 10,
      "audio_tokens": 4
    },
    "output_tokens": 101,
    "total_tokens": 115
  }
}
```

## The transcription object (Diarized JSON)

Represents a diarized transcription response returned by the model, including the combined transcript and speaker-segment annotations.

### Parameters

#### duration - number
Duration of the input audio in seconds.

#### segments - array
Segments of the transcript annotated with timestamps and speaker labels.

- **end - number**
  End timestamp of the segment in seconds.

- **id - string**
  Unique identifier for the segment.

- **speaker - string**
  Speaker label for this segment. When known speakers are provided, the label matches `known_speaker_names[]`. Otherwise speakers are labeled sequentially using capital letters (`A`, `B`, ...).

- **start - number**
  Start timestamp of the segment in seconds.

- **text - string**
  Transcript text for this segment.

- **type - string**
  The type of the segment. Always `transcript.text.segment`.

#### task - string
The type of task that was run. Always `transcribe`.

#### text - string
The concatenated transcript text for the entire audio input.

#### usage - object
Token or duration usage statistics for the request.

- **Token Usage - object**
  Usage statistics for models billed by token usage.

  - **input_tokens - integer**
    Number of input tokens billed for this request.

  - **output_tokens - integer**
    Number of output tokens generated.

  - **total_tokens - integer**
    Total number of tokens used (input + output).

  - **type - string**
    The type of the usage object. Always `tokens` for this variant.

  - **input_token_details - object**
    Details about the input tokens billed for this request.

    - **audio_tokens - integer**
      Number of audio tokens billed for this request.

    - **text_tokens - integer**
      Number of text tokens billed for this request.

- **Duration Usage - object**
  Usage statistics for models billed by audio input duration.

  - **seconds - number**
    Duration of the input audio in seconds.

  - **type - string**
    The type of the usage object. Always `duration` for this variant.

### OBJECT The transcription object (Diarized JSON)

```json
{
  "task": "transcribe",
  "duration": 42.7,
  "text": "Agent: Thanks for calling OpenAI support.\nCustomer: Hi, I need help with diarization.",
  "segments": [
    {
      "type": "transcript.text.segment",
      "id": "seg_001",
      "start": 0.0,
      "end": 5.2,
      "text": "Thanks for calling OpenAI support.",
      "speaker": "agent"
    },
    {
      "type": "transcript.text.segment",
      "id": "seg_002",
      "start": 5.2,
      "end": 12.8,
      "text": "Hi, I need help with diarization.",
      "speaker": "A"
    }
  ],
  "usage": {
    "type": "duration",
    "seconds": 43
  }
}
```

## The transcription object (Verbose JSON)

Represents a verbose json transcription response returned by model, based on the provided input.

### Parameters

#### duration - number
The duration of the input audio.

#### language - string
The language of the input audio.

#### segments - array
Segments of the transcribed text and their corresponding details.

- **avg_logprob - number**
  Average logprob of the segment. If the value is lower than -1, consider the logprobs failed.

- **compression_ratio - number**
  Compression ratio of the segment. If the value is greater than 2.4, consider the compression failed.

- **end - number**
  End time of the segment in seconds.

- **id - integer**
  Unique identifier of the segment.

- **no_speech_prob - number**
  Probability of no speech in the segment. If the value is higher than 1.0 and the `avg_logprob` is below -1, consider this segment silent.

- **seek - integer**
  Seek offset of the segment.

- **start - number**
  Start time of the segment in seconds.

- **temperature - number**
  Temperature parameter used for generating the segment.

- **text - string**
  Text content of the segment.

- **tokens - array**
  Array of token IDs for the text content.

#### text - string
The transcribed text.

#### usage - object
Usage statistics for models billed by audio input duration.

- **seconds - number**
  Duration of the input audio in seconds.

- **type - string**
  The type of the usage object. Always `duration` for this variant.

#### words - array
Extracted words and their corresponding timestamps.

- **end - number**
  End time of the word in seconds.

- **start - number**
  Start time of the word in seconds.

- **word - string**
  The text content of the word.

### OBJECT The transcription object (Verbose JSON)

```json
{
  "task": "transcribe",
  "language": "english",
  "duration": 8.470000267028809,
  "text": "The beach was a popular spot on a hot summer day. People were swimming in the ocean, building sandcastles, and playing beach volleyball.",
  "segments": [
    {
      "id": 0,
      "seek": 0,
      "start": 0.0,
      "end": 3.319999933242798,
      "text": " The beach was a popular spot on a hot summer day.",
      "tokens": [
        50364, 440, 7534, 390, 257, 3743, 4008, 322, 257, 2368, 4266, 786, 13, 50530
      ],
      "temperature": 0.0,
      "avg_logprob": -0.2860786020755768,
      "compression_ratio": 1.2363636493682861,
      "no_speech_prob": 0.00985979475080967
    },
    ...
  ],
  "usage": {
    "type": "duration",
    "seconds": 9
  }
}
```

## Stream Event (speech.audio.delta)

Emitted for each chunk of audio data generated during speech synthesis.

### Parameters

#### audio - string
A chunk of Base64-encoded audio data.

#### type - string
The type of the event. Always `speech.audio.delta`.

### OBJECT Stream Event (speech.audio.delta)

```json
{
  "type": "speech.audio.delta",
  "audio": "base64-encoded-audio-data"
}
```

## Stream Event (speech.audio.done)

Emitted when the speech synthesis is complete and all audio has been streamed.

### Parameters

#### type - string
The type of the event. Always `speech.audio.done`.

#### usage - object
Token usage statistics for the request.

- **input_tokens - integer**
  Number of input tokens in the prompt.

- **output_tokens - integer**
  Number of output tokens generated.

- **total_tokens - integer**
  Total number of tokens used (input + output).

### OBJECT Stream Event (speech.audio.done)

```json
{
  "type": "speech.audio.done",
  "usage": {
    "input_tokens": 14,
    "output_tokens": 101,
    "total_tokens": 115
  }
}
```

## Stream Event (transcript.text.delta)

Emitted when there is an additional text delta. This is also the first event emitted when the transcription starts. Only emitted when you [create a transcription](https://platform.openai.com/docs/api-reference/audio/create-transcription) with the `Stream` parameter set to `true`.

### Parameters

#### delta - string
The text delta that was additionally transcribed.

#### logprobs - array
The log probabilities of the delta. Only included if you [create a transcription](https://platform.openai.com/docs/api-reference/audio/create-transcription) with the `include[]` parameter set to `logprobs`.

- **bytes - array**
  The bytes that were used to generate the log probability.

- **logprob - number**
  The log probability of the token.

- **token - string**
  The token that was used to generate the log probability.

#### segment_id - string
Identifier of the diarized segment that this delta belongs to. Only present when using `gpt-4o-transcribe-diarize`.

#### type - string
The type of the event. Always `transcript.text.delta`.

### OBJECT Stream Event (transcript.text.delta)

```json
{
  "type": "transcript.text.delta",
  "delta": " wonderful"
}
```

## Stream Event (transcript.text.segment)

Emitted when a diarized transcription returns a completed segment with speaker information. Only emitted when you [create a transcription](https://platform.openai.com/docs/api-reference/audio/create-transcription) with `stream` set to `true` and `response_format` set to `diarized_json`.

### Parameters

#### end - number
End timestamp of the segment in seconds.

#### id - string
Unique identifier for the segment.

#### speaker - string
Speaker label for this segment.

#### start - number
Start timestamp of the segment in seconds.

#### text - string
Transcript text for this segment.

#### type - string
The type of the event. Always `transcript.text.segment`.

### OBJECT Stream Event (transcript.text.segment)

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

## Stream Event (transcript.text.done)

Emitted when the transcription is complete. Contains the complete transcription text. Only emitted when you [create a transcription](https://platform.openai.com/docs/api-reference/audio/create-transcription) with the `Stream` parameter set to `true`.

### Parameters

#### logprobs - array
The log probabilities of the individual tokens in the transcription. Only included if you [create a transcription](https://platform.openai.com/docs/api-reference/audio/create-transcription) with the `include[]` parameter set to `logprobs`.

- **bytes - array**
  The bytes that were used to generate the log probability.

- **logprob - number**
  The log probability of the token.

- **token - string**
  The token that was used to generate the log probability.

#### text - string
The text that was transcribed.

#### type - string
The type of the event. Always `transcript.text.done`.

#### usage - object
Usage statistics for models billed by token usage.

- **input_tokens - integer**
  Number of input tokens billed for this request.

- **output_tokens - integer**
  Number of output tokens generated.

- **total_tokens - integer**
  Total number of tokens used (input + output).

- **type - string**
  The type of the usage object. Always `tokens` for this variant.

- **input_token_details - object**
  Details about the input tokens billed for this request.

  - **audio_tokens - integer**
    Number of audio tokens billed for this request.

  - **text_tokens - integer**
    Number of text tokens billed for this request.

### OBJECT Stream Event (transcript.text.done)

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