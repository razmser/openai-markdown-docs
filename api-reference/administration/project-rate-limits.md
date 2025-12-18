# Project rate limits

Manage rate limits per model for projects. Rate limits may be configured to be equal to or lower than the organization's rate limits.

## List project rate limits

Returns the rate limits per model for a project.

### Example request

```bash
curl https://api.openai.com/v1/organization/projects/proj_abc/rate_limits?after=rl_xxx&limit=20 \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
    "object": "list",
    "data": [
        {
          "object": "project.rate_limit",
          "id": "rl-ada",
          "model": "ada",
          "max_requests_per_1_minute": 600,
          "max_tokens_per_1_minute": 150000,
          "max_images_per_1_minute": 10
        }
    ],
    "first_id": "rl-ada",
    "last_id": "rl-ada",
    "has_more": false
}
```

## Modify project rate limit

Updates a project rate limit.

### Example request

```bash
curl -X POST https://api.openai.com/v1/organization/projects/proj_abc/rate_limits/rl_xxx \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "max_requests_per_1_minute": 500
  }'
```

### Response

```json
{
    "object": "project.rate_limit",
    "id": "rl-ada",
    "model": "ada",
    "max_requests_per_1_minute": 600,
    "max_tokens_per_1_minute": 150000,
    "max_images_per_1_minute": 10
  }
```

## The project rate limit object

Represents a project rate limit config.

### Parameters

#### batch_1_day_max_input_tokens - integer
The maximum batch input tokens per day. Only present for relevant models.

#### id - string
The identifier, which can be referenced in API endpoints.

#### max_audio_megabytes_per_1_minute - integer
The maximum audio megabytes per minute. Only present for relevant models.

#### max_images_per_1_minute - integer
The maximum images per minute. Only present for relevant models.

#### max_requests_per_1_day - integer
The maximum requests per day. Only present for relevant models.

#### max_requests_per_1_minute - integer
The maximum requests per minute.

#### max_tokens_per_1_minute - integer
The maximum tokens per minute.

#### model - string
The model this rate limit applies to.

#### object - string
The object type, which is always `project.rate_limit`

### OBJECT The project rate limit object

```json
{
    "object": "project.rate_limit",
    "id": "rl_ada",
    "model": "ada",
    "max_requests_per_1_minute": 600,
    "max_tokens_per_1_minute": 150000,
    "max_images_per_1_minute": 10
}
```