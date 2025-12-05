# Embeddings

Get a vector representation of a given input that can be easily consumed by machine learning models and algorithms. Related guide: [Embeddings](https://platform.openai.com/docs/guides/embeddings)

## Create embeddings

Creates an embedding vector representing the input text.

### Example request

```bash
curl https://api.openai.com/v1/embeddings \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "input": "The food was delicious and the waiter...",
    "model": "text-embedding-ada-002",
    "encoding_format": "float"
  }'
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "object": "embedding",
      "embedding": [
        0.0023064255,
        -0.009327292,
        .... (1536 floats total for ada-002)
        -0.0028842222,
      ],
      "index": 0
    }
  ],
  "model": "text-embedding-ada-002",
  "usage": {
    "prompt_tokens": 8,
    "total_tokens": 8
  }
}
```

## The embedding object

Represents an embedding vector returned by embedding endpoint.

### Parameters

#### embedding - array
The embedding vector, which is a list of floats. The length of vector depends on the model as listed in the [embedding guide](https://platform.openai.com/docs/guides/embeddings).

#### index - integer
The index of the embedding in the list of embeddings.

#### object - string
The object type, which is always "embedding".

### OBJECT The embedding object

```json
{
  "object": "embedding",
  "embedding": [
    0.0023064255,
    -0.009327292,
    .... (1536 floats total for ada-002)
    -0.0028842222,
  ],
  "index": 0
}
```