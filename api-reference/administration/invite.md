# Invites

Invite and manage invitations for an organization.

## List invites

Returns a list of invites in the organization.

### Example request

```bash
curl https://api.openai.com/v1/organization/invites?after=invite-abc&limit=20 \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "object": "organization.invite",
      "id": "invite-abc",
      "email": "user@example.com",
      "role": "owner",
      "status": "accepted",
      "invited_at": 1711471533,
      "expires_at": 1711471533,
      "accepted_at": 1711471533
    }
  ],
  "first_id": "invite-abc",
  "last_id": "invite-abc",
  "has_more": false
}
```

## Create invite

Create an invite for a user to the organization. The invite must be accepted by the user before they have access to the organization.

### Example request

```bash
curl -X POST https://api.openai.com/v1/organization/invites \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "email": "anotheruser@example.com",
      "role": "reader",
      "projects": [
        {
          "id": "project-xyz",
          "role": "member"
        },
        {
          "id": "project-abc",
          "role": "owner"
        }
      ]
  }'
```

### Response

```json
{
  "object": "organization.invite",
  "id": "invite-def",
  "email": "anotheruser@example.com",
  "role": "reader",
  "status": "pending",
  "invited_at": 1711471533,
  "expires_at": 1711471533,
  "accepted_at": null,
  "projects": [
    {
      "id": "project-xyz",
      "role": "member"
    },
    {
      "id": "project-abc",
      "role": "owner"
    }
  ]
}
```

## Retrieve invite

Retrieves an invite.

### Example request

```bash
curl https://api.openai.com/v1/organization/invites/invite-abc \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
    "object": "organization.invite",
    "id": "invite-abc",
    "email": "user@example.com",
    "role": "owner",
    "status": "accepted",
    "invited_at": 1711471533,
    "expires_at": 1711471533,
    "accepted_at": 1711471533
}
```

## Delete invite

Delete an invite. If the invite has already been accepted, it cannot be deleted.

### Example request

```bash
curl -X DELETE https://api.openai.com/v1/organization/invites/invite-abc \
  -H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
    "object": "organization.invite.deleted",
    "id": "invite-abc",
    "deleted": true
}
```

## The invite object

Represents an individual `invite` to the organization.

### Parameters

#### accepted_at - integer
The Unix timestamp (in seconds) of when the invite was accepted.

#### email - string
The email address of the individual to whom the invite was sent

#### expires_at - integer
The Unix timestamp (in seconds) of when the invite expires.

#### id - string
The identifier, which can be referenced in API endpoints

#### invited_at - integer
The Unix timestamp (in seconds) of when the invite was sent.

#### object - string
The object type, which is always `organization.invite`

#### projects - array
The projects that were granted membership upon acceptance of the invite.

- **id - string**
  Project's public ID

- **role - string**
  Project membership role

#### role - string
`owner` or `reader`

#### status - string
`accepted`,`expired`, or `pending`

### OBJECT The invite object

```json
{
  "object": "organization.invite",
  "id": "invite-abc",
  "email": "user@example.com",
  "role": "owner",
  "status": "accepted",
  "invited_at": 1711471533,
  "expires_at": 1711471533,
  "accepted_at": 1711471533,
  "projects": [
    {
      "id": "project-xyz",
      "role": "member"
    }
  ]
}
```