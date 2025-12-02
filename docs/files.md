# Files

Files are used to upload documents that can be used with features like [Assistants](https://platform.openai.com/docs/api-reference/assistants), [Fine-tuning](https://platform.openai.com/docs/api-reference/fine-tuning), and [Batch API](https://platform.openai.com/docs/guides/batch).

## Upload file

Upload a file that can be used across various endpoints. Individual files can be up to 512 MB, and the size of all files uploaded by one organization can be up to 1 TB.

  * The Assistants API supports files up to 2 million tokens and of specific file types. See the [Assistants Tools guide](https://platform.openai.com/docs/assistants/tools) for details.
  * The Fine-tuning API only supports `.jsonl` files. The input also has certain required formats for fine-tuning [chat](https://platform.openai.com/docs/api-reference/fine-tuning/chat-input) or [completions](https://platform.openai.com/docs/api-reference/fine-tuning/completions-input) models.
  * The Batch API only supports `.jsonl` files up to 200 MB in size. The input also has a specific required [format](https://platform.openai.com/docs/api-reference/batch/request-input).

Please [contact us](https://help.openai.com/) if you need to increase these storage limits.

### Example request

```bash
curl https://api.openai.com/v1/files \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -F purpose="fine-tune" \
  -F file="@mydata.jsonl"
  -F expires_after[anchor]="created_at"
  -F expires_after[seconds]=2592000
```

### Response

```json
{
  "id": "file-abc123",
  "object": "file",
  "bytes": 120000,
  "created_at": 1677610602,
  "expires_at": 1677614202,
  "filename": "mydata.jsonl",
  "purpose": "fine-tune",
}
```

## List files

Returns a list of files.

### Example request

```bash
curl https://api.openai.com/v1/files \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "id": "file-abc123",
      "object": "file",
      "bytes": 175,
      "created_at": 1613677385,
      "expires_at": 1677614202,
      "filename": "salesOverview.pdf",
      "purpose": "assistants",
    },
    {
      "id": "file-abc456",
      "object": "file",
      "bytes": 140,
      "created_at": 1613779121,
      "expires_at": 1677614202,
      "filename": "puppy.jsonl",
      "purpose": "fine-tune",
    }
  ],
  "first_id": "file-abc123",
  "last_id": "file-abc456",
  "has_more": false
}
```

## Retrieve file

Returns information about a specific file.

### Example request

```bash
curl https://api.openai.com/v1/files/file-abc123 \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "id": "file-abc123",
  "object": "file",
  "bytes": 120000,
  "created_at": 1677610602,
  "expires_at": 1677614202,
  "filename": "mydata.jsonl",
  "purpose": "fine-tune",
}
```

## Delete file

Delete a file and remove it from all vector stores.

### Example request

```bash
curl https://api.openai.com/v1/files/file-abc123 \
  -X DELETE \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "id": "file-abc123",
  "object": "file",
  "deleted": true
}
```

## Retrieve file content

Returns the contents of the specified file.

### Example request

```bash
curl https://api.openai.com/v1/files/file-abc123/content \
  -H "Authorization: Bearer $OPENAI_API_KEY" > file.jsonl
```

## The file object

The `File` object represents a document that has been uploaded to OpenAI.

### Parameters

#### bytes - integer
The size of the file, in bytes.

#### created_at - integer
The Unix timestamp (in seconds) for when the file was created.

#### expires_at - integer
The Unix timestamp (in seconds) for when the file will expire.

#### filename - string
The name of the file.

#### id - string
The file identifier, which can be referenced in the API endpoints.

#### object - string
The object type, which is always `file`.

#### purpose - string
The intended purpose of the file. Supported values are `assistants`, `assistants_output`, `batch`, `batch_output`, `fine-tune`, `fine-tune-results`, `vision`, and `user_data`.

#### status - string - Deprecated
Deprecated. The current status of the file, which can be either `uploaded`, `processed`, or `error`.

#### status_details - string - Deprecated
Deprecated. For details on why a fine-tuning training file failed validation, see the `error` field on `fine_tuning.job`.

### OBJECT The file object

```json
{
  "id": "file-abc123",
  "object": "file",
  "bytes": 120000,
  "created_at": 1677610602,
  "expires_at": 1680202602,
  "filename": "salesOverview.pdf",
  "purpose": "assistants",
}
```