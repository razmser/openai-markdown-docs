# Server events

These are events emitted from the OpenAI Realtime WebSocket server to the client.

## error

Returned when an error occurs, which could be a client problem or a server problem. Most errors are recoverable and the session will stay open, we recommend to implementors to monitor and log error messages by default.

### Parameters

#### error - object
Details of the error.

- **message - string**
  A human-readable error message.

- **type - string**
  The type of error (e.g., "invalid_request_error", "server_error").

- **code - string**
  Error code, if any.

- **event_id - string**
  The event_id of the client event that caused the error, if applicable.

- **param - string**
  Parameter related to the error, if any.

#### event_id - string
The unique ID of the server event.

#### type - string
The event type, must be `error`.

### OBJECT error

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

Returned when a Session is created. Emitted automatically when a new connection is established as the first server event. This event will contain the default Session configuration.

### Parameters

#### event_id - string
The unique ID of the server event.

#### session - object
The session configuration.

- **Realtime session configuration - object**
  Realtime session object configuration.

  - **type - string**
    The type of session to create. Always `realtime` for the Realtime API.

  - **audio - object**
    Configuration for input and output audio.

    - **input - object**
      The format of the input audio.

      - **format - object**
        The format of the input audio.

        - **PCM audio format - object**
          The PCM audio format. Only a 24kHz sample rate is supported.

          - **rate - integer**
            The sample rate of the audio. Always `24000`.

          - **type - string**
            The audio format. Always `audio/pcm`.

        - **PCMU audio format - object**
          The G.711 μ-law format.

          - **type - string**
            The audio format. Always `audio/pcmu`.

        - **PCMA audio format - object**
          The G.711 A-law format.

          - **type - string**
            The audio format. Always `audio/pcma`.

      - **noise_reduction - object**
        Configuration for input audio noise reduction. This can be set to `null` to turn off. Noise reduction filters audio added to the input audio buffer before it is sent to VAD and the model. Filtering the audio can improve VAD and turn detection accuracy (reducing false positives) and model performance by improving perception of the input audio.

        - **type - string**
          Type of noise reduction. `near_field` is for close-talking microphones such as headphones, `far_field` is for far-field microphones such as laptop or conference room microphones.

      - **transcription - object**
        Configuration for input audio transcription, defaults to off and can be set to `null` to turn off once on. Input audio transcription is not native to the model, since the model consumes audio directly. Transcription runs asynchronously through [the /audio/transcriptions endpoint](https://platform.openai.com/docs/api-reference/audio/createTranscription) and should be treated as guidance of input audio content rather than precisely what the model heard. The client can optionally set the language and prompt for transcription, these offer additional guidance to the transcription service.

        - **language - string**
          The language of the input audio. Supplying the input language in [ISO-639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) (e.g. `en`) format will improve accuracy and latency.

        - **model - string**
          The model to use for transcription. Current options are `whisper-1`, `gpt-4o-mini-transcribe`, `gpt-4o-transcribe`, and `gpt-4o-transcribe-diarize`. Use `gpt-4o-transcribe-diarize` when you need diarization with speaker labels.

        - **prompt - string**
          An optional text to guide the model's style or continue a previous audio segment. For `whisper-1`, the [prompt is a list of keywords](https://platform.openai.com/docs/guides/speech-to-text#prompting). For `gpt-4o-transcribe` models (excluding `gpt-4o-transcribe-diarize`), the prompt is a free text string, for example "expect words related to technology".

      - **turn_detection - object**
        Configuration for turn detection, ether Server VAD or Semantic VAD. This can be set to `null` to turn off, in which case the client must manually trigger model response.

        Server VAD means that the model will detect the start and end of speech based on audio volume and respond at the end of user speech.

        Semantic VAD is more advanced and uses a turn detection model (in conjunction with VAD) to semantically estimate whether the user has finished speaking, then dynamically sets a timeout based on this probability. For example, if user audio trails off with "uhhm", the model will score a low probability of turn end and wait longer for the user to continue speaking. This can be useful for more natural conversations, but may have a higher latency.

        - **Server VAD - object**
          Server-side voice activity detection (VAD) which flips on when user speech is detected and off after a period of silence.

          - **type - string**
            Type of turn detection, `server_vad` to turn on simple Server VAD.

          - **create_response - boolean**
            Whether or not to automatically generate a response when a VAD stop event occurs. If `interrupt_response` is set to `false` this may fail to create a response if the model is already responding.

            If both `create_response` and `interrupt_response` are set to `false`, the model will never respond automatically but VAD events will still be emitted.

          - **idle_timeout_ms - integer**
            Optional timeout after which a model response will be triggered automatically. This is useful for situations in which a long pause from the user is unexpected, such as a phone call. The model will effectively prompt the user to continue the conversation based on the current context.

            The timeout value will be applied after the last model response's audio has finished playing, i.e. it's set to the `response.done` time plus audio playback duration.

            An `input_audio_buffer.timeout_triggered` event (plus events associated with the Response) will be emitted when the timeout is reached. Idle timeout is currently only supported for `server_vad` mode.

          - **interrupt_response - boolean**
            Whether or not to automatically interrupt (cancel) any ongoing response with output to the default conversation (i.e. `conversation` of `auto`) when a VAD start event occurs. If `true` then the response will be cancelled, otherwise it will continue until complete.

            If both `create_response` and `interrupt_response` are set to `false`, the model will never respond automatically but VAD events will still be emitted.

          - **prefix_padding_ms - integer**
            Used only for `server_vad` mode. Amount of audio to include before the VAD detected speech (in milliseconds). Defaults to 300ms.

          - **silence_duration_ms - integer**
            Used only for `server_vad` mode. Duration of silence to detect speech stop (in milliseconds). Defaults to 500ms. With shorter values the model will respond more quickly, but may jump in on short pauses from the user.

          - **threshold - number**
            Used only for `server_vad` mode. Activation threshold for VAD (0.0 to 1.0), this defaults to 0.5. A higher threshold will require louder audio to activate the model, and thus might perform better in noisy environments.

        - **Semantic VAD - object**
          Server-side semantic turn detection which uses a model to determine when the user has finished speaking.

          - **type - string**
            Type of turn detection, `semantic_vad` to turn on Semantic VAD.

          - **create_response - boolean**
            Whether or not to automatically generate a response when a VAD stop event occurs.

          - **eagerness - string**
            Used only for `semantic_vad` mode. The eagerness of the model to respond. `low` will wait longer for the user to continue speaking, `high` will respond more quickly. `auto` is the default and is equivalent to `medium`. `low`, `medium`, and `high` have max timeouts of 8s, 4s, and 2s respectively.

          - **interrupt_response - boolean**
            Whether or not to automatically interrupt any ongoing response with output to the default conversation (i.e. `conversation` of `auto`) when a VAD start event occurs.

    - **output - object**
      The format of the output audio.

      - **format - object**
        The format of the output audio.

        - **PCM audio format - object**
          The PCM audio format. Only a 24kHz sample rate is supported.

          - **rate - integer**
            The sample rate of the audio. Always `24000`.

          - **type - string**
            The audio format. Always `audio/pcm`.

        - **PCMU audio format - object**
          The G.711 μ-law format.

          - **type - string**
            The audio format. Always `audio/pcmu`.

        - **PCMA audio format - object**
          The G.711 A-law format.

          - **type - string**
            The audio format. Always `audio/pcma`.

      - **speed - number**
        The speed of the model's spoken response as a multiple of the original speed. 1.0 is the default speed. 0.25 is the minimum speed. 1.5 is the maximum speed. This value can only be changed in between model turns, not while a response is in progress.

        This parameter is a post-processing adjustment to the audio after it is generated, it's also possible to prompt the model to speak faster or slower.

      - **voice - string**
        The voice the model uses to respond. Voice cannot be changed during the session once the model has responded with audio at least once. Current voice options are `alloy`, `ash`, `ballad`, `coral`, `echo`, `sage`, `shimmer`, `verse`, `marin`, and `cedar`. We recommend `marin` and `cedar` for best quality.

  - **include - array**
    Additional fields to include in server outputs.

    `item.input_audio_transcription.logprobs`: Include logprobs for input audio transcription.

  - **instructions - string**
    The default system instructions (i.e. system message) prepended to model calls. This field allows the client to guide the model on desired responses. The model can be instructed on response content and format, (e.g. "be extremely succinct", "act friendly", "here are examples of good responses") and on audio behavior (e.g. "talk quickly", "inject emotion into your voice", "laugh frequently"). The instructions are not guaranteed to be followed by the model, but they provide guidance to the model on the desired behavior.

    Note that the server sets default instructions which will be used if this field is not set and are visible in the `session.created` event at the start of the session.

  - **max_output_tokens - integer or "inf"**
    Maximum number of output tokens for a single assistant response, inclusive of tool calls. Provide an integer between 1 and 4096 to limit output tokens, or `inf` for the maximum available tokens for a given model. Defaults to `inf`.

  - **model - string**
    The Realtime model used for this session.

  - **output_modalities - array**
    The set of modalities the model can respond with. It defaults to `["audio"]`, indicating that the model will respond with audio plus a transcript. `["text"]` can be used to make the model respond with text only. It is not possible to request both `text` and `audio` at the same time.

  - **prompt - object**
    Reference to a prompt template and its variables. [Learn more](https://platform.openai.com/docs/guides/text?api-mode=responses#reusable-prompts).

    - **id - string**
      The unique identifier of the prompt template to use.

    - **variables - map**
      Optional map of values to substitute in for variables in your prompt. The substitution values can either be strings, or other Response input types like images or files.

    - **version - string**
      Optional version of the prompt template.

  - **tool_choice - string or object**
    How the model chooses tools. Provide one of the string modes or force a specific function/MCP tool.

    - **Tool choice mode - string**
      Controls which (if any) tool is called by the model.

      `none` means the model will not call any tool and instead generates a message.

      `auto` means the model can pick between generating a message or calling one or more tools.

      `required` means the model must call one or more tools.

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

  - **tools - array**
    Tools available to the model.

    - **Function tool - object**
      The description of the function, including guidance on when and how to call it, and guidance about what to tell the user when calling (if anything).

      - **description - string**
        The description of the function, including guidance on when and how to call it, and guidance about what to tell the user when calling (if anything).

      - **name - string**
        The name of the function.

      - **parameters - object**
        Parameters of the function in JSON Schema.

      - **type - string**
        The type of the tool, i.e. `function`.

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

  - **tracing - "auto" or object**
    Realtime API can write session traces to the [Traces Dashboard](/logs?api=traces). Set to null to disable tracing. Once tracing is enabled for a session, the configuration cannot be modified.

    `auto` will create a trace for the session with default values for the workflow name, group id, and metadata.

    - **auto - string**
      Enables tracing and sets default values for tracing configuration options. Always `auto`.

    - **Tracing Configuration - object**
      Granular configuration for tracing.

      - **group_id - string**
        The group id to attach to this trace to enable filtering and grouping in the Traces Dashboard.

      - **metadata - object**
        The arbitrary metadata to attach to this trace to enable filtering in the Traces Dashboard.

      - **workflow_name - string**
        The name of the workflow to attach to this trace. This is used to name the trace in the Traces Dashboard.

  - **truncation - string or object**
    When the number of tokens in a conversation exceeds the model's input token limit, the conversation be truncated, meaning messages (starting from the oldest) will not be included in the model's context. A 32k context model with 4,096 max output tokens can only include 28,224 tokens in the context before truncation occurs.

    Clients can configure truncation behavior to truncate with a lower max token limit, which is an effective way to control token usage and cost.

    Truncation will reduce the number of cached tokens on the next turn (busting the cache), since messages are dropped from the beginning of the context. However, clients can also configure truncation to retain messages up to a fraction of the maximum context size, which will reduce the need for future truncations and thus improve the cache rate.

    Truncation can be disabled entirely, which means the server will never truncate but would instead return an error if the conversation exceeds the model's input token limit.

    - **Retention ratio truncation - object**
      Retain a fraction of the conversation tokens when the conversation exceeds the input token limit. This allows you to amortize truncations across multiple turns, which can help improve cached token usage.

      - **retention_ratio - number**
        Fraction of post-instruction conversation tokens to retain (`0.0` \- `1.0`) when the conversation exceeds the input token limit. Setting this to `0.8` means that messages will be dropped until 80% of the maximum allowed tokens are used. This helps reduce the frequency of truncations and improve cache rates.

      - **type - string**
        Use retention ratio truncation.

      - **token_limits - object**
        Optional custom token limits for this truncation strategy. If not provided, the model's default token limits will be used.

        - **post_instructions - integer**
          Maximum tokens allowed in the conversation after instructions (which including tool definitions). For example, setting this to 5,000 would mean that truncation would occur when the conversation exceeds 5,000 tokens after instructions. This cannot be higher than the model's context window size minus the maximum output tokens.

- **Realtime transcription session configuration - object**
  Realtime transcription session object configuration.

  - **type - string**
    The type of session to create. Always `transcription` for transcription sessions.

  - **audio - object**
    Configuration for input and output audio.

    - **input - object**
      The PCM audio format. Only a 24kHz sample rate is supported.

      - **format - object**
        The PCM audio format. Only a 24kHz sample rate is supported.

        - **PCM audio format - object**
          The PCM audio format. Only a 24kHz sample rate is supported.

          - **rate - integer**
            The sample rate of the audio. Always `24000`.

          - **type - string**
            The audio format. Always `audio/pcm`.

        - **PCMU audio format - object**
          The G.711 μ-law format.

          - **type - string**
            The audio format. Always `audio/pcmu`.

        - **PCMA audio format - object**
          The G.711 A-law format.

          - **type - string**
            The audio format. Always `audio/pcma`.

      - **noise_reduction - object**
        Configuration for input audio noise reduction. This can be set to `null` to turn off. Noise reduction filters audio added to the input audio buffer before it is sent to VAD and the model. Filtering the audio can improve VAD and turn detection accuracy (reducing false positives) and model performance by improving perception of the input audio.

        - **type - string**
          Type of noise reduction. `near_field` is for close-talking microphones such as headphones, `far_field` is for far-field microphones such as laptop or conference room microphones.

      - **transcription - object**
        Configuration for input audio transcription, defaults to off and can be set to `null` to turn off once on. Input audio transcription is not native to the model, since the model consumes audio directly. Transcription runs asynchronously through [the /audio/transcriptions endpoint](https://platform.openai.com/docs/api-reference/audio/createTranscription) and should be treated as guidance of input audio content rather than precisely what the model heard. The client can optionally set the language and prompt for transcription, these offer additional guidance to the transcription service.

        - **language - string**
          The language of the input audio. Supplying the input language in [ISO-639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) (e.g. `en`) format will improve accuracy and latency.

        - **model - string**
          The model to use for transcription. Current options are `whisper-1`, `gpt-4o-mini-transcribe`, `gpt-4o-transcribe`, and `gpt-4o-transcribe-diarize`. Use `gpt-4o-transcribe-diarize` when you need diarization with speaker labels.

        - **prompt - string**
          An optional text to guide the model's style or continue a previous audio segment. For `whisper-1`, the [prompt is a list of keywords](https://platform.openai.com/docs/guides/speech-to-text#prompting). For `gpt-4o-transcribe` models (excluding `gpt-4o-transcribe-diarize`), the prompt is a free text string, for example "expect words related to technology".

      - **turn_detection - object**
        Configuration for turn detection, ether Server VAD or Semantic VAD. This can be set to `null` to turn off, in which case the client must manually trigger model response.

        Server VAD means that the model will detect the start and end of speech based on audio volume and respond at the end of user speech.

        Semantic VAD is more advanced and uses a turn detection model (in conjunction with VAD) to semantically estimate whether the user has finished speaking, then dynamically sets a timeout based on this probability. For example, if user audio trails off with "uhhm", the model will score a low probability of turn end and wait longer for the user to continue speaking. This can be useful for more natural conversations, but may have a higher latency.

        - **Server VAD - object**
          Server-side voice activity detection (VAD) which flips on when user speech is detected and off after a period of silence.

          - **type - string**
            Type of turn detection, `server_vad` to turn on simple Server VAD.

          - **create_response - boolean**
            Whether or not to automatically generate a response when a VAD stop event occurs. If `interrupt_response` is set to `false` this may fail to create a response if the model is already responding.

            If both `create_response` and `interrupt_response` are set to `false`, the model will never respond automatically but VAD events will still be emitted.

          - **idle_timeout_ms - integer**
            Optional timeout after which a model response will be triggered automatically. This is useful for situations in which a long pause from the user is unexpected, such as a phone call. The model will effectively prompt the user to continue the conversation based on the current context.

            The timeout value will be applied after the last model response's audio has finished playing, i.e. it's set to the `response.done` time plus audio playback duration.

            An `input_audio_buffer.timeout_triggered` event (plus events associated with the Response) will be emitted when the timeout is reached. Idle timeout is currently only supported for `server_vad` mode.

          - **interrupt_response - boolean**
            Whether or not to automatically interrupt (cancel) any ongoing response with output to the default conversation (i.e. `conversation` of `auto`) when a VAD start event occurs. If `true` then the response will be cancelled, otherwise it will continue until complete.

            If both `create_response` and `interrupt_response` are set to `false`, the model will never respond automatically but VAD events will still be emitted.

          - **prefix_padding_ms - integer**
            Used only for `server_vad` mode. Amount of audio to include before the VAD detected speech (in milliseconds). Defaults to 300ms.

          - **silence_duration_ms - integer**
            Used only for `server_vad` mode. Duration of silence to detect speech stop (in milliseconds). Defaults to 500ms. With shorter values the model will respond more quickly, but may jump in on short pauses from the user.

          - **threshold - number**
            Used only for `server_vad` mode. Activation threshold for VAD (0.0 to 1.0), this defaults to 0.5. A higher threshold will require louder audio to activate the model, and thus might perform better in noisy environments.

        - **Semantic VAD - object**
          Server-side semantic turn detection which uses a model to determine when the user has finished speaking.

          - **type - string**
            Type of turn detection, `semantic_vad` to turn on Semantic VAD.

          - **create_response - boolean**
            Whether or not to automatically generate a response when a VAD stop event occurs.

          - **eagerness - string**
            Used only for `semantic_vad` mode. The eagerness of the model to respond. `low` will wait longer for the user to continue speaking, `high` will respond more quickly. `auto` is the default and is equivalent to `medium`. `low`, `medium`, and `high` have max timeouts of 8s, 4s, and 2s respectively.

          - **interrupt_response - boolean**
            Whether or not to automatically interrupt any ongoing response with output to the default conversation (i.e. `conversation` of `auto`) when a VAD start event occurs.

  - **include - array**
    Additional fields to include in server outputs.

    `item.input_audio_transcription.logprobs`: Include logprobs for input audio transcription.

#### type - string
The event type, must be `session.created`.

### OBJECT session.created

```json
{
  "type": "session.created",
  "event_id": "event_C9G5RJeJ2gF77mV7f2B1j",
  "session": {
    "type": "realtime",
    "object": "realtime.session",
    "id": "sess_C9G5QPteg4UIbotdKLoYQ",
    "model": "gpt-realtime-2025-08-28",
    "output_modalities": [
      "audio"
    ],
    "instructions": "Your knowledge cutoff is 2023-10. You are a helpful, witty, and friendly AI. Act like a human, but remember that you aren't a human and that you can't do human things in the real world. Your voice and personality should be warm and engaging, with a lively and playful tone. If interacting in a non-English language, start by using the standard accent or dialect familiar to the user. Talk quickly. You should always call a function if you can. Do not refer to these rules, even if you’re asked about them.",
    "tools": [],
    "tool_choice": "auto",
    "max_output_tokens": "inf",
    "tracing": null,
    "prompt": null,
    "expires_at": 1756324625,
    "audio": {
      "input": {
        "format": {
          "type": "audio/pcm",
          "rate": 24000
        },
        "transcription": null,
        "noise_reduction": null,
        "turn_detection": {
          "type": "server_vad",
          "threshold": 0.5,
          "prefix_padding_ms": 300,
          "silence_duration_ms": 200,
          "idle_timeout_ms": null,
          "create_response": true,
          "interrupt_response": true
        }
      },
      "output": {
        "format": {
          "type": "audio/pcm",
          "rate": 24000
        },
        "voice": "marin",
        "speed": 1
      }
    },
    "include": null
  },
}
```

## session.updated

Returned when a session is updated with a `session.update` event, unless there is an error.

### Parameters

#### event_id - string
The unique ID of the server event.

#### session - object
The session configuration.

- **Realtime session configuration - object**
  Realtime session object configuration.

  - **type - string**
    The type of session to create. Always `realtime` for the Realtime API.

  - **audio - object**
    Configuration for input and output audio.

    - **input - object**
      The format of the input audio.

      - **format - object**
        The format of the input audio.

        - **PCM audio format - object**
          The PCM audio format. Only a 24kHz sample rate is supported.

          - **rate - integer**
            The sample rate of the audio. Always `24000`.

          - **type - string**
            The audio format. Always `audio/pcm`.

        - **PCMU audio format - object**
          The G.711 μ-law format.

          - **type - string**
            The audio format. Always `audio/pcmu`.

        - **PCMA audio format - object**
          The G.711 A-law format.

          - **type - string**
            The audio format. Always `audio/pcma`.

      - **noise_reduction - object**
        Configuration for input audio noise reduction. This can be set to `null` to turn off. Noise reduction filters audio added to the input audio buffer before it is sent to VAD and the model. Filtering the audio can improve VAD and turn detection accuracy (reducing false positives) and model performance by improving perception of the input audio.

        - **type - string**
          Type of noise reduction. `near_field` is for close-talking microphones such as headphones, `far_field` is for far-field microphones such as laptop or conference room microphones.

      - **transcription - object**
        Configuration for input audio transcription, defaults to off and can be set to `null` to turn off once on. Input audio transcription is not native to the model, since the model consumes audio directly. Transcription runs asynchronously through [the /audio/transcriptions endpoint](https://platform.openai.com/docs/api-reference/audio/createTranscription) and should be treated as guidance of input audio content rather than precisely what the model heard. The client can optionally set the language and prompt for transcription, these offer additional guidance to the transcription service.

        - **language - string**
          The language of the input audio. Supplying the input language in [ISO-639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) (e.g. `en`) format will improve accuracy and latency.

        - **model - string**
          The model to use for transcription. Current options are `whisper-1`, `gpt-4o-mini-transcribe`, `gpt-4o-transcribe`, and `gpt-4o-transcribe-diarize`. Use `gpt-4o-transcribe-diarize` when you need diarization with speaker labels.

        - **prompt - string**
          An optional text to guide the model's style or continue a previous audio segment. For `whisper-1`, the [prompt is a list of keywords](https://platform.openai.com/docs/guides/speech-to-text#prompting). For `gpt-4o-transcribe` models (excluding `gpt-4o-transcribe-diarize`), the prompt is a free text string, for example "expect words related to technology".

      - **turn_detection - object**
        Configuration for turn detection, ether Server VAD or Semantic VAD. This can be set to `null` to turn off, in which case the client must manually trigger model response.

        Server VAD means that the model will detect the start and end of speech based on audio volume and respond at the end of user speech.

        Semantic VAD is more advanced and uses a turn detection model (in conjunction with VAD) to semantically estimate whether the user has finished speaking, then dynamically sets a timeout based on this probability. For example, if user audio trails off with "uhhm", the model will score a low probability of turn end and wait longer for the user to continue speaking. This can be useful for more natural conversations, but may have a higher latency.

        - **Server VAD - object**
          Server-side voice activity detection (VAD) which flips on when user speech is detected and off after a period of silence.

          - **type - string**
            Type of turn detection, `server_vad` to turn on simple Server VAD.

          - **create_response - boolean**
            Whether or not to automatically generate a response when a VAD stop event occurs. If `interrupt_response` is set to `false` this may fail to create a response if the model is already responding.

            If both `create_response` and `interrupt_response` are set to `false`, the model will never respond automatically but VAD events will still be emitted.

          - **idle_timeout_ms - integer**
            Optional timeout after which a model response will be triggered automatically. This is useful for situations in which a long pause from the user is unexpected, such as a phone call. The model will effectively prompt the user to continue the conversation based on the current context.

            The timeout value will be applied after the last model response's audio has finished playing, i.e. it's set to the `response.done` time plus audio playback duration.

            An `input_audio_buffer.timeout_triggered` event (plus events associated with the Response) will be emitted when the timeout is reached. Idle timeout is currently only supported for `server_vad` mode.

          - **interrupt_response - boolean**
            Whether or not to automatically interrupt (cancel) any ongoing response with output to the default conversation (i.e. `conversation` of `auto`) when a VAD start event occurs. If `true` then the response will be cancelled, otherwise it will continue until complete.

            If both `create_response` and `interrupt_response` are set to `false`, the model will never respond automatically but VAD events will still be emitted.

          - **prefix_padding_ms - integer**
            Used only for `server_vad` mode. Amount of audio to include before the VAD detected speech (in milliseconds). Defaults to 300ms.

          - **silence_duration_ms - integer**
            Used only for `server_vad` mode. Duration of silence to detect speech stop (in milliseconds). Defaults to 500ms. With shorter values the model will respond more quickly, but may jump in on short pauses from the user.

          - **threshold - number**
            Used only for `server_vad` mode. Activation threshold for VAD (0.0 to 1.0), this defaults to 0.5. A higher threshold will require louder audio to activate the model, and thus might perform better in noisy environments.

        - **Semantic VAD - object**
          Server-side semantic turn detection which uses a model to determine when the user has finished speaking.

          - **type - string**
            Type of turn detection, `semantic_vad` to turn on Semantic VAD.

          - **create_response - boolean**
            Whether or not to automatically generate a response when a VAD stop event occurs.

          - **eagerness - string**
            Used only for `semantic_vad` mode. The eagerness of the model to respond. `low` will wait longer for the user to continue speaking, `high` will respond more quickly. `auto` is the default and is equivalent to `medium`. `low`, `medium`, and `high` have max timeouts of 8s, 4s, and 2s respectively.

          - **interrupt_response - boolean**
            Whether or not to automatically interrupt any ongoing response with output to the default conversation (i.e. `conversation` of `auto`) when a VAD start event occurs.

    - **output - object**
      The format of the output audio.

      - **format - object**
        The format of the output audio.

        - **PCM audio format - object**
          The PCM audio format. Only a 24kHz sample rate is supported.

          - **rate - integer**
            The sample rate of the audio. Always `24000`.

          - **type - string**
            The audio format. Always `audio/pcm`.

        - **PCMU audio format - object**
          The G.711 μ-law format.

          - **type - string**
            The audio format. Always `audio/pcmu`.

        - **PCMA audio format - object**
          The G.711 A-law format.

          - **type - string**
            The audio format. Always `audio/pcma`.

      - **speed - number**
        The speed of the model's spoken response as a multiple of the original speed. 1.0 is the default speed. 0.25 is the minimum speed. 1.5 is the maximum speed. This value can only be changed in between model turns, not while a response is in progress.

        This parameter is a post-processing adjustment to the audio after it is generated, it's also possible to prompt the model to speak faster or slower.

      - **voice - string**
        The voice the model uses to respond. Voice cannot be changed during the session once the model has responded with audio at least once. Current voice options are `alloy`, `ash`, `ballad`, `coral`, `echo`, `sage`, `shimmer`, `verse`, `marin`, and `cedar`. We recommend `marin` and `cedar` for best quality.

  - **include - array**
    Additional fields to include in server outputs.

    `item.input_audio_transcription.logprobs`: Include logprobs for input audio transcription.

  - **instructions - string**
    The default system instructions (i.e. system message) prepended to model calls. This field allows the client to guide the model on desired responses. The model can be instructed on response content and format, (e.g. "be extremely succinct", "act friendly", "here are examples of good responses") and on audio behavior (e.g. "talk quickly", "inject emotion into your voice", "laugh frequently"). The instructions are not guaranteed to be followed by the model, but they provide guidance to the model on the desired behavior.

    Note that the server sets default instructions which will be used if this field is not set and are visible in the `session.created` event at the start of the session.

  - **max_output_tokens - integer or "inf"**
    Maximum number of output tokens for a single assistant response, inclusive of tool calls. Provide an integer between 1 and 4096 to limit output tokens, or `inf` for the maximum available tokens for a given model. Defaults to `inf`.

  - **model - string**
    The Realtime model used for this session.

  - **output_modalities - array**
    The set of modalities the model can respond with. It defaults to `["audio"]`, indicating that the model will respond with audio plus a transcript. `["text"]` can be used to make the model respond with text only. It is not possible to request both `text` and `audio` at the same time.

  - **prompt - object**
    Reference to a prompt template and its variables. [Learn more](https://platform.openai.com/docs/guides/text?api-mode=responses#reusable-prompts).

    - **id - string**
      The unique identifier of the prompt template to use.

    - **variables - map**
      Optional map of values to substitute in for variables in your prompt. The substitution values can either be strings, or other Response input types like images or files.

    - **version - string**
      Optional version of the prompt template.

  - **tool_choice - string or object**
    How the model chooses tools. Provide one of the string modes or force a specific function/MCP tool.

    - **Tool choice mode - string**
      Controls which (if any) tool is called by the model.

      `none` means the model will not call any tool and instead generates a message.

      `auto` means the model can pick between generating a message or calling one or more tools.

      `required` means the model must call one or more tools.

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

  - **tools - array**
    Tools available to the model.

    - **Function tool - object**
      The description of the function, including guidance on when and how to call it, and guidance about what to tell the user when calling (if anything).

      - **description - string**
        The description of the function, including guidance on when and how to call it, and guidance about what to tell the user when calling (if anything).

      - **name - string**
        The name of the function.

      - **parameters - object**
        Parameters of the function in JSON Schema.

      - **type - string**
        The type of the tool, i.e. `function`.

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

  - **tracing - "auto" or object**
    Realtime API can write session traces to the [Traces Dashboard](/logs?api=traces). Set to null to disable tracing. Once tracing is enabled for a session, the configuration cannot be modified.

    `auto` will create a trace for the session with default values for the workflow name, group id, and metadata.

    - **auto - string**
      Enables tracing and sets default values for tracing configuration options. Always `auto`.

    - **Tracing Configuration - object**
      Granular configuration for tracing.

      - **group_id - string**
        The group id to attach to this trace to enable filtering and grouping in the Traces Dashboard.

      - **metadata - object**
        The arbitrary metadata to attach to this trace to enable filtering in the Traces Dashboard.

      - **workflow_name - string**
        The name of the workflow to attach to this trace. This is used to name the trace in the Traces Dashboard.

  - **truncation - string or object**
    When the number of tokens in a conversation exceeds the model's input token limit, the conversation be truncated, meaning messages (starting from the oldest) will not be included in the model's context. A 32k context model with 4,096 max output tokens can only include 28,224 tokens in the context before truncation occurs.

    Clients can configure truncation behavior to truncate with a lower max token limit, which is an effective way to control token usage and cost.

    Truncation will reduce the number of cached tokens on the next turn (busting the cache), since messages are dropped from the beginning of the context. However, clients can also configure truncation to retain messages up to a fraction of the maximum context size, which will reduce the need for future truncations and thus improve the cache rate.

    Truncation can be disabled entirely, which means the server will never truncate but would instead return an error if the conversation exceeds the model's input token limit.

    - **Retention ratio truncation - object**
      Retain a fraction of the conversation tokens when the conversation exceeds the input token limit. This allows you to amortize truncations across multiple turns, which can help improve cached token usage.

      - **retention_ratio - number**
        Fraction of post-instruction conversation tokens to retain (`0.0` \- `1.0`) when the conversation exceeds the input token limit. Setting this to `0.8` means that messages will be dropped until 80% of the maximum allowed tokens are used. This helps reduce the frequency of truncations and improve cache rates.

      - **type - string**
        Use retention ratio truncation.

      - **token_limits - object**
        Optional custom token limits for this truncation strategy. If not provided, the model's default token limits will be used.

        - **post_instructions - integer**
          Maximum tokens allowed in the conversation after instructions (which including tool definitions). For example, setting this to 5,000 would mean that truncation would occur when the conversation exceeds 5,000 tokens after instructions. This cannot be higher than the model's context window size minus the maximum output tokens.

- **Realtime transcription session configuration - object**
  Realtime transcription session object configuration.

  - **type - string**
    The type of session to create. Always `transcription` for transcription sessions.

  - **audio - object**
    Configuration for input and output audio.

    - **input - object**
      The PCM audio format. Only a 24kHz sample rate is supported.

      - **format - object**
        The PCM audio format. Only a 24kHz sample rate is supported.

        - **PCM audio format - object**
          The PCM audio format. Only a 24kHz sample rate is supported.

          - **rate - integer**
            The sample rate of the audio. Always `24000`.

          - **type - string**
            The audio format. Always `audio/pcm`.

        - **PCMU audio format - object**
          The G.711 μ-law format.

          - **type - string**
            The audio format. Always `audio/pcmu`.

        - **PCMA audio format - object**
          The G.711 A-law format.

          - **type - string**
            The audio format. Always `audio/pcma`.

      - **noise_reduction - object**
        Configuration for input audio noise reduction. This can be set to `null` to turn off. Noise reduction filters audio added to the input audio buffer before it is sent to VAD and the model. Filtering the audio can improve VAD and turn detection accuracy (reducing false positives) and model performance by improving perception of the input audio.

        - **type - string**
          Type of noise reduction. `near_field` is for close-talking microphones such as headphones, `far_field` is for far-field microphones such as laptop or conference room microphones.

      - **transcription - object**
        Configuration for input audio transcription, defaults to off and can be set to `null` to turn off once on. Input audio transcription is not native to the model, since the model consumes audio directly. Transcription runs asynchronously through [the /audio/transcriptions endpoint](https://platform.openai.com/docs/api-reference/audio/createTranscription) and should be treated as guidance of input audio content rather than precisely what the model heard. The client can optionally set the language and prompt for transcription, these offer additional guidance to the transcription service.

        - **language - string**
          The language of the input audio. Supplying the input language in [ISO-639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) (e.g. `en`) format will improve accuracy and latency.

        - **model - string**
          The model to use for transcription. Current options are `whisper-1`, `gpt-4o-mini-transcribe`, `gpt-4o-transcribe`, and `gpt-4o-transcribe-diarize`. Use `gpt-4o-transcribe-diarize` when you need diarization with speaker labels.

        - **prompt - string**
          An optional text to guide the model's style or continue a previous audio segment. For `whisper-1`, the [prompt is a list of keywords](https://platform.openai.com/docs/guides/speech-to-text#prompting). For `gpt-4o-transcribe` models (excluding `gpt-4o-transcribe-diarize`), the prompt is a free text string, for example "expect words related to technology".

      - **turn_detection - object**
        Configuration for turn detection, ether Server VAD or Semantic VAD. This can be set to `null` to turn off, in which case the client must manually trigger model response.

        Server VAD means that the model will detect the start and end of speech based on audio volume and respond at the end of user speech.

        Semantic VAD is more advanced and uses a turn detection model (in conjunction with VAD) to semantically estimate whether the user has finished speaking, then dynamically sets a timeout based on this probability. For example, if user audio trails off with "uhhm", the model will score a low probability of turn end and wait longer for the user to continue speaking. This can be useful for more natural conversations, but may have a higher latency.

        - **Server VAD - object**
          Server-side voice activity detection (VAD) which flips on when user speech is detected and off after a period of silence.

          - **type - string**
            Type of turn detection, `server_vad` to turn on simple Server VAD.

          - **create_response - boolean**
            Whether or not to automatically generate a response when a VAD stop event occurs. If `interrupt_response` is set to `false` this may fail to create a response if the model is already responding.

            If both `create_response` and `interrupt_response` are set to `false`, the model will never respond automatically but VAD events will still be emitted.

          - **idle_timeout_ms - integer**
            Optional timeout after which a model response will be triggered automatically. This is useful for situations in which a long pause from the user is unexpected, such as a phone call. The model will effectively prompt the user to continue the conversation based on the current context.

            The timeout value will be applied after the last model response's audio has finished playing, i.e. it's set to the `response.done` time plus audio playback duration.

            An `input_audio_buffer.timeout_triggered` event (plus events associated with the Response) will be emitted when the timeout is reached. Idle timeout is currently only supported for `server_vad` mode.

          - **interrupt_response - boolean**
            Whether or not to automatically interrupt (cancel) any ongoing response with output to the default conversation (i.e. `conversation` of `auto`) when a VAD start event occurs. If `true` then the response will be cancelled, otherwise it will continue until complete.

            If both `create_response` and `interrupt_response` are set to `false`, the model will never respond automatically but VAD events will still be emitted.

          - **prefix_padding_ms - integer**
            Used only for `server_vad` mode. Amount of audio to include before the VAD detected speech (in milliseconds). Defaults to 300ms.

          - **silence_duration_ms - integer**
            Used only for `server_vad` mode. Duration of silence to detect speech stop (in milliseconds). Defaults to 500ms. With shorter values the model will respond more quickly, but may jump in on short pauses from the user.

          - **threshold - number**
            Used only for `server_vad` mode. Activation threshold for VAD (0.0 to 1.0), this defaults to 0.5. A higher threshold will require louder audio to activate the model, and thus might perform better in noisy environments.

        - **Semantic VAD - object**
          Server-side semantic turn detection which uses a model to determine when the user has finished speaking.

          - **type - string**
            Type of turn detection, `semantic_vad` to turn on Semantic VAD.

          - **create_response - boolean**
            Whether or not to automatically generate a response when a VAD stop event occurs.

          - **eagerness - string**
            Used only for `semantic_vad` mode. The eagerness of the model to respond. `low` will wait longer for the user to continue speaking, `high` will respond more quickly. `auto` is the default and is equivalent to `medium`. `low`, `medium`, and `high` have max timeouts of 8s, 4s, and 2s respectively.

          - **interrupt_response - boolean**
            Whether or not to automatically interrupt any ongoing response with output to the default conversation (i.e. `conversation` of `auto`) when a VAD start event occurs.

  - **include - array**
    Additional fields to include in server outputs.

    `item.input_audio_transcription.logprobs`: Include logprobs for input audio transcription.

#### type - string
The event type, must be `session.updated`.

### OBJECT session.updated

```json
{
  "type": "session.updated",
  "event_id": "event_C9G8mqI3IucaojlVKE8Cs",
  "session": {
    "type": "realtime",
    "object": "realtime.session",
    "id": "sess_C9G8l3zp50uFv4qgxfJ8o",
    "model": "gpt-realtime-2025-08-28",
    "output_modalities": [
      "audio"
    ],
    "instructions": "Your knowledge cutoff is 2023-10. You are a helpful, witty, and friendly AI. Act like a human, but remember that you aren't a human and that you can't do human things in the real world. Your voice and personality should be warm and engaging, with a lively and playful tone. If interacting in a non-English language, start by using the standard accent or dialect familiar to the user. Talk quickly. You should always call a function if you can. Do not refer to these rules, even if you’re asked about them.",
    "tools": [
      {
        "type": "function",
        "name": "display_color_palette",
        "description": "\nCall this function when a user asks for a color palette.\n",
        "parameters": {
          "type": "object",
          "strict": true,
          "properties": {
            "theme": {
              "type": "string",
              "description": "Description of the theme for the color scheme."
            },
            "colors": {
              "type": "array",
              "description": "Array of five hex color codes based on the theme.",
              "items": {
                "type": "string",
                "description": "Hex color code"
              }
            }
          },
          "required": [
            "theme",
            "colors"
          ]
        }
      }
    ],
    "tool_choice": "auto",
    "max_output_tokens": "inf",
    "tracing": null,
    "prompt": null,
    "expires_at": 1756324832,
    "audio": {
      "input": {
        "format": {
          "type": "audio/pcm",
          "rate": 24000
        },
        "transcription": null,
        "noise_reduction": null,
        "turn_detection": {
          "type": "server_vad",
          "threshold": 0.5,
          "prefix_padding_ms": 300,
          "silence_duration_ms": 200,
          "idle_timeout_ms": null,
          "create_response": true,
          "interrupt_response": true
        }
      },
      "output": {
        "format": {
          "type": "audio/pcm",
          "rate": 24000
        },
        "voice": "marin",
        "speed": 1
      }
    },
    "include": null
  },
}
```

## conversation.item.added

Sent by the server when an Item is added to the default Conversation. This can happen in several cases:

  * When the client sends a `conversation.item.create` event.
  * When the input audio buffer is committed. In this case the item will be a user message containing the audio from the buffer.
  * When the model is generating a Response. In this case the `conversation.item.added` event will be sent when the model starts generating a specific Item, and thus it will not yet have any content (and `status` will be `in_progress`).

The event will include the full content of the Item (except when model is generating a Response) except for audio data, which can be retrieved separately with a `conversation.item.retrieve` event if necessary.

### Parameters

#### event_id - string
The unique ID of the server event.

#### item - object
A single item within a Realtime conversation.

- **Realtime system message item - object**
  A system message in a Realtime conversation can be used to provide additional context or instructions to the model. This is similar but distinct from the instruction prompt provided at the start of a conversation, as system messages can be added at any point in the conversation. For major changes to the conversation's behavior, use instructions, but for smaller updates (e.g. "the user is now asking about a different topic"), use system messages.

  - **content - array**
    The content of the message.

    - **text - string**
      The text content.

    - **type - string**
      The content type. Always `input_text` for system messages.

  - **role - string**
    The role of the message sender. Always `system`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime user message item - object**
  A user message item in a Realtime conversation.

  - **content - array**
    The content of the message.

    - **audio - string**
      Base64-encoded audio bytes (for `input_audio`), these will be parsed as the format specified in the session input audio type configuration. This defaults to PCM 16-bit 24kHz mono if not specified.

    - **detail - string**
      The detail level of the image (for `input_image`). `auto` will default to `high`.

    - **image_url - string**
      Base64-encoded image bytes (for `input_image`) as a data URI. For example `data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...`. Supported formats are PNG and JPEG.

    - **text - string**
      The text content (for `input_text`).

    - **transcript - string**
      Transcript of the audio (for `input_audio`). This is not sent to the model, but will be attached to the message item for reference.

    - **type - string**
      The content type (`input_text`, `input_audio`, or `input_image`).

  - **role - string**
    The role of the message sender. Always `user`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime assistant message item - object**
  An assistant message item in a Realtime conversation.

  - **content - array**
    The content of the message.

    - **audio - string**
      Base64-encoded audio bytes, these will be parsed as the format specified in the session output audio type configuration. This defaults to PCM 16-bit 24kHz mono if not specified.

    - **text - string**
      The text content.

    - **transcript - string**
      The transcript of the audio content, this will always be present if the output type is `audio`.

    - **type - string**
      The content type, `output_text` or `output_audio` depending on the session `output_modalities` configuration.

  - **role - string**
    The role of the message sender. Always `assistant`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime function call item - object**
  A function call item in a Realtime conversation.

  - **arguments - string**
    The arguments of the function call. This is a JSON-encoded string representing the arguments passed to the function, for example `{"arg1": "value1", "arg2": 42}`.

  - **name - string**
    The name of the function being called.

  - **type - string**
    The type of the item. Always `function_call`.

  - **call_id - string**
    The ID of the function call.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime function call output item - object**
  A function call output item in a Realtime conversation.

  - **call_id - string**
    The ID of the function call this output is for.

  - **output - string**
    The output of the function call, this is free text and can contain any information or simply be empty.

  - **type - string**
    The type of the item. Always `function_call_output`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime MCP approval response - object**
  A Realtime item responding to an MCP approval request.

  - **approval_request_id - string**
    The ID of the approval request being answered.

  - **approve - boolean**
    Whether the request was approved.

  - **id - string**
    The unique ID of the approval response.

  - **type - string**
    The type of the item. Always `mcp_approval_response`.

  - **reason - string**
    Optional reason for the decision.

- **Realtime MCP list tools - object**
  A Realtime item listing tools available on an MCP server.

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

  - **id - string**
    The unique ID of the list.

- **Realtime MCP tool call - object**
  A Realtime item representing an invocation of a tool on an MCP server.

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
    The ID of an associated approval request, if any.

  - **error - object**
    The error from the tool call, if any.

    - **Realtime MCP protocol error - object**

      - **code - integer**

      - **message - string**

      - **type - string**

    - **Realtime MCP tool execution error - object**

      - **message - string**

      - **type - string**

    - **Realtime MCP HTTP error - object**

      - **code - integer**

      - **message - string**

      - **type - string**

  - **output - string**
    The output from the tool call.

- **Realtime MCP approval request - object**
  A Realtime item requesting human approval of a tool invocation.

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

#### previous_item_id - string
The ID of the item that precedes this one, if any. This is used to maintain ordering when items are inserted.

#### type - string
The event type, must be `conversation.item.added`.

### OBJECT conversation.item.added

```json
{
  "type": "conversation.item.added",
  "event_id": "event_C9G8pjSJCfRNEhMEnYAVy",
  "previous_item_id": null,
  "item": {
    "id": "item_C9G8pGVKYnaZu8PH5YQ9O",
    "type": "message",
    "status": "completed",
    "role": "user",
    "content": [
      {
        "type": "input_text",
        "text": "hi"
      }
    ]
  }
}
```

## conversation.item.done

Returned when a conversation item is finalized.

The event will include the full content of the Item except for audio data, which can be retrieved separately with a `conversation.item.retrieve` event if needed.

### Parameters

#### event_id - string
The unique ID of the server event.

#### item - object
A single item within a Realtime conversation.

- **Realtime system message item - object**
  A system message in a Realtime conversation can be used to provide additional context or instructions to the model. This is similar but distinct from the instruction prompt provided at the start of a conversation, as system messages can be added at any point in the conversation. For major changes to the conversation's behavior, use instructions, but for smaller updates (e.g. "the user is now asking about a different topic"), use system messages.

  - **content - array**
    The content of the message.

    - **text - string**
      The text content.

    - **type - string**
      The content type. Always `input_text` for system messages.

  - **role - string**
    The role of the message sender. Always `system`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime user message item - object**
  A user message item in a Realtime conversation.

  - **content - array**
    The content of the message.

    - **audio - string**
      Base64-encoded audio bytes (for `input_audio`), these will be parsed as the format specified in the session input audio type configuration. This defaults to PCM 16-bit 24kHz mono if not specified.

    - **detail - string**
      The detail level of the image (for `input_image`). `auto` will default to `high`.

    - **image_url - string**
      Base64-encoded image bytes (for `input_image`) as a data URI. For example `data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...`. Supported formats are PNG and JPEG.

    - **text - string**
      The text content (for `input_text`).

    - **transcript - string**
      Transcript of the audio (for `input_audio`). This is not sent to the model, but will be attached to the message item for reference.

    - **type - string**
      The content type (`input_text`, `input_audio`, or `input_image`).

  - **role - string**
    The role of the message sender. Always `user`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime assistant message item - object**
  An assistant message item in a Realtime conversation.

  - **content - array**
    The content of the message.

    - **audio - string**
      Base64-encoded audio bytes, these will be parsed as the format specified in the session output audio type configuration. This defaults to PCM 16-bit 24kHz mono if not specified.

    - **text - string**
      The text content.

    - **transcript - string**
      The transcript of the audio content, this will always be present if the output type is `audio`.

    - **type - string**
      The content type, `output_text` or `output_audio` depending on the session `output_modalities` configuration.

  - **role - string**
    The role of the message sender. Always `assistant`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime function call item - object**
  A function call item in a Realtime conversation.

  - **arguments - string**
    The arguments of the function call. This is a JSON-encoded string representing the arguments passed to the function, for example `{"arg1": "value1", "arg2": 42}`.

  - **name - string**
    The name of the function being called.

  - **type - string**
    The type of the item. Always `function_call`.

  - **call_id - string**
    The ID of the function call.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime function call output item - object**
  A function call output item in a Realtime conversation.

  - **call_id - string**
    The ID of the function call this output is for.

  - **output - string**
    The output of the function call, this is free text and can contain any information or simply be empty.

  - **type - string**
    The type of the item. Always `function_call_output`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime MCP approval response - object**
  A Realtime item responding to an MCP approval request.

  - **approval_request_id - string**
    The ID of the approval request being answered.

  - **approve - boolean**
    Whether the request was approved.

  - **id - string**
    The unique ID of the approval response.

  - **type - string**
    The type of the item. Always `mcp_approval_response`.

  - **reason - string**
    Optional reason for the decision.

- **Realtime MCP list tools - object**
  A Realtime item listing tools available on an MCP server.

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

  - **id - string**
    The unique ID of the list.

- **Realtime MCP tool call - object**
  A Realtime item representing an invocation of a tool on an MCP server.

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
    The ID of an associated approval request, if any.

  - **error - object**
    The error from the tool call, if any.

    - **Realtime MCP protocol error - object**

      - **code - integer**

      - **message - string**

      - **type - string**

    - **Realtime MCP tool execution error - object**

      - **message - string**

      - **type - string**

    - **Realtime MCP HTTP error - object**

      - **code - integer**

      - **message - string**

      - **type - string**

  - **output - string**
    The output from the tool call.

- **Realtime MCP approval request - object**
  A Realtime item requesting human approval of a tool invocation.

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

#### previous_item_id - string
The ID of the item that precedes this one, if any. This is used to maintain ordering when items are inserted.

#### type - string
The event type, must be `conversation.item.done`.

### OBJECT conversation.item.done

```json
{
  "type": "conversation.item.done",
  "event_id": "event_CCXLgMZPo3qioWCeQa4WH",
  "previous_item_id": "item_CCXLecNJVIVR2HUy3ABLj",
  "item": {
    "id": "item_CCXLfxmM5sXVJVz4mCa2S",
    "type": "message",
    "status": "completed",
    "role": "assistant",
    "content": [
      {
        "type": "output_audio",
        "transcript": "Oh, I can hear you loud and clear! Sounds like we're connected just fine. What can I help you with today?"
      }
    ]
  }
}
```

## conversation.item.retrieved

Returned when a conversation item is retrieved with `conversation.item.retrieve`. This is provided as a way to fetch the server's representation of an item, for example to get access to the post-processed audio data after noise cancellation and VAD. It includes the full content of the Item, including audio data.

### Parameters

#### event_id - string
The unique ID of the server event.

#### item - object
A single item within a Realtime conversation.

- **Realtime system message item - object**
  A system message in a Realtime conversation can be used to provide additional context or instructions to the model. This is similar but distinct from the instruction prompt provided at the start of a conversation, as system messages can be added at any point in the conversation. For major changes to the conversation's behavior, use instructions, but for smaller updates (e.g. "the user is now asking about a different topic"), use system messages.

  - **content - array**
    The content of the message.

    - **text - string**
      The text content.

    - **type - string**
      The content type. Always `input_text` for system messages.

  - **role - string**
    The role of the message sender. Always `system`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime user message item - object**
  A user message item in a Realtime conversation.

  - **content - array**
    The content of the message.

    - **audio - string**
      Base64-encoded audio bytes (for `input_audio`), these will be parsed as the format specified in the session input audio type configuration. This defaults to PCM 16-bit 24kHz mono if not specified.

    - **detail - string**
      The detail level of the image (for `input_image`). `auto` will default to `high`.

    - **image_url - string**
      Base64-encoded image bytes (for `input_image`) as a data URI. For example `data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...`. Supported formats are PNG and JPEG.

    - **text - string**
      The text content (for `input_text`).

    - **transcript - string**
      Transcript of the audio (for `input_audio`). This is not sent to the model, but will be attached to the message item for reference.

    - **type - string**
      The content type (`input_text`, `input_audio`, or `input_image`).

  - **role - string**
    The role of the message sender. Always `user`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime assistant message item - object**
  An assistant message item in a Realtime conversation.

  - **content - array**
    The content of the message.

    - **audio - string**
      Base64-encoded audio bytes, these will be parsed as the format specified in the session output audio type configuration. This defaults to PCM 16-bit 24kHz mono if not specified.

    - **text - string**
      The text content.

    - **transcript - string**
      The transcript of the audio content, this will always be present if the output type is `audio`.

    - **type - string**
      The content type, `output_text` or `output_audio` depending on the session `output_modalities` configuration.

  - **role - string**
    The role of the message sender. Always `assistant`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime function call item - object**
  A function call item in a Realtime conversation.

  - **arguments - string**
    The arguments of the function call. This is a JSON-encoded string representing the arguments passed to the function, for example `{"arg1": "value1", "arg2": 42}`.

  - **name - string**
    The name of the function being called.

  - **type - string**
    The type of the item. Always `function_call`.

  - **call_id - string**
    The ID of the function call.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime function call output item - object**
  A function call output item in a Realtime conversation.

  - **call_id - string**
    The ID of the function call this output is for.

  - **output - string**
    The output of the function call, this is free text and can contain any information or simply be empty.

  - **type - string**
    The type of the item. Always `function_call_output`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime MCP approval response - object**
  A Realtime item responding to an MCP approval request.

  - **approval_request_id - string**
    The ID of the approval request being answered.

  - **approve - boolean**
    Whether the request was approved.

  - **id - string**
    The unique ID of the approval response.

  - **type - string**
    The type of the item. Always `mcp_approval_response`.

  - **reason - string**
    Optional reason for the decision.

- **Realtime MCP list tools - object**
  A Realtime item listing tools available on an MCP server.

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

  - **id - string**
    The unique ID of the list.

- **Realtime MCP tool call - object**
  A Realtime item representing an invocation of a tool on an MCP server.

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
    The ID of an associated approval request, if any.

  - **error - object**
    The error from the tool call, if any.

    - **Realtime MCP protocol error - object**

      - **code - integer**

      - **message - string**

      - **type - string**

    - **Realtime MCP tool execution error - object**

      - **message - string**

      - **type - string**

    - **Realtime MCP HTTP error - object**

      - **code - integer**

      - **message - string**

      - **type - string**

  - **output - string**
    The output from the tool call.

- **Realtime MCP approval request - object**
  A Realtime item requesting human approval of a tool invocation.

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

#### type - string
The event type, must be `conversation.item.retrieved`.

### OBJECT conversation.item.retrieved

```json
{
  "type": "conversation.item.retrieved",
  "event_id": "event_CCXGSizgEppa2d4XbKA7K",
  "item": {
    "id": "item_CCXGRxbY0n6WE4EszhF5w",
    "object": "realtime.item",
    "type": "message",
    "status": "completed",
    "role": "assistant",
    "content": [
      {
        "type": "audio",
        "transcript": "Yes, I can hear you loud and clear. How can I help you today?",
        "audio": "8//2//v/9//q/+//+P/s...",
        "format": "pcm16"
      }
    ]
  }
}
```

## conversation.item.input_audio_transcription.completed

This event is the output of audio transcription for user audio written to the user audio buffer. Transcription begins when the input audio buffer is committed by the client or server (when VAD is enabled). Transcription runs asynchronously with Response creation, so this event may come before or after the Response events.

Realtime API models accept audio natively, and thus input transcription is a separate process run on a separate ASR (Automatic Speech Recognition) model. The transcript may diverge somewhat from the model's interpretation, and should be treated as a rough guide.

### Parameters

#### content_index - integer
The index of the content part containing the audio.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the item containing the audio that is being transcribed.

#### logprobs - array
The log probabilities of the transcription.

- **bytes - array**
  The bytes that were used to generate the log probability.

- **logprob - number**
  The log probability of the token.

- **token - string**
  The token that was used to generate the log probability.

#### transcript - string
The transcribed text.

#### type - string
The event type, must be `conversation.item.input_audio_transcription.completed`.

#### usage - object
Usage statistics for the transcription, this is billed according to the ASR model's pricing rather than the realtime model's pricing.

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

### OBJECT conversation.item.input_audio_transcription.completed

```json
{
  "type": "conversation.item.input_audio_transcription.completed",
  "event_id": "event_CCXGRvtUVrax5SJAnNOWZ",
  "item_id": "item_CCXGQ4e1ht4cOraEYcuR2",
  "content_index": 0,
  "transcript": "Hey, can you hear me?",
  "usage": {
    "type": "tokens",
    "total_tokens": 22,
    "input_tokens": 13,
    "input_token_details": {
      "text_tokens": 0,
      "audio_tokens": 13
    },
    "output_tokens": 9
  }
}
```

## conversation.item.input_audio_transcription.delta

Returned when the text value of an input audio transcription content part is updated with incremental transcription results.

### Parameters

#### content_index - integer
The index of the content part in the item's content array.

#### delta - string
The text delta.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the item containing the audio that is being transcribed.

#### logprobs - array
The log probabilities of the transcription. These can be enabled by configurating the session with `"include": ["item.input_audio_transcription.logprobs"]`. Each entry in the array corresponds a log probability of which token would be selected for this chunk of transcription. This can help to identify if it was possible there were multiple valid options for a given chunk of transcription.

- **bytes - array**
  The bytes that were used to generate the log probability.

- **logprob - number**
  The log probability of the token.

- **token - string**
  The token that was used to generate the log probability.

#### type - string
The event type, must be `conversation.item.input_audio_transcription.delta`.

### OBJECT conversation.item.input_audio_transcription.delta

```json
{
  "type": "conversation.item.input_audio_transcription.delta",
  "event_id": "event_CCXGRxsAimPAs8kS2Wc7Z",
  "item_id": "item_CCXGQ4e1ht4cOraEYcuR2",
  "content_index": 0,
  "delta": "Hey",
  "obfuscation": "aLxx0jTEciOGe"
}
```

## conversation.item.input_audio_transcription.segment

Returned when an input audio transcription segment is identified for an item.

### Parameters

#### content_index - integer
The index of the input audio content part within the item.

#### end - number
End time of the segment in seconds.

#### event_id - string
The unique ID of the server event.

#### id - string
The segment identifier.

#### item_id - string
The ID of the item containing the input audio content.

#### speaker - string
The detected speaker label for this segment.

#### start - number
Start time of the segment in seconds.

#### text - string
The text for this segment.

#### type - string
The event type, must be `conversation.item.input_audio_transcription.segment`.

### OBJECT conversation.item.input_audio_transcription.segment

```json
{
    "event_id": "event_6501",
    "type": "conversation.item.input_audio_transcription.segment",
    "item_id": "msg_011",
    "content_index": 0,
    "text": "hello",
    "id": "seg_0001",
    "speaker": "spk_1",
    "start": 0.0,
    "end": 0.4
}
```

## conversation.item.input_audio_transcription.failed

Returned when input audio transcription is configured, and a transcription request for a user message failed. These events are separate from other `error` events so that the client can identify the related Item.

### Parameters

#### content_index - integer
The index of the content part containing the audio.

#### error - object
Details of the transcription error.

- **code - string**
  Error code, if any.

- **message - string**
  A human-readable error message.

- **param - string**
  Parameter related to the error, if any.

- **type - string**
  The type of error.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the user message item.

#### type - string
The event type, must be `conversation.item.input_audio_transcription.failed`.

### OBJECT conversation.item.input_audio_transcription.failed

```json
{
    "event_id": "event_2324",
    "type": "conversation.item.input_audio_transcription.failed",
    "item_id": "msg_003",
    "content_index": 0,
    "error": {
        "type": "transcription_error",
        "code": "audio_unintelligible",
        "message": "The audio could not be transcribed.",
        "param": null
    }
}
```

## conversation.item.truncated

Returned when an earlier assistant audio message item is truncated by the client with a `conversation.item.truncate` event. This event is used to synchronize the server's understanding of the audio with the client's playback.

This action will truncate the audio and remove the server-side text transcript to ensure there is no text in the context that hasn't been heard by the user.

### Parameters

#### audio_end_ms - integer
The duration up to which the audio was truncated, in milliseconds.

#### content_index - integer
The index of the content part that was truncated.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the assistant message item that was truncated.

#### type - string
The event type, must be `conversation.item.truncated`.

### OBJECT conversation.item.truncated

```json
{
    "event_id": "event_2526",
    "type": "conversation.item.truncated",
    "item_id": "msg_004",
    "content_index": 0,
    "audio_end_ms": 1500
}
```

## conversation.item.deleted

Returned when an item in the conversation is deleted by the client with a `conversation.item.delete` event. This event is used to synchronize the server's understanding of the conversation history with the client's view.

### Parameters

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the item that was deleted.

#### type - string
The event type, must be `conversation.item.deleted`.

### OBJECT conversation.item.deleted

```json
{
    "event_id": "event_2728",
    "type": "conversation.item.deleted",
    "item_id": "msg_005"
}
```

## input_audio_buffer.committed

Returned when an input audio buffer is committed, either by the client or automatically in server VAD mode. The `item_id` property is the ID of the user message item that will be created, thus a `conversation.item.created` event will also be sent to the client.

### Parameters

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the user message item that will be created.

#### previous_item_id - string
The ID of the preceding item after which the new item will be inserted. Can be `null` if the item has no predecessor.

#### type - string
The event type, must be `input_audio_buffer.committed`.

### OBJECT input_audio_buffer.committed

```json
{
    "event_id": "event_1121",
    "type": "input_audio_buffer.committed",
    "previous_item_id": "msg_001",
    "item_id": "msg_002"
}
```

## input_audio_buffer.dtmf_event_received

**SIP Only:** Returned when an DTMF event is received. A DTMF event is a message that represents a telephone keypad press (0–9, \*, \#, A–D). The `event` property is the keypad that the user press. The `received_at` is the UTC Unix Timestamp that the server received the event.

### Parameters

#### event - string
The telephone keypad that was pressed by the user.

#### received_at - integer
UTC Unix Timestamp when DTMF Event was received by server.

#### type - string
The event type, must be `input_audio_buffer.dtmf_event_received`.

### OBJECT input_audio_buffer.dtmf_event_received

```json
{
    "type":" input_audio_buffer.dtmf_event_received",
    "event": "9",
    "received_at": 1763605109,
}
```

## input_audio_buffer.cleared

Returned when the input audio buffer is cleared by the client with a `input_audio_buffer.clear` event.

### Parameters

#### event_id - string
The unique ID of the server event.

#### type - string
The event type, must be `input_audio_buffer.cleared`.

### OBJECT input_audio_buffer.cleared

```json
{
    "event_id": "event_1314",
    "type": "input_audio_buffer.cleared"
}
```

## input_audio_buffer.speech_started

Sent by the server when in `server_vad` mode to indicate that speech has been detected in the audio buffer. This can happen any time audio is added to the buffer (unless speech is already detected). The client may want to use this event to interrupt audio playback or provide visual feedback to the user.

The client should expect to receive a `input_audio_buffer.speech_stopped` event when speech stops. The `item_id` property is the ID of the user message item that will be created when speech stops and will also be included in the `input_audio_buffer.speech_stopped` event (unless the client manually commits the audio buffer during VAD activation).

### Parameters

#### audio_start_ms - integer
Milliseconds from the start of all audio written to the buffer during the session when speech was first detected. This will correspond to the beginning of audio sent to the model, and thus includes the `prefix_padding_ms` configured in the Session.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the user message item that will be created when speech stops.

#### type - string
The event type, must be `input_audio_buffer.speech_started`.

### OBJECT input_audio_buffer.speech_started

```json
{
    "event_id": "event_1516",
    "type": "input_audio_buffer.speech_started",
    "audio_start_ms": 1000,
    "item_id": "msg_003"
}
```

## input_audio_buffer.speech_stopped

Returned in `server_vad` mode when the server detects the end of speech in the audio buffer. The server will also send an `conversation.item.created` event with the user message item that is created from the audio buffer.

### Parameters

#### audio_end_ms - integer
Milliseconds since the session started when speech stopped. This will correspond to the end of audio sent to the model, and thus includes the `min_silence_duration_ms` configured in the Session.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the user message item that will be created.

#### type - string
The event type, must be `input_audio_buffer.speech_stopped`.

### OBJECT input_audio_buffer.speech_stopped

```json
{
    "event_id": "event_1718",
    "type": "input_audio_buffer.speech_stopped",
    "audio_end_ms": 2000,
    "item_id": "msg_003"
}
```

## input_audio_buffer.timeout_triggered

Returned when the Server VAD timeout is triggered for the input audio buffer. This is configured with `idle_timeout_ms` in the `turn_detection` settings of the session, and it indicates that there hasn't been any speech detected for the configured duration.

The `audio_start_ms` and `audio_end_ms` fields indicate the segment of audio after the last model response up to the triggering time, as an offset from the beginning of audio written to the input audio buffer. This means it demarcates the segment of audio that was silent and the difference between the start and end values will roughly match the configured timeout.

The empty audio will be committed to the conversation as an `input_audio` item (there will be a `input_audio_buffer.committed` event) and a model response will be generated. There may be speech that didn't trigger VAD but is still detected by the model, so the model may respond with something relevant to the conversation or a prompt to continue speaking.

### Parameters

#### audio_end_ms - integer
Millisecond offset of audio written to the input audio buffer at the time the timeout was triggered.

#### audio_start_ms - integer
Millisecond offset of audio written to the input audio buffer that was after the playback time of the last model response.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the item associated with this segment.

#### type - string
The event type, must be `input_audio_buffer.timeout_triggered`.

### OBJECT input_audio_buffer.timeout_triggered

```json
{
    "type":"input_audio_buffer.timeout_triggered",
    "event_id":"event_CEKKrf1KTGvemCPyiJTJ2",
    "audio_start_ms":13216,
    "audio_end_ms":19232,
    "item_id":"item_CEKKrWH0GiwN0ET97NUZc"
}
```

## output_audio_buffer.started

**WebRTC/SIP Only:** Emitted when the server begins streaming audio to the client. This event is emitted after an audio content part has been added (`response.content_part.added`) to the response. [Learn more](https://platform.openai.com/docs/guides/realtime-conversations#client-and-server-events-for-audio-in-webrtc).

### Parameters

#### event_id - string
The unique ID of the server event.

#### response_id - string
The unique ID of the response that produced the audio.

#### type - string
The event type, must be `output_audio_buffer.started`.

### OBJECT output_audio_buffer.started

```json
{
    "event_id": "event_abc123",
    "type": "output_audio_buffer.started",
    "response_id": "resp_abc123"
}
```

## output_audio_buffer.stopped

**WebRTC/SIP Only:** Emitted when the output audio buffer has been completely drained on the server, and no more audio is forthcoming. This event is emitted after the full response data has been sent to the client (`response.done`). [Learn more](https://platform.openai.com/docs/guides/realtime-conversations#client-and-server-events-for-audio-in-webrtc).

### Parameters

#### event_id - string
The unique ID of the server event.

#### response_id - string
The unique ID of the response that produced the audio.

#### type - string
The event type, must be `output_audio_buffer.stopped`.

### OBJECT output_audio_buffer.stopped

```json
{
    "event_id": "event_abc123",
    "type": "output_audio_buffer.stopped",
    "response_id": "resp_abc123"
}
```

## output_audio_buffer.cleared

**WebRTC/SIP Only:** Emitted when the output audio buffer is cleared. This happens either in VAD mode when the user has interrupted (`input_audio_buffer.speech_started`), or when the client has emitted the `output_audio_buffer.clear` event to manually cut off the current audio response. [Learn more](https://platform.openai.com/docs/guides/realtime-conversations#client-and-server-events-for-audio-in-webrtc).

### Parameters

#### event_id - string
The unique ID of the server event.

#### response_id - string
The unique ID of the response that produced the audio.

#### type - string
The event type, must be `output_audio_buffer.cleared`.

### OBJECT output_audio_buffer.cleared

```json
{
    "event_id": "event_abc123",
    "type": "output_audio_buffer.cleared",
    "response_id": "resp_abc123"
}
```

## response.created

Returned when a new Response is created. The first event of response creation, where the response is in an initial state of `in_progress`.

### Parameters

#### event_id - string
The unique ID of the server event.

#### response - object
The response resource.

- **audio - object**
  Configuration for audio output.

  - **output - object**
    The format of the output audio.

    - **format - object**
      The format of the output audio.

      - **PCM audio format - object**
        The PCM audio format. Only a 24kHz sample rate is supported.

        - **rate - integer**
          The sample rate of the audio. Always `24000`.

        - **type - string**
          The audio format. Always `audio/pcm`.

      - **PCMU audio format - object**
        The G.711 μ-law format.

        - **type - string**
          The audio format. Always `audio/pcmu`.

      - **PCMA audio format - object**
        The G.711 A-law format.

        - **type - string**
          The audio format. Always `audio/pcma`.

    - **voice - string**
      The voice the model uses to respond. Voice cannot be changed during the session once the model has responded with audio at least once. Current voice options are `alloy`, `ash`, `ballad`, `coral`, `echo`, `sage`, `shimmer`, `verse`, `marin`, and `cedar`. We recommend `marin` and `cedar` for best quality.

- **conversation_id - string**
  Which conversation the response is added to, determined by the `conversation` field in the `response.create` event. If `auto`, the response will be added to the default conversation and the value of `conversation_id` will be an id like `conv_1234`. If `none`, the response will not be added to any conversation and the value of `conversation_id` will be `null`. If responses are being triggered automatically by VAD the response will be added to the default conversation

- **id - string**
  The unique ID of the response, will look like `resp_1234`.

- **max_output_tokens - integer or "inf"**
  Maximum number of output tokens for a single assistant response, inclusive of tool calls, that was used in this response.

- **metadata - map**
  Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard.

  Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters.

- **object - string**
  The object type, must be `realtime.response`.

- **output - array**
  The list of output items generated by the response.

  - **Realtime system message item - object**
    A system message in a Realtime conversation can be used to provide additional context or instructions to the model. This is similar but distinct from the instruction prompt provided at the start of a conversation, as system messages can be added at any point in the conversation. For major changes to the conversation's behavior, use instructions, but for smaller updates (e.g. "the user is now asking about a different topic"), use system messages.

    - **content - array**
      The content of the message.

      - **text - string**
        The text content.

      - **type - string**
        The content type. Always `input_text` for system messages.

    - **role - string**
      The role of the message sender. Always `system`.

    - **type - string**
      The type of the item. Always `message`.

    - **id - string**
      The unique ID of the item. This may be provided by the client or generated by the server.

    - **object - string**
      Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

    - **status - string**
      The status of the item. Has no effect on the conversation.

  - **Realtime user message item - object**
    A user message item in a Realtime conversation.

    - **content - array**
      The content of the message.

      - **audio - string**
        Base64-encoded audio bytes (for `input_audio`), these will be parsed as the format specified in the session input audio type configuration. This defaults to PCM 16-bit 24kHz mono if not specified.

      - **detail - string**
        The detail level of the image (for `input_image`). `auto` will default to `high`.

      - **image_url - string**
        Base64-encoded image bytes (for `input_image`) as a data URI. For example `data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...`. Supported formats are PNG and JPEG.

      - **text - string**
        The text content (for `input_text`).

      - **transcript - string**
        Transcript of the audio (for `input_audio`). This is not sent to the model, but will be attached to the message item for reference.

      - **type - string**
        The content type (`input_text`, `input_audio`, or `input_image`).

    - **role - string**
      The role of the message sender. Always `user`.

    - **type - string**
      The type of the item. Always `message`.

    - **id - string**
      The unique ID of the item. This may be provided by the client or generated by the server.

    - **object - string**
      Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

    - **status - string**
      The status of the item. Has no effect on the conversation.

  - **Realtime assistant message item - object**
    An assistant message item in a Realtime conversation.

    - **content - array**
      The content of the message.

      - **audio - string**
        Base64-encoded audio bytes, these will be parsed as the format specified in the session output audio type configuration. This defaults to PCM 16-bit 24kHz mono if not specified.

      - **text - string**
        The text content.

      - **transcript - string**
        The transcript of the audio content, this will always be present if the output type is `audio`.

      - **type - string**
        The content type, `output_text` or `output_audio` depending on the session `output_modalities` configuration.

    - **role - string**
      The role of the message sender. Always `assistant`.

    - **type - string**
      The type of the item. Always `message`.

    - **id - string**
      The unique ID of the item. This may be provided by the client or generated by the server.

    - **object - string**
      Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

    - **status - string**
      The status of the item. Has no effect on the conversation.

  - **Realtime function call item - object**
    A function call item in a Realtime conversation.

    - **arguments - string**
      The arguments of the function call. This is a JSON-encoded string representing the arguments passed to the function, for example `{"arg1": "value1", "arg2": 42}`.

    - **name - string**
      The name of the function being called.

    - **type - string**
      The type of the item. Always `function_call`.

    - **call_id - string**
      The ID of the function call.

    - **id - string**
      The unique ID of the item. This may be provided by the client or generated by the server.

    - **object - string**
      Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

    - **status - string**
      The status of the item. Has no effect on the conversation.

  - **Realtime function call output item - object**
    A function call output item in a Realtime conversation.

    - **call_id - string**
      The ID of the function call this output is for.

    - **output - string**
      The output of the function call, this is free text and can contain any information or simply be empty.

    - **type - string**
      The type of the item. Always `function_call_output`.

    - **id - string**
      The unique ID of the item. This may be provided by the client or generated by the server.

    - **object - string**
      Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

    - **status - string**
      The status of the item. Has no effect on the conversation.

  - **Realtime MCP approval response - object**
    A Realtime item responding to an MCP approval request.

    - **approval_request_id - string**
      The ID of the approval request being answered.

    - **approve - boolean**
      Whether the request was approved.

    - **id - string**
      The unique ID of the approval response.

    - **type - string**
      The type of the item. Always `mcp_approval_response`.

    - **reason - string**
      Optional reason for the decision.

  - **Realtime MCP list tools - object**
    A Realtime item listing tools available on an MCP server.

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

    - **id - string**
      The unique ID of the list.

  - **Realtime MCP tool call - object**
    A Realtime item representing an invocation of a tool on an MCP server.

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
      The ID of an associated approval request, if any.

    - **error - object**
      The error from the tool call, if any.

      - **Realtime MCP protocol error - object**

        - **code - integer**

        - **message - string**

        - **type - string**

      - **Realtime MCP tool execution error - object**

        - **message - string**

        - **type - string**

      - **Realtime MCP HTTP error - object**

        - **code - integer**

        - **message - string**

        - **type - string**

    - **output - string**
      The output from the tool call.

  - **Realtime MCP approval request - object**
    A Realtime item requesting human approval of a tool invocation.

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

- **output_modalities - array**
  The set of modalities the model used to respond, currently the only possible values are `[\"audio\"]`, `[\"text\"]`. Audio output always include a text transcript. Setting the output to mode `text` will disable audio output from the model.

- **status - string**
  The final status of the response (`completed`, `cancelled`, `failed`, or `incomplete`, `in_progress`).

- **status_details - object**
  Additional details about the status.

  - **error - object**
    A description of the error that caused the response to fail, populated when the `status` is `failed`.

    - **code - string**
      Error code, if any.

    - **type - string**
      The type of error.

  - **reason - string**
    The reason the Response did not complete. For a `cancelled` Response, one of `turn_detected` (the server VAD detected a new start of speech) or `client_cancelled` (the client sent a cancel event). For an `incomplete` Response, one of `max_output_tokens` or `content_filter` (the server-side safety filter activated and cut off the response).

  - **type - string**
    The type of error that caused the response to fail, corresponding with the `status` field (`completed`, `cancelled`, `incomplete`, `failed`).

- **usage - object**
  Usage statistics for the Response, this will correspond to billing. A Realtime API session will maintain a conversation context and append new Items to the Conversation, thus output from previous turns (text and audio tokens) will become the input for later turns.

  - **input_token_details - object**
    Details about the input tokens used in the Response. Cached tokens are tokens from previous turns in the conversation that are included as context for the current response. Cached tokens here are counted as a subset of input tokens, meaning input tokens will include cached and uncached tokens.

    - **audio_tokens - integer**
      The number of audio tokens used as input for the Response.

    - **cached_tokens - integer**
      The number of cached tokens used as input for the Response.

    - **cached_tokens_details - object**
      Details about the cached tokens used as input for the Response.

      - **audio_tokens - integer**
        The number of cached audio tokens used as input for the Response.

      - **image_tokens - integer**
        The number of cached image tokens used as input for the Response.

      - **text_tokens - integer**
        The number of cached text tokens used as input for the Response.

    - **image_tokens - integer**
      The number of image tokens used as input for the Response.

    - **text_tokens - integer**
      The number of text tokens used as input for the Response.

  - **input_tokens - integer**
    The number of input tokens used in the Response, including text and audio tokens.

  - **output_token_details - object**
    Details about the output tokens used in the Response.

    - **audio_tokens - integer**
      The number of audio tokens used in the Response.

    - **text_tokens - integer**
      The number of text tokens used in the Response.

  - **output_tokens - integer**
    The number of output tokens sent in the Response, including text and audio tokens.

  - **total_tokens - integer**
    The total number of tokens in the Response including input and output text and audio tokens.

#### type - string
The event type, must be `response.created`.

### OBJECT response.created

```json
{
  "type": "response.created",
  "event_id": "event_C9G8pqbTEddBSIxbBN6Os",
  "response": {
    "object": "realtime.response",
    "id": "resp_C9G8p7IH2WxLbkgPNouYL",
    "status": "in_progress",
    "status_details": null,
    "output": [],
    "conversation_id": "conv_C9G8mmBkLhQJwCon3hoJN",
    "output_modalities": [
      "audio"
    ],
    "max_output_tokens": "inf",
    "audio": {
      "output": {
        "format": {
          "type": "audio/pcm",
          "rate": 24000
        },
        "voice": "marin"
      }
    },
    "usage": null,
    "metadata": null
  },
}
```

## response.done

Returned when a Response is done streaming. Always emitted, no matter the final state. The Response object included in the `response.done` event will include all output Items in the Response but will omit the raw audio data.

Clients should check the `status` field of the Response to determine if it was successful (`completed`) or if there was another outcome: `cancelled`, `failed`, or `incomplete`.

A response will contain all output items that were generated during the response, excluding any audio content.

### Parameters

#### event_id - string
The unique ID of the server event.

#### response - object
The response resource.

- **audio - object**
  Configuration for audio output.

  - **output - object**
    The format of the output audio.

    - **format - object**
      The format of the output audio.

      - **PCM audio format - object**
        The PCM audio format. Only a 24kHz sample rate is supported.

        - **rate - integer**
          The sample rate of the audio. Always `24000`.

        - **type - string**
          The audio format. Always `audio/pcm`.

      - **PCMU audio format - object**
        The G.711 μ-law format.

        - **type - string**
          The audio format. Always `audio/pcmu`.

      - **PCMA audio format - object**
        The G.711 A-law format.

        - **type - string**
          The audio format. Always `audio/pcma`.

    - **voice - string**
      The voice the model uses to respond. Voice cannot be changed during the session once the model has responded with audio at least once. Current voice options are `alloy`, `ash`, `ballad`, `coral`, `echo`, `sage`, `shimmer`, `verse`, `marin`, and `cedar`. We recommend `marin` and `cedar` for best quality.

- **conversation_id - string**
  Which conversation the response is added to, determined by the `conversation` field in the `response.create` event. If `auto`, the response will be added to the default conversation and the value of `conversation_id` will be an id like `conv_1234`. If `none`, the response will not be added to any conversation and the value of `conversation_id` will be `null`. If responses are being triggered automatically by VAD the response will be added to the default conversation

- **id - string**
  The unique ID of the response, will look like `resp_1234`.

- **max_output_tokens - integer or "inf"**
  Maximum number of output tokens for a single assistant response, inclusive of tool calls, that was used in this response.

- **metadata - map**
  Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard.

  Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters.

- **object - string**
  The object type, must be `realtime.response`.

- **output - array**
  The list of output items generated by the response.

  - **Realtime system message item - object**
    A system message in a Realtime conversation can be used to provide additional context or instructions to the model. This is similar but distinct from the instruction prompt provided at the start of a conversation, as system messages can be added at any point in the conversation. For major changes to the conversation's behavior, use instructions, but for smaller updates (e.g. "the user is now asking about a different topic"), use system messages.

    - **content - array**
      The content of the message.

      - **text - string**
        The text content.

      - **type - string**
        The content type. Always `input_text` for system messages.

    - **role - string**
      The role of the message sender. Always `system`.

    - **type - string**
      The type of the item. Always `message`.

    - **id - string**
      The unique ID of the item. This may be provided by the client or generated by the server.

    - **object - string**
      Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

    - **status - string**
      The status of the item. Has no effect on the conversation.

  - **Realtime user message item - object**
    A user message item in a Realtime conversation.

    - **content - array**
      The content of the message.

      - **audio - string**
        Base64-encoded audio bytes (for `input_audio`), these will be parsed as the format specified in the session input audio type configuration. This defaults to PCM 16-bit 24kHz mono if not specified.

      - **detail - string**
        The detail level of the image (for `input_image`). `auto` will default to `high`.

      - **image_url - string**
        Base64-encoded image bytes (for `input_image`) as a data URI. For example `data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...`. Supported formats are PNG and JPEG.

      - **text - string**
        The text content (for `input_text`).

      - **transcript - string**
        Transcript of the audio (for `input_audio`). This is not sent to the model, but will be attached to the message item for reference.

      - **type - string**
        The content type (`input_text`, `input_audio`, or `input_image`).

    - **role - string**
      The role of the message sender. Always `user`.

    - **type - string**
      The type of the item. Always `message`.

    - **id - string**
      The unique ID of the item. This may be provided by the client or generated by the server.

    - **object - string**
      Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

    - **status - string**
      The status of the item. Has no effect on the conversation.

  - **Realtime assistant message item - object**
    An assistant message item in a Realtime conversation.

    - **content - array**
      The content of the message.

      - **audio - string**
        Base64-encoded audio bytes, these will be parsed as the format specified in the session output audio type configuration. This defaults to PCM 16-bit 24kHz mono if not specified.

      - **text - string**
        The text content.

      - **transcript - string**
        The transcript of the audio content, this will always be present if the output type is `audio`.

      - **type - string**
        The content type, `output_text` or `output_audio` depending on the session `output_modalities` configuration.

    - **role - string**
      The role of the message sender. Always `assistant`.

    - **type - string**
      The type of the item. Always `message`.

    - **id - string**
      The unique ID of the item. This may be provided by the client or generated by the server.

    - **object - string**
      Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

    - **status - string**
      The status of the item. Has no effect on the conversation.

  - **Realtime function call item - object**
    A function call item in a Realtime conversation.

    - **arguments - string**
      The arguments of the function call. This is a JSON-encoded string representing the arguments passed to the function, for example `{"arg1": "value1", "arg2": 42}`.

    - **name - string**
      The name of the function being called.

    - **type - string**
      The type of the item. Always `function_call`.

    - **call_id - string**
      The ID of the function call.

    - **id - string**
      The unique ID of the item. This may be provided by the client or generated by the server.

    - **object - string**
      Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

    - **status - string**
      The status of the item. Has no effect on the conversation.

  - **Realtime function call output item - object**
    A function call output item in a Realtime conversation.

    - **call_id - string**
      The ID of the function call this output is for.

    - **output - string**
      The output of the function call, this is free text and can contain any information or simply be empty.

    - **type - string**
      The type of the item. Always `function_call_output`.

    - **id - string**
      The unique ID of the item. This may be provided by the client or generated by the server.

    - **object - string**
      Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

    - **status - string**
      The status of the item. Has no effect on the conversation.

  - **Realtime MCP approval response - object**
    A Realtime item responding to an MCP approval request.

    - **approval_request_id - string**
      The ID of the approval request being answered.

    - **approve - boolean**
      Whether the request was approved.

    - **id - string**
      The unique ID of the approval response.

    - **type - string**
      The type of the item. Always `mcp_approval_response`.

    - **reason - string**
      Optional reason for the decision.

  - **Realtime MCP list tools - object**
    A Realtime item listing tools available on an MCP server.

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

    - **id - string**
      The unique ID of the list.

  - **Realtime MCP tool call - object**
    A Realtime item representing an invocation of a tool on an MCP server.

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
      The ID of an associated approval request, if any.

    - **error - object**
      The error from the tool call, if any.

      - **Realtime MCP protocol error - object**

        - **code - integer**

        - **message - string**

        - **type - string**

      - **Realtime MCP tool execution error - object**

        - **message - string**

        - **type - string**

      - **Realtime MCP HTTP error - object**

        - **code - integer**

        - **message - string**

        - **type - string**

    - **output - string**
      The output from the tool call.

  - **Realtime MCP approval request - object**
    A Realtime item requesting human approval of a tool invocation.

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

- **output_modalities - array**
  The set of modalities the model used to respond, currently the only possible values are `[\"audio\"]`, `[\"text\"]`. Audio output always include a text transcript. Setting the output to mode `text` will disable audio output from the model.

- **status - string**
  The final status of the response (`completed`, `cancelled`, `failed`, or `incomplete`, `in_progress`).

- **status_details - object**
  Additional details about the status.

  - **error - object**
    A description of the error that caused the response to fail, populated when the `status` is `failed`.

    - **code - string**
      Error code, if any.

    - **type - string**
      The type of error.

  - **reason - string**
    The reason the Response did not complete. For a `cancelled` Response, one of `turn_detected` (the server VAD detected a new start of speech) or `client_cancelled` (the client sent a cancel event). For an `incomplete` Response, one of `max_output_tokens` or `content_filter` (the server-side safety filter activated and cut off the response).

  - **type - string**
    The type of error that caused the response to fail, corresponding with the `status` field (`completed`, `cancelled`, `incomplete`, `failed`).

- **usage - object**
  Usage statistics for the Response, this will correspond to billing. A Realtime API session will maintain a conversation context and append new Items to the Conversation, thus output from previous turns (text and audio tokens) will become the input for later turns.

  - **input_token_details - object**
    Details about the input tokens used in the Response. Cached tokens are tokens from previous turns in the conversation that are included as context for the current response. Cached tokens here are counted as a subset of input tokens, meaning input tokens will include cached and uncached tokens.

    - **audio_tokens - integer**
      The number of audio tokens used as input for the Response.

    - **cached_tokens - integer**
      The number of cached tokens used as input for the Response.

    - **cached_tokens_details - object**
      Details about the cached tokens used as input for the Response.

      - **audio_tokens - integer**
        The number of cached audio tokens used as input for the Response.

      - **image_tokens - integer**
        The number of cached image tokens used as input for the Response.

      - **text_tokens - integer**
        The number of cached text tokens used as input for the Response.

    - **image_tokens - integer**
      The number of image tokens used as input for the Response.

    - **text_tokens - integer**
      The number of text tokens used as input for the Response.

  - **input_tokens - integer**
    The number of input tokens used in the Response, including text and audio tokens.

  - **output_token_details - object**
    Details about the output tokens used in the Response.

    - **audio_tokens - integer**
      The number of audio tokens used in the Response.

    - **text_tokens - integer**
      The number of text tokens used in the Response.

  - **output_tokens - integer**
    The number of output tokens sent in the Response, including text and audio tokens.

  - **total_tokens - integer**
    The total number of tokens in the Response including input and output text and audio tokens.

#### type - string
The event type, must be `response.done`.

### OBJECT response.done

```json
{
  "type": "response.done",
  "event_id": "event_CCXHxcMy86rrKhBLDdqCh",
  "response": {
    "object": "realtime.response",
    "id": "resp_CCXHw0UJld10EzIUXQCNh",
    "status": "completed",
    "status_details": null,
    "output": [
      {
        "id": "item_CCXHwGjjDUfOXbiySlK7i",
        "type": "message",
        "status": "completed",
        "role": "assistant",
        "content": [
          {
            "type": "output_audio",
            "transcript": "Loud and clear! I can hear you perfectly. How can I help you today?"
          }
        ]
      }
    ],
    "conversation_id": "conv_CCXHsurMKcaVxIZvaCI5m",
    "output_modalities": [
      "audio"
    ],
    "max_output_tokens": "inf",
    "audio": {
      "output": {
        "format": {
          "type": "audio/pcm",
          "rate": 24000
        },
        "voice": "alloy"
      }
    },
    "usage": {
      "total_tokens": 253,
      "input_tokens": 132,
      "output_tokens": 121,
      "input_token_details": {
        "text_tokens": 119,
        "audio_tokens": 13,
        "image_tokens": 0,
        "cached_tokens": 64,
        "cached_tokens_details": {
          "text_tokens": 64,
          "audio_tokens": 0,
          "image_tokens": 0
        }
      },
      "output_token_details": {
        "text_tokens": 30,
        "audio_tokens": 91
      }
    },
    "metadata": null
  }
}
```

## response.output_item.added

Returned when a new Item is created during Response generation.

### Parameters

#### event_id - string
The unique ID of the server event.

#### item - object
A single item within a Realtime conversation.

- **Realtime system message item - object**
  A system message in a Realtime conversation can be used to provide additional context or instructions to the model. This is similar but distinct from the instruction prompt provided at the start of a conversation, as system messages can be added at any point in the conversation. For major changes to the conversation's behavior, use instructions, but for smaller updates (e.g. "the user is now asking about a different topic"), use system messages.

  - **content - array**
    The content of the message.

    - **text - string**
      The text content.

    - **type - string**
      The content type. Always `input_text` for system messages.

  - **role - string**
    The role of the message sender. Always `system`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime user message item - object**
  A user message item in a Realtime conversation.

  - **content - array**
    The content of the message.

    - **audio - string**
      Base64-encoded audio bytes (for `input_audio`), these will be parsed as the format specified in the session input audio type configuration. This defaults to PCM 16-bit 24kHz mono if not specified.

    - **detail - string**
      The detail level of the image (for `input_image`). `auto` will default to `high`.

    - **image_url - string**
      Base64-encoded image bytes (for `input_image`) as a data URI. For example `data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...`. Supported formats are PNG and JPEG.

    - **text - string**
      The text content (for `input_text`).

    - **transcript - string**
      Transcript of the audio (for `input_audio`). This is not sent to the model, but will be attached to the message item for reference.

    - **type - string**
      The content type (`input_text`, `input_audio`, or `input_image`).

  - **role - string**
    The role of the message sender. Always `user`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime assistant message item - object**
  An assistant message item in a Realtime conversation.

  - **content - array**
    The content of the message.

    - **audio - string**
      Base64-encoded audio bytes, these will be parsed as the format specified in the session output audio type configuration. This defaults to PCM 16-bit 24kHz mono if not specified.

    - **text - string**
      The text content.

    - **transcript - string**
      The transcript of the audio content, this will always be present if the output type is `audio`.

    - **type - string**
      The content type, `output_text` or `output_audio` depending on the session `output_modalities` configuration.

  - **role - string**
    The role of the message sender. Always `assistant`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime function call item - object**
  A function call item in a Realtime conversation.

  - **arguments - string**
    The arguments of the function call. This is a JSON-encoded string representing the arguments passed to the function, for example `{"arg1": "value1", "arg2": 42}`.

  - **name - string**
    The name of the function being called.

  - **type - string**
    The type of the item. Always `function_call`.

  - **call_id - string**
    The ID of the function call.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime function call output item - object**
  A function call output item in a Realtime conversation.

  - **call_id - string**
    The ID of the function call this output is for.

  - **output - string**
    The output of the function call, this is free text and can contain any information or simply be empty.

  - **type - string**
    The type of the item. Always `function_call_output`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime MCP approval response - object**
  A Realtime item responding to an MCP approval request.

  - **approval_request_id - string**
    The ID of the approval request being answered.

  - **approve - boolean**
    Whether the request was approved.

  - **id - string**
    The unique ID of the approval response.

  - **type - string**
    The type of the item. Always `mcp_approval_response`.

  - **reason - string**
    Optional reason for the decision.

- **Realtime MCP list tools - object**
  A Realtime item listing tools available on an MCP server.

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

  - **id - string**
    The unique ID of the list.

- **Realtime MCP tool call - object**
  A Realtime item representing an invocation of a tool on an MCP server.

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
    The ID of an associated approval request, if any.

  - **error - object**
    The error from the tool call, if any.

    - **Realtime MCP protocol error - object**

      - **code - integer**

      - **message - string**

      - **type - string**

    - **Realtime MCP tool execution error - object**

      - **message - string**

      - **type - string**

    - **Realtime MCP HTTP error - object**

      - **code - integer**

      - **message - string**

      - **type - string**

  - **output - string**
    The output from the tool call.

- **Realtime MCP approval request - object**
  A Realtime item requesting human approval of a tool invocation.

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

#### output_index - integer
The index of the output item in the Response.

#### response_id - string
The ID of the Response to which the item belongs.

#### type - string
The event type, must be `response.output_item.added`.

### OBJECT response.output_item.added

```json
{
    "event_id": "event_3334",
    "type": "response.output_item.added",
    "response_id": "resp_001",
    "output_index": 0,
    "item": {
        "id": "msg_007",
        "object": "realtime.item",
        "type": "message",
        "status": "in_progress",
        "role": "assistant",
        "content": []
    }
}
```

## response.output_item.done

Returned when an Item is done streaming. Also emitted when a Response is interrupted, incomplete, or cancelled.

### Parameters

#### event_id - string
The unique ID of the server event.

#### item - object
A single item within a Realtime conversation.

- **Realtime system message item - object**
  A system message in a Realtime conversation can be used to provide additional context or instructions to the model. This is similar but distinct from the instruction prompt provided at the start of a conversation, as system messages can be added at any point in the conversation. For major changes to the conversation's behavior, use instructions, but for smaller updates (e.g. "the user is now asking about a different topic"), use system messages.

  - **content - array**
    The content of the message.

    - **text - string**
      The text content.

    - **type - string**
      The content type. Always `input_text` for system messages.

  - **role - string**
    The role of the message sender. Always `system`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime user message item - object**
  A user message item in a Realtime conversation.

  - **content - array**
    The content of the message.

    - **audio - string**
      Base64-encoded audio bytes (for `input_audio`), these will be parsed as the format specified in the session input audio type configuration. This defaults to PCM 16-bit 24kHz mono if not specified.

    - **detail - string**
      The detail level of the image (for `input_image`). `auto` will default to `high`.

    - **image_url - string**
      Base64-encoded image bytes (for `input_image`) as a data URI. For example `data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...`. Supported formats are PNG and JPEG.

    - **text - string**
      The text content (for `input_text`).

    - **transcript - string**
      Transcript of the audio (for `input_audio`). This is not sent to the model, but will be attached to the message item for reference.

    - **type - string**
      The content type (`input_text`, `input_audio`, or `input_image`).

  - **role - string**
    The role of the message sender. Always `user`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime assistant message item - object**
  An assistant message item in a Realtime conversation.

  - **content - array**
    The content of the message.

    - **audio - string**
      Base64-encoded audio bytes, these will be parsed as the format specified in the session output audio type configuration. This defaults to PCM 16-bit 24kHz mono if not specified.

    - **text - string**
      The text content.

    - **transcript - string**
      The transcript of the audio content, this will always be present if the output type is `audio`.

    - **type - string**
      The content type, `output_text` or `output_audio` depending on the session `output_modalities` configuration.

  - **role - string**
    The role of the message sender. Always `assistant`.

  - **type - string**
    The type of the item. Always `message`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime function call item - object**
  A function call item in a Realtime conversation.

  - **arguments - string**
    The arguments of the function call. This is a JSON-encoded string representing the arguments passed to the function, for example `{"arg1": "value1", "arg2": 42}`.

  - **name - string**
    The name of the function being called.

  - **type - string**
    The type of the item. Always `function_call`.

  - **call_id - string**
    The ID of the function call.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime function call output item - object**
  A function call output item in a Realtime conversation.

  - **call_id - string**
    The ID of the function call this output is for.

  - **output - string**
    The output of the function call, this is free text and can contain any information or simply be empty.

  - **type - string**
    The type of the item. Always `function_call_output`.

  - **id - string**
    The unique ID of the item. This may be provided by the client or generated by the server.

  - **object - string**
    Identifier for the API object being returned - always `realtime.item`. Optional when creating a new item.

  - **status - string**
    The status of the item. Has no effect on the conversation.

- **Realtime MCP approval response - object**
  A Realtime item responding to an MCP approval request.

  - **approval_request_id - string**
    The ID of the approval request being answered.

  - **approve - boolean**
    Whether the request was approved.

  - **id - string**
    The unique ID of the approval response.

  - **type - string**
    The type of the item. Always `mcp_approval_response`.

  - **reason - string**
    Optional reason for the decision.

- **Realtime MCP list tools - object**
  A Realtime item listing tools available on an MCP server.

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

  - **id - string**
    The unique ID of the list.

- **Realtime MCP tool call - object**
  A Realtime item representing an invocation of a tool on an MCP server.

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
    The ID of an associated approval request, if any.

  - **error - object**
    The error from the tool call, if any.

    - **Realtime MCP protocol error - object**

      - **code - integer**

      - **message - string**

      - **type - string**

    - **Realtime MCP tool execution error - object**

      - **message - string**

      - **type - string**

    - **Realtime MCP HTTP error - object**

      - **code - integer**

      - **message - string**

      - **type - string**

  - **output - string**
    The output from the tool call.

- **Realtime MCP approval request - object**
  A Realtime item requesting human approval of a tool invocation.

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

#### output_index - integer
The index of the output item in the Response.

#### response_id - string
The ID of the Response to which the item belongs.

#### type - string
The event type, must be `response.output_item.done`.

### OBJECT response.output_item.done

```json
{
    "event_id": "event_3536",
    "type": "response.output_item.done",
    "response_id": "resp_001",
    "output_index": 0,
    "item": {
        "id": "msg_007",
        "object": "realtime.item",
        "type": "message",
        "status": "completed",
        "role": "assistant",
        "content": [
            {
                "type": "text",
                "text": "Sure, I can help with that."
            }
        ]
    }
}
```

## response.content_part.added

Returned when a new content part is added to an assistant message item during response generation.

### Parameters

#### content_index - integer
The index of the content part in the item's content array.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the item to which the content part was added.

#### output_index - integer
The index of the output item in the response.

#### part - object
The content part that was added.

- **audio - string**
  Base64-encoded audio data (if type is "audio").

- **text - string**
  The text content (if type is "text").

- **transcript - string**
  The transcript of the audio (if type is "audio").

- **type - string**
  The content type ("text", "audio").

#### response_id - string
The ID of the response.

#### type - string
The event type, must be `response.content_part.added`.

### OBJECT response.content_part.added

```json
{
    "event_id": "event_3738",
    "type": "response.content_part.added",
    "response_id": "resp_001",
    "item_id": "msg_007",
    "output_index": 0,
    "content_index": 0,
    "part": {
        "type": "text",
        "text": ""
    }
}
```

## response.content_part.done

Returned when a content part is done streaming in an assistant message item. Also emitted when a Response is interrupted, incomplete, or cancelled.

### Parameters

#### content_index - integer
The index of the content part in the item's content array.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the item.

#### output_index - integer
The index of the output item in the response.

#### part - object
The content part that is done.

- **audio - string**
  Base64-encoded audio data (if type is "audio").

- **text - string**
  The text content (if type is "text").

- **transcript - string**
  The transcript of the audio (if type is "audio").

- **type - string**
  The content type ("text", "audio").

#### response_id - string
The ID of the response.

#### type - string
The event type, must be `response.content_part.done`.

### OBJECT response.content_part.done

```json
{
    "event_id": "event_3940",
    "type": "response.content_part.done",
    "response_id": "resp_001",
    "item_id": "msg_007",
    "output_index": 0,
    "content_index": 0,
    "part": {
        "type": "text",
        "text": "Sure, I can help with that."
    }
}
```

## response.output_text.delta

Returned when the text value of an "output_text" content part is updated.

### Parameters

#### content_index - integer
The index of the content part in the item's content array.

#### delta - string
The text delta.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the item.

#### output_index - integer
The index of the output item in the response.

#### response_id - string
The ID of the response.

#### type - string
The event type, must be `response.output_text.delta`.

### OBJECT response.output_text.delta

```json
{
    "event_id": "event_4142",
    "type": "response.output_text.delta",
    "response_id": "resp_001",
    "item_id": "msg_007",
    "output_index": 0,
    "content_index": 0,
    "delta": "Sure, I can h"
}
```

## response.output_text.done

Returned when the text value of an "output_text" content part is done streaming. Also emitted when a Response is interrupted, incomplete, or cancelled.

### Parameters

#### content_index - integer
The index of the content part in the item's content array.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the item.

#### output_index - integer
The index of the output item in the response.

#### response_id - string
The ID of the response.

#### text - string
The final text content.

#### type - string
The event type, must be `response.output_text.done`.

### OBJECT response.output_text.done

```json
{
    "event_id": "event_4344",
    "type": "response.output_text.done",
    "response_id": "resp_001",
    "item_id": "msg_007",
    "output_index": 0,
    "content_index": 0,
    "text": "Sure, I can help with that."
}
```

## response.output_audio_transcript.delta

Returned when the model-generated transcription of audio output is updated.

### Parameters

#### content_index - integer
The index of the content part in the item's content array.

#### delta - string
The transcript delta.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the item.

#### output_index - integer
The index of the output item in the response.

#### response_id - string
The ID of the response.

#### type - string
The event type, must be `response.output_audio_transcript.delta`.

### OBJECT response.output_audio_transcript.delta

```json
{
    "event_id": "event_4546",
    "type": "response.output_audio_transcript.delta",
    "response_id": "resp_001",
    "item_id": "msg_008",
    "output_index": 0,
    "content_index": 0,
    "delta": "Hello, how can I a"
}
```

## response.output_audio_transcript.done

Returned when the model-generated transcription of audio output is done streaming. Also emitted when a Response is interrupted, incomplete, or cancelled.

### Parameters

#### content_index - integer
The index of the content part in the item's content array.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the item.

#### output_index - integer
The index of the output item in the response.

#### response_id - string
The ID of the response.

#### transcript - string
The final transcript of the audio.

#### type - string
The event type, must be `response.output_audio_transcript.done`.

### OBJECT response.output_audio_transcript.done

```json
{
    "event_id": "event_4748",
    "type": "response.output_audio_transcript.done",
    "response_id": "resp_001",
    "item_id": "msg_008",
    "output_index": 0,
    "content_index": 0,
    "transcript": "Hello, how can I assist you today?"
}
```

## response.output_audio.delta

Returned when the model-generated audio is updated.

### Parameters

#### content_index - integer
The index of the content part in the item's content array.

#### delta - string
Base64-encoded audio data delta.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the item.

#### output_index - integer
The index of the output item in the response.

#### response_id - string
The ID of the response.

#### type - string
The event type, must be `response.output_audio.delta`.

### OBJECT response.output_audio.delta

```json
{
    "event_id": "event_4950",
    "type": "response.output_audio.delta",
    "response_id": "resp_001",
    "item_id": "msg_008",
    "output_index": 0,
    "content_index": 0,
    "delta": "Base64EncodedAudioDelta"
}
```

## response.output_audio.done

Returned when the model-generated audio is done. Also emitted when a Response is interrupted, incomplete, or cancelled.

### Parameters

#### content_index - integer
The index of the content part in the item's content array.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the item.

#### output_index - integer
The index of the output item in the response.

#### response_id - string
The ID of the response.

#### type - string
The event type, must be `response.output_audio.done`.

### OBJECT response.output_audio.done

```json
{
    "event_id": "event_5152",
    "type": "response.output_audio.done",
    "response_id": "resp_001",
    "item_id": "msg_008",
    "output_index": 0,
    "content_index": 0
}
```

## response.function_call_arguments.delta

Returned when the model-generated function call arguments are updated.

### Parameters

#### call_id - string
The ID of the function call.

#### delta - string
The arguments delta as a JSON string.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the function call item.

#### output_index - integer
The index of the output item in the response.

#### response_id - string
The ID of the response.

#### type - string
The event type, must be `response.function_call_arguments.delta`.

### OBJECT response.function_call_arguments.delta

```json
{
    "event_id": "event_5354",
    "type": "response.function_call_arguments.delta",
    "response_id": "resp_002",
    "item_id": "fc_001",
    "output_index": 0,
    "call_id": "call_001",
    "delta": "{\"location\": \"San\""
}
```

## response.function_call_arguments.done

Returned when the model-generated function call arguments are done streaming. Also emitted when a Response is interrupted, incomplete, or cancelled.

### Parameters

#### arguments - string
The final arguments as a JSON string.

#### call_id - string
The ID of the function call.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the function call item.

#### output_index - integer
The index of the output item in the response.

#### response_id - string
The ID of the response.

#### type - string
The event type, must be `response.function_call_arguments.done`.

### OBJECT response.function_call_arguments.done

```json
{
    "event_id": "event_5556",
    "type": "response.function_call_arguments.done",
    "response_id": "resp_002",
    "item_id": "fc_001",
    "output_index": 0,
    "call_id": "call_001",
    "arguments": "{\"location\": \"San Francisco\"}"
}
```

## response.mcp_call_arguments.delta

Returned when MCP tool call arguments are updated during response generation.

### Parameters

#### delta - string
The JSON-encoded arguments delta.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the MCP tool call item.

#### obfuscation - string
If present, indicates the delta text was obfuscated.

#### output_index - integer
The index of the output item in the response.

#### response_id - string
The ID of the response.

#### type - string
The event type, must be `response.mcp_call_arguments.delta`.

### OBJECT response.mcp_call_arguments.delta

```json
{
    "event_id": "event_6201",
    "type": "response.mcp_call_arguments.delta",
    "response_id": "resp_001",
    "item_id": "mcp_call_001",
    "output_index": 0,
    "delta": "{\"partial\":true}"
}
```

## response.mcp_call_arguments.done

Returned when MCP tool call arguments are finalized during response generation.

### Parameters

#### arguments - string
The final JSON-encoded arguments string.

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the MCP tool call item.

#### output_index - integer
The index of the output item in the response.

#### response_id - string
The ID of the response.

#### type - string
The event type, must be `response.mcp_call_arguments.done`.

### OBJECT response.mcp_call_arguments.done

```json
{
    "event_id": "event_6202",
    "type": "response.mcp_call_arguments.done",
    "response_id": "resp_001",
    "item_id": "mcp_call_001",
    "output_index": 0,
    "arguments": "{\"q\":\"docs\"}"
}
```

## response.mcp_call.in_progress

Returned when an MCP tool call has started and is in progress.

### Parameters

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the MCP tool call item.

#### output_index - integer
The index of the output item in the response.

#### type - string
The event type, must be `response.mcp_call.in_progress`.

### OBJECT response.mcp_call.in_progress

```json
{
    "event_id": "event_6301",
    "type": "response.mcp_call.in_progress",
    "output_index": 0,
    "item_id": "mcp_call_001"
}
```

## response.mcp_call.completed

Returned when an MCP tool call has completed successfully.

### Parameters

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the MCP tool call item.

#### output_index - integer
The index of the output item in the response.

#### type - string
The event type, must be `response.mcp_call.completed`.

### OBJECT response.mcp_call.completed

```json
{
    "event_id": "event_6302",
    "type": "response.mcp_call.completed",
    "output_index": 0,
    "item_id": "mcp_call_001"
}
```

## response.mcp_call.failed

Returned when an MCP tool call has failed.

### Parameters

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the MCP tool call item.

#### output_index - integer
The index of the output item in the response.

#### type - string
The event type, must be `response.mcp_call.failed`.

### OBJECT response.mcp_call.failed

```json
{
    "event_id": "event_6303",
    "type": "response.mcp_call.failed",
    "output_index": 0,
    "item_id": "mcp_call_001"
}
```

## mcp_list_tools.in_progress

Returned when listing MCP tools is in progress for an item.

### Parameters

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the MCP list tools item.

#### type - string
The event type, must be `mcp_list_tools.in_progress`.

### OBJECT mcp_list_tools.in_progress

```json
{
    "event_id": "event_6101",
    "type": "mcp_list_tools.in_progress",
    "item_id": "mcp_list_tools_001"
}
```

## mcp_list_tools.completed

Returned when listing MCP tools has completed for an item.

### Parameters

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the MCP list tools item.

#### type - string
The event type, must be `mcp_list_tools.completed`.

### OBJECT mcp_list_tools.completed

```json
{
    "event_id": "event_6102",
    "type": "mcp_list_tools.completed",
    "item_id": "mcp_list_tools_001"
}
```

## mcp_list_tools.failed

Returned when listing MCP tools has failed for an item.

### Parameters

#### event_id - string
The unique ID of the server event.

#### item_id - string
The ID of the MCP list tools item.

#### type - string
The event type, must be `mcp_list_tools.failed`.

### OBJECT mcp_list_tools.failed

```json
{
    "event_id": "event_6103",
    "type": "mcp_list_tools.failed",
    "item_id": "mcp_list_tools_001"
}
```

## rate_limits.updated

Emitted at the beginning of a Response to indicate the updated rate limits. When a Response is created some tokens will be "reserved" for the output tokens, the rate limits shown here reflect that reservation, which is then adjusted accordingly once the Response is completed.

### Parameters

#### event_id - string
The unique ID of the server event.

#### rate_limits - array
List of rate limit information.

- **limit - integer**
  The maximum allowed value for the rate limit.

- **name - string**
  The name of the rate limit (`requests`, `tokens`).

- **remaining - integer**
  The remaining value before the limit is reached.

- **reset_seconds - number**
  Seconds until the rate limit resets.

#### type - string
The event type, must be `rate_limits.updated`.

### OBJECT rate_limits.updated

```json
{
    "event_id": "event_5758",
    "type": "rate_limits.updated",
    "rate_limits": [
        {
            "name": "requests",
            "limit": 1000,
            "remaining": 999,
            "reset_seconds": 60
        },
        {
            "name": "tokens",
            "limit": 50000,
            "remaining": 49950,
            "reset_seconds": 60
        }
    ]
}
```