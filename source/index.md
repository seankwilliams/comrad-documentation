---
title: Comrad API v1
language_tabs:
  - javascript: JavaScript
language_clients:
  - javascript: request
toc_footers: []
includes: []
search: false
highlight_theme: darkula
headingLevel: 2

---

<h1 id="comrad-api">Comrad API v1</h1>

> Scroll down for code samples, example requests and responses. Select a language for code samples from the tabs above or the mobile navigation menu.

The API for Comrad, source software for radio stations that handles playlist logging, traffic management, reporting, and more

# Getting Started

> Sample code 

```javascript
var apiKey = 'your api key (it will be a UUID)';
var comradUrl = 'http://localhost:5000/v1'; //If running Comrad locally. Otherwise, replace with your API URL.

let startDate = new Date(); // the current date/time
let endDate = new Date(startDate.getTime() + (3 * 60 * 60 * 1000) ); // three hours from now

fetch(
    comradUrl + '/events/shows?' +
    'startDate=' + encodeURIComponent(startDate.toJSON()) + '&' +
    'endDate=' + encodeURIComponent(endDate.toJSON()), 
    { 'headers': {'Authorization': apiKey } }
  ).then(response => {
    if (!response.ok) {
      //API call was unsuccessful
      console.log('API call unsuccessful. Status code: ' + response.status);
      
      //422 errors usually include a message with the reason the API call was unsuccessful.
      if (response.status === 422) {
        response.json().then(jsonResponse => {
          if (jsonResponse.message) {
            console.log('Details:', jsonResponse.message);
          } else {
            console.log('No additional error details provided');
          }
        });
      }
    } else {
      response.json().then(jsonResponse => {
        // output results to the console
        console.log('Results:');
        jsonResponse.forEach(show => {
          console.log(show.show_details.title + ' starts at ' + show.start_time_utc + ' (UTC) and ends at ' + show.end_time_utc + ' (UTC)');
        });
      });
    }
  });
```

To get started using the API, we will walk through how to authenticate with the API and how to make an API call. We will make an API call to get all shows that are occurring in the next three hours. See the example code accompanying this section for reference.

First, we will create an API key with the `DJ` role, which has the least amount of permissions of the available roles. To create an API key:

1. Log into Comrad with an account that has the `Admin` role
2. In the left menu, click on the Users icon at the bottom
3. At the top of the page, click `Add User`
4. For First Name, enter your name. For last name, enter "API Usage". 
5. For email address, enter an email address that does not already have an account in Comrad. This does not have to be a real email address, for instance, you could use `api.test@getcomrad.org`
6. For Roles, choose `DJ`
7. Enter a password
8. Click Submit
9. Now, you'll be back on the users page. Search for your new user, and click on that user's row in the search results table.
10. Scroll down and click `Create API` on the left.
11. Now, at the top of the screen, you'll have a message with your API key. Record the API key and keep it somewhere safe: Comrad will not display it again.

Now, the API key is created. Plug that value into the example code in the `apiKey` variable. In the example code, you'll also want to replace `comradUrl`'s value with the URL of your API. If you're running Comrad locally, that's `http://localhost:5000/v1`, but if you have Comrad hosted on a server, the URL would usually be `https://yourdomain.com/api/v1`.

Once those values are replaced, you can run the sample code to retrieve events in the next three hours. The code is makinng a `GET` request to `/events/shows` with the following parameters:

- `startDate`: the current date/time
- `endDate`: three hours in the future from the current date/time

The code passes your API key in the `Authorization` header.

The example code will write out all of the events in the next three hours. If an error occurs with the API request, a status code will be printed. A `422` error means there's something wrong with the request you provided - usually, a validation error. There will be more details about this in the API response. The example code writes out any validation messages that come back from the server. A `401` error means your credentials are not valid. A `403` error means you have valid credentials, but your API key does not have the proper permissions to access the endpoint. A `404` error means "not found", and if you get that error on this example code, it probably means that you have the wrong value for `comradUrl` or, if you're trying to test locally, your local development environment is not running.

# Authentication

* API Key (ApiKeyAuth)
    - Parameter Name: **Authorization**, in: header. Place the API key in the authorization header

<h1 id="comrad-api-access-control">Access Control</h1>

## Create

<a id="opIdCreateAccessControl"></a>

> Code samples

```javascript
const inputBody = '{
  "role": "Admin",
  "resource": "TestResource",
  "action": "create:any",
  "attributes": "*"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/access-control',
{
  method: 'POST',
  body: inputBody,
  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`POST /access-control`

Create a new AccessControl record

The following roles can access this API endpoint: `Admin`, `Full Access`

> Body parameter

```json
{
  "role": "Admin",
  "resource": "TestResource",
  "action": "create:any",
  "attributes": "*"
}
```

<h3 id="create-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|[AccessControl](#schemaaccesscontrol)|false|none|

> Example responses

> 200 Response

```json
{
  "_id": "5f3d436c50dacd5124dc6ae6",
  "role": "Admin",
  "resource": "TestResource",
  "action": "create:any",
  "attributes": "*",
  "__v": 0
}
```

<h3 id="create-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|The AccessControl record was created successfully|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Find All

<a id="opIdFindAllAccessControls"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/access-control',
{
  method: 'GET',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`GET /access-control`

Retrieve all AccessControl records

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

```json
[
  {
    "_id": "5f35ab60541a624afc4db9b8",
    "role": "Admin",
    "resource": "Genres",
    "action": "create:any",
    "attributes": "*",
    "__v": 0
  },
  {
    "_id": "5f35ab60541a624afc4db9b9",
    "role": "Admin",
    "resource": "Genres",
    "action": "read:any",
    "attributes": "*",
    "__v": 0
  }
]
```

<h3 id="find-all-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

<h1 id="comrad-api-configuration">Configuration</h1>

## Get Custom Fields for Entity

<a id="opIdConfigCustomFields"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/config/fields/{entity name}',
{
  method: 'GET',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`GET /config/fields/{entity name}`

Returns an array with the different custom fields available for the given `entity name`.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="get-custom-fields-for-entity-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|entity name|path|undefined|true|none|

> Example responses

> 200 Response

```json
[
  {
    "name": "local_artist",
    "label": "Local Artist",
    "editFieldType": "checkbox"
  },
  {
    "name": "location",
    "label": "Location",
    "editFieldType": "dropdown",
    "options": [
      "New Releases Bin",
      "Library",
      "Was in Library, but Lost"
    ]
  }
]
```

<h3 id="get-custom-fields-for-entity-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Compliance Reporting Period

<a id="opIdConfigInComplianceReportingPeriod"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/config/compliance-reporting-period',
{
  method: 'GET',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`GET /config/compliance-reporting-period`

Returns a boolean value indicating whether we are currently in a compliance reporting period

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

```json
true
```

<h3 id="compliance-reporting-period-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Resources - Get Categories

<a id="opIdConfigResourcesCategories"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/config/resources-categories',
{
  method: 'GET',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`GET /config/resources-categories`

Returns an array with the different categories available for the Resources section

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

```json
[
  "Announcements",
  "Policies",
  "Fund Drives",
  "Other Important Documents"
]
```

<h3 id="resources---get-categories-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

<h1 id="comrad-api-shows">Shows</h1>

## Find

<a id="opIdFindShows"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/shows',
{
  method: 'GET',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`GET /events/shows`

Returns shows in a given timeframe, ordered by time from earliest to latest

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="find-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|startDate|query|undefined|true|Retrieve events with either a start time or end time at or after this value. Should be parseable by `Date` constructor in JavaScript.|
|endDate|query|undefined|true|Retrieve events with either a start time or end time at or before this value. Should be parseable by `Date` constructor in JavaScript.|
|host|query|undefined|false|Return only shows hosted by the user specified by the id, or a host group containing that user|
|showsWithNoHost|query|undefined|false|If true, will only return shows that have no host|

> Example responses

> 200 Response

```json
[
  {
    "repeat_rule": {
      "byweekday": [
        "MO",
        "TU",
        "WE",
        "TH",
        "FR"
      ],
      "repeat_start_date": "2011-03-28T15:30:00.000Z",
      "frequency": 2,
      "repeat_end_date": "9999-01-01T06:00:00.000Z"
    },
    "show_details": {
      "host_type": "User",
      "guests": [
        null
      ],
      "title": "Morning Sound Alternative",
      "summary": "Diverse and eclectic sounds, on the mellow side.",
      "description": "<p>Diverse and eclectic sounds, on the mellow side. You'll hear everything from Ambient Electronics to Reggae to Folk.</p>",
      "producer": null,
      "host": null,
      "custom": {
        "my_custom_property": "Custom value"
      }
    },
    "status": "active",
    "_id": "5f35a6ef783e63454cd918f1",
    "start_time_utc": "2020-09-16T15:30:00Z",
    "end_time_utc": "2020-09-16T18:06:00Z",
    "is_recurring": true,
    "created_at": "2020-08-13T20:47:43.675Z",
    "updated_at": "2020-08-13T20:47:43.675Z",
    "__v": 0,
    "master_event_id": {
      "_id": "5f35a6ef783e63454cd918f1"
    },
    "master_time_id": "5f35a6ef783e63454cd918f1-1600270200000"
  }
]
```

<h3 id="find-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the parameters you provided. See response for more details|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-responseschema">Response Schema</h3>

undefined

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

<h1 id="comrad-api-traffic">Traffic</h1>

## Find

<a id="opIdFindTraffic"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/traffic',
{
  method: 'GET',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`GET /events/traffic`

Returns traffic in a given timeframe, ordered by time from earliest to latest

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="find-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|startDate|query|undefined|true|Retrieve events with either a start time or end time at or after this value. Should be parseable by `Date` constructor in JavaScript.|
|endDate|query|undefined|true|Retrieve events with either a start time or end time at or before this value. Should be parseable by `Date` constructor in JavaScript.|
|filterByTrafficType[]|query|undefined|false|Return only shows matching the specified traffic types|

> Example responses

> 200 Response

```json
[
  {
    "_id": "5f35a719783e63454cd9a071",
    "repeat_rule": {
      "byweekday": [],
      "repeat_start_date": "2011-03-29T16:00:00.000Z",
      "frequency": 3,
      "repeat_end_date": "9999-01-01T06:00:00.000Z"
    },
    "traffic_details": {
      "type": "Legal ID",
      "title": "Legal Id",
      "description": "\"KGNU, Boulder, Denver and Fort Collins\"",
      "producer": null,
      "custom": {
        "custom_property": "A custom value"
      }
    },
    "status": "active",
    "start_time_utc": "2020-09-16T16:00:00Z",
    "end_time_utc": "2020-09-16T16:01:00Z",
    "is_recurring": true,
    "created_at": "2020-08-13T20:48:25.305Z",
    "updated_at": "2020-08-13T20:48:25.305Z",
    "__v": 0,
    "MasterEvent": [],
    "master_event_id": {
      "_id": "5f35a719783e63454cd9a071"
    },
    "master_time_id": "5f35a719783e63454cd9a071-1600272000000"
  }
]
```

<h3 id="find-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the parameters you provided. See response for more details|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-responseschema">Response Schema</h3>

undefined

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

<h1 id="comrad-api-genres">Genres</h1>

## Find All

<a id="opIdFindAllGenres"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/genres',
{
  method: 'GET',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`GET /genres`

Retrieve all Genres

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

```json
[
  {
    "_id": "5f35a41e783e63454ccee914",
    "name": "Bluegrass",
    "created_at": "2020-08-13T20:35:42.440Z",
    "updated_at": "2020-08-13T20:35:42.440Z",
    "__v": 0
  },
  {
    "_id": "5f35a41e783e63454ccee90c",
    "name": "Blues",
    "created_at": "2020-08-13T20:35:42.424Z",
    "updated_at": "2020-08-13T20:35:42.424Z",
    "__v": 0
  }
]
```

<h3 id="find-all-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

<h1 id="comrad-api-library-albums-artists-tracks-">Library (Albums, Artists, Tracks)</h1>

## Create

<a id="opIdCreateLibrary"></a>

> Code samples

```javascript
const inputBody = '[
  {
    "type": "album",
    "name": "Flamenco On Fire",
    "artist": "5f35a3e7783e63454ccde9db",
    "label": null,
    "genre": null,
    "compilation": false
  },
  {
    "type": "artist",
    "name": "Sabicas"
  },
  {
    "type": "track",
    "artists": [
      "5f35a3e7783e63454ccde9db"
    ],
    "name": "Fantasia Inca",
    "album": "5f35a495783e63454cd19670",
    "track_number": 4,
    "disk_number": 1,
    "duration_in_seconds": 237
  }
]';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/library',
{
  method: 'POST',
  body: inputBody,
  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`POST /library`

Create a new Library record

The following roles can access this API endpoint: `Admin`, `Full Access`, `Music Library Admin`

> Body parameter

```json
[
  {
    "type": "album",
    "name": "Flamenco On Fire",
    "artist": "5f35a3e7783e63454ccde9db",
    "label": null,
    "genre": null,
    "compilation": false
  },
  {
    "type": "artist",
    "name": "Sabicas"
  },
  {
    "type": "track",
    "artists": [
      "5f35a3e7783e63454ccde9db"
    ],
    "name": "Fantasia Inca",
    "album": "5f35a495783e63454cd19670",
    "track_number": 4,
    "disk_number": 1,
    "duration_in_seconds": 237
  }
]
```

<h3 id="create-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|[Library](#schemalibrary)|false|none|

> Example responses

> 200 Response

```json
{
  "popularity": 0,
  "_id": "5f5fd9ebfa3e4138f8d38ecd",
  "name": "An API Artist",
  "type": "artist",
  "created_at": "2020-09-14T21:00:27.381Z",
  "updated_at": "2020-09-14T21:00:27.381Z",
  "__v": 0
}
```

<h3 id="create-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|The Library record was created successfully|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Find All

<a id="opIdFindAllLibrary"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/library',
{
  method: 'GET',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`GET /library`

Finds all items from the library. By default, this returns tracks, artists and albums together but the endpoint can be filtered to only return a particular type (artists, albums, tracks).

Results are paged. The `page` parameter can be used different pages in the result set. The API result will also include a `nextPage` object if there is another page of results. The `nextPage` object will consist of a `page` value (the numerical value of the next page) and a `url` value (the URL of the API call to access the next page)

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="find-all-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|undefined|query|string|false|If provided, this endpoint will only return the specified entity type|

#### Enumerated Values

|Parameter|Value|
|---|---|
|undefined|artist|
|undefined|album|
|undefined|track|

> Example responses

> 200 Response

```json
{
  "docs": [
    {
      "popularity": 7,
      "artists": [
        {
          "popularity": 10,
          "_id": "5f35a3e7783e63454ccde9db",
          "name": "Sabicas",
          "type": "artist",
          "created_at": "2020-08-13T20:34:47.886Z",
          "updated_at": "2020-08-13T20:34:47.886Z"
        }
      ],
      "_id": "5f35a645783e63454cd916b1",
      "name": "Fantasia Inca",
      "album": {
        "popularity": 2,
        "_id": "5f35a495783e63454cd19670",
        "name": "Flamenco On Fire",
        "artist": "5f35a3e7783e63454ccde9db",
        "label": "Sample Label",
        "genre": "4a44a3e7783e63454ccdc897",
        "compilation": false,
        "created_at": "2020-05-05T15:20:28.000Z",
        "custom": {
          "sample_custom_property": "1475159102",
          "another_custom_property": "A custom value"
        },
        "type": "album",
        "updated_at": "2020-08-13T20:37:41.468Z"
      },
      "track_number": 4,
      "disk_number": 1,
      "duration_in_seconds": 237,
      "custom": {
        "sample_custom_property_for_track": "591391"
      },
      "type": "track",
      "created_at": "2020-08-13T20:44:53.977Z",
      "updated_at": "2020-08-13T20:44:53.977Z"
    },
    {
      "popularity": 24,
      "_id": "5f35a645783e63454cd9108d",
      "name": "Blind Willie Johnson ",
      "type": "artist",
      "created_at": "2020-08-13T20:44:53.733Z",
      "updated_at": "2020-08-13T20:44:53.733Z",
      "__v": 0
    },
    {
      "popularity": 2,
      "_id": "5f35a495783e63454cd19666",
      "name": "Whoops, We Did It (Live) - EP",
      "artist": {
        "popularity": 4,
        "_id": "5f35a3ea783e63454cce7ddd",
        "name": "Dandu",
        "type": "artist",
        "created_at": "2020-08-13T20:34:50.188Z",
        "updated_at": "2020-08-13T20:34:50.188Z"
      },
      "label": "Sample Label Value",
      "genre": "5f35a41e783e63454ccee914",
      "compilation": false,
      "created_at": "2020-05-04T18:01:57.000Z",
      "custom": {
        "sample_custom_property": "1475159102",
        "another_custom_property": "A custom value"
      },
      "type": "album",
      "updated_at": "2020-08-13T20:37:41.468Z"
    }
  ],
  "totalPages": 7623,
  "nextPage": {
    "page": 1,
    "url": "/v1/library?page=1&sortBy=updated_at&sortDescending=1"
  }
}
```

<h3 id="find-all-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-all-responseschema">Response Schema</h3>

undefined

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Create Many

<a id="opIdCreateManyLibrary"></a>

> Code samples

```javascript
const inputBody = '[
  [
    {
      "type": "album",
      "name": "Flamenco On Fire",
      "artist": "5f35a3e7783e63454ccde9db",
      "label": null,
      "genre": null,
      "compilation": false
    },
    {
      "type": "artist",
      "name": "Sabicas"
    },
    {
      "type": "track",
      "artists": [
        "5f35a3e7783e63454ccde9db"
      ],
      "name": "Fantasia Inca",
      "album": "5f35a495783e63454cd19670",
      "track_number": 4,
      "disk_number": 1,
      "duration_in_seconds": 237
    }
  ]
]';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/library/many',
{
  method: 'POST',
  body: inputBody,
  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`POST /library/many`

Create multiple Library records

The following roles can access this API endpoint: `Admin`, `Full Access`, `Music Library Admin`

> Body parameter

```json
[
  [
    {
      "type": "album",
      "name": "Flamenco On Fire",
      "artist": "5f35a3e7783e63454ccde9db",
      "label": null,
      "genre": null,
      "compilation": false
    },
    {
      "type": "artist",
      "name": "Sabicas"
    },
    {
      "type": "track",
      "artists": [
        "5f35a3e7783e63454ccde9db"
      ],
      "name": "Fantasia Inca",
      "album": "5f35a495783e63454cd19670",
      "track_number": 4,
      "disk_number": 1,
      "duration_in_seconds": 237
    }
  ]
]
```

<h3 id="create-many-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|[Library](#schemalibrary)|false|none|

> Example responses

> 200 Response

```json
[
  {
    "popularity": 0,
    "_id": "5f5fdb69e546d851980aa75d",
    "name": "A New Artist",
    "type": "artist",
    "created_at": "2020-09-14T21:06:49.598Z",
    "updated_at": "2020-09-14T21:06:49.599Z",
    "__v": 0
  },
  {
    "popularity": 0,
    "_id": "5f5fdb69e546d851980aa75e",
    "name": "A New Album",
    "type": "album",
    "artist": "5f5fd9ebfa3e4138f8d38ecd",
    "label": "Comrad Records",
    "created_at": "2020-09-14T21:06:49.600Z",
    "updated_at": "2020-09-14T21:06:49.600Z",
    "__v": 0
  }
]
```

<h3 id="create-many-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|The Library records were created successfully|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|

<h3 id="create-many-responseschema">Response Schema</h3>

undefined

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Get by ID

<a id="opIdGetByIdLibrary"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/library/{id}',
{
  method: 'GET',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`GET /library/{id}`

Get an individual library item by ID

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="get-by-id-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|none|

> Example responses

> 200 Response

```json
{
  "docs": null,
  "popularity": 77,
  "_id": "5f35a3e7783e63454ccdd9d9",
  "name": "Janet Jackson & Michael Jackson",
  "type": "artist",
  "created_at": "2020-08-13T20:34:47.650Z",
  "updated_at": "2020-08-13T20:34:47.650Z",
  "albums": [
    {
      "popularity": 55,
      "_id": "5f35a43a783e63454ccfa464",
      "artists": [],
      "name": "Number Ones",
      "artist": "5f35a3e7783e63454ccdd9d9",
      "label": null,
      "genre": null,
      "compilation": false,
      "created_at": "2012-10-18T06:07:25.000Z",
      "custom": {
        "a_custom_property": "a custom value"
      },
      "type": "album",
      "updated_at": "2020-08-13T20:36:10.758Z",
      "number_of_tracks": 1
    }
  ]
}
```

<h3 id="get-by-id-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="get-by-id-responseschema">Response Schema</h3>

undefined

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Delete

<a id="opIdDeleteLibrary"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/library/{id}',
{
  method: 'DELETE',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`DELETE /library/{id}`

Delete a library item

The following roles can access this API endpoint: `Admin`, `Full Access`, `Music Library Admin`

<h3 id="delete-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|none|

> Example responses

> 200 Response

```json
{
  "popularity": 0,
  "_id": "5f5fdb69e546d851980aa75d",
  "name": "A New Artist",
  "type": "artist",
  "created_at": "2020-09-14T21:06:49.598Z",
  "updated_at": "2020-09-14T21:06:49.599Z",
  "__v": 0
}
```

<h3 id="delete-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|404|[Not Found](https://tools.ietf.org/html/rfc7231#section-6.5.4)|Library item does not exist|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|

<h3 id="delete-responseschema">Response Schema</h3>

Status Code **200**

*The record that was deleted*

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|

undefined

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Update

<a id="opIdUpdateLibrary"></a>

> Code samples

```javascript
const inputBody = '{
  "label": "Comrad Records"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/library/{id}',
{
  method: 'PUT',
  body: inputBody,
  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`PUT /library/{id}`

Update a library item

The following roles can access this API endpoint: `Admin`, `Full Access`, `Music Library Admin`

> Body parameter

```json
{
  "label": "Comrad Records"
}
```

<h3 id="update-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|object|true|JSON object of properties to update|
|id|path|undefined|true|none|

> Example responses

> 200 Response

```json
{
  "popularity": 0,
  "_id": "5f35a425783e63454ccf2ce7",
  "name": "The Legend of Johnny Cash",
  "artist": "5f35a3e5783e63454ccd79be",
  "label": "Comrad Records",
  "genre": {
    "_id": "5f35a41e783e63454ccee90f",
    "name": "Country",
    "created_at": "2020-08-13T20:35:42.430Z",
    "updated_at": "2020-08-13T20:35:42.430Z",
    "__v": 0
  },
  "compilation": false,
  "custom": {
    "a_custom_property": "a custom value"
  },
  "type": "album",
  "created_at": "2020-08-13T20:35:49.013Z",
  "updated_at": "2020-08-13T20:35:49.013Z"
}
```

<h3 id="update-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|

<h3 id="update-responseschema">Response Schema</h3>

Status Code **200**

*The new, updated record*

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|

undefined

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Search

<a id="opIdSearchLibrary"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/library/search',
{
  method: 'GET',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`GET /library/search`

Search for library items based on a search string. Results will be ordered with the most relevant result first.

This endpoint returns a `totalPages` value, but that value will always be 1. The `totalPages` value is only included for compatibility with some components within Comrad.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="search-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|s|query|undefined|true|The string to search for|
|type|query|string|false|If provided, this endpoint will only return the specified entity type|
|limit|query|undefined|false|The number of results to return. Defaults to 100.|

#### Enumerated Values

|Parameter|Value|
|---|---|
|type|artist|
|type|album|
|type|track|

> Example responses

> 200 Response

```json
{
  "docs": [
    {
      "popularity": 80,
      "_id": "5f35a3e7783e63454ccdd9d9",
      "artists": [],
      "name": "Janet Jackson & Michael Jackson",
      "type": "artist",
      "updated_at": "2020-08-13T20:34:47.650Z",
      "score": 1.125,
      "relevance": 3.375
    },
    {
      "popularity": 0,
      "_id": "5f35a4e8783e63454cd2cec9",
      "artists": [
        {
          "popularity": 61,
          "_id": "5f35a3e5783e63454ccd79be",
          "name": "Johnny Cash",
          "type": "artist",
          "created_at": "2020-08-13T20:34:45.558Z",
          "updated_at": "2020-08-13T20:34:45.558Z"
        }
      ],
      "name": "JACKSON",
      "album": {
        "popularity": 75,
        "_id": "5f35a425783e63454ccf2ce7",
        "name": "The Legend of Johnny Cash",
        "artist": "5f35a3e5783e63454ccd79be",
        "label": "Legacy",
        "genre": "5f35a41e783e63454ccee90f",
        "compilation": false,
        "type": "album",
        "created_at": "2020-08-13T20:35:49.013Z",
        "updated_at": "2020-08-13T20:35:49.013Z"
      },
      "type": "track",
      "updated_at": "2020-08-13T20:39:04.848Z",
      "score": 1.1,
      "relevance": 2.2
    },
    {
      "popularity": 54,
      "_id": "5f35a4b5783e63454cd1b72c",
      "artists": [
        {
          "popularity": 52,
          "_id": "5f35a3e5783e63454ccd75bb",
          "artists": [],
          "name": "Lucinda Williams",
          "type": "artist",
          "created_at": "2020-08-13T20:34:45.259Z",
          "updated_at": "2020-08-13T20:34:45.259Z"
        }
      ],
      "name": "Jackson",
      "album": {
        "popularity": 38,
        "_id": "5f35a41f783e63454ccef3b9",
        "artists": [],
        "name": "Car Wheels on a Gravel Road",
        "artist": "5f35a3e5783e63454ccd75bb",
        "label": "Mercury Records",
        "genre": "5f35a41e783e63454ccee908",
        "compilation": false,
        "type": "album",
        "created_at": "2020-08-13T20:35:43.158Z",
        "updated_at": "2020-08-13T20:35:43.158Z"
      },
      "type": "track",
      "updated_at": "2020-08-13T20:38:13.160Z",
      "score": 1.1,
      "relevance": 2.2
    }
  ],
  "totalPages": 1
}
```

<h3 id="search-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="search-responseschema">Response Schema</h3>

undefined

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

<h1 id="comrad-api-users">Users</h1>

## Create

<a id="opIdCreateUser"></a>

> Code samples

```javascript
const inputBody = '{}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/users',
{
  method: 'POST',
  body: inputBody,
  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`POST /users`

Create a new User record

The following roles can access this API endpoint: `Admin`

If the `SHOW_DEVELOPMENT_SIGN_UP` environment variable is set to `TRUE`, then all users can access this endpoint.

> Body parameter

```json
{}
```

> Example responses

> 200 Response

```json
{
  "api_key": {
    "last_used": null
  },
  "on_air_name": null,
  "primary_phone": null,
  "roles": [
    "DJ"
  ],
  "status": "Active",
  "_id": "5f6a0833b1452b50c020ba83",
  "email": "s2314@getcomrad.org",
  "first_name": "Sean",
  "last_name": "Williams",
  "__v": 0
}
```

<h3 id="create-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|The User record was created successfully|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Find All

<a id="opIdFindAllUsers"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/users',
{
  method: 'GET',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`GET /users`

Finds all users.

Results are not paged. This API endpoint will return all results.

The following roles can access this API endpoint: `Admin`

> Example responses

> 200 Response

```json
[
  {
    "api_key": {
      "last_used": "2020-09-22T14:10:04.032Z"
    },
    "on_air_name": "DJ Cool Software",
    "primary_phone": null,
    "roles": [
      "Admin"
    ],
    "status": "Active",
    "_id": "5f35a3cf783e63454ccd7525",
    "can_delete": true,
    "email": "comrad.development@gmail.com",
    "first_name": "Comrad",
    "last_name": "Develpment",
    "__v": 0
  }
]
```

<h3 id="find-all-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-all-responseschema">Response Schema</h3>

undefined

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

# Schemas

<h2 id="tocS_AccessControl">AccessControl</h2>

<a id="schemaaccesscontrol"></a>
<a id="schema_AccessControl"></a>
<a id="tocSaccesscontrol"></a>
<a id="tocsaccesscontrol"></a>

```json
{
  "role": "Admin",
  "resource": "TestResource",
  "action": "create:any",
  "attributes": "*"
}

```

### Properties

|Name|Type|Required|Description|
|---|---|---|---|---|
|role|string|true|The role name (Admin, DJ, etc.) to grant permissions to|
|resource|string|true|The resource (Library, Playlists, etc.) to grant permissions to|
|action|string|true|The actions that can be performed on the resource. Possible values: read:own, read:any, update:own, update:any|
|attributes|string|true|The attributes that can be modified by the role. Can be *, or a comma-delimited list of attributes|

<h2 id="tocS_Genre">Genre</h2>

<a id="schemagenre"></a>
<a id="schema_Genre"></a>
<a id="tocSgenre"></a>
<a id="tocsgenre"></a>

```json
{
  "name": "Bluegrass"
}

```

### Properties

|Name|Type|Required|Description|
|---|---|---|---|---|
|name|string|true|The name of the genre|

<h2 id="tocS_HostGroup">HostGroup</h2>

<a id="schemahostgroup"></a>
<a id="schema_HostGroup"></a>
<a id="tocShostgroup"></a>
<a id="tocshostgroup"></a>

```json
{
  "users": [
    "5f35a3cf783e63454ccd7525",
    "5f35a651783e63454cd916d4",
    "5f35a651783e63454cd1425d",
    "5f35a651783e63454cd97ae4"
  ],
  "_id": "5f35abd3661c7f37f047dbcb",
  "on_air_name": "The Way Low Down",
  "__v": 0
}

```

When multiple Comrad users are hosting a show together, they are represented as a `Host Group`. If a `Host Group` is hosting a show, Comrad will consider the show as "belonging" to each of the users. So when filtering by any of the users in a host group, the show will appear. `Host Groups` have custom on-air names, so the group can be identified any way you'd like. For example, the group could display as "Robby, Sean, Pete and Josh" or "The Way Low Down".

### Properties

|Name|Type|Required|Description|
|---|---|---|---|---|
|on_air_name|string|true|The public-facing on-air name the group should be identified by|
|users|[[User](#schemauser)]|true|The users that will be hosting together|

<h2 id="tocS_Library">Library</h2>

<a id="schemalibrary"></a>
<a id="schema_Library"></a>
<a id="tocSlibrary"></a>
<a id="tocslibrary"></a>

```json
[
  {
    "type": "album",
    "name": "Flamenco On Fire",
    "artist": "5f35a3e7783e63454ccde9db",
    "label": null,
    "genre": null,
    "compilation": false
  },
  {
    "type": "artist",
    "name": "Sabicas"
  },
  {
    "type": "track",
    "artists": [
      "5f35a3e7783e63454ccde9db"
    ],
    "name": "Fantasia Inca",
    "album": "5f35a495783e63454cd19670",
    "track_number": 4,
    "disk_number": 1,
    "duration_in_seconds": 237
  }
]

```

### Properties

|Name|Type|Required|Description|
|---|---|---|---|---|
|name|string|true|The name of the artist, album or track|
|resource|string|false|The resource (Library, Playlists, etc.) to grant permissions to|
|artist|string(id)|false|Used by albums. The ID of the artist associated with the album.|
|label|string|false|Used by albums. The label that released the album|
|genre|string(id)|false|Used by albums. The genre of the album.|
|compilation|boolean|false|Used by albums. Whether or not the album is a compilation that was not released by a specific artist (such as a film soundtrack)|
|album|string(id)|false|Used by tracks. The album containing the track.|
|artists|[string]|false|Used by tracks. The artists credited with the track (the order of the array matters: the artists will be displayed in that order)|
|track_number|integer|false|Used by tracks. The track number in the track listing on the album's disc|
|disk_number|integer|false|Used by tracks. The disk number of the track within the album.|
|duration_in_seconds|integer|false|Used by tracks. The duration of the song, in seconds|

<h2 id="tocS_Resource">Resource</h2>

<a id="schemaresource"></a>
<a id="schema_Resource"></a>
<a id="tocSresource"></a>
<a id="tocsresource"></a>

```json
[
  {
    "category": "Announcements",
    "description": "ON-AIR - Online Ticket Giveaways",
    "link": "https://getcomrad.org/example/On-AirTicketGiveawayInstructions.pdf"
  }
]

```

A link to an external file that can Comrad users can reference. This could be anything a DJ or other user would need to access, like a list of station rules

### Properties

|Name|Type|Required|Description|
|---|---|---|---|---|
|category|string|true|The category associated with the link. Possible values can be obtained from the Configuration > Resources - Get Categories API endpoint|
|description|string|true|A description of what the link is|
|link|string(uri)|true|The URL link of the resource|

<h2 id="tocS_Show">Show</h2>

<a id="schemashow"></a>
<a id="schema_Show"></a>
<a id="tocSshow"></a>
<a id="tocsshow"></a>

```json
{
  "repeat_rule": {
    "byweekday": [
      "MO",
      "TU",
      "WE",
      "TH",
      "FR"
    ],
    "repeat_start_date": "2011-03-28T15:30:00.000Z",
    "frequency": 2,
    "repeat_end_date": "9999-01-01T06:00:00.000Z"
  },
  "show_details": {
    "host_type": "User",
    "title": "Morning Sound Alternative",
    "summary": "Diverse and eclectic sounds, on the mellow side.",
    "description": "<p>Diverse and eclectic sounds, on the mellow side. You'll hear everything from Ambient Electronics to Reggae to Folk.</p>",
    "producer": null,
    "host": null,
    "custom": {
      "a_custom_property": "Custom value"
    }
  },
  "status": "active",
  "_id": "5f35a6ef783e63454cd918f1",
  "start_time_utc": "2011-03-28T15:30:00.000Z",
  "end_time_utc": "2011-03-28T18:06:00.000Z",
  "is_recurring": true,
  "created_at": "2020-08-13T20:47:43.675Z",
  "updated_at": "2020-08-13T20:47:43.675Z",
  "__v": 0
}

```

### Properties

|Name|Type|Required|Description|
|---|---|---|---|---|
|show_details|any|false|[object Object]|
|status|string|true|none|
|start_time_utc|string(date-time)|true|The start time of the event|
|end_time_utc|string(date-time)|true|The end time of the event|
|master_event_id|[object Object]|false|If this is an instance within a repeating series, this field links to its event series|
|is_recurring|boolean|true|Whether the event is a repeating series of events|
|repeat_rule|[RepeatRule](#schemarepeatrule)|false|none|
|created_at|string(date-time)|false|Read only. The time the event was created at.|
|updated_at|string(date-time)|false|Read only. The time the event was updated at.|

#### Enumerated Values

|Property|Value|
|---|---|
|status|active|
|status|deleted|
|status|canceled|

<h2 id="tocS_Traffic">Traffic</h2>

<a id="schematraffic"></a>
<a id="schema_Traffic"></a>
<a id="tocStraffic"></a>
<a id="tocstraffic"></a>

```json
{
  "repeat_rule": {
    "byweekday": [
      "MO",
      "TU",
      "WE",
      "TH",
      "FR"
    ],
    "repeat_start_date": "2020-04-06T16:40:00.000Z",
    "frequency": 2,
    "repeat_end_date": "9999-01-01T06:00:00.000Z"
  },
  "traffic_details": {
    "type": "Announcement",
    "title": "Virtual Events Calendar",
    "description": "<p>Please play from the \"Programs\" folder.</p>",
    "producer": null,
    "custom": {
      "old_comrad_event_id": "3302",
      "old_comrad_scheduled_event_ids": [
        "17572"
      ]
    }
  },
  "status": "active",
  "_id": "5f35a728783e63454cd9d791",
  "start_time_utc": "2020-09-16T16:40:00Z",
  "end_time_utc": "2020-09-16T16:41:00Z",
  "is_recurring": true,
  "created_at": "2020-08-13T20:48:40.673Z",
  "updated_at": "2020-08-13T20:48:40.673Z",
  "__v": 0,
  "master_event_id": {
    "_id": "5f35a728783e63454cd9d791"
  },
  "master_time_id": "5f35a728783e63454cd9d791-1600274400000"
}

```

### Properties

|Name|Type|Required|Description|
|---|---|---|---|---|
|traffic_details|[object Object]|false|[object Object]|
|status|string|true|none|
|start_time_utc|string(date-time)|true|The start time of the event|
|end_time_utc|string(date-time)|true|The end time of the event|
|master_event_id|[object Object]|false|If this is an instance within a repeating series, this field links to its event series|
|is_recurring|boolean|true|Whether the event is a repeating series of events|
|repeat_rule|[RepeatRule](#schemarepeatrule)|false|none|
|created_at|string(date-time)|false|Read only. The time the event was created at.|
|updated_at|string(date-time)|false|Read only. The time the event was updated at.|

#### Enumerated Values

|Property|Value|
|---|---|
|status|active|
|status|deleted|
|status|canceled|

<h2 id="tocS_User">User</h2>

<a id="schemauser"></a>
<a id="schema_User"></a>
<a id="tocSuser"></a>
<a id="tocsuser"></a>

```json
{
  "api_key": {
    "last_used": "2020-09-16T17:41:32.271Z"
  },
  "on_air_name": "Sean W",
  "primary_phone": null,
  "roles": [
    "DJ",
    "Music Library Admin"
  ],
  "status": "Active",
  "email": "s@getcomrad.org",
  "first_name": "Sean",
  "last_name": "Williams"
}

```

Represents a user that can log into Comrad, have an API to Comrad, and host shows in Comrad.

### Properties

|Name|Type|Required|Description|
|---|---|---|---|---|
|api_key|any|false|none|
|email|string|true|The user's email address|
|first_name|string|true|The user's first name|
|last_name|string|true|The user's last name|
|password|string|false|The user's password. Should be plain text when creating or updating a user. Encrypted at rest in the database.|
|roles|[string]|true|The roles that the user has, which affects what APIs they can access|
|status|string|false|Whether the user can currently log into the system. Defaults to Active.|

#### Enumerated Values

|Property|Value|
|---|---|
|status|Active|
|status|Inactive|

<h2 id="tocS_RepeatRule">RepeatRule</h2>

<a id="schemarepeatrule"></a>
<a id="schema_RepeatRule"></a>
<a id="tocSrepeatrule"></a>
<a id="tocsrepeatrule"></a>

```json
{
  "byweekday": [
    "MO",
    "TU",
    "WE",
    "TH",
    "FR"
  ],
  "repeat_start_date": "2011-03-28T15:30:00.000Z",
  "frequency": 2,
  "repeat_end_date": "9999-01-01T06:00:00.000Z"
}

```

### Properties

|Name|Type|Required|Description|
|---|---|---|---|---|
|frequency|integer|true|A value specifying the interval at which events should repeat.<br><br>Values:<br>0 = Repeat yearly<br>1 = Repeat monthly<br>2 = Repeat weekly<br>3 = Repeat daily<br>4 = Repeat hourly<br>5 = Repeat minutely<br>6 = Repeat secondly|
|repeat_start_date|string(date-time)|true|The date/time to start repeating|
|repeat_end_date|string(date-time)|true|The date/time to end repeating|
|count|integer|false|The maximum number of occurrences to be generated|
|interval|integer|false|The interval to repeat at. For example, if this is 2 and `frequency` is 3, the event will repeat every two days|
|byweekday|[string]|false|Events will only repeat on these weekdays|
|bymonth|integer|false|Events will only repeat in this month. A number from 1-12.|
|bysetpos|integer|false|Events will only repeat on the `bysetpos` value in the frequency period. Negative values repesent distance from the end of the frequency period. For example, if we have an event with `frequency` of 1 (monthly), `byweekday` of `['MO']` (Monday) and `bysetpos` of -1, the event will repeat on the last Monday of each month.|
|bymonthday|integer|false|Events will only repeat on this day of the month. A number from 1-31.|

#### Enumerated Values

|Property|Value|
|---|---|
|frequency|0|
|frequency|1|
|frequency|2|
|frequency|3|
|frequency|4|
|frequency|5|
|frequency|6|
|frequency|7|

 
