# Users

Manage users and their role in an organization.

## List users

Lists all of the users in the organization.

### Example request

```bash
curl https://api.openai.com/v1/organization/users?after=user_abc&limit=20 \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
    "object": "list",
    "data": [
        {
            "object": "organization.user",
            "id": "user_abc",
            "name": "First Last",
            "email": "user@example.com",
            "role": "owner",
            "added_at": 1711471533
        }
    ],
    "first_id": "user-abc",
    "last_id": "user-xyz",
    "has_more": false
}
```

## Modify user

Modifies a user's role in the organization.

### Example request

```bash
curl -X POST https://api.openai.com/v1/organization/users/user_abc \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "role": "owner"
  }'
```

### Response

```json
{
    "object": "organization.user",
    "id": "user_abc",
    "name": "First Last",
    "email": "user@example.com",
    "role": "owner",
    "added_at": 1711471533
}
```

## Retrieve user

Retrieves a user by their identifier.

### Example request

```bash
curl https://api.openai.com/v1/organization/users/user_abc \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
    "object": "organization.user",
    "id": "user_abc",
    "name": "First Last",
    "email": "user@example.com",
    "role": "owner",
    "added_at": 1711471533
}
```

## Delete user

Deletes a user from the organization.

### Example request

```bash
curl -X DELETE https://api.openai.com/v1/organization/users/user_abc \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
    "object": "organization.user.deleted",
    "id": "user_abc",
    "deleted": true
}
```

## The user object

Represents an individual `user` within an organization.

### Parameters

#### added_at - integer
The Unix timestamp (in seconds) of when the user was added.

#### email - string
The email address of the user

#### id - string
The identifier, which can be referenced in API endpoints

#### name - string
The name of the user

#### object - string
The object type, which is always `organization.user`

#### role - string
`owner` or `reader`

### OBJECT The user object

```json
{
    "object": "organization.user",
    "id": "user_abc",
    "name": "First Last",
    "email": "user@example.com",
    "role": "owner",
    "added_at": 1711471533
}
```