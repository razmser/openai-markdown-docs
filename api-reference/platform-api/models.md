# Models

List and describe the various models available in the API. You can refer to the [Models](https://platform.openai.com/docs/models) documentation to understand what models are available and the differences between them.

## List models

Lists the currently available models, and provides basic information about each one such as the owner and availability.

### Example request

```bash
curl https://api.openai.com/v1/models \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "id": "model-id-0",
      "object": "model",
      "created": 1686935002,
      "owned_by": "organization-owner"
    },
    {
      "id": "model-id-1",
      "object": "model",
      "created": 1686935002,
      "owned_by": "organization-owner",
    },
    {
      "id": "model-id-2",
      "object": "model",
      "created": 1686935002,
      "owned_by": "openai"
    },
  ],
  "object": "list"
}
```

## Retrieve model

Retrieves a model instance, providing basic information about the model such as the owner and permissioning.

### Example request

```bash
curl https://api.openai.com/v1/models/gpt-5.1 \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "id": "gpt-5.1",
  "object": "model",
  "created": 1686935002,
  "owned_by": "openai"
}
```

## Delete a fine-tuned model

Delete a fine-tuned model. You must have the Owner role in your organization to delete a model.

### Example request

```bash
curl https://api.openai.com/v1/models/ft:gpt-4o-mini:acemeco:suffix:abc123 \
  -X DELETE \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "id": "ft:gpt-4o-mini:acemeco:suffix:abc123",
  "object": "model",
  "deleted": true
}
```

## The model object

Describes an OpenAI model offering that can be used with the API.

### Parameters

#### created - integer
The Unix timestamp (in seconds) when the model was created.

#### id - string
The model identifier, which can be referenced in the API endpoints.

#### object - string
The object type, which is always "model".

#### owned_by - string
The organization that owns the model.

### OBJECT The model object

```json
{
  "id": "gpt-5.1",
  "object": "model",
  "created": 1686935002,
  "owned_by": "openai"
}
```