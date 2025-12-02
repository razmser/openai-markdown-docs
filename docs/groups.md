# Groups

Manage reusable collections of users for organization-wide access control and maintain their membership.

## List groups

Lists all groups in the organization.

### Example request

```bash
curl https://api.openai.com/v1/organization/groups?limit=20&order=asc \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
    "object": "list",
    "data": [
        {
            "object": "group",
            "id": "group_01J1F8ABCDXYZ",
            "name": "Support Team",
            "created_at": 1711471533,
            "is_scim_managed": false
        }
    ],
    "has_more": false,
    "next": null
}
```

## Create group

Creates a new group in the organization.

### Example request

```bash
curl -X POST https://api.openai.com/v1/organization/groups \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "name": "Support Team"
  }'
```

### Response

```json
{
    "object": "group",
    "id": "group_01J1F8ABCDXYZ",
    "name": "Support Team",
    "created_at": 1711471533,
    "is_scim_managed": false
}
```

## Update group

Updates a group's information.

### Example request

```bash
curl -X POST https://api.openai.com/v1/organization/groups/group_01J1F8ABCDXYZ \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "name": "Escalations"
  }'
```

### Response

```json
{
    "id": "group_01J1F8ABCDXYZ",
    "name": "Escalations",
    "created_at": 1711471533,
    "is_scim_managed": false
}
```

## Delete group

Deletes a group from the organization.

### Example request

```bash
curl -X DELETE https://api.openai.com/v1/organization/groups/group_01J1F8ABCDXYZ \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
    "object": "group.deleted",
    "id": "group_01J1F8ABCDXYZ",
    "deleted": true
}
```

## List group users

Lists the users assigned to a group.

### Example request

```bash
curl https://api.openai.com/v1/organization/groups/group_01J1F8ABCDXYZ/users?limit=20 \
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
            "id": "user_abc123",
            "name": "Ada Lovelace",
            "email": "ada@example.com",
            "role": "owner",
            "added_at": 1711471533
        }
    ],
    "has_more": false,
    "next": null
}
```

## Add group user

Adds a user to a group.

### Example request

```bash
curl -X POST https://api.openai.com/v1/organization/groups/group_01J1F8ABCDXYZ/users \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "user_id": "user_abc123"
  }'
```

### Response

```json
{
    "object": "group.user",
    "user_id": "user_abc123",
    "group_id": "group_01J1F8ABCDXYZ"
}
```

## Remove group user

Removes a user from a group.

### Example request

```bash
curl -X DELETE https://api.openai.com/v1/organization/groups/group_01J1F8ABCDXYZ/users/user_abc123 \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
    "object": "group.user.deleted",
    "deleted": true
}
```

## The group user object

Confirmation payload returned after adding a user to a group.

### Parameters

#### group_id - string
Identifier of the group the user was added to.

#### object - string
Always `group.user`.

#### user_id - string
Identifier of the user that was added.

### OBJECT The group user object

```json
{
    "object": "group.user",
    "user_id": "user_abc123",
    "group_id": "group_01J1F8ABCDXYZ"
}
```

## The group object

Summary information about a group returned in role assignment responses.

### Parameters

#### created_at - integer
Unix timestamp (in seconds) when the group was created.

#### id - string
Identifier for the group.

#### name - string
Display name of the group.

#### object - string
Always `group`.

#### scim_managed - boolean
Whether the group is managed through SCIM.

### OBJECT The group object

```json
{
    "object": "group",
    "id": "group_01J1F8ABCDXYZ",
    "name": "Support Team",
    "created_at": 1711471533,
    "scim_managed": false
}
```