# Assistants streaming events

> For the complete documentation index, see [llms.txt](https://developers.openai.com/llms.txt). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Stream the result of executing a Run or resuming a Run after submitting tool outputs.
You can stream events from the [Create Thread and Run](https://developers.openai.com/docs/api-reference/runs/createThreadAndRun),
[Create Run](https://developers.openai.com/docs/api-reference/runs/createRun), and [Submit Tool Outputs](https://developers.openai.com/docs/api-reference/runs/submitToolOutputs)
endpoints by passing `"stream": true`. The response will be a [Server-Sent events](https://html.spec.whatwg.org/multipage/server-sent-events.html#server-sent-events) stream.
Our Node and Python SDKs provide helpful utilities to make streaming easy. Reference the
[Assistants API quickstart](https://developers.openai.com/docs/assistants/overview) to learn more.

## event

Occurs when a new [thread](https://developers.openai.com/docs/api-reference/threads/object) is created.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 0`

### Example

```json
{}
```

## event

Occurs when a new [run](https://developers.openai.com/docs/api-reference/runs/object) is created.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 1`

### Example

```json
{}
```

## event

Occurs when a [run](https://developers.openai.com/docs/api-reference/runs/object) moves to a `queued` status.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 2`

### Example

```json
{}
```

## event

Occurs when a [run](https://developers.openai.com/docs/api-reference/runs/object) moves to an `in_progress` status.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 3`

### Example

```json
{}
```

## event

Occurs when a [run](https://developers.openai.com/docs/api-reference/runs/object) moves to a `requires_action` status.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 4`

### Example

```json
{}
```

## event

Occurs when a [run](https://developers.openai.com/docs/api-reference/runs/object) is completed.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 5`

### Example

```json
{}
```

## event

Occurs when a [run](https://developers.openai.com/docs/api-reference/runs/object) ends with status `incomplete`.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 6`

### Example

```json
{}
```

## event

Occurs when a [run](https://developers.openai.com/docs/api-reference/runs/object) fails.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 7`

### Example

```json
{}
```

## event

Occurs when a [run](https://developers.openai.com/docs/api-reference/runs/object) moves to a `cancelling` status.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 8`

### Example

```json
{}
```

## event

Occurs when a [run](https://developers.openai.com/docs/api-reference/runs/object) is cancelled.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 9`

### Example

```json
{}
```

## event

Occurs when a [run](https://developers.openai.com/docs/api-reference/runs/object) expires.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 10`

### Example

```json
{}
```

## event

Occurs when a [run step](https://developers.openai.com/docs/api-reference/run-steps/step-object) is created.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 11`

### Example

```json
{}
```

## event

Occurs when a [run step](https://developers.openai.com/docs/api-reference/run-steps/step-object) moves to an `in_progress` state.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 12`

### Example

```json
{}
```

## event

Occurs when parts of a [run step](https://developers.openai.com/docs/api-reference/run-steps/step-object) are being streamed.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 13`

### Example

```json
{}
```

## event

Occurs when a [run step](https://developers.openai.com/docs/api-reference/run-steps/step-object) is completed.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 14`

### Example

```json
{}
```

## event

Occurs when a [run step](https://developers.openai.com/docs/api-reference/run-steps/step-object) fails.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 15`

### Example

```json
{}
```

## event

Occurs when a [run step](https://developers.openai.com/docs/api-reference/run-steps/step-object) is cancelled.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 16`

### Example

```json
{}
```

## event

Occurs when a [run step](https://developers.openai.com/docs/api-reference/run-steps/step-object) expires.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 17`

### Example

```json
{}
```

## event

Occurs when a [message](https://developers.openai.com/docs/api-reference/messages/object) is created.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 18`

### Example

```json
{}
```

## event

Occurs when a [message](https://developers.openai.com/docs/api-reference/messages/object) moves to an `in_progress` state.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 19`

### Example

```json
{}
```

## event

Occurs when parts of a [Message](https://developers.openai.com/docs/api-reference/messages/object) are being streamed.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 20`

### Example

```json
{}
```

## event

Occurs when a [message](https://developers.openai.com/docs/api-reference/messages/object) is completed.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 21`

### Example

```json
{}
```

## event

Occurs when a [message](https://developers.openai.com/docs/api-reference/messages/object) ends before it is completed.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 22`

### Example

```json
{}
```

## event

Occurs when an [error](https://developers.openai.com/docs/guides/error-codes#api-errors) occurs. This can happen due to an internal server error or a timeout.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 23`

### Example

```json
{}
```

## event

Occurs when a stream ends.

### Schema

Schema name: `(resource) beta.assistants > (model) assistant_stream_event > (schema) > (variant) 24`

### Example

```json
{}
```
