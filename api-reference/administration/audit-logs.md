# Audit logs

Logs of user actions and configuration changes within this organization. To log events, an Organization Owner must activate logging in the [Data Controls Settings](/settings/organization/data-controls/data-retention). Once activated, for security reasons, logging cannot be deactivated.

## List audit logs

List user actions and configuration changes within this organization.

### Example request

```bash
curl https://api.openai.com/v1/organization/audit_logs \
-H "Authorization: Bearer $OPENAI_ADMIN_KEY" \
-H "Content-Type: application/json"
```

### Response

```json
{
    "object": "list",
    "data": [
        {
            "id": "audit_log-xxx_yyyymmdd",
            "type": "project.archived",
            "effective_at": 1722461446,
            "actor": {
                "type": "api_key",
                "api_key": {
                    "type": "user",
                    "user": {
                        "id": "user-xxx",
                        "email": "user@example.com"
                    }
                }
            },
            "project.archived": {
                "id": "proj_abc"
            },
        },
        {
            "id": "audit_log-yyy__20240101",
            "type": "api_key.updated",
            "effective_at": 1720804190,
            "actor": {
                "type": "session",
                "session": {
                    "user": {
                        "id": "user-xxx",
                        "email": "user@example.com"
                    },
                    "ip_address": "127.0.0.1",
                    "user_agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36",
                    "ja3": "a497151ce4338a12c4418c44d375173e",
                    "ja4": "q13d0313h3_55b375c5d22e_c7319ce65786",
                    "ip_address_details": {
                      "country": "US",
                      "city": "San Francisco",
                      "region": "California",
                      "region_code": "CA",
                      "asn": "1234",
                      "latitude": "37.77490",
                      "longitude": "-122.41940"
                    }
                }
            },
            "api_key.updated": {
                "id": "key_xxxx",
                "data": {
                    "scopes": ["resource_2.operation_2"]
                }
            },
        }
    ],
    "first_id": "audit_log-xxx__20240101",
    "last_id": "audit_log_yyy__20240101",
    "has_more": true
}
```

## The audit log object

A log of a user action or configuration change within this organization.

### Parameters

#### actor - object
The actor who performed the audit logged action.

- **api_key - object**
  The API Key used to perform the audit logged action.

  - **id - string**
    The tracking id of the API key.

  - **service_account - object**
    The service account that performed the audit logged action.

    - **id - string**
      The service account id.

  - **type - string**
    The type of API key. Can be either `user` or `service_account`.

  - **user - object**
    The user who performed the audit logged action.

    - **email - string**
      The user email.

    - **id - string**
      The user id.

- **session - object**
  The session in which the audit logged action was performed.

  - **ip_address - string**
    The IP address from which the action was performed.

  - **user - object**
    The user who performed the audit logged action.

    - **email - string**
      The user email.

    - **id - string**
      The user id.

- **type - string**
  The type of actor. Is either `session` or `api_key`.

#### api_key.created - object
The details for events with this `type`.

- **data - object**
  The payload used to create the API key.

  - **scopes - array**
    A list of scopes allowed for the API key, e.g. `["api.model.request"]`

- **id - string**
  The tracking ID of the API key.

#### api_key.deleted - object
The details for events with this `type`.

- **id - string**
  The tracking ID of the API key.

#### api_key.updated - object
The details for events with this `type`.

- **changes_requested - object**
  The payload used to update the API key.

  - **scopes - array**
    A list of scopes allowed for the API key, e.g. `["api.model.request"]`

- **id - string**
  The tracking ID of the API key.

#### certificate.created - object
The details for events with this `type`.

- **id - string**
  The certificate ID.

- **name - string**
  The name of the certificate.

#### certificate.deleted - object
The details for events with this `type`.

- **certificate - string**
  The certificate content in PEM format.

- **id - string**
  The certificate ID.

- **name - string**
  The name of the certificate.

#### certificate.updated - object
The details for events with this `type`.

- **id - string**
  The certificate ID.

- **name - string**
  The name of the certificate.

#### certificates.activated - object
The details for events with this `type`.

- **certificates - array**
  The certificate ID.

  - **id - string**
    The certificate ID.

  - **name - string**
    The name of the certificate.

#### certificates.deactivated - object
The details for events with this `type`.

- **certificates - array**
  The certificate ID.

  - **id - string**
    The certificate ID.

  - **name - string**
    The name of the certificate.

#### checkpoint.permission.created - object
The project and fine-tuned model checkpoint that the checkpoint permission was created for.

- **data - object**
  The payload used to create the checkpoint permission.

  - **fine_tuned_model_checkpoint - string**
    The ID of the fine-tuned model checkpoint.

  - **project_id - string**
    The ID of the project that the checkpoint permission was created for.

- **id - string**
  The ID of the checkpoint permission.

#### checkpoint.permission.deleted - object
The details for events with this `type`.

- **id - string**
  The ID of the checkpoint permission.

#### effective_at - integer
The Unix timestamp (in seconds) of the event.

#### external_key.registered - object
The details for events with this `type`.

- **data - object**
  The configuration for the external key.

- **id - string**
  The ID of the external key configuration.

#### external_key.removed - object
The details for events with this `type`.

- **id - string**
  The ID of the external key configuration.

#### group.created - object
The details for events with this `type`.

- **data - object**
  Information about the created group.

  - **group_name - string**
    The group name.

- **id - string**
  The ID of the group.

#### group.deleted - object
The details for events with this `type`.

- **id - string**
  The ID of the group.

#### group.updated - object
The details for events with this `type`.

- **changes_requested - object**
  The payload used to update the group.

  - **group_name - string**
    The updated group name.

- **id - string**
  The ID of the group.

#### id - string
The ID of this log.

#### invite.accepted - object
The details for events with this `type`.

- **id - string**
  The ID of the invite.

#### invite.deleted - object
The details for events with this `type`.

- **id - string**
  The ID of the invite.

#### invite.sent - object
The details for events with this `type`.

- **data - object**
  The payload used to create the invite.

  - **email - string**
    The email invited to the organization.

  - **role - string**
    The role the email was invited to be. Is either `owner` or `member`.

- **id - string**
  The ID of the invite.

#### ip_allowlist.config.activated - object
The details for events with this `type`.

- **configs - array**
  The configurations that were activated.

  - **id - string**
    The ID of the IP allowlist configuration.

  - **name - string**
    The name of the IP allowlist configuration.

#### ip_allowlist.config.deactivated - object
The details for events with this `type`.

- **configs - array**
  The configurations that were deactivated.

  - **id - string**
    The ID of the IP allowlist configuration.

  - **name - string**
    The name of the IP allowlist configuration.

#### ip_allowlist.created - object
The details for events with this `type`.

- **allowed_ips - array**
  The IP addresses or CIDR ranges included in the configuration.

- **id - string**
  The ID of the IP allowlist configuration.

- **name - string**
  The name of the IP allowlist configuration.

#### ip_allowlist.deleted - object
The details for events with this `type`.

- **allowed_ips - array**
  The IP addresses or CIDR ranges that were in the configuration.

- **id - string**
  The ID of the IP allowlist configuration.

- **name - string**
  The name of the IP allowlist configuration.

#### ip_allowlist.updated - object
The details for events with this `type`.

- **allowed_ips - array**
  The updated set of IP addresses or CIDR ranges in the configuration.

- **id - string**
  The ID of the IP allowlist configuration.

#### login.failed - object
The details for events with this `type`.

- **error_code - string**
  The error code of the failure.

- **error_message - string**
  The error message of the failure.

#### login.succeeded - object
This event has no additional fields beyond the standard audit log attributes.

#### logout.failed - object
The details for events with this `type`.

- **error_code - string**
  The error code of the failure.

- **error_message - string**
  The error message of the failure.

#### logout.succeeded - object
This event has no additional fields beyond the standard audit log attributes.

#### organization.updated - object
The details for events with this `type`.

- **changes_requested - object**
  The payload used to update the organization settings.

  - **api_call_logging - string**
    How your organization logs data from supported API calls. One of `disabled`, `enabled_per_call`, `enabled_for_all_projects`, or `enabled_for_selected_projects`

  - **api_call_logging_project_ids - string**
    The list of project ids if api_call_logging is set to `enabled_for_selected_projects`

  - **description - string**
    The organization description.

  - **name - string**
    The organization name.

  - **threads_ui_visibility - string**
    Visibility of the threads page which shows messages created with the Assistants API and Playground. One of `ANY_ROLE`, `OWNERS`, or `NONE`.

  - **title - string**
    The organization title.

  - **usage_dashboard_visibility - string**
    Visibility of the usage dashboard which shows activity and costs for your organization. One of `ANY_ROLE` or `OWNERS`.

- **id - string**
  The organization ID.

#### project - object
The project that the action was scoped to. Absent for actions not scoped to projects. Note that any admin actions taken via Admin API keys are associated with the default project.

- **id - string**
  The project ID.

- **name - string**
  The project title.

#### project.archived - object
The details for events with this `type`.

- **id - string**
  The project ID.

#### project.created - object
The details for events with this `type`.

- **data - object**
  The payload used to create the project.

  - **name - string**
    The project name.

  - **title - string**
    The title of the project as seen on the dashboard.

- **id - string**
  The project ID.

#### project.deleted - object
The details for events with this `type`.

- **id - string**
  The project ID.

#### project.updated - object
The details for events with this `type`.

- **changes_requested - object**
  The payload used to update the project.

  - **title - string**
    The title of the project as seen on the dashboard.

- **id - string**
  The project ID.

#### rate_limit.deleted - object
The details for events with this `type`.

- **id - string**
  The rate limit ID

#### rate_limit.updated - object
The details for events with this `type`.

- **changes_requested - object**
  The payload used to update the rate limits.

  - **batch_1_day_max_input_tokens - integer**
    The maximum batch input tokens per day. Only relevant for certain models.

  - **max_audio_megabytes_per_1_minute - integer**
    The maximum audio megabytes per minute. Only relevant for certain models.

  - **max_images_per_1_minute - integer**
    The maximum images per minute. Only relevant for certain models.

  - **max_requests_per_1_day - integer**
    The maximum requests per day. Only relevant for certain models.

  - **max_requests_per_1_minute - integer**
    The maximum requests per minute.

  - **max_tokens_per_1_minute - integer**
    The maximum tokens per minute.

- **id - string**
  The rate limit ID

#### role.assignment.created - object
The details for events with this `type`.

- **id - string**
  The identifier of the role assignment.

- **principal_id - string**
  The principal (user or group) that received the role.

- **principal_type - string**
  The type of principal (user or group) that received the role.

- **resource_id - string**
  The resource the role assignment is scoped to.

- **resource_type - string**
  The type of resource the role assignment is scoped to.

#### role.assignment.deleted - object
The details for events with this `type`.

- **id - string**
  The identifier of the role assignment.

- **principal_id - string**
  The principal (user or group) that had the role removed.

- **principal_type - string**
  The type of principal (user or group) that had the role removed.

- **resource_id - string**
  The resource the role assignment was scoped to.

- **resource_type - string**
  The type of resource the role assignment was scoped to.

#### role.created - object
The details for events with this `type`.

- **id - string**
  The role ID.

- **permissions - array**
  The permissions granted by the role.

- **resource_id - string**
  The resource the role is scoped to.

- **resource_type - string**
  The type of resource the role belongs to.

- **role_name - string**
  The name of the role.

#### role.deleted - object
The details for events with this `type`.

- **id - string**
  The role ID.

#### role.updated - object
The details for events with this `type`.

- **changes_requested - object**
  The payload used to update the role.

  - **description - string**
    The updated role description, when provided.

  - **metadata - object**
    Additional metadata stored on the role.

  - **permissions_added - array**
    The permissions added to the role.

  - **permissions_removed - array**
    The permissions removed from the role.

  - **resource_id - string**
    The resource the role is scoped to.

  - **resource_type - string**
    The type of resource the role belongs to.

  - **role_name - string**
    The updated role name, when provided.

- **id - string**
  The role ID.

#### scim.disabled - object
The details for events with this `type`.

- **id - string**
  The ID of the SCIM was disabled for.

#### scim.enabled - object
The details for events with this `type`.

- **id - string**
  The ID of the SCIM was enabled for.

#### service_account.created - object
The details for events with this `type`.

- **data - object**
  The payload used to create the service account.

  - **role - string**
    The role of the service account. Is either `owner` or `member`.

- **id - string**
  The service account ID.

#### service_account.deleted - object
The details for events with this `type`.

- **id - string**
  The service account ID.

#### service_account.updated - object
The details for events with this `type`.

- **changes_requested - object**
  The payload used to updated the service account.

  - **role - string**
    The role of the service account. Is either `owner` or `member`.

- **id - string**
  The service account ID.

#### type - string
The event type.

#### user.added - object
The details for events with this `type`.

- **data - object**
  The payload used to add the user to the project.

  - **role - string**
    The role of the user. Is either `owner` or `member`.

- **id - string**
  The user ID.

#### user.deleted - object
The details for events with this `type`.

- **id - string**
  The user ID.

#### user.updated - object
The details for events with this `type`.

- **changes_requested - object**
  The payload used to update the user.

  - **role - string**
    The role of the user. Is either `owner` or `member`.

- **id - string**
  The project ID.

### OBJECT The audit log object

```json
{
    "id": "req_xxx_20240101",
    "type": "api_key.created",
    "effective_at": 1720804090,
    "actor": {
        "type": "session",
        "session": {
            "user": {
                "id": "user-xxx",
                "email": "user@example.com"
            },
            "ip_address": "127.0.0.1",
            "user_agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36"
        }
    },
    "api_key.created": {
        "id": "key_xxxx",
        "data": {
            "scopes": ["resource.operation"]
        }
    }
}
```