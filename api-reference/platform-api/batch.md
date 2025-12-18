# Batch

Create large batches of API requests for asynchronous processing. The Batch API returns completions within 24 hours for a 50% discount. Related guide: [Batch](https://platform.openai.com/docs/guides/batch)

## Create batch

Creates and executes a batch from an uploaded file of requests

### Example request

```bash
curl https://api.openai.com/v1/batches \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "input_file_id": "file-abc123",
    "endpoint": "/v1/chat/completions",
    "completion_window": "24h"
  }'
```

### Response

```json
{
  "id": "batch_abc123",
  "object": "batch",
  "endpoint": "/v1/chat/completions",
  "errors": null,
  "input_file_id": "file-abc123",
  "completion_window": "24h",
  "status": "validating",
  "output_file_id": null,
  "error_file_id": null,
  "created_at": 1711471533,
  "in_progress_at": null,
  "expires_at": null,
  "finalizing_at": null,
  "completed_at": null,
  "failed_at": null,
  "expired_at": null,
  "cancelling_at": null,
  "cancelled_at": null,
  "request_counts": {
    "total": 0,
    "completed": 0,
    "failed": 0
  },
  "metadata": {
    "customer_id": "user_123456789",
    "batch_description": "Nightly eval job",
  }
}
```

## Retrieve batch

Retrieves a batch.

### Example request

```bash
curl https://api.openai.com/v1/batches/batch_abc123 \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
```

### Response

```json
{
  "id": "batch_abc123",
  "object": "batch",
  "endpoint": "/v1/completions",
  "errors": null,
  "input_file_id": "file-abc123",
  "completion_window": "24h",
  "status": "completed",
  "output_file_id": "file-cvaTdG",
  "error_file_id": "file-HOWS94",
  "created_at": 1711471533,
  "in_progress_at": 1711471538,
  "expires_at": 1711557933,
  "finalizing_at": 1711493133,
  "completed_at": 1711493163,
  "failed_at": null,
  "expired_at": null,
  "cancelling_at": null,
  "cancelled_at": null,
  "request_counts": {
    "total": 100,
    "completed": 95,
    "failed": 5
  },
  "metadata": {
    "customer_id": "user_123456789",
    "batch_description": "Nightly eval job",
  }
}
```

## Cancel batch

Cancels an in-progress batch. The batch will be in status `cancelling` for up to 10 minutes, before changing to `cancelled`, where it will have partial results (if any) available in the output file.

### Example request

```bash
curl https://api.openai.com/v1/batches/batch_abc123/cancel \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -X POST
```

### Response

```json
{
  "id": "batch_abc123",
  "object": "batch",
  "endpoint": "/v1/chat/completions",
  "errors": null,
  "input_file_id": "file-abc123",
  "completion_window": "24h",
  "status": "cancelling",
  "output_file_id": null,
  "error_file_id": null,
  "created_at": 1711471533,
  "in_progress_at": 1711471538,
  "expires_at": 1711557933,
  "finalizing_at": null,
  "completed_at": null,
  "failed_at": null,
  "expired_at": null,
  "cancelling_at": 1711475133,
  "cancelled_at": null,
  "request_counts": {
    "total": 100,
    "completed": 23,
    "failed": 1
  },
  "metadata": {
    "customer_id": "user_123456789",
    "batch_description": "Nightly eval job",
  }
}
```

## List batch

List your organization's batches.

### Example request

```bash
curl https://api.openai.com/v1/batches?limit=2 \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "id": "batch_abc123",
      "object": "batch",
      "endpoint": "/v1/chat/completions",
      "errors": null,
      "input_file_id": "file-abc123",
      "completion_window": "24h",
      "status": "completed",
      "output_file_id": "file-cvaTdG",
      "error_file_id": "file-HOWS94",
      "created_at": 1711471533,
      "in_progress_at": 1711471538,
      "expires_at": 1711557933,
      "finalizing_at": 1711493133,
      "completed_at": 1711493163,
      "failed_at": null,
      "expired_at": null,
      "cancelling_at": null,
      "cancelled_at": null,
      "request_counts": {
        "total": 100,
        "completed": 95,
        "failed": 5
      },
      "metadata": {
        "customer_id": "user_123456789",
        "batch_description": "Nightly job",
      }
    },
    { ... },
  ],
  "first_id": "batch_abc123",
  "last_id": "batch_abc456",
  "has_more": true
}
```

## The batch object

### Parameters

#### cancelled_at - integer
The Unix timestamp (in seconds) for when the batch was cancelled.

#### cancelling_at - integer
The Unix timestamp (in seconds) for when the batch started cancelling.

#### completed_at - integer
The Unix timestamp (in seconds) for when the batch was completed.

#### completion_window - string
The time frame within which the batch should be processed.

#### created_at - integer
The Unix timestamp (in seconds) for when the batch was created.

#### endpoint - string
The OpenAI API endpoint used by the batch.

#### error_file_id - string
The ID of the file containing the outputs of requests with errors.

#### errors - object
An error code identifying the error type.

- **data - array**
  An error code identifying the error type.

  - **code - string**
    An error code identifying the error type.

  - **line - integer**
    The line number of the input file where the error occurred, if applicable.

  - **message - string**
    A human-readable message providing more details about the error.

  - **param - string**
    The name of the parameter that caused the error, if applicable.

- **object - string**
  The object type, which is always `list`.

#### expired_at - integer
The Unix timestamp (in seconds) for when the batch expired.

#### expires_at - integer
The Unix timestamp (in seconds) for when the batch will expire.

#### failed_at - integer
The Unix timestamp (in seconds) for when the batch failed.

#### finalizing_at - integer
The Unix timestamp (in seconds) for when the batch started finalizing.

#### id - string

#### in_progress_at - integer
The Unix timestamp (in seconds) for when the batch started processing.

#### input_file_id - string
The ID of the input file for the batch.

#### metadata - map
Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard.

Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters.

#### model - string
Model ID used to process the batch, like `gpt-5-2025-08-07`. OpenAI offers a wide range of models with different capabilities, performance characteristics, and price points. Refer to the [model guide](https://platform.openai.com/docs/models) to browse and compare available models.

#### object - string
The object type, which is always `batch`.

#### output_file_id - string
The ID of the file containing the outputs of successfully executed requests.

#### request_counts - object
The request counts for different statuses within the batch.

- **completed - integer**
  Number of requests that have been completed successfully.

- **failed - integer**
  Number of requests that have failed.

- **total - integer**
  Total number of requests in the batch.

#### status - string
The current status of the batch.

#### usage - object
Represents token usage details including input tokens, output tokens, a breakdown of output tokens, and the total tokens used. Only populated on batches created after September 7, 2025.

- **input_tokens - integer**
  The number of input tokens.

- **input_tokens_details - object**
  A detailed breakdown of the input tokens.

  - **cached_tokens - integer**
    The number of tokens that were retrieved from the cache. [More on prompt caching](https://platform.openai.com/docs/guides/prompt-caching).

- **output_tokens - integer**
  The number of output tokens.

- **output_tokens_details - object**
  A detailed breakdown of the output tokens.

  - **reasoning_tokens - integer**
    The number of reasoning tokens.

- **total_tokens - integer**
  The total number of tokens used.

### OBJECT The batch object

```json
{
  "id": "batch_abc123",
  "object": "batch",
  "endpoint": "/v1/completions",
  "model": "gpt-5-2025-08-07",
  "errors": null,
  "input_file_id": "file-abc123",
  "completion_window": "24h",
  "status": "completed",
  "output_file_id": "file-cvaTdG",
  "error_file_id": "file-HOWS94",
  "created_at": 1711471533,
  "in_progress_at": 1711471538,
  "expires_at": 1711557933,
  "finalizing_at": 1711493133,
  "completed_at": 1711493163,
  "failed_at": null,
  "expired_at": null,
  "cancelling_at": null,
  "cancelled_at": null,
  "request_counts": {
    "total": 100,
    "completed": 95,
    "failed": 5
  },
  "usage": {
    "input_tokens": 1500,
    "input_tokens_details": {
      "cached_tokens": 1024
    },
    "output_tokens": 500,
    "output_tokens_details": {
      "reasoning_tokens": 300
    },
    "total_tokens": 2000
  },
  "metadata": {
    "customer_id": "user_123456789",
    "batch_description": "Nightly eval job",
  }
}
```

## The request input object

The per-line object of the batch input file

### Parameters

#### custom_id - string
A developer-provided per-request id that will be used to match outputs to inputs. Must be unique for each request in a batch.

#### method - string
The HTTP method to be used for the request. Currently only `POST` is supported.

#### url - string
The OpenAI API relative URL to be used for the request. Currently `/v1/responses`, `/v1/chat/completions`, `/v1/embeddings`, `/v1/completions`, and `/v1/moderations` are supported.

### OBJECT The request input object

```json
{"custom_id": "request-1", "method": "POST", "url": "/v1/chat/completions", "body": {"model": "gpt-4o-mini", "messages": [{"role": "system", "content": "You are a helpful assistant."}, {"role": "user", "content": "What is 2+2?"}]}}
```

## The request output object

The per-line object of the batch output and error files

### Parameters

#### custom_id - string
A developer-provided per-request id that will be used to match outputs to inputs.

#### error - object
For requests that failed with a non-HTTP error, this will contain more information on the cause of the failure.

- **code - string**
  A machine-readable error code.

  Possible values:

    * `batch_expired`: The request could not be executed before the completion window ended.
    * `batch_cancelled`: The batch was cancelled before this request executed.
    * `request_timeout`: The underlying call to the model timed out.

- **message - string**
  A human-readable error message.

#### id - string

#### response - object
The JSON body of the response

- **body - map**
  The JSON body of the response

- **request_id - string**
  An unique identifier for the OpenAI API request. Please include this request ID when contacting support.

- **status_code - integer**
  The HTTP status code of the response

### OBJECT The request output object

```json
{"id": "batch_req_wnaDys", "custom_id": "request-2", "response": {"status_code": 200, "request_id": "req_c187b3", "body": {"id": "chatcmpl-9758Iw", "object": "chat.completion", "created": 1711475054, "model": "gpt-4o-mini", "choices": [{"index": 0, "message": {"role": "assistant", "content": "2 + 2 equals 4."}, "finish_reason": "stop"}], "usage": {"prompt_tokens": 24, "completion_tokens": 15, "total_tokens": 39}, "system_fingerprint": null}}, "error": null}
```