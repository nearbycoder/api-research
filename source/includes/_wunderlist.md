# Wunderlist API

## Authentication
Authenticated through basic OAuth. This will work much like our Harvest Implemenation. However the access_token does not seem to expire so no need to go fetch a new token

### User Authorization Endpoint

#### Redirect users to request Wunderlist access
`GET https://www.wunderlist.com/oauth/authorize?client_id=ID&redirect_uri=URL&state=RANDOM`

Parameter | Description
--------- | -----------
client_id | required The Client ID you received from Wunderlist when you registered your application.
redirect_uri | required The URL in your app where users will be sent after authorization. See details below about redirect urls.
state | required An unguessable random string. It is used to protect against cross-site request forgery attacks.

#### Exchange code for an access token
`POST https://www.wunderlist.com/oauth/access_token`

```json
Response Back
{
  "access_token": "976d16a81ccf621a654fcc23193b09498b220e89eb72ced3"
}
```

Parameter | Description
--------- | -----------
client_id | required The Client ID you received from Wunderlist when you registered your application.
client_secret | required. The client secret you received from Wunderlist when you registered.
code | required. The code you received as a response to Step 1.

## Basic API

```json
GET a.wunderlist.com/api/v1/lists
Status: 200
{
  "id": 83526310,
  "created_at": "2013-08-30T08:29:46.203Z",
  "title": "Read Later",
  "list_type": "list",
  "type": "list",
  "revision": 10
}
```

### Get all lists

#### Request

`GET a.wunderlist.com/api/v1/lists`


### Get all tasks in a list
```json
GET a.wunderlist.com/api/v1/tasks
Status: 200
[
  {
    "id": 409233670,
    "assignee_id": 12345,
    "assigner_id": 5432,
    "created_at": "2013-08-30T08:36:13.273Z",
    "created_by_id": 6234958,
    "due_date": "2013-08-30",
    "list_id": 123,
    "revision": 1,
    "starred": true,
    "title": "Hello"
  }
]
```
#### Request

`GET a.wunderlist.com/api/v1/tasks`

Parameter | Description
--------- | -----------
list_id | required


### Get all subtasks in a task
```json
GET a.wunderlist.com/api/v1/subtasks
Status: 200
json
[
  {
    "id": 409233670,
    "task_id": 123,
    "created_at": "2013-08-30T08:36:13.273Z",
    "created_by_id": 123456,
    "revision": 1,
    "title": "Hello"
  }
]
```
#### Request

`GET a.wunderlist.com/api/v1/subtasks`

Parameter | Description
--------- | -----------
task_id | required

