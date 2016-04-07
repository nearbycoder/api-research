# FreshBooks API
### side note: The API docs at FreshBooks is super sparse.
### bigger side note: xml api only! 

## Authentication
Authenticated through basic OAuth 1.0 flow. This will require us to go through three endpoints in order to have a user authorized. The access_token does not seem to expire so no need to go fetch a new token

### User Authorization Endpoint

#### Request Token URL
You must use HTTP POST when requesting an unauthorized Request Token. Because FreshBooks uses 1.0 Revision A of the OAuth specification, the ‘oauth_callback’ parameter must be specified.

`https://sample.freshbooks.com/oauth/oauth_request.php`


#### Redirect users to request FreshBooks access
HTTP GET should be used when sending a user to the User Authorization URL to authorize an OAuth Request Token. The user will then enter their FreshBooks account credentials using a secure form and the information will be sent to FreshBooks using HTTP POST. Upon verification of the user credentials, FreshBooks will redirect the user to the URL specified in the ‘oauth_callback’ parameter supplied when requesting an unauthorized Request Token.

`GET https://#{sample}.freshbooks.com/oauth/oauth_authorize.php`

```json
Response Back
{
  "access_token": "976d16a81ccf621a654fcc23193b09498b220e89eb72ced3"
}
```
#### Exchange code for an access token

You must use HTTP POST when exchanging an authorized Request Token for an Access Token. Once again, because FreshBooks uses 1.0 Revision A of the OAuth specification, the ‘oauth_verifier’ parameter must be specified.

`POST https://#{sample}.freshbooks.com/oauth/oauth_access.php`

## Basic API

```json
Get Clients
request XML to JSON
{
  "request": {
    "-method": "client.list",
    "email": "janedoe@freshbooks.com",
    "username": "janedoe",
    "updated_from": "2009-01-01 00:00:00",
    "updated_to": "2009-12-01 00:00:00",
    "page": "1",
    "per_page": "15",
    "folder": "active",
    "notes": "email"
  }
}
```

### Get Clients

`https://#{sample}.freshbooks.com/api/2.1/xml-in`

```json
Get Clients
response XML to JSON
{
  "response": {
    "-xmlns": "https://www.freshbooks.com/api/",
    "-status": "ok",
    "clients": {
      "-page": "1",
      "-per_page": "15",
      "-pages": "3",
      "-total": "42",
      "client": {
        "client_id": "13",
        "first_name": "Jane",
        "last_name": "Doe",
        "organization": "ABC Corp",
        "email": "janedoe@freshbooks.com",
        "username": "janedoe",
        "contacts": {
          "contact_id": "15",
          "username": "alex",
          "email": "johnmcnicol@freshbooks.com"
        },
        "work_phone": "(555) 123-4567",
        "home_phone": "(555) 234-5678",
        "language": "en",
        "currency_code": "USD",
        "credit": {
          "-currency": "USD",
          "-deprecated": "true",
          "#text": "123.45"
        },
        "credits": {
          "credit": [
            {
              "-currency": "USD",
              "#text": "123.45"
            },
            {
              "-currency": "CAD",
              "#text": "43.45"
            },
            {
              "-currency": "EUR",
              "#text": "12.00"
            }
          ]
        },
        "notes": "Prefers email over phone calls",
        "p_street1": "123 Fake St.",
        "p_street2": "Unit 555",
        "p_city": "New York",
        "p_state": "New York",
        "p_country": "United States",
        "p_code": "553132",
        "url": {
          "-deprecated": "true",
          "#text": "https://sample.freshbooks.com/view/Vbbfs324trTkxer"
        },
        "auth_url": {
          "-deprecated": "true",
          "#text": "https://sample.freshbooks.com/clients/13"
        },
        "links": {
          "client_view": "https://sample.freshbooks.com/view/Vbbfs324trTkxer",
          "view": "https://sample.freshbooks.com/clients/13",
          "statement": "https://sample.freshbooks.com/view/3EbEwAkJGJkuJHSN"
        },
        "updated": "2009-08-12 09:00:00",
        "folder": "active"
      },
      "#text": "  
    ...  
  "
    }
  }
}
```

### Get Client Projects

`https://#{sample}.freshbooks.com/api/2.1/xml-in`

Filters
* client_id – return only projects assigned to this client
* task_id – return only projects with the given task_id

```json
Get Client Projects
request XML to JSON
{
  "request": {
    "-method": "project.list",
    "page": "1",
    "per_page": "15"
  }
}
```
```json
Get Client Projects
response XML to JSON
{
  "response": {
    "-xmlns": "https://www.freshbooks.com/api/",
    "-status": "ok",
    "parsererror": {
      "-style": "display: block; white-space: pre; border: 2px solid #c77; padding: 0 1em 0 1em; margin: 1em; background-color: #fdd; color: black",
      "h3": [
        "This page contains the following errors:",
        "Below is a rendering of the page up to the first error."
      ],
      "div": {
        "-style": "font-family:monospace;font-size:12px",
        "#text": "error on line 41 at column 18: StartTag: invalid element name
"
      }
    },
    "projects": {
      "-page": "1",
      "-per_page": "15",
      "-pages": "1",
      "-total": "5",
      "project": {
        "project_id": "6",
        "name": "Super Fun Project",
        "rate": "11000",
        "bill_method": "flat-rate",
        "client_id": "119",
        "hour_budget": "100",
        "tasks": {
          "task": [
            { "task_id": "5" },
            { "task_id": "12" },
            {
              "task_id": "6",
              "rate": "25"
            },
            {
              "task_id": "7",
              "rate": "35"
            }
          ]
        },
        "staff": {
          "staff": [
            { "staff_id": "1" },
            { "staff_id": "2" },
            { "staff_id": "3" }
          ],
          "staff_id": [
            {
              "-deprecated": "true",
              "#text": "1"
            },
            {
              "-deprecated": "true",
              "#text": "2"
            },
            {
              "-deprecated": "true",
              "#text": "3"
            }
          ]
        }
      }
    }
  }
}
```
### Get Project Time Entries

`https://#{sample}.freshbooks.com/api/2.1/xml-in`

Filters
* project_id
* task_id
* date_from
* date_to

```json
Get Projects Time Entries
request XML to JSON
{
  "request": {
    "-method": "time_entry.create",
    "time_entry": {
      "project_id": "1",
      "task_id": "1",
      "staff_id": "1",
      "hours": "4.5",
      "notes": "Phone consultation",
      "date": "2007-01-01"
    }
  }
}
```

```json
Get Projects Time Entries
response XML to JSON
{
  "response": {
    "-xmlns": "https://www.freshbooks.com/api/",
    "-status": "ok",
    "time_entries": {
      "-page": "1",
      "-per_page": "15",
      "-pages": "10",
      "-total": "142",
      "time_entry": [
        {
          "time_entry_id": "211",
          "staff_id": "1",
          "project_id": "1",
          "task_id": "1",
          "hours": "2",
          "date": "2009-03-13",
          "notes": "Sample Notes",
          "billed": "0"
        },
        "  
    ...  
    "
      ],
      "#text": "  
    ...  
  "
    }
  }
}
```