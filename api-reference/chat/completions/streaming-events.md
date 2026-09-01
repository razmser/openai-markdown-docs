# Chat Completions streaming events

> For the complete documentation index, see [llms.txt](https://developers.openai.com/llms.txt). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Stream Chat Completions in real time. Receive chunks of completions
returned from the model using server-sent events.
[Learn more](https://developers.openai.com/docs/guides/streaming-responses?api-mode=chat).

## chat.completion.chunk

Represents a streamed chunk of a chat completion response returned
by the model, based on the provided input.
[Learn more](https://developers.openai.com/docs/guides/streaming-responses).

### Schema

Schema name: `CreateChatCompletionStreamResponse`

### Example

```json
{"id":"chatcmpl-123","object":"chat.completion.chunk","created":1694268190,"model":"gpt-5.6-sol", "system_fingerprint": "fp_44709d6fcb", "choices":[{"index":0,"delta":{"role":"assistant","content":""},"logprobs":null,"finish_reason":null}],"obfuscation":"r4N7vQ2m"}

{"id":"chatcmpl-123","object":"chat.completion.chunk","created":1694268190,"model":"gpt-5.6-sol", "system_fingerprint": "fp_44709d6fcb", "choices":[{"index":0,"delta":{"content":"Hello"},"logprobs":null,"finish_reason":null}],"obfuscation":"p9K3xT6w"}

....

{"id":"chatcmpl-123","object":"chat.completion.chunk","created":1694268190,"model":"gpt-5.6-sol", "system_fingerprint": "fp_44709d6fcb", "choices":[{"index":0,"delta":{},"logprobs":null,"finish_reason":"stop"}],"obfuscation":""}
```
