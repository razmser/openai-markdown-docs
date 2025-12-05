# Uploads

Allows you to upload large files in multiple parts.

## Create upload

Creates an intermediate [Upload](https://platform.openai.com/docs/api-reference/uploads/object) object that you can add [Parts](https://platform.openai.com/docs/api-reference/uploads/part-object) to. Currently, an Upload can accept at most 8 GB in total and expires after an hour after you create it.

Once you complete the Upload, we will create a [File](https://platform.openai.com/docs/api-reference/files/object) object that contains all the parts you uploaded. This File is usable in the rest of our platform as a regular File object.

For certain `purpose` values, the correct `mime_type` must be specified. Please refer to documentation for the [supported MIME types for your use case](https://platform.openai.com/docs/assistants/tools/file-search#supported-files).

For guidance on the proper filename extensions for each purpose, please follow the documentation on [creating a File](https://platform.openai.com/docs/api-reference/files/create).

### Example request

```bash
curl https://api.openai.com/v1/uploads \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "purpose": "fine-tune",
    "filename": "training_examples.jsonl",
    "bytes": 2147483648,
    "mime_type": "text/jsonl",
    "expires_after": {
      "anchor": "created_at",
      "seconds": 3600
    }
  }'
```

### Response

```json
{
  "id": "upload_abc123",
  "object": "upload",
  "bytes": 2147483648,
  "created_at": 1719184911,
  "filename": "training_examples.jsonl",
  "purpose": "fine-tune",
  "status": "pending",
  "expires_at": 1719127296
}
```

## Add upload part

Adds a [Part](https://platform.openai.com/docs/api-reference/uploads/part-object) to an [Upload](https://platform.openai.com/docs/api-reference/uploads/object) object. A Part represents a chunk of bytes from the file you are trying to upload.

Each Part can be at most 64 MB, and you can add Parts until you hit the Upload maximum of 8 GB.

It is possible to add multiple Parts in parallel. You can decide the intended order of the Parts when you [complete the Upload](https://platform.openai.com/docs/api-reference/uploads/complete).

### Example request

```bash
curl https://api.openai.com/v1/uploads/upload_abc123/parts
  -F data="aHR0cHM6Ly9hcGkub3BlbmFpLmNvbS92MS91cGxvYWRz..."
```

### Response

```json
{
  "id": "part_def456",
  "object": "upload.part",
  "created_at": 1719185911,
  "upload_id": "upload_abc123"
}
```

## Complete upload

Completes the [Upload](https://platform.openai.com/docs/api-reference/uploads/object).

Within the returned Upload object, there is a nested [File](https://platform.openai.com/docs/api-reference/files/object) object that is ready to use in the rest of the platform.

You can specify the order of the Parts by passing in an ordered list of the Part IDs.

The number of bytes uploaded upon completion must match the number of bytes initially specified when creating the Upload object. No Parts may be added after an Upload is completed.

### Example request

```bash
curl https://api.openai.com/v1/uploads/upload_abc123/complete
  -d '{
    "part_ids": ["part_def456", "part_ghi789"]
  }'
```

### Response

```json
{
  "id": "upload_abc123",
  "object": "upload",
  "bytes": 2147483648,
  "created_at": 1719184911,
  "filename": "training_examples.jsonl",
  "purpose": "fine-tune",
  "status": "completed",
  "expires_at": 1719127296,
  "file": {
    "id": "file-xyz321",
    "object": "file",
    "bytes": 2147483648,
    "created_at": 1719186911,
    "expires_at": 1719127296,
    "filename": "training_examples.jsonl",
    "purpose": "fine-tune",
  }
}
```

## Cancel upload

Cancels the Upload. No Parts may be added after an Upload is cancelled.

### Example request

```bash
curl https://api.openai.com/v1/uploads/upload_abc123/cancel
```

### Response

```json
{
  "id": "upload_abc123",
  "object": "upload",
  "bytes": 2147483648,
  "created_at": 1719184911,
  "filename": "training_examples.jsonl",
  "purpose": "fine-tune",
  "status": "cancelled",
  "expires_at": 1719127296
}
```

## The upload object

The Upload object can accept byte chunks in the form of Parts.

### Parameters

#### bytes - integer
The intended number of bytes to be uploaded.

#### created_at - integer
The Unix timestamp (in seconds) for when the Upload was created.

#### expires_at - integer
The Unix timestamp (in seconds) for when the Upload will expire.

#### file - undefined or null
The ready File object after the Upload is completed.

#### filename - string
The name of the file to be uploaded.

#### id - string
The Upload unique identifier, which can be referenced in API endpoints.

#### object - string
The object type, which is always "upload".

#### purpose - string
The intended purpose of the file. [Please refer here](https://platform.openai.com/docs/api-reference/files/object#files/object-purpose) for acceptable values.

#### status - string
The status of the Upload.

### OBJECT The upload object

```json
{
  "id": "upload_abc123",
  "object": "upload",
  "bytes": 2147483648,
  "created_at": 1719184911,
  "filename": "training_examples.jsonl",
  "purpose": "fine-tune",
  "status": "completed",
  "expires_at": 1719127296,
  "file": {
    "id": "file-xyz321",
    "object": "file",
    "bytes": 2147483648,
    "created_at": 1719186911,
    "filename": "training_examples.jsonl",
    "purpose": "fine-tune",
  }
}
```

## The upload part object

The upload Part represents a chunk of bytes we can add to an Upload object.

### Parameters

#### created_at - integer
The Unix timestamp (in seconds) for when the Part was created.

#### id - string
The upload Part unique identifier, which can be referenced in API endpoints.

#### object - string
The object type, which is always `upload.part`.

#### upload_id - string
The ID of the Upload object that this Part was added to.

### OBJECT The upload part object

```json
{
    "id": "part_def456",
    "object": "upload.part",
    "created_at": 1719186911,
    "upload_id": "upload_abc123"
}
```