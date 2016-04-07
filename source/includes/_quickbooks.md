# QuickBooks API

## Authentication
Authenticated through basic OAuth 1.0 flow. This will require us to go through three endpoints in order to have a user authorized. The flow is similiar to wait is explained in the freshbooks auth.

Intuit OAuth Service URLs

* Request token URL is:  https://oauth.intuit.com/oauth/v1/get_request_token
* User authorization URL is: https://appcenter.intuit.com/Connect/Begin
* Access Token Request URL is: https://oauth.intuit.com/oauth/v1/get_access_token

From the redirect to the OAuth callback URL, get these parameters:
* realmId
* oauth_verifier
* oauth_token


`note:` realmId is the companyId we will use to query the api by.

`additional note:` Implement an auto-renew strategy for the OAuth access token and the access token secret  because they expire after 180 days. 

## Basic API
The API is pretty advanced but if we are only going to use this for time tracking then we really only need one endpoint to hit as you do everything through sql queries.

###Query a timeactivity object

Production Base URL: https://quickbooks.api.intuit.com

```json
Operation: GET /v3/company/<companyID>/query?query=<selectStatement>
Content type: application/text

SAMPLE QUERY
select * from TimeActivity where TxnDate > '2014-09-14'
```

```json
{
  "TimeActivity": {
    "TxnDate": "2014-09-16",
    "NameOf": "Employee",
    "EmployeeRef": {
      "value": "54",
      "name": "John Johnson"
    },
    "CustomerRef": {
      "value": "1",
      "name": "Amy's Bird Sanctuary"
    },
    "ItemRef": {
      "value": "4",
      "name": "Design"
    },
    "BillableStatus": "Billable",
    "Taxable": false,
    "HourlyRate": 75,
    "Hours": 5,
    "Minutes": 0,
    "Description": "Updated descirption",
    "domain": "QBO",
    "sparse": false,
    "Id": "3",
    "SyncToken": "1",
    "MetaData": {
      "CreateTime": "2014-09-17T11:53:15-07:00",
      "LastUpdatedTime": "2015-07-28T11:59:41-07:00"
    }
  },
  "time": "2015-07-28T11:59:41.178-07:00"
}
```
``

