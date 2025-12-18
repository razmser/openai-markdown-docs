# Project API keys

Manage API keys for a given project. Supports listing and deleting keys for users. This API does not allow issuing keys for users, as users need to authorize themselves to generate keys.

## List project API keys

Returns a list of API keys in the project.

### Example request

```bash
curl https://api.openai.com/v1/organization/projects/proj_abc/api_keys?after=key_abc&limit=20 \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
    "object": "list",
    "data": [
        {
            "object": "organization.project.api_key",
            "redacted_value": "sk-abc...def",
            "name": "My API Key",
            "created_at": 1711471533,
            "last_used_at": 1711471534,
            "id": "key_abc",
            "owner": {
                "type": "user",
                "user": {
                    "object": "organization.project.user",
                    "id": "user_abc",
                    "name": "First Last",
                    "email": "user@example.com",
                    "role": "owner",
                    "added_at": 1711471533
                }
            }
        }
    ],
    "first_id": "key_abc",
    "last_id": "key_xyz",
    "has_more": false
}
```

## Retrieve project API key

Retrieves an API key in the project.

### Example request

```bash
curl https://api.openai.com/v1/organization/projects/proj_abc/api_keys/key_abc \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
    "object": "organization.project.api_key",
    "redacted_value": "sk-abc...def",
    "name": "My API Key",
    "created_at": 1711471533,
    "last_used_at": 1711471534,
    "id": "key_abc",
    "owner": {
        "type": "user",
        "user": {
            "object": "organization.project.user",
            "id": "user_abc",
            "name": "First Last",
            "email": "user@example.com",
            "role": "owner",
            "added_at": 1711471533
        }
    }
}
```

## Delete project API key

Deletes an API key from the project.

### Example request

```bash
curl -X DELETE https://api.openai.com/v1/organization/projects/proj_abc/api_keys/key_abc \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
    "object": "organization.project.api_key.deleted",
    "id": "key_abc",
    "deleted": true
}
```

## The project API key object

Represents an individual API key in a project.

### Parameters

#### created_at - integer
The Unix timestamp (in seconds) of when the API key was created

#### id - string
The identifier, which can be referenced in API endpoints

#### last_used_at - integer
The Unix timestamp (in seconds) of when the API key was last used.

#### name - string
The name of the API key

#### object - string
The object type, which is always `organization.project.api_key`

#### owner - object
Represents an individual service account in a project.

- **service_account - object**
  Represents an individual service account in a project.

  - **created_at - integer**
    The Unix timestamp (in seconds) of when the service account was created

  - **id - string**
    The identifier, which can be referenced in API endpoints

  - **name - string**
    The name of the service account

  - **object - string**
    The object type, which is always `organization.project.service_account`

  - **role - string**
    `owner` or `member`

- **type - string**
  `user` or `service_account`

- **user - object**
  Represents an individual user in a project.

  - **added_at - integer**
    The Unix timestamp (in seconds) of when the project was added.

  - **email - string**
    The email address of the user

  - **id - string**
    The identifier, which can be referenced in API endpoints

  - **name - string**
    The name of the user

  - **object - string**
    The object type, which is always `organization.project.user`

  - **role - string**
    `owner` or `member`

#### redacted_value - string
The redacted value of the API key

### OBJECT The project API key object

```json
{
    "object": "organization.project.api_key",
    "redacted_value": "sk-abc...def",
    "name": "My API Key",
    "created_at": 1711471533,
    "last_used_at": 1711471534,
    "id": "key_abc",
    "owner": {
        "type": "user",
        "user": {
            "object": "organization.project.user",
            "id": "user_abc",
            "name": "First Last",
            "email": "user@example.com",
            "role": "owner",
            "created_at": 1711471533
        }
    }
}
```