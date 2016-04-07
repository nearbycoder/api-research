# Asana API


## Authentication
Authenticated through basic OAuth. This will work much like our Harvest Implemenation. We will need to check against the token for expiry first before making our api calls.

### User Authorization Endpoint

#### Redirect users to request Asana access
`GET https://app.asana.com/-/oauth_authorize`

Parameter | Description
--------- | -----------
client_id | required The Client ID uniquely identifies the application making the request.
redirect_uri | required The URI to redirect to on success or error. This must match the Redirect URL specified in the application settings.
response_type | required Must be one of either code (if using the Authorization Code Grant flow) or token (if using the Implicit Grant flow). Other flows are currently not supported.
state | required Encodes state of the app, which will be returned verbatim in the response and can be used to match the response up to a given request.

#### Response

Parameter | Description
--------- | -----------
code | If response_type=code in the request, this is the code your app can exchange for a token
token | If response_type=token in the request, this is the token your app can use to make requests of the API
state | The state parameter that was sent with the authorizing request

### Token Exchange Endpoint

#### Request
`POST https://app.asana.com/-/oauth_token`

Parameter | Description
--------- | -----------
grant_type | required One of authorization_code, refresh_token or password. See below for more details.
client_id | required The Client ID uniquely identifies the application making the request.
client_secret | required The Client Secret belonging to the app, found in the details pane of the developer console.
redirect_uri | required Must match the redirect_uri specified in the original request.
code | sometimes required If grant_type=authorization_code this is the code you are exchanging for an authorization token.
refresh_token | sometimes required If grant_type=refresh_token this is the refresh token you are using to be granted a new access token.
username | sometimes required If grant_type=password this is the username you are exchanging for an authorization token.

#### Response
Parameter | Description
--------- | -----------
access_token | The token to use in future requests against the API
expires_in | The number of seconds the token is valid, typically 3600 (one hour)
token_type | The type of token, in our case, bearer
refresh_token | If exchanging a code, a long-lived token that can be used to get new access tokens when old ones expire.
data | A JSON object encoding a few key fields about the logged-in user, currently id, name, and email.

## Basic API

### Get Workspaces
```json
{
  "data": [
    {
      "id": 82341452549678,
      "name": "weekend-devs",
      "is_organization": false
    },
    {
      "id": 498346170860,
      "name": "Personal Projects",
      "is_organization": true
    }
  ],
  "next_page": null
}
```
#### Request
`GET /workspaces`

`GET /workspaces?opt_fields=is_organization,id,name&limit=100`

Parameter | Description
--------- | -----------
id | Read-only. Globally unique ID of the workspace.
name | The name of the workspace.
is_organization | Whether the workspace is an organization.

### Get Projects for Workspaces

```json
{
  "data": [
    {
      "id": 82341455320268,
      "owner": {
        "id": 82341441964652
      },
      "due_date": null,
      "current_status": null,
      "public": true,
      "name": "DonutRun Frontend",
      "notes": "",
      "archived": false,
      "workspace": {
        "id": 82341452549678
      },
      "color": null
    },
    {
      "id": 82341455320269,
      "owner": {
        "id": 82341441964652
      },
      "due_date": null,
      "current_status": null,
      "public": true,
      "name": "DonutRun Backend",
      "notes": "",
      "archived": false,
      "workspace": {
        "id": 82341452549678
      },
      "color": null
    }
  ],
  "next_page": null
}
```
#### Request
`GET /workspaces/:workspace/projects`

`GET /workspaces/82341452549678/projects?opt_fields=name,id,owner,current_status,due_date,archived,public,color,notes,workspace,team&limit=100`

Parameter | Description
--------- | -----------
name | Name of the project. This is generally a short sentence fragment that fits on a line in the UI for maximum readability. However, it can be longer.
id | Read-only. Globally unique ID of the project.
owner | The current owner of the project, may be null.
current_status | A description of the project’s status containing a color (must be either null or one of: green, yellow, red) and a short description.
due_date | The day on which this project is due. This takes a date with format YYYY-MM-DD.
created_at | Read-only. The time at which this project was created.
modified_at | Read-only. The time at which this project was last modified. 
archived | True if the project is archived, false if not. Archived projects do not show in the UI by default and may be treated differently for queries.
public | True if the project is public to the organization. If false, do not share this project with other users in this organization without explicitly checking to see if they have access.
members | Read-only. Array of users who are members of this project.
followers | Read-only. Array of users following this project. Followers are a subset of members who receive all notifcations for a project, the default notification setting when adding members to a project in-product.
color | Color of the project. Must be either null or one of: dark-pink, dark-green, dark-blue, dark-red, dark-teal, dark-brown, dark-orange, dark-purple, dark-warm-gray, light-pink, light-green, light-blue, light-red, light-teal, light-yellow, light-orange, light-purple, light-warm-gray.
notes | More detailed, free-form textual information associated with the project.
workspace | The workspace or organization this project is associated with.
team | The team that this project is shared with.

### Get Tasks for Project

```json
{
  "data": [
    {
      "id": 82341455320278,
      "created_at": "2016-01-20T16:23:57.243Z",
      "modified_at": "2016-01-20T16:23:58.850Z",
      "name": "BackLog:",
      "notes": "",
      "completed": false,
      "assignee_status": "upcoming",
      "completed_at": null,
      "due_on": null,
      "due_at": null,
      "assignee": null,
      "hearted": false,
      "followers": [
        {
          "id": 82341441964652
        }
      ]
    },
    {
      "id": 82341455320286,
      "created_at": "2016-01-20T16:24:00.314Z",
      "modified_at": "2016-04-06T22:26:16.653Z",
      "name": "Finish Strip API integration",
      "notes": "testing",
      "completed": false,
      "assignee_status": "inbox",
      "completed_at": null,
      "due_on": null,
      "due_at": null,
      "hearted": false,
      "assignee": {
        "id": 82335341048251
      },
      "followers": [
        {
          "id": 82341441964652
        },
        {
          "id": 82335341048251
        }
      ]
    }
  ]
}
```

#### Request
`GET/projects/:projectId/tasks`

`GET /projects/82341455320268/tasks?opt_fields=id,assignee,assignee_status,created_at,completed,completed_at,due_on,due_at,followers,hearted,modified_at,notes,name&limit=10`

Parameter | Description
--------- | -----------
id | Globally unique ID of the task.
assignee | User to which this task is assigned, or null if the task is unassigned.
assignee_status | Scheduling status of this task for the user it is assigned to. 
created_at | The time at which this task was created.
completed | True if the task is currently marked complete, false if not.
completed_at | The time at which this task was completed, or null if the task is incomplete.
due_on | Date on which this task is due, or null if the task has no due date. 
due_at | Date and time on which this task is due, or null if the task has no due time.
external | The external field allows you to store app-specific metadata on tasks, including an id that can be used to retrieve tasks and a data blob that can store app-specific character strings. 
followers | Array of users following this task.
hearted | True if the task is hearted by the authorized user, false if not.
hearts | Array of users who have hearted this task.
modfied_at | The time at which this task was last modified.
name | Name of the task.
notes | More detailed, free-form textual information associated with the task.
num_hearts | The number of users who have hearted this task.
projects | Array of projects this task is associated with.
parent | The parent of this task, or null if this is not a subtask.
workspace | The workspace this task is associated with.
membership | Array of projects this task is associated with and the section it is in.
tags |  Array of tags associated with this task.

