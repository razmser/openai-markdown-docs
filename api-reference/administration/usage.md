# Usage

The **Usage API** provides detailed insights into your activity across the OpenAI API. It also includes a separate [Costs endpoint](https://platform.openai.com/docs/api-reference/usage/costs), which offers visibility into your spend, breaking down consumption by invoice line items and project IDs.

While the Usage API delivers granular usage data, it may not always reconcile perfectly with the Costs due to minor differences in how usage and spend are recorded. For financial purposes, we recommend using the [Costs endpoint](https://platform.openai.com/docs/api-reference/usage/costs) or the [Costs tab](/settings/organization/usage) in the Usage Dashboard, which will reconcile back to your billing invoice.

## Completions

Get completions usage details for the organization.

### Example request

```bash
curl "https://api.openai.com/v1/organization/usage/completions?start_time=1730419200&limit=1" \
-H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
-H "Content-Type: application/json"
```

### Response

```json
{
    "object": "page",
    "data": [
        {
            "object": "bucket",
            "start_time": 1730419200,
            "end_time": 1730505600,
            "results": [
                {
                    "object": "organization.usage.completions.result",
                    "input_tokens": 1000,
                    "output_tokens": 500,
                    "input_cached_tokens": 800,
                    "input_audio_tokens": 0,
                    "output_audio_tokens": 0,
                    "num_model_requests": 5,
                    "project_id": null,
                    "user_id": null,
                    "api_key_id": null,
                    "model": null,
                    "batch": null,
                    "service_tier": null
                }
            ]
        }
    ],
    "has_more": true,
    "next_page": "page_AAAAAGdGxdEiJdKOAAAAAGcqsYA="
}
```

## Completions usage object

The aggregated completions usage details of the specific time bucket.

### Parameters

#### api_key_id - string
When `group_by=api_key_id`, this field provides the API key ID of the grouped usage result.

#### batch - boolean
When `group_by=batch`, this field tells whether the grouped usage result is batch or not.

#### input_audio_tokens - integer
The aggregated number of audio input tokens used, including cached tokens.

#### input_cached_tokens - integer
The aggregated number of text input tokens that has been cached from previous requests. For customers subscribe to scale tier, this includes scale tier tokens.

#### input_tokens - integer
The aggregated number of text input tokens used, including cached tokens. For customers subscribe to scale tier, this includes scale tier tokens.

#### model - string
When `group_by=model`, this field provides the model name of the grouped usage result.

#### num_model_requests - integer
The count of requests made to the model.

#### object - string

#### output_audio_tokens - integer
The aggregated number of audio output tokens used.

#### output_tokens - integer
The aggregated number of text output tokens used. For customers subscribe to scale tier, this includes scale tier tokens.

#### project_id - string
When `group_by=project_id`, this field provides the project ID of the grouped usage result.

#### service_tier - string
When `group_by=service_tier`, this field provides the service tier of the grouped usage result.

#### user_id - string
When `group_by=user_id`, this field provides the user ID of the grouped usage result.

### OBJECT Completions usage object

```json
{
    "object": "organization.usage.completions.result",
    "input_tokens": 5000,
    "output_tokens": 1000,
    "input_cached_tokens": 4000,
    "input_audio_tokens": 300,
    "output_audio_tokens": 200,
    "num_model_requests": 5,
    "project_id": "proj_abc",
    "user_id": "user-abc",
    "api_key_id": "key_abc",
    "model": "gpt-4o-mini-2024-07-18",
    "batch": false,
    "service_tier": "default"
}
```

## Embeddings

Get embeddings usage details for the organization.

### Example request

```bash
curl "https://api.openai.com/v1/organization/usage/embeddings?start_time=1730419200&limit=1" \
-H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
-H "Content-Type: application/json"
```

### Response

```json
{
    "object": "page",
    "data": [
        {
            "object": "bucket",
            "start_time": 1730419200,
            "end_time": 1730505600,
            "results": [
                {
                    "object": "organization.usage.embeddings.result",
                    "input_tokens": 16,
                    "num_model_requests": 2,
                    "project_id": null,
                    "user_id": null,
                    "api_key_id": null,
                    "model": null
                }
            ]
        }
    ],
    "has_more": false,
    "next_page": null
}
```

## Embeddings usage object

The aggregated embeddings usage details of the specific time bucket.

### Parameters

#### api_key_id - string
When `group_by=api_key_id`, this field provides the API key ID of the grouped usage result.

#### input_tokens - integer
The aggregated number of input tokens used.

#### model - string
When `group_by=model`, this field provides the model name of the grouped usage result.

#### num_model_requests - integer
The count of requests made to the model.

#### object - string

#### project_id - string
When `group_by=project_id`, this field provides the project ID of the grouped usage result.

#### user_id - string
When `group_by=user_id`, this field provides the user ID of the grouped usage result.

### OBJECT Embeddings usage object

```json
{
    "object": "organization.usage.embeddings.result",
    "input_tokens": 20,
    "num_model_requests": 2,
    "project_id": "proj_abc",
    "user_id": "user-abc",
    "api_key_id": "key_abc",
    "model": "text-embedding-ada-002-v2"
}
```

## Moderations

Get moderations usage details for the organization.

### Example request

```bash
curl "https://api.openai.com/v1/organization/usage/moderations?start_time=1730419200&limit=1" \
-H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
-H "Content-Type: application/json"
```

### Response

```json
{
    "object": "page",
    "data": [
        {
            "object": "bucket",
            "start_time": 1730419200,
            "end_time": 1730505600,
            "results": [
                {
                    "object": "organization.usage.moderations.result",
                    "input_tokens": 16,
                    "num_model_requests": 2,
                    "project_id": null,
                    "user_id": null,
                    "api_key_id": null,
                    "model": null
                }
            ]
        }
    ],
    "has_more": false,
    "next_page": null
}
```

## Moderations usage object

The aggregated moderations usage details of the specific time bucket.

### Parameters

#### api_key_id - string
When `group_by=api_key_id`, this field provides the API key ID of the grouped usage result.

#### input_tokens - integer
The aggregated number of input tokens used.

#### model - string
When `group_by=model`, this field provides the model name of the grouped usage result.

#### num_model_requests - integer
The count of requests made to the model.

#### object - string

#### project_id - string
When `group_by=project_id`, this field provides the project ID of the grouped usage result.

#### user_id - string
When `group_by=user_id`, this field provides the user ID of the grouped usage result.

### OBJECT Moderations usage object

```json
{
    "object": "organization.usage.moderations.result",
    "input_tokens": 20,
    "num_model_requests": 2,
    "project_id": "proj_abc",
    "user_id": "user-abc",
    "api_key_id": "key_abc",
    "model": "text-moderation"
}
```

## Images

Get images usage details for the organization.

### Example request

```bash
curl "https://api.openai.com/v1/organization/usage/images?start_time=1730419200&limit=1" \
-H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
-H "Content-Type: application/json"
```

### Response

```json
{
    "object": "page",
    "data": [
        {
            "object": "bucket",
            "start_time": 1730419200,
            "end_time": 1730505600,
            "results": [
                {
                    "object": "organization.usage.images.result",
                    "images": 2,
                    "num_model_requests": 2,
                    "size": null,
                    "source": null,
                    "project_id": null,
                    "user_id": null,
                    "api_key_id": null,
                    "model": null
                }
            ]
        }
    ],
    "has_more": false,
    "next_page": null
}
```

## Images usage object

The aggregated images usage details of the specific time bucket.

### Parameters

#### api_key_id - string
When `group_by=api_key_id`, this field provides the API key ID of the grouped usage result.

#### images - integer
The number of images processed.

#### model - string
When `group_by=model`, this field provides the model name of the grouped usage result.

#### num_model_requests - integer
The count of requests made to the model.

#### object - string

#### project_id - string
When `group_by=project_id`, this field provides the project ID of the grouped usage result.

#### size - string
When `group_by=size`, this field provides the image size of the grouped usage result.

#### source - string
When `group_by=source`, this field provides the source of the grouped usage result, possible values are `image.generation`, `image.edit`, `image.variation`.

#### user_id - string
When `group_by=user_id`, this field provides the user ID of the grouped usage result.

### OBJECT Images usage object

```json
{
    "object": "organization.usage.images.result",
    "images": 2,
    "num_model_requests": 2,
    "size": "1024x1024",
    "source": "image.generation",
    "project_id": "proj_abc",
    "user_id": "user-abc",
    "api_key_id": "key_abc",
    "model": "dall-e-3"
}
```

## Audio speeches

Get audio speeches usage details for the organization.

### Example request

```bash
curl "https://api.openai.com/v1/organization/usage/audio_speeches?start_time=1730419200&limit=1" \
-H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
-H "Content-Type: application/json"
```

### Response

```json
{
    "object": "page",
    "data": [
        {
            "object": "bucket",
            "start_time": 1730419200,
            "end_time": 1730505600,
            "results": [
                {
                    "object": "organization.usage.audio_speeches.result",
                    "characters": 45,
                    "num_model_requests": 1,
                    "project_id": null,
                    "user_id": null,
                    "api_key_id": null,
                    "model": null
                }
            ]
        }
    ],
    "has_more": false,
    "next_page": null
}
```

## Audio speeches usage object

The aggregated audio speeches usage details of the specific time bucket.

### Parameters

#### api_key_id - string
When `group_by=api_key_id`, this field provides the API key ID of the grouped usage result.

#### characters - integer
The number of characters processed.

#### model - string
When `group_by=model`, this field provides the model name of the grouped usage result.

#### num_model_requests - integer
The count of requests made to the model.

#### object - string

#### project_id - string
When `group_by=project_id`, this field provides the project ID of the grouped usage result.

#### user_id - string
When `group_by=user_id`, this field provides the user ID of the grouped usage result.

### OBJECT Audio speeches usage object

```json
{
    "object": "organization.usage.audio_speeches.result",
    "characters": 45,
    "num_model_requests": 1,
    "project_id": "proj_abc",
    "user_id": "user-abc",
    "api_key_id": "key_abc",
    "model": "tts-1"
}
```

## Audio transcriptions

Get audio transcriptions usage details for the organization.

### Example request

```bash
curl "https://api.openai.com/v1/organization/usage/audio_transcriptions?start_time=1730419200&limit=1" \
-H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
-H "Content-Type: application/json"
```

### Response

```json
{
    "object": "page",
    "data": [
        {
            "object": "bucket",
            "start_time": 1730419200,
            "end_time": 1730505600,
            "results": [
                {
                    "object": "organization.usage.audio_transcriptions.result",
                    "seconds": 20,
                    "num_model_requests": 1,
                    "project_id": null,
                    "user_id": null,
                    "api_key_id": null,
                    "model": null
                }
            ]
        }
    ],
    "has_more": false,
    "next_page": null
}
```

## Audio transcriptions usage object

The aggregated audio transcriptions usage details of the specific time bucket.

### Parameters

#### api_key_id - string
When `group_by=api_key_id`, this field provides the API key ID of the grouped usage result.

#### model - string
When `group_by=model`, this field provides the model name of the grouped usage result.

#### num_model_requests - integer
The count of requests made to the model.

#### object - string

#### project_id - string
When `group_by=project_id`, this field provides the project ID of the grouped usage result.

#### seconds - integer
The number of seconds processed.

#### user_id - string
When `group_by=user_id`, this field provides the user ID of the grouped usage result.

### OBJECT Audio transcriptions usage object

```json
{
    "object": "organization.usage.audio_transcriptions.result",
    "seconds": 10,
    "num_model_requests": 1,
    "project_id": "proj_abc",
    "user_id": "user-abc",
    "api_key_id": "key_abc",
    "model": "tts-1"
}
```

## Vector stores

Get vector stores usage details for the organization.

### Example request

```bash
curl "https://api.openai.com/v1/organization/usage/vector_stores?start_time=1730419200&limit=1" \
-H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
-H "Content-Type: application/json"
```

### Response

```json
{
    "object": "page",
    "data": [
        {
            "object": "bucket",
            "start_time": 1730419200,
            "end_time": 1730505600,
            "results": [
                {
                    "object": "organization.usage.vector_stores.result",
                    "usage_bytes": 1024,
                    "project_id": null
                }
            ]
        }
    ],
    "has_more": false,
    "next_page": null
}
```

## Vector stores usage object

The aggregated vector stores usage details of the specific time bucket.

### Parameters

#### object - string

#### project_id - string
When `group_by=project_id`, this field provides the project ID of the grouped usage result.

#### usage_bytes - integer
The vector stores usage in bytes.

### OBJECT Vector stores usage object

```json
{
    "object": "organization.usage.vector_stores.result",
    "usage_bytes": 1024,
    "project_id": "proj_abc"
}
```

## Code interpreter sessions

Get code interpreter sessions usage details for the organization.

### Example request

```bash
curl "https://api.openai.com/v1/organization/usage/code_interpreter_sessions?start_time=1730419200&limit=1" \
-H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
-H "Content-Type: application/json"
```

### Response

```json
{
    "object": "page",
    "data": [
        {
            "object": "bucket",
            "start_time": 1730419200,
            "end_time": 1730505600,
            "results": [
                {
                    "object": "organization.usage.code_interpreter_sessions.result",
                    "num_sessions": 1,
                    "project_id": null
                }
            ]
        }
    ],
    "has_more": false,
    "next_page": null
}
```

## Code interpreter sessions usage object

The aggregated code interpreter sessions usage details of the specific time bucket.

### Parameters

#### num_sessions - integer
The number of code interpreter sessions.

#### object - string

#### project_id - string
When `group_by=project_id`, this field provides the project ID of the grouped usage result.

### OBJECT Code interpreter sessions usage object

```json
{
    "object": "organization.usage.code_interpreter_sessions.result",
    "num_sessions": 1,
    "project_id": "proj_abc"
}
```

## Costs

Get costs details for the organization.

### Example request

```bash
curl "https://api.openai.com/v1/organization/costs?start_time=1730419200&limit=1" \
-H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
-H "Content-Type: application/json"
```

### Response

```json
{
    "object": "page",
    "data": [
        {
            "object": "bucket",
            "start_time": 1730419200,
            "end_time": 1730505600,
            "results": [
                {
                    "object": "organization.costs.result",
                    "amount": {
                        "value": 0.06,
                        "currency": "usd"
                    },
                    "line_item": null,
                    "project_id": null
                }
            ]
        }
    ],
    "has_more": false,
    "next_page": null
}
```

## Costs object

The aggregated costs details of the specific time bucket.

### Parameters

#### amount - object
The monetary value in its associated currency.

- **currency - string**
  Lowercase ISO-4217 currency e.g. "usd"

- **value - number**
  The numeric value of the cost.

#### line_item - string
When `group_by=line_item`, this field provides the line item of the grouped costs result.

#### object - string

#### project_id - string
When `group_by=project_id`, this field provides the project ID of the grouped costs result.

### OBJECT Costs object

```json
{
    "object": "organization.costs.result",
    "amount": {
      "value": 0.06,
      "currency": "usd"
    },
    "line_item": "Image models",
    "project_id": "proj_abc"
}
```