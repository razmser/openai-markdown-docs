# Client events

These are events that the OpenAI Realtime WebSocket server will accept from the client.

## session.update

Send this event to update the session’s configuration. The client may send this event at any time to update any field except for `voice` and `model`. `voice` can be updated only if there have been no other audio outputs yet.

When the server receives a `session.update`, it will respond with a `session.updated` event showing the full, effective configuration. Only the fields that are present in the `session.update` are updated. To clear a field like `instructions`, pass an empty string. To clear a field like `tools`, pass an empty array. To clear a field like `turn_detection`, pass `null`.

### Parameters

#### event_id - string
Optional client-generated ID used to identify this event. This is an arbitrary string that a client may assign. It will be passed back if there is an error with the event, but the corresponding `session.updated` event will not include it.

#### session - object
Update the Realtime session. Choose either a realtime session or a transcription session.

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
The event type, must be `session.update`.

### OBJECT session.update

```json
{
  "type": "session.update",
  "session": {
    "type": "realtime",
    "instructions": "You are a creative assistant that helps with design tasks.",
    "tools": [
      {
        "type": "function",
        "name": "display_color_palette",
        "description": "Call this function when a user asks for a color palette.",
        "parameters": {
          "type": "object",
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
    "tool_choice": "auto"
  }
}
```

## input_audio_buffer.append

Send this event to append audio bytes to the input audio buffer. The audio buffer is temporary storage you can write to and later commit. A "commit" will create a new user message item in the conversation history from the buffer content and clear the buffer. Input audio transcription (if enabled) will be generated when the buffer is committed.

If VAD is enabled the audio buffer is used to detect speech and the server will decide when to commit. When Server VAD is disabled, you must commit the audio buffer manually. Input audio noise reduction operates on writes to the audio buffer.

The client may choose how much audio to place in each event up to a maximum of 15 MiB, for example streaming smaller chunks from the client may allow the VAD to be more responsive. Unlike most other client events, the server will not send a confirmation response to this event.

### Parameters

#### audio - string
Base64-encoded audio bytes. This must be in the format specified by the `input_audio_format` field in the session configuration.

#### event_id - string
Optional client-generated ID used to identify this event.

#### type - string
The event type, must be `input_audio_buffer.append`.

### OBJECT input_audio_buffer.append

```json
{
    "event_id": "event_456",
    "type": "input_audio_buffer.append",
    "audio": "Base64EncodedAudioData"
}
```

## input_audio_buffer.commit

Send this event to commit the user input audio buffer, which will create a new user message item in the conversation. This event will produce an error if the input audio buffer is empty. When in Server VAD mode, the client does not need to send this event, the server will commit the audio buffer automatically.

Committing the input audio buffer will trigger input audio transcription (if enabled in session configuration), but it will not create a response from the model. The server will respond with an `input_audio_buffer.committed` event.

### Parameters

#### event_id - string
Optional client-generated ID used to identify this event.

#### type - string
The event type, must be `input_audio_buffer.commit`.

### OBJECT input_audio_buffer.commit

```json
{
    "event_id": "event_789",
    "type": "input_audio_buffer.commit"
}
```

## input_audio_buffer.clear

Send this event to clear the audio bytes in the buffer. The server will respond with an `input_audio_buffer.cleared` event.

### Parameters

#### event_id - string
Optional client-generated ID used to identify this event.

#### type - string
The event type, must be `input_audio_buffer.clear`.

### OBJECT input_audio_buffer.clear

```json
{
    "event_id": "event_012",
    "type": "input_audio_buffer.clear"
}
```

## conversation.item.create

Add a new Item to the Conversation's context, including messages, function calls, and function call responses. This event can be used both to populate a "history" of the conversation and to add new items mid-stream, but has the current limitation that it cannot populate assistant audio messages.

If successful, the server will respond with a `conversation.item.created` event, otherwise an `error` event will be sent.

### Parameters

#### event_id - string
Optional client-generated ID used to identify this event.

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
The ID of the preceding item after which the new item will be inserted. If not set, the new item will be appended to the end of the conversation. If set to `root`, the new item will be added to the beginning of the conversation. If set to an existing ID, it allows an item to be inserted mid-conversation. If the ID cannot be found, an error will be returned and the item will not be added.

#### type - string
The event type, must be `conversation.item.create`.

### OBJECT conversation.item.create

```json
{
  "type": "conversation.item.create",
  "item": {
    "type": "message",
    "role": "user",
    "content": [
      {
        "type": "input_text",
        "text": "hi"
      }
    ]
  },
  "event_id": "b904fba0-0ec4-40af-8bbb-f908a9b26793",
}
```

## conversation.item.retrieve

Send this event when you want to retrieve the server's representation of a specific item in the conversation history. This is useful, for example, to inspect user audio after noise cancellation and VAD. The server will respond with a `conversation.item.retrieved` event, unless the item does not exist in the conversation history, in which case the server will respond with an error.

### Parameters

#### event_id - string
Optional client-generated ID used to identify this event.

#### item_id - string
The ID of the item to retrieve.

#### type - string
The event type, must be `conversation.item.retrieve`.

### OBJECT conversation.item.retrieve

```json
{
    "event_id": "event_901",
    "type": "conversation.item.retrieve",
    "item_id": "item_003"
}
```

## conversation.item.truncate

Send this event to truncate a previous assistant message’s audio. The server will produce audio faster than realtime, so this event is useful when the user interrupts to truncate audio that has already been sent to the client but not yet played. This will synchronize the server's understanding of the audio with the client's playback.

Truncating audio will delete the server-side text transcript to ensure there is not text in the context that hasn't been heard by the user.

If successful, the server will respond with a `conversation.item.truncated` event.

### Parameters

#### audio_end_ms - integer
Inclusive duration up to which audio is truncated, in milliseconds. If the audio_end_ms is greater than the actual audio duration, the server will respond with an error.

#### content_index - integer
The index of the content part to truncate. Set this to `0`.

#### event_id - string
Optional client-generated ID used to identify this event.

#### item_id - string
The ID of the assistant message item to truncate. Only assistant message items can be truncated.

#### type - string
The event type, must be `conversation.item.truncate`.

### OBJECT conversation.item.truncate

```json
{
    "event_id": "event_678",
    "type": "conversation.item.truncate",
    "item_id": "item_002",
    "content_index": 0,
    "audio_end_ms": 1500
}
```

## conversation.item.delete

Send this event when you want to remove any item from the conversation history. The server will respond with a `conversation.item.deleted` event, unless the item does not exist in the conversation history, in which case the server will respond with an error.

### Parameters

#### event_id - string
Optional client-generated ID used to identify this event.

#### item_id - string
The ID of the item to delete.

#### type - string
The event type, must be `conversation.item.delete`.

### OBJECT conversation.item.delete

```json
{
    "event_id": "event_901",
    "type": "conversation.item.delete",
    "item_id": "item_003"
}
```

## response.create

This event instructs the server to create a Response, which means triggering model inference. When in Server VAD mode, the server will create Responses automatically.

A Response will include at least one Item, and may have two, in which case the second will be a function call. These Items will be appended to the conversation history by default.

The server will respond with a `response.created` event, events for Items and content created, and finally a `response.done` event to indicate the Response is complete.

The `response.create` event includes inference configuration like `instructions` and `tools`. If these are set, they will override the Session's configuration for this Response only.

Responses can be created out-of-band of the default Conversation, meaning that they can have arbitrary input, and it's possible to disable writing the output to the Conversation. Only one Response can write to the default Conversation at a time, but otherwise multiple Responses can be created in parallel. The `metadata` field is a good way to disambiguate multiple simultaneous Responses.

Clients can set `conversation` to `none` to create a Response that does not write to the default Conversation. Arbitrary input can be provided with the `input` field, which is an array accepting raw Items and references to existing Items.

### Parameters

#### event_id - string
Optional client-generated ID used to identify this event.

#### response - object
Create a new Realtime response with these parameters

- **audio - object**
  Configuration for audio input and output.

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

- **conversation - string**
  Controls which conversation the response is added to. Currently supports `auto` and `none`, with `auto` as the default value. The `auto` value means that the contents of the response will be added to the default conversation. Set this to `none` to create an out-of-band response which will not add items to default conversation.

- **input - array**
  Input items to include in the prompt for the model. Using this field creates a new context for this Response instead of using the default conversation. An empty array `[]` will clear the context for this Response. Note that this can include references to items that previously appeared in the session using their id.

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

- **instructions - string**
  The default system instructions (i.e. system message) prepended to model calls. This field allows the client to guide the model on desired responses. The model can be instructed on response content and format, (e.g. "be extremely succinct", "act friendly", "here are examples of good responses") and on audio behavior (e.g. "talk quickly", "inject emotion into your voice", "laugh frequently"). The instructions are not guaranteed to be followed by the model, but they provide guidance to the model on the desired behavior. Note that the server sets default instructions which will be used if this field is not set and are visible in the `session.created` event at the start of the session.

- **max_output_tokens - integer or "inf"**
  Maximum number of output tokens for a single assistant response, inclusive of tool calls. Provide an integer between 1 and 4096 to limit output tokens, or `inf` for the maximum available tokens for a given model. Defaults to `inf`.

- **metadata - map**
  Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard.

  Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters.

- **output_modalities - array**
  The set of modalities the model used to respond, currently the only possible values are `[\"audio\"]`, `[\"text\"]`. Audio output always include a text transcript. Setting the output to mode `text` will disable audio output from the model.

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

#### type - string
The event type, must be `response.create`.

### OBJECT response.create

```json
// Trigger a response with the default Conversation and no special parameters
{
  "type": "response.create",
}

// Trigger an out-of-band response that does not write to the default Conversation
{
  "type": "response.create",
  "response": {
    "instructions": "Provide a concise answer.",
    "tools": [], // clear any session tools
    "conversation": "none",
    "output_modalities": ["text"],
    "metadata": {
      "response_purpose": "summarization"
    },
    "input": [
      {
        "type": "item_reference",
        "id": "item_12345",
      },
      {
        "type": "message",
        "role": "user",
        "content": [
          {
            "type": "input_text",
            "text": "Summarize the above message in one sentence."
          }
        ]
      }
    ],
  }
}
```

## response.cancel

Send this event to cancel an in-progress response. The server will respond with a `response.done` event with a status of `response.status=cancelled`. If there is no response to cancel, the server will respond with an error. It's safe to call `response.cancel` even if no response is in progress, an error will be returned the session will remain unaffected.

### Parameters

#### event_id - string
Optional client-generated ID used to identify this event.

#### response_id - string
A specific response ID to cancel - if not provided, will cancel an in-progress response in the default conversation.

#### type - string
The event type, must be `response.cancel`.

### OBJECT response.cancel

```json
{
    "type": "response.cancel"
    "response_id": "resp_12345",
}
```

## output_audio_buffer.clear

**WebRTC/SIP Only:** Emit to cut off the current audio response. This will trigger the server to stop generating audio and emit a `output_audio_buffer.cleared` event. This event should be preceded by a `response.cancel` client event to stop the generation of the current response. [Learn more](https://platform.openai.com/docs/guides/realtime-conversations#client-and-server-events-for-audio-in-webrtc).

### Parameters

#### event_id - string
The unique ID of the client event used for error handling.

#### type - string
The event type, must be `output_audio_buffer.clear`.

### OBJECT output_audio_buffer.clear

```json
{
    "event_id": "optional_client_event_id",
    "type": "output_audio_buffer.clear"
}
```