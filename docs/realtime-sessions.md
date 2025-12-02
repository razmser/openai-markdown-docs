# Client secrets

REST API endpoint to generate ephemeral client secrets for use in client-side applications. Client secrets are short-lived tokens that can be passed to a client app, such as a web frontend or mobile client, which grants access to the Realtime API without leaking your main API key. You can configure a custom TTL for each client secret.

You can also attach session configuration options to the client secret, which will be applied to any sessions created using that client secret, but these can also be overridden by the client connection.

[Learn more about authentication with client secrets over WebRTC](https://platform.openai.com/docs/guides/realtime-webrtc).

## Create client secret

Create a Realtime client secret with an associated session configuration.

### Example request

```bash
curl -X POST https://api.openai.com/v1/realtime/client_secrets \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "expires_after": {
      "anchor": "created_at",
      "seconds": 600
    },
    "session": {
      "type": "realtime",
      "model": "gpt-realtime",
      "instructions": "You are a friendly assistant."
    }
  }'
```

### Response

```json
{
  "value": "ek_68af296e8e408191a1120ab6383263c2",
  "expires_at": 1756310470,
  "session": {
    "type": "realtime",
    "object": "realtime.session",
    "id": "sess_C9CiUVUzUzYIssh3ELY1d",
    "model": "gpt-realtime",
    "output_modalities": [
      "audio"
    ],
    "instructions": "You are a friendly assistant.",
    "tools": [],
    "tool_choice": "auto",
    "max_output_tokens": "inf",
    "tracing": null,
    "truncation": "auto",
    "prompt": null,
    "expires_at": 0,
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
        }
      },
      "output": {
        "format": {
          "type": "audio/pcm",
          "rate": 24000
        },
        "voice": "alloy",
        "speed": 1.0
      }
    },
    "include": null
  }
}
```

## Session response object

Response from creating a session and client secret for the Realtime API.

### Parameters

#### expires_at - integer
Expiration timestamp for the client secret, in seconds since epoch.

#### session - object
The session configuration for either a realtime or transcription session.

  - **client_secret - object**
    Ephemeral key returned by the API.

    - **expires_at - integer**
      Timestamp for when the token expires. Currently, all tokens expire after one minute.

    - **value - string**
      Ephemeral key usable in client environments to authenticate connections to the Realtime API. Use this in client-side environments rather than a standard API token, which should only be used server-side.

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

- **Realtime transcription session configuration object - object**
  A Realtime transcription session configuration object.

  - **id - string**
    Unique identifier for the session that looks like `sess_1234567890abcdef`.

  - **object - string**
    The object type. Always `realtime.transcription_session`.

  - **type - string**
    The type of session. Always `transcription` for transcription sessions.

  - **audio - object**
    Configuration for input audio for the session.

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
        Configuration for input audio noise reduction.

        - **type - string**
          Type of noise reduction. `near_field` is for close-talking microphones such as headphones, `far_field` is for far-field microphones such as laptop or conference room microphones.

      - **transcription - object**
        Configuration of the transcription model.

        - **language - string**
          The language of the input audio. Supplying the input language in [ISO-639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) (e.g. `en`) format will improve accuracy and latency.

        - **model - string**
          The model to use for transcription. Current options are `whisper-1`, `gpt-4o-mini-transcribe`, `gpt-4o-transcribe`, and `gpt-4o-transcribe-diarize`. Use `gpt-4o-transcribe-diarize` when you need diarization with speaker labels.

        - **prompt - string**
          An optional text to guide the model's style or continue a previous audio segment. For `whisper-1`, the [prompt is a list of keywords](https://platform.openai.com/docs/guides/speech-to-text#prompting). For `gpt-4o-transcribe` models (excluding `gpt-4o-transcribe-diarize`), the prompt is a free text string, for example "expect words related to technology".

      - **turn_detection - object**
        Configuration for turn detection. Can be set to `null` to turn off. Server VAD means that the model will detect the start and end of speech based on audio volume and respond at the end of user speech.

        - **prefix_padding_ms - integer**
          Amount of audio to include before the VAD detected speech (in milliseconds). Defaults to 300ms.

        - **silence_duration_ms - integer**
          Duration of silence to detect speech stop (in milliseconds). Defaults to 500ms. With shorter values the model will respond more quickly, but may jump in on short pauses from the user.

        - **threshold - number**
          Activation threshold for VAD (0.0 to 1.0), this defaults to 0.5. A higher threshold will require louder audio to activate the model, and thus might perform better in noisy environments.

        - **type - string**
          Type of turn detection, only `server_vad` is currently supported.

  - **expires_at - integer**
    Expiration timestamp for the session, in seconds since epoch.

  - **include - array**
    Additional fields to include in server outputs.

    null.

#### value - string
The generated client secret value.

### OBJECT Session response object

```json
{
  "value": "ek_68af296e8e408191a1120ab6383263c2",
  "expires_at": 1756310470,
  "session": {
    "type": "realtime",
    "object": "realtime.session",
    "id": "sess_C9CiUVUzUzYIssh3ELY1d",
    "model": "gpt-realtime-2025-08-25",
    "output_modalities": [
      "audio"
    ],
    "instructions": "You are a friendly assistant.",
    "tools": [],
    "tool_choice": "auto",
    "max_output_tokens": "inf",
    "tracing": null,
    "truncation": "auto",
    "prompt": null,
    "expires_at": 0,
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
        "voice": "alloy",
        "speed": 1.0
      }
    },
    "include": null
  }
}
```