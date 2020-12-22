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
generator: widdershins v4.0.1

---

<h1 id="comrad-api">Comrad API v1</h1>

> Scroll down for code samples, example requests and responses. Select a language for code samples from the tabs above or the mobile navigation menu.

The API for Comrad, source software for radio stations that handles playlist logging, traffic management, reporting, and more

# Getting Started

## Retrieve the Current Show

> Sample code 

```javascript
var apiKey = 'your api key (it will be a UUID)';
var comradUrl = 'http://localhost:5000/v1'; //If running Comrad locally. Otherwise, replace with your API URL.

let startDate = new Date(); // the current date/time
let endDate = new Date(startDate.getTime() + (3 * 60 * 60 * 1000) ); // three hours from now

fetch(
    comradUrl + '/current-show',
    { 'headers': {'Authorization': apiKey } }
  ).then(response => {
    if (!response.ok) {
      //API call was unsuccessful
      console.log('API call unsuccessful. Status code: ' + response.status);
    } else {
      response.json().then(jsonResponse => {
        // output results to the console
        console.log('Results:');
        console.log(jsonResponse.show_details.title + ' starts at ' + jsonResponse.start_time_utc + ' (UTC) and ends at ' + jsonResponse.end_time_utc + ' (UTC)');
        console.log(jsonResponse);
      });
    }
  });
```

To get started using the API, we will walk through how to authenticate with the API and how to make an API call. See the example code accompanying this section for reference.

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

This code will call the `/current-show` endpoint, one of the [simple API endpoints](#comrad-api-simple-endpoints) that are designed for easily integrating with Comrad. The code passes your API key in the `Authorization` header. It will return a JSON object of the current show, including the playlist of the show, which consists of tracks, voice breaks, comments, and traffic events.

If an error occurs with the API request, a status code will be printed. A `500` error indicates a server error, and the output of the API endpoint may include more details. A `401` error means your credentials are not valid. A `403` error means you have valid credentials, but your API key does not have the proper permissions to access the endpoint. A `404` error means "not found", and if you get that error on this example code, it probably means that you have the wrong value for `comradUrl` or, if you're trying to test locally, your local development environment is not running.

## Using API Endpoints that take parameters

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

The endpoints in the [Simple Endpoints](#comrad-api-simple-endpoints) section are designed to cover common use cases without having to deal with the more complex aspects of the API. Many of the other endpoints take parameters and return much more data. One use case for the more complex endpoints would be if you wanted to pull all of the shows in the next three hours. As in the previous example, you'll want to set values for `apiKey` and `comradUrl`. 

The sample code is makinng a `GET` request to `/events/shows` with the following parameters:

- `startDate`: the current date/time
- `endDate`: three hours in the future from the current date/time

The code passes your API key in the `Authorization` header.

The example code will write out all of the events in the next three hours. If an error occurs with the API request, a status code will be printed. A `422` error means there's something wrong with the request you provided - usually, a validation error. There will be more details about this in the API response. The example code writes out any validation messages that come back from the server. A `401` error means your credentials are not valid. A `403` error means you have valid credentials, but your API key does not have the proper permissions to access the endpoint. A `404` error means "not found", and if you get that error on this example code, it probably means that you have the wrong value for `comradUrl` or, if you're trying to test locally, your local development environment is not running.

# Authentication

* API Key (ApiKeyAuth)
    - Parameter Name: **Authorization**, in: header. Place the API key in the authorization header

 

<!-- sort "Simple Endpoints" first -->

<h1 id="comrad-api-simple-endpoints">Simple Endpoints</h1>

## Current Show

<a id="opIdCurrentShow"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/current-show',
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

`GET /current-show`

Returns the show that's currently playing, or null if there is no show currently playing.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

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
  "master_time_id": "5f35a6ef783e63454cd918f1-1600270200000",
  "playlist_executed": [
    {
      "_id": "5f7b21b818540a4330aba289",
      "executed_time_utc": "2020-10-05T13:27:27.465Z",
      "type": "track",
      "track": {
        "popularity": 0,
        "_id": "5f72104dab73564244687fd7",
        "name": "Low is the Way",
        "album": {
          "popularity": 48,
          "_id": "5f720ef866320235249b55ab",
          "name": "Uncloudy Day",
          "artist": "5f720ed9663202352499d351",
          "label": "Rhino Records",
          "genre": "5f720ef466320235249b2da0",
          "compilation": false,
          "custom": {
            "my_custom_property": "Custom value"
          },
          "type": "album",
          "created_at": "2020-09-28T16:27:36.929Z",
          "updated_at": "2020-09-28T16:27:36.929Z"
        },
        "track_number": 0,
        "disk_number": 1,
        "duration_in_seconds": 0,
        "custom": {
          "a_custom_property": "Custom Value"
        },
        "type": "track",
        "artists": [
          {
            "popularity": 53,
            "_id": "5f720ed9663202352499d351",
            "name": "The Staple Singers",
            "type": "artist",
            "created_at": "2020-09-28T16:27:05.636Z",
            "updated_at": "2020-09-28T16:27:05.636Z"
          }
        ],
        "created_at": "2020-09-28T16:33:17.805Z",
        "updated_at": "2020-09-28T16:33:17.805Z"
      }
    },
    {
      "_id": "5f7b219018540a4330aba284",
      "type": "traffic",
      "executed_time_utc": "2020-10-05T13:37:20.601Z",
      "traffic": {
        "repeat_rule": {
          "byweekday": [],
          "repeat_start_date": "2011-04-03T13:00:00.000Z",
          "frequency": 3,
          "repeat_end_date": "9999-01-01T06:00:00.000Z"
        },
        "traffic_details": {
          "type": "Legal ID",
          "title": "Legal Id",
          "description": "\"KGNU, Boulder, Denver and Fort Collins\"",
          "producer": null,
          "custom": {
            "a_custom_property": "custom value"
          }
        },
        "status": "active",
        "_id": "5f7211f2ab735642446feea4",
        "start_time_utc": "2020-10-05T13:00:00Z",
        "end_time_utc": "2020-10-05T13:01:00Z",
        "is_recurring": true,
        "created_at": "2020-09-28T16:40:18.628Z",
        "updated_at": "2020-09-28T16:40:18.628Z",
        "__v": 0,
        "master_event_id": {
          "_id": "5f7211f2ab735642446feea4"
        },
        "master_time_id": "5f7211f2ab735642446feea4-1601902800000"
      },
      "master_time_id": "5f7211f2ab735642446feea4-1601902800000"
    },
    {
      "_id": "5f7b219218540a4330aba286",
      "type": "traffic",
      "executed_time_utc": "2020-10-05T13:37:22.408Z",
      "traffic": {
        "repeat_rule": {
          "byweekday": [
            "MO",
            "FR",
            "MO",
            "FR"
          ],
          "repeat_start_date": "2020-05-01T13:00:00.000Z",
          "frequency": 2,
          "repeat_end_date": "2021-04-02T13:00:00.000Z"
        },
        "traffic_details": {
          "type": "Underwriting",
          "title": "Comrad Software 2020",
          "description": "<p>Support comes from</p>\n<p>Comrad Software. </p>",
          "producer": null,
          "custom": {
            "custom_property": "custom value"
          },
          "underwriter_name": "Save Home Heat 2020"
        },
        "status": "active",
        "_id": "5f7211feab735642447026c4",
        "start_time_utc": "2020-10-05T13:00:00Z",
        "end_time_utc": "2020-10-05T13:01:00Z",
        "is_recurring": true,
        "created_at": "2020-09-28T16:40:30.823Z",
        "updated_at": "2020-09-28T16:40:30.823Z",
        "__v": 0,
        "master_event_id": {
          "_id": "5f7211feab735642447026c4"
        },
        "master_time_id": "5f7211feab735642447026c4-1601902800000"
      },
      "master_time_id": "5f7211feab735642447026c4-1601902800000"
    },
    {
      "_id": "5f7b219718540a4330aba288",
      "type": "voice_break",
      "executed_time_utc": "2020-10-05T13:37:27.465Z"
    }
  ]
}
```

<h3 id="current-show-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|The show that is currently playing, including its playlist. `null` if there is no show currently playing.|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Next Show

<a id="opIdNextShow"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/next-show',
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

`GET /next-show`

Returns the next show that will be playing. Returns `null` if there are no shows occurring within the next day.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

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
  "master_time_id": "5f35a6ef783e63454cd918f1-1600270200000",
  "playlist_executed": [
    {
      "_id": "5f7b21b818540a4330aba289",
      "executed_time_utc": "2020-10-05T13:27:27.465Z",
      "type": "track",
      "track": {
        "popularity": 0,
        "_id": "5f72104dab73564244687fd7",
        "name": "Low is the Way",
        "album": {
          "popularity": 48,
          "_id": "5f720ef866320235249b55ab",
          "name": "Uncloudy Day",
          "artist": "5f720ed9663202352499d351",
          "label": "Rhino Records",
          "genre": "5f720ef466320235249b2da0",
          "compilation": false,
          "custom": {
            "my_custom_property": "Custom value"
          },
          "type": "album",
          "created_at": "2020-09-28T16:27:36.929Z",
          "updated_at": "2020-09-28T16:27:36.929Z"
        },
        "track_number": 0,
        "disk_number": 1,
        "duration_in_seconds": 0,
        "custom": {
          "a_custom_property": "Custom Value"
        },
        "type": "track",
        "artists": [
          {
            "popularity": 53,
            "_id": "5f720ed9663202352499d351",
            "name": "The Staple Singers",
            "type": "artist",
            "created_at": "2020-09-28T16:27:05.636Z",
            "updated_at": "2020-09-28T16:27:05.636Z"
          }
        ],
        "created_at": "2020-09-28T16:33:17.805Z",
        "updated_at": "2020-09-28T16:33:17.805Z"
      }
    },
    {
      "_id": "5f7b219018540a4330aba284",
      "type": "traffic",
      "executed_time_utc": "2020-10-05T13:37:20.601Z",
      "traffic": {
        "repeat_rule": {
          "byweekday": [],
          "repeat_start_date": "2011-04-03T13:00:00.000Z",
          "frequency": 3,
          "repeat_end_date": "9999-01-01T06:00:00.000Z"
        },
        "traffic_details": {
          "type": "Legal ID",
          "title": "Legal Id",
          "description": "\"KGNU, Boulder, Denver and Fort Collins\"",
          "producer": null,
          "custom": {
            "a_custom_property": "custom value"
          }
        },
        "status": "active",
        "_id": "5f7211f2ab735642446feea4",
        "start_time_utc": "2020-10-05T13:00:00Z",
        "end_time_utc": "2020-10-05T13:01:00Z",
        "is_recurring": true,
        "created_at": "2020-09-28T16:40:18.628Z",
        "updated_at": "2020-09-28T16:40:18.628Z",
        "__v": 0,
        "master_event_id": {
          "_id": "5f7211f2ab735642446feea4"
        },
        "master_time_id": "5f7211f2ab735642446feea4-1601902800000"
      },
      "master_time_id": "5f7211f2ab735642446feea4-1601902800000"
    },
    {
      "_id": "5f7b219218540a4330aba286",
      "type": "traffic",
      "executed_time_utc": "2020-10-05T13:37:22.408Z",
      "traffic": {
        "repeat_rule": {
          "byweekday": [
            "MO",
            "FR",
            "MO",
            "FR"
          ],
          "repeat_start_date": "2020-05-01T13:00:00.000Z",
          "frequency": 2,
          "repeat_end_date": "2021-04-02T13:00:00.000Z"
        },
        "traffic_details": {
          "type": "Underwriting",
          "title": "Comrad Software 2020",
          "description": "<p>Support comes from</p>\n<p>Comrad Software. </p>",
          "producer": null,
          "custom": {
            "custom_property": "custom value"
          },
          "underwriter_name": "Save Home Heat 2020"
        },
        "status": "active",
        "_id": "5f7211feab735642447026c4",
        "start_time_utc": "2020-10-05T13:00:00Z",
        "end_time_utc": "2020-10-05T13:01:00Z",
        "is_recurring": true,
        "created_at": "2020-09-28T16:40:30.823Z",
        "updated_at": "2020-09-28T16:40:30.823Z",
        "__v": 0,
        "master_event_id": {
          "_id": "5f7211feab735642447026c4"
        },
        "master_time_id": "5f7211feab735642447026c4-1601902800000"
      },
      "master_time_id": "5f7211feab735642447026c4-1601902800000"
    },
    {
      "_id": "5f7b219718540a4330aba288",
      "type": "voice_break",
      "executed_time_utc": "2020-10-05T13:37:27.465Z"
    }
  ]
}
```

<h3 id="next-show-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|An object containing the next show, including its playlist. `null` if no shows occur during the next day.|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Previous Show

<a id="opIdPreviousShow"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/previous-show',
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

`GET /previous-show`

Returns the previous show that was playing. Returns `null` if there are no shows occurring within the previous day.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

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
  "master_time_id": "5f35a6ef783e63454cd918f1-1600270200000",
  "playlist_executed": [
    {
      "_id": "5f7b21b818540a4330aba289",
      "executed_time_utc": "2020-10-05T13:27:27.465Z",
      "type": "track",
      "track": {
        "popularity": 0,
        "_id": "5f72104dab73564244687fd7",
        "name": "Low is the Way",
        "album": {
          "popularity": 48,
          "_id": "5f720ef866320235249b55ab",
          "name": "Uncloudy Day",
          "artist": "5f720ed9663202352499d351",
          "label": "Rhino Records",
          "genre": "5f720ef466320235249b2da0",
          "compilation": false,
          "custom": {
            "my_custom_property": "Custom value"
          },
          "type": "album",
          "created_at": "2020-09-28T16:27:36.929Z",
          "updated_at": "2020-09-28T16:27:36.929Z"
        },
        "track_number": 0,
        "disk_number": 1,
        "duration_in_seconds": 0,
        "custom": {
          "a_custom_property": "Custom Value"
        },
        "type": "track",
        "artists": [
          {
            "popularity": 53,
            "_id": "5f720ed9663202352499d351",
            "name": "The Staple Singers",
            "type": "artist",
            "created_at": "2020-09-28T16:27:05.636Z",
            "updated_at": "2020-09-28T16:27:05.636Z"
          }
        ],
        "created_at": "2020-09-28T16:33:17.805Z",
        "updated_at": "2020-09-28T16:33:17.805Z"
      }
    },
    {
      "_id": "5f7b219018540a4330aba284",
      "type": "traffic",
      "executed_time_utc": "2020-10-05T13:37:20.601Z",
      "traffic": {
        "repeat_rule": {
          "byweekday": [],
          "repeat_start_date": "2011-04-03T13:00:00.000Z",
          "frequency": 3,
          "repeat_end_date": "9999-01-01T06:00:00.000Z"
        },
        "traffic_details": {
          "type": "Legal ID",
          "title": "Legal Id",
          "description": "\"KGNU, Boulder, Denver and Fort Collins\"",
          "producer": null,
          "custom": {
            "a_custom_property": "custom value"
          }
        },
        "status": "active",
        "_id": "5f7211f2ab735642446feea4",
        "start_time_utc": "2020-10-05T13:00:00Z",
        "end_time_utc": "2020-10-05T13:01:00Z",
        "is_recurring": true,
        "created_at": "2020-09-28T16:40:18.628Z",
        "updated_at": "2020-09-28T16:40:18.628Z",
        "__v": 0,
        "master_event_id": {
          "_id": "5f7211f2ab735642446feea4"
        },
        "master_time_id": "5f7211f2ab735642446feea4-1601902800000"
      },
      "master_time_id": "5f7211f2ab735642446feea4-1601902800000"
    },
    {
      "_id": "5f7b219218540a4330aba286",
      "type": "traffic",
      "executed_time_utc": "2020-10-05T13:37:22.408Z",
      "traffic": {
        "repeat_rule": {
          "byweekday": [
            "MO",
            "FR",
            "MO",
            "FR"
          ],
          "repeat_start_date": "2020-05-01T13:00:00.000Z",
          "frequency": 2,
          "repeat_end_date": "2021-04-02T13:00:00.000Z"
        },
        "traffic_details": {
          "type": "Underwriting",
          "title": "Comrad Software 2020",
          "description": "<p>Support comes from</p>\n<p>Comrad Software. </p>",
          "producer": null,
          "custom": {
            "custom_property": "custom value"
          },
          "underwriter_name": "Save Home Heat 2020"
        },
        "status": "active",
        "_id": "5f7211feab735642447026c4",
        "start_time_utc": "2020-10-05T13:00:00Z",
        "end_time_utc": "2020-10-05T13:01:00Z",
        "is_recurring": true,
        "created_at": "2020-09-28T16:40:30.823Z",
        "updated_at": "2020-09-28T16:40:30.823Z",
        "__v": 0,
        "master_event_id": {
          "_id": "5f7211feab735642447026c4"
        },
        "master_time_id": "5f7211feab735642447026c4-1601902800000"
      },
      "master_time_id": "5f7211feab735642447026c4-1601902800000"
    },
    {
      "_id": "5f7b219718540a4330aba288",
      "type": "voice_break",
      "executed_time_utc": "2020-10-05T13:37:27.465Z"
    }
  ]
}
```

<h3 id="previous-show-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|an object containing the previous show, or `null` if no shows occurred within the previous day|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Recent Shows

<a id="opIdRecentShows"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/recent-shows',
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

`GET /recent-shows`

Returns an array of the most recent shows, including the current show. The array is sorted by the show time, descending, so the current show is first in the array and the oldest show is last.

Only returns shows from the past week, and will return a maximum of 50 shows.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

```json
[
  {
    "repeat_rule": {
      "byweekday": [
        "TU"
      ],
      "repeat_start_date": "2011-04-12T00:30:00.000Z",
      "frequency": 2,
      "repeat_end_date": "9999-01-01T06:00:00.000Z"
    },
    "show_details": {
      "host_type": "User",
      "guests": [
        null
      ],
      "title": "Outsources",
      "summary": "Gay, Lesbian, Bisexual, Transgender News and Interviews.",
      "description": "<p>Presenting news and information from the local GLBT community and beyond.</p>",
      "producer": null,
      "host": null,
      "custom": {
        "record_audio": "1",
        "url": "outsources",
        "source": "KGNU"
      }
    },
    "status": "active",
    "_id": "5f7211d1ab735642446f6734",
    "start_time_utc": "2020-12-22T01:30:00Z",
    "end_time_utc": "2020-12-22T02:00:00Z",
    "is_recurring": true,
    "created_at": "2020-09-28T16:39:45.553Z",
    "updated_at": "2020-09-28T16:39:45.553Z",
    "__v": 0,
    "master_event_id": {
      "_id": "5f7211d1ab735642446f6734"
    },
    "master_time_id": "5f7211d1ab735642446f6734-1608600600000",
    "playlist_executed": [
      {
        "_id": "5fe150d34e533440f014e7ec",
        "type": "traffic",
        "executed_time_utc": "2020-12-22T01:50:11.457Z",
        "traffic": {
          "repeat_rule": {
            "byweekday": [
              "TU"
            ],
            "repeat_start_date": "2019-04-02T00:30:00.000Z",
            "frequency": 2,
            "repeat_end_date": "2023-11-28T01:30:00.000Z"
          },
          "traffic_details": {
            "type": "Underwriting",
            "title": "Gay And Lesbian Fund For Colorado",
            "description": "<p>**********A few Versions available to choose from. Please play from wav cart- Underwriting file********</p>",
            "producer": null,
            "custom": {
              "old_comrad_event_id": "2172",
              "old_comrad_scheduled_event_ids": [
                "13925"
              ]
            },
            "underwriter_name": "Gay And Lesbian Fund For Colorado"
          },
          "status": "active",
          "_id": "5f7211faab73564244701108",
          "start_time_utc": "2020-12-22T01:30:00Z",
          "end_time_utc": "2020-12-22T01:31:00Z",
          "is_recurring": true,
          "created_at": "2020-09-28T16:40:26.782Z",
          "updated_at": "2020-09-28T16:40:26.782Z",
          "__v": 0,
          "master_event_id": {
            "_id": "5f7211faab73564244701108"
          },
          "master_time_id": "5f7211faab73564244701108-1608600600000"
        },
        "master_time_id": "5f7211faab73564244701108-1608600600000"
      },
      {
        "_id": "5fe150fe4e533440f014e7ee",
        "type": "track",
        "track": {
          "popularity": 0,
          "_id": "5f721073ab7356424469e2d1",
          "name": "Booker's Waltz",
          "album": {
            "popularity": 0,
            "_id": "5f720f0566320235249bba6e",
            "name": "Blue Glass Music",
            "artist": "5f720eda66320235249a07c4",
            "label": "test",
            "genre": null,
            "compilation": false,
            "created_at": "2011-09-27T20:05:38.000Z",
            "custom": {
              "itunes_id": null,
              "old_comrad_id": "1111189315",
              "local": null,
              "location": "Gnu Bin"
            },
            "type": "album",
            "updated_at": "2020-09-28T16:27:49.218Z"
          },
          "track_number": 4,
          "disk_number": 1,
          "duration_in_seconds": 369,
          "custom": {
            "old_comrad_id": "229776"
          },
          "type": "track",
          "artists": [
            {
              "popularity": 0,
              "_id": "5f720eda66320235249a07c4",
              "name": "Carol Morgan Quartet",
              "type": "artist",
              "created_at": "2020-09-28T16:27:06.262Z",
              "updated_at": "2020-09-28T16:27:06.262Z"
            }
          ],
          "created_at": "2020-09-28T16:33:55.737Z",
          "updated_at": "2020-09-28T16:33:55.737Z"
        },
        "executed_time_utc": "2020-12-22T01:50:54.520Z"
      }
    ]
  },
  {
    "repeat_rule": {
      "byweekday": [
        "TU"
      ],
      "repeat_start_date": "2015-09-08T00:00:00.000Z",
      "frequency": 2,
      "repeat_end_date": "9999-01-01T06:00:00.000Z"
    },
    "show_details": {
      "host_type": "User",
      "guests": [
        null
      ],
      "title": "Labor Exchange",
      "summary": "Interviews with Local and National Labor Activists and Workers",
      "description": "<p>Interviews with local and national labor activists and workers every other Monday evening at 6pm.</p>",
      "producer": null,
      "host": {
        "on_air_name": "Dennis Creese",
        "_id": "5f7211bfab735642446f66b1",
        "first_name": "Dennis",
        "last_name": "Creese"
      },
      "custom": {
        "record_audio": "1",
        "url": "laborexchange",
        "source": "KGNU"
      }
    },
    "status": "active",
    "_id": "5f7211d1ab735642446f6733",
    "start_time_utc": "2020-12-22T01:00:00Z",
    "end_time_utc": "2020-12-22T01:30:00Z",
    "is_recurring": true,
    "created_at": "2020-09-28T16:39:45.548Z",
    "updated_at": "2020-09-28T16:39:45.548Z",
    "__v": 0,
    "master_event_id": {
      "_id": "5f7211d1ab735642446f6733"
    },
    "master_time_id": "5f7211d1ab735642446f6733-1608598800000",
    "playlist_executed": []
  },
  {
    "repeat_rule": {
      "byweekday": [
        "TU"
      ],
      "repeat_start_date": "2015-09-15T00:00:00.000Z",
      "frequency": 2,
      "repeat_end_date": "9999-01-01T06:00:00.000Z"
    },
    "show_details": {
      "host_type": "User",
      "guests": [
        null
      ],
      "title": "La Lucha Sigue",
      "summary": "News About Latin America and the Caribbean",
      "description": "<p><span style=\"font-family: Arial,sans-serif;\">La Lucha Sigue means \"The Struggle Continues\". This half hour news show, played every other Monday on KGNU, explores stories and issues from Latin America and the Caribbean that are seldom heard on US media.</span></p>",
      "producer": null,
      "host": {
        "on_air_name": "Leo Gruip-Ruiz and Marge Taniwaki",
        "_id": "5f7211bfab735642446f66b0",
        "first_name": "Leo",
        "last_name": "Gruip-Ruiz and Marge Taniwaki"
      },
      "custom": {
        "record_audio": "1",
        "url": "laluchasigue",
        "source": "KGNU"
      }
    },
    "status": "active",
    "_id": "5f7211d1ab735642446f6732",
    "start_time_utc": "2020-12-22T01:00:00Z",
    "end_time_utc": "2020-12-22T01:30:00Z",
    "is_recurring": true,
    "created_at": "2020-09-28T16:39:45.543Z",
    "updated_at": "2020-09-28T16:39:45.543Z",
    "__v": 0,
    "master_event_id": {
      "_id": "5f7211d1ab735642446f6732"
    },
    "master_time_id": "5f7211d1ab735642446f6732-1608598800000",
    "playlist_executed": []
  }
]
```

<h3 id="recent-shows-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Returns an array containing the current and recent shows|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Recent Plays

<a id="opIdRecentPlays"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/recent-plays',
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

`GET /recent-plays`

Returns the 10 most recents items played. Items can be tracks, voice breaks, comments, or traffic. Only returns items from the past 24 hours.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

```json
[
  {
    "type": "track",
    "track": {
      "name": "Morning Morning",
      "album": "Second Album",
      "album_artist": "The Fugs",
      "artists": [
        "The Fugs"
      ],
      "duration_in_seconds": 129
    }
  },
  {
    "type": "track",
    "track": {
      "name": "Watt",
      "album": "Floored by Four",
      "album_artist": "Floored by Four",
      "artists": [
        "Floored by Four"
      ],
      "duration_in_seconds": 237
    }
  },
  {
    "type": "traffic",
    "traffic": {
      "type": "Legal ID",
      "title": "Legal Id"
    }
  },
  {
    "type": "track",
    "track": {
      "name": "Restless",
      "album": "Toss Up",
      "album_artist": "Kevin Krauter",
      "artists": [
        "Kevin Krauter"
      ],
      "duration_in_seconds": 183
    }
  },
  {
    "type": "traffic",
    "traffic": {
      "type": "Legal ID",
      "title": "Legal Id"
    }
  },
  {
    "type": "voice_break"
  },
  {
    "type": "track",
    "track": {
      "name": "Clash",
      "album": "Fantasma",
      "album_artist": "Cornelius",
      "artists": [
        "Cornelius"
      ],
      "duration_in_seconds": 356
    }
  },
  {
    "type": "track",
    "track": {
      "name": "Clash",
      "album": "Night Wave",
      "album_artist": "Yuko Fujiyama",
      "artists": [
        "Yuko Fujiyama"
      ],
      "duration_in_seconds": 60
    }
  },
  {
    "type": "traffic",
    "traffic": {
      "type": "Announcement",
      "title": "Ongoing Promo"
    }
  },
  {
    "type": "comment",
    "description": "<p>test comment</p>"
  }
]
```

<h3 id="recent-plays-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Array of the 10 most recent items played|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="recent-plays-responseschema">Response Schema</h3>

Status Code **200**

|Name|Type|Description|
|---|---|---|---|---|
|Array of items played|[object]|Array of objects, where each object is an item played. Array appears in the order the items were played.|
|» type|string|The type of item. Possible values: `track`, `traffic`, `comment, `voice_break`|

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
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

<h1 id="comrad-api-shows">Shows</h1>

## Create

<a id="opIdCreateShow"></a>

> Code samples

```javascript
const inputBody = '{
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
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/shows',
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

`POST /events/shows`

Create a new Show record.

If `startDate` and `endDate` are provided in the request body, the API endpoint will return the show instances that occur between the start date and end date. Otherwise, no data will be returned.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`

> Body parameter

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

<h3 id="create-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|[Show](#schemashow)|false|none|

> Example responses

> 201 Response

```json
[
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
]
```

<h3 id="create-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|201|[Created](https://tools.ietf.org/html/rfc7231#section-6.3.2)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the parameters you provided. See response for more details|

<h3 id="create-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

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
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the parameters you provided. See response for more details|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Create Instance from Series

<a id="opIdCreateInstanceShow"></a>

> Code samples

```javascript
const inputBody = '{
  "start_time_utc": "2020-11-03T16:30:00Z",
  "end_time_utc": "2020-11-03T18:00:00Z",
  "show_details": {
    "host": "5f720bae0504f73464bd83eb"
  }
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/shows/{id}',
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

`POST /events/shows/{id}`

Creates an instance for the show series

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. In addition, `DJ` users can access this endpoint for show series on which they are the host.

> Body parameter

```json
{
  "start_time_utc": "2020-11-03T16:30:00Z",
  "end_time_utc": "2020-11-03T18:00:00Z",
  "show_details": {
    "host": "5f720bae0504f73464bd83eb"
  }
}
```

<h3 id="create-instance-from-series-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|any|false|none|
|id|path|undefined|true|The id of the show series or instance to update|

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
      "repeat_start_date": "2020-11-03T16:30:00.000Z",
      "frequency": 2,
      "repeat_end_date": "2020-11-03T18:00:00.000Z"
    },
    "show_details": {
      "host_type": "User",
      "guests": [],
      "title": "Morning Sound Alternative",
      "summary": "Diverse and eclectic sounds, on the mellow side.",
      "description": "<p>Diverse and eclectic sounds, on the mellow side. You'll hear everything from Ambient Electronics to Reggae to Folk.</p>",
      "producer": null,
      "host": {
        "on_air_name": "DJ Cool Software",
        "_id": "5f720bae0504f73464bd83eb",
        "first_name": "Comrad",
        "last_name": "Develpment"
      },
      "custom": {
        "record_audio": "1",
        "url": "morningsound",
        "source": "KGNU"
      }
    },
    "status": "active",
    "_id": "5fa17ce4f13d484dc0922aef",
    "start_time_utc": "2020-11-03T16:30:00Z",
    "end_time_utc": "2020-11-03T18:00:00Z",
    "is_recurring": false,
    "created_at": "2020-11-03T15:53:08.207Z",
    "updated_at": "2020-11-03T15:53:08.207Z",
    "__v": 0,
    "master_event_id": {
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
          "record_audio": "1",
          "url": "morningsound",
          "source": "KGNU"
        }
      },
      "status": "active",
      "_id": "5f7211d1ab735642446f672c",
      "start_time_utc": "2011-03-28T15:30:00.000Z",
      "end_time_utc": "2011-03-28T18:06:00.000Z",
      "is_recurring": true,
      "created_at": "2020-09-28T16:39:45.464Z",
      "updated_at": "2020-09-28T16:39:45.464Z",
      "__v": 0
    },
    "replace_event_date": "2020-11-03T16:30:00.000Z",
    "master_time_id": "5f7211d1ab735642446f672c-1604421000000"
  }
]
```

<h3 id="create-instance-from-series-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|The populated show record of the newly created instance|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|A server error occurred. Check the response for more details.|

<h3 id="create-instance-from-series-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Get by ID

<a id="opIdGetByIdShows"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/shows/{id}',
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

`GET /events/shows/{id}`

Get a show by ID, or by a show id with a master time id

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="get-by-id-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|The id of the show series or instance to update|

> Example responses

> 200 Response

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
    "guests": [
      "Sample Guest"
    ],
    "title": "Morning Sound Alternative",
    "summary": "Diverse and eclectic sounds, on the mellow side.",
    "description": "<p>Diverse and eclectic sounds, on the mellow side. You'll hear everything from Ambient Electronics to Reggae to Folk.</p>",
    "producer": null,
    "host": null,
    "custom": {
      "custom_property": "Custom value"
    }
  },
  "status": "active",
  "_id": "5f7211d1ab735642446f672c",
  "start_time_utc": "2020-10-19T15:30:00Z",
  "end_time_utc": "2020-10-19T18:06:00Z",
  "is_recurring": true,
  "created_at": "2020-09-28T16:39:45.464Z",
  "updated_at": "2020-09-28T16:39:45.464Z",
  "__v": 0,
  "master_event_id": {
    "_id": "5f7211d1ab735642446f672c"
  },
  "master_time_id": "5f7211d1ab735642446f672c-1603121400000"
}
```

<h3 id="get-by-id-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="get-by-id-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Delete

<a id="opIdDeleteShow"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/shows/{id}',
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

`DELETE /events/shows/{id}`

Deletes a show with the specified ID. Returns the deleted show.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`

<h3 id="delete-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|The id of the show series or instance to update|

> Example responses

> 200 Response

```json
{
  "_id": "5fa4a53f65691339fcda6c10",
  "repeat_rule": {
    "byweekday": []
  },
  "show_details": {
    "host_type": "User",
    "guests": [],
    "title": "test show"
  },
  "status": "deleted",
  "start_time_utc": "2020-10-28T11:00:00.000Z",
  "end_time_utc": "2020-10-28T12:00:00.000Z",
  "created_at": "2020-11-06T01:22:07.965Z",
  "updated_at": "2020-11-06T01:22:07.965Z",
  "__v": 0,
  "master_time_id": "5fa4a53f65691339fcda6c10"
}
```

<h3 id="delete-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="delete-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Update

<a id="opIdUpdateShow"></a>

> Code samples

```javascript
const inputBody = '{
  "show_details": {
    "guests": [
      "Sample guest 1",
      "Sample guest 2"
    ]
  }
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/shows/{id}',
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

`PUT /events/shows/{id}`

Update a show series or show instance.

If the time of a show series is updated with a new start/end time, the start/end time of all future occurrences will also be updated.

If `startDate` and `endDate` are provided in the request body, the API endpoint will return the show instances that occur between the start date and end date.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. In addition, `DJ` users can access this endpoint for show series on which they are the host.

> Body parameter

```json
{
  "show_details": {
    "guests": [
      "Sample guest 1",
      "Sample guest 2"
    ]
  }
}
```

<h3 id="update-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|object|true|JSON object of properties to update|
|id|path|undefined|true|The id of the show series or instance to update|

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
      "repeat_start_date": "2020-11-03T16:30:00.000Z",
      "frequency": 2,
      "repeat_end_date": "2020-11-03T18:00:00.000Z"
    },
    "show_details": {
      "host_type": "User",
      "guests": [
        "Sample guest 1",
        "Sample guest 2"
      ],
      "title": "Morning Sound Alternative",
      "summary": "Diverse and eclectic sounds, on the mellow side.",
      "description": "<p>Diverse and eclectic sounds, on the mellow side. You'll hear everything from Ambient Electronics to Reggae to Folk.</p>",
      "producer": null,
      "host": {
        "on_air_name": "DJ Cool Software",
        "_id": "5f720bae0504f73464bd83eb",
        "first_name": "Comrad",
        "last_name": "Develpment"
      },
      "custom": {
        "record_audio": "1",
        "url": "morningsound",
        "source": "KGNU"
      }
    },
    "status": "active",
    "_id": "5fa17ce4f13d484dc0922aef",
    "start_time_utc": "2020-11-03T16:30:00Z",
    "end_time_utc": "2020-11-03T18:00:00Z",
    "is_recurring": false,
    "created_at": "2020-11-03T15:53:08.207Z",
    "updated_at": "2020-11-03T15:53:08.207Z",
    "__v": 0,
    "master_event_id": {
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
          "record_audio": "1",
          "url": "morningsound",
          "source": "KGNU"
        }
      },
      "status": "active",
      "_id": "5f7211d1ab735642446f672c",
      "start_time_utc": "2011-03-28T15:30:00.000Z",
      "end_time_utc": "2011-03-28T18:06:00.000Z",
      "is_recurring": true,
      "created_at": "2020-09-28T16:39:45.464Z",
      "updated_at": "2020-09-28T16:39:45.464Z",
      "__v": 0
    },
    "replace_event_date": "2020-11-03T16:30:00.000Z",
    "master_time_id": "5f7211d1ab735642446f672c-1604421000000"
  }
]
```

<h3 id="update-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|

<h3 id="update-responseschema">Response Schema</h3>

Status Code **200**

|Name|Type|Description|
|---|---|---|---|---|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Current Show

<a id="opIdCurrentShow"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/current-show',
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

`GET /current-show`

Returns the show that's currently playing, or null if there is no show currently playing.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

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
  "master_time_id": "5f35a6ef783e63454cd918f1-1600270200000",
  "playlist_executed": [
    {
      "_id": "5f7b21b818540a4330aba289",
      "executed_time_utc": "2020-10-05T13:27:27.465Z",
      "type": "track",
      "track": {
        "popularity": 0,
        "_id": "5f72104dab73564244687fd7",
        "name": "Low is the Way",
        "album": {
          "popularity": 48,
          "_id": "5f720ef866320235249b55ab",
          "name": "Uncloudy Day",
          "artist": "5f720ed9663202352499d351",
          "label": "Rhino Records",
          "genre": "5f720ef466320235249b2da0",
          "compilation": false,
          "custom": {
            "my_custom_property": "Custom value"
          },
          "type": "album",
          "created_at": "2020-09-28T16:27:36.929Z",
          "updated_at": "2020-09-28T16:27:36.929Z"
        },
        "track_number": 0,
        "disk_number": 1,
        "duration_in_seconds": 0,
        "custom": {
          "a_custom_property": "Custom Value"
        },
        "type": "track",
        "artists": [
          {
            "popularity": 53,
            "_id": "5f720ed9663202352499d351",
            "name": "The Staple Singers",
            "type": "artist",
            "created_at": "2020-09-28T16:27:05.636Z",
            "updated_at": "2020-09-28T16:27:05.636Z"
          }
        ],
        "created_at": "2020-09-28T16:33:17.805Z",
        "updated_at": "2020-09-28T16:33:17.805Z"
      }
    },
    {
      "_id": "5f7b219018540a4330aba284",
      "type": "traffic",
      "executed_time_utc": "2020-10-05T13:37:20.601Z",
      "traffic": {
        "repeat_rule": {
          "byweekday": [],
          "repeat_start_date": "2011-04-03T13:00:00.000Z",
          "frequency": 3,
          "repeat_end_date": "9999-01-01T06:00:00.000Z"
        },
        "traffic_details": {
          "type": "Legal ID",
          "title": "Legal Id",
          "description": "\"KGNU, Boulder, Denver and Fort Collins\"",
          "producer": null,
          "custom": {
            "a_custom_property": "custom value"
          }
        },
        "status": "active",
        "_id": "5f7211f2ab735642446feea4",
        "start_time_utc": "2020-10-05T13:00:00Z",
        "end_time_utc": "2020-10-05T13:01:00Z",
        "is_recurring": true,
        "created_at": "2020-09-28T16:40:18.628Z",
        "updated_at": "2020-09-28T16:40:18.628Z",
        "__v": 0,
        "master_event_id": {
          "_id": "5f7211f2ab735642446feea4"
        },
        "master_time_id": "5f7211f2ab735642446feea4-1601902800000"
      },
      "master_time_id": "5f7211f2ab735642446feea4-1601902800000"
    },
    {
      "_id": "5f7b219218540a4330aba286",
      "type": "traffic",
      "executed_time_utc": "2020-10-05T13:37:22.408Z",
      "traffic": {
        "repeat_rule": {
          "byweekday": [
            "MO",
            "FR",
            "MO",
            "FR"
          ],
          "repeat_start_date": "2020-05-01T13:00:00.000Z",
          "frequency": 2,
          "repeat_end_date": "2021-04-02T13:00:00.000Z"
        },
        "traffic_details": {
          "type": "Underwriting",
          "title": "Comrad Software 2020",
          "description": "<p>Support comes from</p>\n<p>Comrad Software. </p>",
          "producer": null,
          "custom": {
            "custom_property": "custom value"
          },
          "underwriter_name": "Save Home Heat 2020"
        },
        "status": "active",
        "_id": "5f7211feab735642447026c4",
        "start_time_utc": "2020-10-05T13:00:00Z",
        "end_time_utc": "2020-10-05T13:01:00Z",
        "is_recurring": true,
        "created_at": "2020-09-28T16:40:30.823Z",
        "updated_at": "2020-09-28T16:40:30.823Z",
        "__v": 0,
        "master_event_id": {
          "_id": "5f7211feab735642447026c4"
        },
        "master_time_id": "5f7211feab735642447026c4-1601902800000"
      },
      "master_time_id": "5f7211feab735642447026c4-1601902800000"
    },
    {
      "_id": "5f7b219718540a4330aba288",
      "type": "voice_break",
      "executed_time_utc": "2020-10-05T13:37:27.465Z"
    }
  ]
}
```

<h3 id="current-show-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|The show that is currently playing, including its playlist. `null` if there is no show currently playing.|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Find by Date and Name

<a id="opIdFindShowByDateAndName"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/shows/{date}',
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

`GET /events/shows/{date}`

Pull all shows on a particular date, filtered by those matching the optional `name` parameter.  If `name` is provided, only returns the most relevant results.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="find-by-date-and-name-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|name|query|undefined|false|Return shows matching this string|
|date|path|undefined|true|The date on which to pull shows|

> Example responses

> 200 Response

```json
[
  [
    {
      "_id": "5f7211d1ab735642446f66fa",
      "repeat_rule": {
        "byweekday": [
          "TU",
          "WE",
          "TH",
          "FR"
        ],
        "repeat_start_date": "2011-03-29T09:00:00.000Z",
        "frequency": 2,
        "repeat_end_date": "9999-01-01T06:00:00.000Z"
      },
      "show_details": {
        "host_type": "User",
        "guests": [
          null
        ],
        "title": "Restless Mornings",
        "summary": "The proving ground for new talent",
        "description": "<p><span style=\"font-family: Arial,sans-serif;\">Anything can happen as new DJs get their chops behind the mixing board.<br /></span></p>",
        "producer": null,
        "host": null,
        "custom": {
          "record_audio": "1",
          "url": "restless",
          "source": "KGNU"
        }
      },
      "status": "active",
      "start_time_utc": "2020-12-16T10:00:00Z",
      "end_time_utc": "2020-12-16T12:30:00Z",
      "is_recurring": true,
      "created_at": "2020-09-28T16:39:45.137Z",
      "updated_at": "2020-09-28T16:39:45.137Z",
      "__v": 0,
      "master_event_id": {
        "_id": "5f7211d1ab735642446f66fa"
      },
      "master_time_id": "5f7211d1ab735642446f66fa-1608112800000",
      "playlist_executed": [
        {
          "_id": "5fdb8857dd9f7341283d80dd",
          "type": "track",
          "track": {
            "popularity": 0,
            "_id": "5f72109aab735642446ad5f8",
            "name": "I Think, Therefore I Am",
            "album": {
              "popularity": 0,
              "_id": "5f720efe66320235249b9b04",
              "name": "Take One",
              "artist": "5f720eda663202352499f795",
              "label": "Destination Unknown",
              "genre": "5f720ef466320235249b2d93",
              "compilation": false,
              "custom": {
                "itunes_id": null,
                "old_comrad_id": "1111177433",
                "local": "0",
                "location": "Digital Library"
              },
              "type": "album",
              "created_at": "2020-09-28T16:27:42.994Z",
              "updated_at": "2020-09-28T16:27:42.994Z"
            },
            "track_number": 2,
            "disk_number": 1,
            "duration_in_seconds": 259,
            "custom": {
              "old_comrad_id": "292290"
            },
            "type": "track",
            "artists": [
              {
                "popularity": 0,
                "_id": "5f720eda663202352499f795",
                "name": "The Way Low Down",
                "type": "artist",
                "created_at": "2020-09-28T16:27:06.069Z",
                "updated_at": "2020-09-28T16:27:06.069Z"
              }
            ],
            "created_at": "2020-09-28T16:34:34.312Z",
            "updated_at": "2020-09-28T16:34:34.312Z"
          },
          "executed_time_utc": "2020-12-17T16:33:27.295Z"
        }
      ]
    }
  ]
]
```

<h3 id="find-by-date-and-name-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the parameters you provided. See response for more details|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-by-date-and-name-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Next Show

<a id="opIdNextShow"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/next-show',
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

`GET /next-show`

Returns the next show that will be playing. Returns `null` if there are no shows occurring within the next day.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

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
  "master_time_id": "5f35a6ef783e63454cd918f1-1600270200000",
  "playlist_executed": [
    {
      "_id": "5f7b21b818540a4330aba289",
      "executed_time_utc": "2020-10-05T13:27:27.465Z",
      "type": "track",
      "track": {
        "popularity": 0,
        "_id": "5f72104dab73564244687fd7",
        "name": "Low is the Way",
        "album": {
          "popularity": 48,
          "_id": "5f720ef866320235249b55ab",
          "name": "Uncloudy Day",
          "artist": "5f720ed9663202352499d351",
          "label": "Rhino Records",
          "genre": "5f720ef466320235249b2da0",
          "compilation": false,
          "custom": {
            "my_custom_property": "Custom value"
          },
          "type": "album",
          "created_at": "2020-09-28T16:27:36.929Z",
          "updated_at": "2020-09-28T16:27:36.929Z"
        },
        "track_number": 0,
        "disk_number": 1,
        "duration_in_seconds": 0,
        "custom": {
          "a_custom_property": "Custom Value"
        },
        "type": "track",
        "artists": [
          {
            "popularity": 53,
            "_id": "5f720ed9663202352499d351",
            "name": "The Staple Singers",
            "type": "artist",
            "created_at": "2020-09-28T16:27:05.636Z",
            "updated_at": "2020-09-28T16:27:05.636Z"
          }
        ],
        "created_at": "2020-09-28T16:33:17.805Z",
        "updated_at": "2020-09-28T16:33:17.805Z"
      }
    },
    {
      "_id": "5f7b219018540a4330aba284",
      "type": "traffic",
      "executed_time_utc": "2020-10-05T13:37:20.601Z",
      "traffic": {
        "repeat_rule": {
          "byweekday": [],
          "repeat_start_date": "2011-04-03T13:00:00.000Z",
          "frequency": 3,
          "repeat_end_date": "9999-01-01T06:00:00.000Z"
        },
        "traffic_details": {
          "type": "Legal ID",
          "title": "Legal Id",
          "description": "\"KGNU, Boulder, Denver and Fort Collins\"",
          "producer": null,
          "custom": {
            "a_custom_property": "custom value"
          }
        },
        "status": "active",
        "_id": "5f7211f2ab735642446feea4",
        "start_time_utc": "2020-10-05T13:00:00Z",
        "end_time_utc": "2020-10-05T13:01:00Z",
        "is_recurring": true,
        "created_at": "2020-09-28T16:40:18.628Z",
        "updated_at": "2020-09-28T16:40:18.628Z",
        "__v": 0,
        "master_event_id": {
          "_id": "5f7211f2ab735642446feea4"
        },
        "master_time_id": "5f7211f2ab735642446feea4-1601902800000"
      },
      "master_time_id": "5f7211f2ab735642446feea4-1601902800000"
    },
    {
      "_id": "5f7b219218540a4330aba286",
      "type": "traffic",
      "executed_time_utc": "2020-10-05T13:37:22.408Z",
      "traffic": {
        "repeat_rule": {
          "byweekday": [
            "MO",
            "FR",
            "MO",
            "FR"
          ],
          "repeat_start_date": "2020-05-01T13:00:00.000Z",
          "frequency": 2,
          "repeat_end_date": "2021-04-02T13:00:00.000Z"
        },
        "traffic_details": {
          "type": "Underwriting",
          "title": "Comrad Software 2020",
          "description": "<p>Support comes from</p>\n<p>Comrad Software. </p>",
          "producer": null,
          "custom": {
            "custom_property": "custom value"
          },
          "underwriter_name": "Save Home Heat 2020"
        },
        "status": "active",
        "_id": "5f7211feab735642447026c4",
        "start_time_utc": "2020-10-05T13:00:00Z",
        "end_time_utc": "2020-10-05T13:01:00Z",
        "is_recurring": true,
        "created_at": "2020-09-28T16:40:30.823Z",
        "updated_at": "2020-09-28T16:40:30.823Z",
        "__v": 0,
        "master_event_id": {
          "_id": "5f7211feab735642447026c4"
        },
        "master_time_id": "5f7211feab735642447026c4-1601902800000"
      },
      "master_time_id": "5f7211feab735642447026c4-1601902800000"
    },
    {
      "_id": "5f7b219718540a4330aba288",
      "type": "voice_break",
      "executed_time_utc": "2020-10-05T13:37:27.465Z"
    }
  ]
}
```

<h3 id="next-show-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|An object containing the next show, including its playlist. `null` if no shows occur during the next day.|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Previous Show

<a id="opIdPreviousShow"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/previous-show',
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

`GET /previous-show`

Returns the previous show that was playing. Returns `null` if there are no shows occurring within the previous day.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

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
  "master_time_id": "5f35a6ef783e63454cd918f1-1600270200000",
  "playlist_executed": [
    {
      "_id": "5f7b21b818540a4330aba289",
      "executed_time_utc": "2020-10-05T13:27:27.465Z",
      "type": "track",
      "track": {
        "popularity": 0,
        "_id": "5f72104dab73564244687fd7",
        "name": "Low is the Way",
        "album": {
          "popularity": 48,
          "_id": "5f720ef866320235249b55ab",
          "name": "Uncloudy Day",
          "artist": "5f720ed9663202352499d351",
          "label": "Rhino Records",
          "genre": "5f720ef466320235249b2da0",
          "compilation": false,
          "custom": {
            "my_custom_property": "Custom value"
          },
          "type": "album",
          "created_at": "2020-09-28T16:27:36.929Z",
          "updated_at": "2020-09-28T16:27:36.929Z"
        },
        "track_number": 0,
        "disk_number": 1,
        "duration_in_seconds": 0,
        "custom": {
          "a_custom_property": "Custom Value"
        },
        "type": "track",
        "artists": [
          {
            "popularity": 53,
            "_id": "5f720ed9663202352499d351",
            "name": "The Staple Singers",
            "type": "artist",
            "created_at": "2020-09-28T16:27:05.636Z",
            "updated_at": "2020-09-28T16:27:05.636Z"
          }
        ],
        "created_at": "2020-09-28T16:33:17.805Z",
        "updated_at": "2020-09-28T16:33:17.805Z"
      }
    },
    {
      "_id": "5f7b219018540a4330aba284",
      "type": "traffic",
      "executed_time_utc": "2020-10-05T13:37:20.601Z",
      "traffic": {
        "repeat_rule": {
          "byweekday": [],
          "repeat_start_date": "2011-04-03T13:00:00.000Z",
          "frequency": 3,
          "repeat_end_date": "9999-01-01T06:00:00.000Z"
        },
        "traffic_details": {
          "type": "Legal ID",
          "title": "Legal Id",
          "description": "\"KGNU, Boulder, Denver and Fort Collins\"",
          "producer": null,
          "custom": {
            "a_custom_property": "custom value"
          }
        },
        "status": "active",
        "_id": "5f7211f2ab735642446feea4",
        "start_time_utc": "2020-10-05T13:00:00Z",
        "end_time_utc": "2020-10-05T13:01:00Z",
        "is_recurring": true,
        "created_at": "2020-09-28T16:40:18.628Z",
        "updated_at": "2020-09-28T16:40:18.628Z",
        "__v": 0,
        "master_event_id": {
          "_id": "5f7211f2ab735642446feea4"
        },
        "master_time_id": "5f7211f2ab735642446feea4-1601902800000"
      },
      "master_time_id": "5f7211f2ab735642446feea4-1601902800000"
    },
    {
      "_id": "5f7b219218540a4330aba286",
      "type": "traffic",
      "executed_time_utc": "2020-10-05T13:37:22.408Z",
      "traffic": {
        "repeat_rule": {
          "byweekday": [
            "MO",
            "FR",
            "MO",
            "FR"
          ],
          "repeat_start_date": "2020-05-01T13:00:00.000Z",
          "frequency": 2,
          "repeat_end_date": "2021-04-02T13:00:00.000Z"
        },
        "traffic_details": {
          "type": "Underwriting",
          "title": "Comrad Software 2020",
          "description": "<p>Support comes from</p>\n<p>Comrad Software. </p>",
          "producer": null,
          "custom": {
            "custom_property": "custom value"
          },
          "underwriter_name": "Save Home Heat 2020"
        },
        "status": "active",
        "_id": "5f7211feab735642447026c4",
        "start_time_utc": "2020-10-05T13:00:00Z",
        "end_time_utc": "2020-10-05T13:01:00Z",
        "is_recurring": true,
        "created_at": "2020-09-28T16:40:30.823Z",
        "updated_at": "2020-09-28T16:40:30.823Z",
        "__v": 0,
        "master_event_id": {
          "_id": "5f7211feab735642447026c4"
        },
        "master_time_id": "5f7211feab735642447026c4-1601902800000"
      },
      "master_time_id": "5f7211feab735642447026c4-1601902800000"
    },
    {
      "_id": "5f7b219718540a4330aba288",
      "type": "voice_break",
      "executed_time_utc": "2020-10-05T13:37:27.465Z"
    }
  ]
}
```

<h3 id="previous-show-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|an object containing the previous show, or `null` if no shows occurred within the previous day|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Recent Shows

<a id="opIdRecentShows"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/recent-shows',
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

`GET /recent-shows`

Returns an array of the most recent shows, including the current show. The array is sorted by the show time, descending, so the current show is first in the array and the oldest show is last.

Only returns shows from the past week, and will return a maximum of 50 shows.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

```json
[
  {
    "repeat_rule": {
      "byweekday": [
        "TU"
      ],
      "repeat_start_date": "2011-04-12T00:30:00.000Z",
      "frequency": 2,
      "repeat_end_date": "9999-01-01T06:00:00.000Z"
    },
    "show_details": {
      "host_type": "User",
      "guests": [
        null
      ],
      "title": "Outsources",
      "summary": "Gay, Lesbian, Bisexual, Transgender News and Interviews.",
      "description": "<p>Presenting news and information from the local GLBT community and beyond.</p>",
      "producer": null,
      "host": null,
      "custom": {
        "record_audio": "1",
        "url": "outsources",
        "source": "KGNU"
      }
    },
    "status": "active",
    "_id": "5f7211d1ab735642446f6734",
    "start_time_utc": "2020-12-22T01:30:00Z",
    "end_time_utc": "2020-12-22T02:00:00Z",
    "is_recurring": true,
    "created_at": "2020-09-28T16:39:45.553Z",
    "updated_at": "2020-09-28T16:39:45.553Z",
    "__v": 0,
    "master_event_id": {
      "_id": "5f7211d1ab735642446f6734"
    },
    "master_time_id": "5f7211d1ab735642446f6734-1608600600000",
    "playlist_executed": [
      {
        "_id": "5fe150d34e533440f014e7ec",
        "type": "traffic",
        "executed_time_utc": "2020-12-22T01:50:11.457Z",
        "traffic": {
          "repeat_rule": {
            "byweekday": [
              "TU"
            ],
            "repeat_start_date": "2019-04-02T00:30:00.000Z",
            "frequency": 2,
            "repeat_end_date": "2023-11-28T01:30:00.000Z"
          },
          "traffic_details": {
            "type": "Underwriting",
            "title": "Gay And Lesbian Fund For Colorado",
            "description": "<p>**********A few Versions available to choose from. Please play from wav cart- Underwriting file********</p>",
            "producer": null,
            "custom": {
              "old_comrad_event_id": "2172",
              "old_comrad_scheduled_event_ids": [
                "13925"
              ]
            },
            "underwriter_name": "Gay And Lesbian Fund For Colorado"
          },
          "status": "active",
          "_id": "5f7211faab73564244701108",
          "start_time_utc": "2020-12-22T01:30:00Z",
          "end_time_utc": "2020-12-22T01:31:00Z",
          "is_recurring": true,
          "created_at": "2020-09-28T16:40:26.782Z",
          "updated_at": "2020-09-28T16:40:26.782Z",
          "__v": 0,
          "master_event_id": {
            "_id": "5f7211faab73564244701108"
          },
          "master_time_id": "5f7211faab73564244701108-1608600600000"
        },
        "master_time_id": "5f7211faab73564244701108-1608600600000"
      },
      {
        "_id": "5fe150fe4e533440f014e7ee",
        "type": "track",
        "track": {
          "popularity": 0,
          "_id": "5f721073ab7356424469e2d1",
          "name": "Booker's Waltz",
          "album": {
            "popularity": 0,
            "_id": "5f720f0566320235249bba6e",
            "name": "Blue Glass Music",
            "artist": "5f720eda66320235249a07c4",
            "label": "test",
            "genre": null,
            "compilation": false,
            "created_at": "2011-09-27T20:05:38.000Z",
            "custom": {
              "itunes_id": null,
              "old_comrad_id": "1111189315",
              "local": null,
              "location": "Gnu Bin"
            },
            "type": "album",
            "updated_at": "2020-09-28T16:27:49.218Z"
          },
          "track_number": 4,
          "disk_number": 1,
          "duration_in_seconds": 369,
          "custom": {
            "old_comrad_id": "229776"
          },
          "type": "track",
          "artists": [
            {
              "popularity": 0,
              "_id": "5f720eda66320235249a07c4",
              "name": "Carol Morgan Quartet",
              "type": "artist",
              "created_at": "2020-09-28T16:27:06.262Z",
              "updated_at": "2020-09-28T16:27:06.262Z"
            }
          ],
          "created_at": "2020-09-28T16:33:55.737Z",
          "updated_at": "2020-09-28T16:33:55.737Z"
        },
        "executed_time_utc": "2020-12-22T01:50:54.520Z"
      }
    ]
  },
  {
    "repeat_rule": {
      "byweekday": [
        "TU"
      ],
      "repeat_start_date": "2015-09-08T00:00:00.000Z",
      "frequency": 2,
      "repeat_end_date": "9999-01-01T06:00:00.000Z"
    },
    "show_details": {
      "host_type": "User",
      "guests": [
        null
      ],
      "title": "Labor Exchange",
      "summary": "Interviews with Local and National Labor Activists and Workers",
      "description": "<p>Interviews with local and national labor activists and workers every other Monday evening at 6pm.</p>",
      "producer": null,
      "host": {
        "on_air_name": "Dennis Creese",
        "_id": "5f7211bfab735642446f66b1",
        "first_name": "Dennis",
        "last_name": "Creese"
      },
      "custom": {
        "record_audio": "1",
        "url": "laborexchange",
        "source": "KGNU"
      }
    },
    "status": "active",
    "_id": "5f7211d1ab735642446f6733",
    "start_time_utc": "2020-12-22T01:00:00Z",
    "end_time_utc": "2020-12-22T01:30:00Z",
    "is_recurring": true,
    "created_at": "2020-09-28T16:39:45.548Z",
    "updated_at": "2020-09-28T16:39:45.548Z",
    "__v": 0,
    "master_event_id": {
      "_id": "5f7211d1ab735642446f6733"
    },
    "master_time_id": "5f7211d1ab735642446f6733-1608598800000",
    "playlist_executed": []
  },
  {
    "repeat_rule": {
      "byweekday": [
        "TU"
      ],
      "repeat_start_date": "2015-09-15T00:00:00.000Z",
      "frequency": 2,
      "repeat_end_date": "9999-01-01T06:00:00.000Z"
    },
    "show_details": {
      "host_type": "User",
      "guests": [
        null
      ],
      "title": "La Lucha Sigue",
      "summary": "News About Latin America and the Caribbean",
      "description": "<p><span style=\"font-family: Arial,sans-serif;\">La Lucha Sigue means \"The Struggle Continues\". This half hour news show, played every other Monday on KGNU, explores stories and issues from Latin America and the Caribbean that are seldom heard on US media.</span></p>",
      "producer": null,
      "host": {
        "on_air_name": "Leo Gruip-Ruiz and Marge Taniwaki",
        "_id": "5f7211bfab735642446f66b0",
        "first_name": "Leo",
        "last_name": "Gruip-Ruiz and Marge Taniwaki"
      },
      "custom": {
        "record_audio": "1",
        "url": "laluchasigue",
        "source": "KGNU"
      }
    },
    "status": "active",
    "_id": "5f7211d1ab735642446f6732",
    "start_time_utc": "2020-12-22T01:00:00Z",
    "end_time_utc": "2020-12-22T01:30:00Z",
    "is_recurring": true,
    "created_at": "2020-09-28T16:39:45.543Z",
    "updated_at": "2020-09-28T16:39:45.543Z",
    "__v": 0,
    "master_event_id": {
      "_id": "5f7211d1ab735642446f6732"
    },
    "master_time_id": "5f7211d1ab735642446f6732-1608598800000",
    "playlist_executed": []
  }
]
```

<h3 id="recent-shows-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Returns an array containing the current and recent shows|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Delete Instance from Series

<a id="opIdDeleteInstanceFromShowSeries"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/shows/{id}/remove-instance-from-series',
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

`DELETE /events/shows/{id}/remove-instance-from-series`

Creates an exceptioin to a series's usual repeat rule so that there will not be a show instance at the specified date/time.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`

<h3 id="delete-instance-from-series-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|The id of the show series|

> Example responses

> 200 Response

```json
{
  "repeat_rule": {
    "byweekday": [],
    "repeat_start_date": "2020-11-09T16:30:00.000Z",
    "repeat_end_date": "2020-11-09T19:06:00.000Z"
  },
  "show_details": {
    "host_type": "User",
    "guests": []
  },
  "status": "deleted",
  "_id": "5fa9377600452c4f3cf2c87b",
  "master_event_id": "5f7211d1ab735642446f672c",
  "start_time_utc": "2020-11-09T16:30:00.000Z",
  "end_time_utc": "2020-11-09T19:06:00.000Z",
  "replace_event_date": "2020-11-09T16:30:00.000Z",
  "is_recurring": false,
  "created_at": "2020-11-09T12:35:02.033Z",
  "updated_at": "2020-11-09T12:35:02.033Z",
  "__v": 0,
  "master_time_id": "5f7211d1ab735642446f672c-1604939400000"
}
```

<h3 id="delete-instance-from-series-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="delete-instance-from-series-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Search

<a id="opIdSearchShows"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/shows/search',
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

`GET /events/shows/search`

Search for a show series, looking for the provided search string in the show title.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="search-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|s|query|undefined|true|Find series matching this search string|

> Example responses

> 200 Response

```json
[
  [
    {
      "show_details": {
        "host_type": "User",
        "title": "Sleepless Nights",
        "summary": "Late night freeform radio",
        "description": "<p>This freeform show makes room for everything, combining the aesthetics of the Morning and Afternoon shows and leaving the door open for more extreme and intense audio excursions.</p>",
        "producer": null,
        "host": null,
        "custom": {
          "record_audio": "1",
          "url": "sleepless",
          "source": "KGNU"
        }
      },
      "_id": "5f7211d1ab735642446f66f8",
      "start_time_utc": "2011-03-27T06:00:00.000Z",
      "score": 0.75
    }
  ]
]
```

<h3 id="search-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the parameters you provided. See response for more details|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="search-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

<h1 id="comrad-api-traffic">Traffic</h1>

## Create

<a id="opIdCreateTraffic"></a>

> Code samples

```javascript
const inputBody = '{
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
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/traffic',
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

`POST /events/traffic`

Create a new Traffic record.

If `startDate` and `endDate` are provided in the request body, the API endpoint will return the show instances that occur between the start date and end date. Otherwise, no data will be returned.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Underwriting`

> Body parameter

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

<h3 id="create-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|[Show](#schemashow)|false|none|

> Example responses

> 201 Response

```json
[
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
      "__v": 0
    }
  ]
]
```

<h3 id="create-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|201|[Created](https://tools.ietf.org/html/rfc7231#section-6.3.2)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the parameters you provided. See response for more details|

<h3 id="create-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

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
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the parameters you provided. See response for more details|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Create Instance from Series

<a id="opIdCreateInstanceTraffic"></a>

> Code samples

```javascript
const inputBody = '{
  "start_time_utc": "2020-11-03T22:00:00.000Z",
  "end_time_utc": "2020-11-03T22:00:00.000Z"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/traffic/{id}',
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

`POST /events/traffic/{id}`

Creates an instance for the traffic series

The following roles can access this API endpoint: `Admin`, `Full Access`, `Underwriting`.

> Body parameter

```json
{
  "start_time_utc": "2020-11-03T22:00:00.000Z",
  "end_time_utc": "2020-11-03T22:00:00.000Z"
}
```

<h3 id="create-instance-from-series-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|any|false|none|
|id|path|undefined|true|The id of the traffic series or instance to update|

> Example responses

> 200 Response

```json
[
  {
    "repeat_rule": {
      "byweekday": [],
      "repeat_start_date": "2020-11-03T22:00:00.000Z",
      "frequency": 3,
      "repeat_end_date": "2020-11-03T22:00:00.000Z"
    },
    "status": "active",
    "_id": "5fa180826a7cc716704afe0f",
    "start_time_utc": "2020-11-03T22:00:00Z",
    "end_time_utc": "2020-11-03T22:00:00Z",
    "is_recurring": false,
    "created_at": "2020-11-03T16:08:34.371Z",
    "updated_at": "2020-11-03T16:08:34.371Z",
    "__v": 0,
    "master_event_id": {
      "repeat_rule": {
        "byweekday": [],
        "repeat_start_date": "2011-05-30T21:00:00.000Z",
        "frequency": 3,
        "repeat_end_date": "9999-01-01T06:00:00.000Z"
      },
      "traffic_details": {
        "type": "Legal ID",
        "title": "Legal Id",
        "description": "\"KGNU, Boulder, Denver and Fort Collins\"",
        "producer": null,
        "custom": {
          "old_comrad_event_id": "2",
          "old_comrad_scheduled_event_ids": [
            "297"
          ]
        }
      },
      "status": "active",
      "_id": "5f7211f2ab735642446fee9c",
      "start_time_utc": "2011-05-30T21:00:00.000Z",
      "end_time_utc": "2011-05-30T21:01:00.000Z",
      "is_recurring": true,
      "created_at": "2020-09-28T16:40:18.625Z",
      "updated_at": "2020-09-28T16:40:18.625Z",
      "__v": 0
    },
    "replace_event_date": "2020-11-03T22:00:00.000Z",
    "traffic_details": {
      "type": "Legal ID",
      "title": "Legal Id",
      "description": "\"KGNU, Boulder, Denver and Fort Collins\"",
      "producer": null,
      "custom": {
        "old_comrad_event_id": "2",
        "old_comrad_scheduled_event_ids": [
          "297"
        ]
      }
    },
    "master_time_id": "5f7211f2ab735642446fee9c-1604440800000"
  }
]
```

<h3 id="create-instance-from-series-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|The populated show record of the newly created instance|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|A server error occurred. Check the response for more details.|

<h3 id="create-instance-from-series-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Get by ID

<a id="opIdGetByIdTraffic"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/traffic/{id}',
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

`GET /events/traffic/{id}`

Get a traffic event by ID, or by a traffic id with a master time id

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="get-by-id-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|The id of the traffic series or instance to update|

> Example responses

> 200 Response

```json
{
  "traffic_details": {
    "type": "Underwriting",
    "title": "Rocky Mountain Oysters",
    "description": "<p>a</p>",
    "producer": null,
    "custom": {
      "custom_property": "Custom value"
    },
    "underwriter_name": "Rocky Mountain Oysters"
  },
  "status": "active",
  "_id": "5f7211f4ab735642446ff94a",
  "start_time_utc": "2013-06-11T01:00:00.000Z",
  "end_time_utc": "2013-06-11T01:01:00.000Z",
  "created_at": "2020-09-28T16:40:20.397Z",
  "updated_at": "2020-09-28T16:40:20.397Z",
  "__v": 0
}
```

<h3 id="get-by-id-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="get-by-id-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Delete

<a id="opIdDeleteTraffic"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/traffic/{id}',
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

`DELETE /events/traffic/{id}`

Deletes the traffic event with the specified ID

The following roles can access this API endpoint: `Admin`, `Full Access`, `Underwriting`

<h3 id="delete-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|The id of the traffic series or instance to update|

> Example responses

> 200 Response

```json
{
  "_id": "5fa180826a7cc716704afe0f",
  "repeat_rule": {
    "byweekday": [],
    "repeat_start_date": "2020-11-03T22:00:00.000Z",
    "frequency": 3,
    "repeat_end_date": "2020-11-03T22:00:00.000Z"
  },
  "status": "deleted",
  "start_time_utc": "2020-11-03T22:00:00.000Z",
  "end_time_utc": "2020-11-03T22:00:00.000Z",
  "is_recurring": false,
  "created_at": "2020-11-03T16:08:34.371Z",
  "updated_at": "2020-11-03T16:08:34.371Z",
  "__v": 0,
  "master_event_id": "5f7211f2ab735642446fee9c",
  "replace_event_date": "2020-11-03T22:00:00.000Z",
  "traffic_details": {
    "description": "This is an updated description"
  },
  "master_time_id": "5f7211f2ab735642446fee9c-1604440800000"
}
```

<h3 id="delete-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="delete-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Update

<a id="opIdUpdateTraffic"></a>

> Code samples

```javascript
const inputBody = '{
  "traffic_details": {
    "description": "This is an updated description"
  }
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/traffic/{id}',
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

`PUT /events/traffic/{id}`

Update a traffic series or show instance.

If the time of a traffic series is updated with a new start/end time, the start/end time of all future occurrences will also be updated.

If `startDate` and `endDate` are provided in the request body, the API endpoint will return the show instances that occur between the start date and end date.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Underwriting`.

> Body parameter

```json
{
  "traffic_details": {
    "description": "This is an updated description"
  }
}
```

<h3 id="update-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|object|true|JSON object of properties to update|
|id|path|undefined|true|The id of the traffic series or instance to update|

> Example responses

> 200 Response

```json
[
  {
    "repeat_rule": {
      "byweekday": [],
      "repeat_start_date": "2020-11-03T22:00:00.000Z",
      "frequency": 3,
      "repeat_end_date": "2020-11-03T22:00:00.000Z"
    },
    "traffic_details": {
      "type": "Legal ID",
      "title": "Legal Id",
      "description": "This is an updated description",
      "producer": null,
      "custom": {
        "custom_property": "custom value"
      }
    },
    "status": "active",
    "_id": "5fa180826a7cc716704afe0f",
    "start_time_utc": "2020-11-03T22:00:00Z",
    "end_time_utc": "2020-11-03T22:00:00Z",
    "is_recurring": false,
    "created_at": "2020-11-03T16:08:34.371Z",
    "updated_at": "2020-11-03T16:08:34.371Z",
    "__v": 0,
    "master_event_id": {
      "repeat_rule": {
        "byweekday": [],
        "repeat_start_date": "2011-05-30T21:00:00.000Z",
        "frequency": 3,
        "repeat_end_date": "9999-01-01T06:00:00.000Z"
      },
      "traffic_details": {
        "type": "Legal ID",
        "title": "Legal Id",
        "description": "test desc",
        "producer": null,
        "custom": {
          "custom_property": "custom value"
        }
      },
      "status": "active",
      "_id": "5f7211f2ab735642446fee9c",
      "start_time_utc": "2011-05-30T21:00:00.000Z",
      "end_time_utc": "2011-05-30T21:01:00.000Z",
      "is_recurring": true,
      "created_at": "2020-09-28T16:40:18.625Z",
      "updated_at": "2020-09-28T16:40:18.625Z",
      "__v": 0
    },
    "replace_event_date": "2020-11-03T22:00:00.000Z",
    "master_time_id": "5f7211f2ab735642446fee9c-1604440800000"
  }
]
```

<h3 id="update-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|

<h3 id="update-responseschema">Response Schema</h3>

Status Code **200**

|Name|Type|Description|
|---|---|---|---|---|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Find by Date and Name

<a id="opIdFindTrafficByDateAndName"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/traffic/{date}',
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

`GET /events/traffic/{date}`

Pull all traffic on a particular date, filtered by those matching the optional `name` parameter. If `name` is provided, only returns the most relevant results.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="find-by-date-and-name-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|name|query|undefined|false|Return traffic matching this string|
|date|path|undefined|true|The date on which to pull traffic|

> Example responses

> 200 Response

```json
[
  [
    {
      "_id": "5f7211f2ab735642446feeb1",
      "repeat_rule": {
        "byweekday": [
          "MO",
          "TU",
          "WE",
          "TH",
          "FR"
        ],
        "repeat_start_date": "2011-04-11T14:01:00.000Z",
        "frequency": 2,
        "repeat_end_date": "9999-01-01T06:00:00.000Z"
      },
      "traffic_details": {
        "type": "Feature",
        "title": "BBC News Headlines",
        "description": "<p>Found on Channel 10A.&nbsp; Listen for an out cue, you must cut away gracefully after 5 minutes.</p>",
        "producer": "BBC",
        "custom": {
          "old_comrad_event_id": "14",
          "old_comrad_scheduled_event_ids": [
            "146"
          ]
        }
      },
      "status": "active",
      "start_time_utc": "2020-12-16T15:01:00Z",
      "end_time_utc": "2020-12-16T15:06:00Z",
      "is_recurring": true,
      "created_at": "2020-09-28T16:40:18.639Z",
      "updated_at": "2020-09-28T16:40:18.639Z",
      "__v": 0,
      "master_event_id": {
        "_id": "5f7211f2ab735642446feeb1"
      },
      "master_time_id": "5f7211f2ab735642446feeb1-1608130860000"
    },
    {
      "_id": "5f7211f2ab735642446feeaf",
      "repeat_rule": {
        "byweekday": [
          "MO",
          "TU",
          "WE",
          "TH",
          "FR"
        ],
        "repeat_start_date": "2015-07-14T18:01:00.000Z",
        "frequency": 2,
        "repeat_end_date": "9999-01-01T06:00:00.000Z"
      },
      "traffic_details": {
        "type": "Feature",
        "title": "BBC News Headlines",
        "description": "<p>Found on Channel 10A.&nbsp; Listen for an out cue, you must cut away gracefully after 5 minutes.</p>",
        "producer": "BBC",
        "custom": {
          "old_comrad_event_id": "14",
          "old_comrad_scheduled_event_ids": [
            "4873"
          ]
        }
      },
      "status": "active",
      "start_time_utc": "2020-12-16T19:01:00Z",
      "end_time_utc": "2020-12-16T19:06:00Z",
      "is_recurring": true,
      "created_at": "2020-09-28T16:40:18.634Z",
      "updated_at": "2020-09-28T16:40:18.634Z",
      "__v": 0,
      "master_event_id": {
        "_id": "5f7211f2ab735642446feeaf"
      },
      "master_time_id": "5f7211f2ab735642446feeaf-1608145260000"
    }
  ]
]
```

<h3 id="find-by-date-and-name-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the parameters you provided. See response for more details|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-by-date-and-name-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Find Earliest

<a id="opIdFindTrafficEarliest"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/traffic/earliest',
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

`GET /events/traffic/earliest`

Returns the earliest occurring traffic event of a specific type.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="find-earliest-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|type|query|undefined|true|Filter by this type of traffic|

> Example responses

> 200 Response

```json
[
  {
    "repeat_rule": {
      "byweekday": [
        "WE"
      ],
      "repeat_start_date": "2020-01-01T16:00:00.000Z",
      "frequency": 2,
      "repeat_end_date": "2020-12-31T16:00:00.000Z"
    },
    "traffic_details": {
      "type": "Underwriting",
      "title": "Grant Farms",
      "description": "<p>This program is supported by you, the KGNU listener-member, and by Grant Family Farms. Grant Family Farms CSA delivers locally grown, organic vegetables, fruit, eggs, and farm-raised meats year&lsquo;round to neighborhoods in Boulder, Denver, and beyond.</p>",
      "producer": null,
      "custom": {
        "custom_value": "sample custom value"
      },
      "underwriter_name": "Grant Farms"
    },
    "status": "active",
    "_id": "5f7211f2ab735642446feeb3",
    "start_time_utc": "2012-05-02T16:00:00.000Z",
    "end_time_utc": "2012-05-02T16:01:00.000Z",
    "is_recurring": true,
    "created_at": "2020-09-28T16:40:18.668Z",
    "updated_at": "2020-09-28T16:40:18.668Z",
    "__v": 0
  }
]
```

<h3 id="find-earliest-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the parameters you provided. See response for more details|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-earliest-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Delete Instance from Series

<a id="opIdDeleteInstanceFromTrafficSeries"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/traffic/{id}/remove-instance-from-series',
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

`DELETE /events/traffic/{id}/remove-instance-from-series`

Creates an exceptioin to a series's usual repeat rule so that there will not be a traffic instance at the specified date/time.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`

<h3 id="delete-instance-from-series-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|The id of the traffic series|

> Example responses

> 200 Response

```json
{
  "repeat_rule": {
    "byweekday": [],
    "repeat_start_date": "2020-11-09T16:00:00.000Z",
    "repeat_end_date": "2020-11-09T16:01:00.000Z"
  },
  "status": "deleted",
  "_id": "5fa94dcd72207e3b289d5197",
  "master_event_id": "5f7211f2ab735642446feead",
  "start_time_utc": "2020-11-09T16:00:00.000Z",
  "end_time_utc": "2020-11-09T16:01:00.000Z",
  "replace_event_date": "2020-11-09T16:00:00.000Z",
  "is_recurring": false,
  "created_at": "2020-11-09T14:10:21.299Z",
  "updated_at": "2020-11-09T14:10:21.299Z",
  "__v": 0,
  "master_time_id": "5f7211f2ab735642446feead-1604937600000"
}
```

<h3 id="delete-instance-from-series-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="delete-instance-from-series-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Search

<a id="opIdSearchTraffic"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/traffic/search',
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

`GET /events/traffic/search`

Search for a traffic series, looking for the provided search string in the traffic title.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="search-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|s|query|undefined|true|Find series matching this search string|

> Example responses

> 200 Response

```json
[
  [
    {
      "traffic_details": {
        "type": "Underwriting",
        "title": "Rocky Mountain Oysters",
        "description": "<p>Description of the underwriting event</p>",
        "producer": null,
        "custom": {
          "custom_property": "test"
        },
        "underwriter_name": "Rocky Mountain Oysters"
      },
      "_id": "5f7211f4ab735642446ff94a",
      "start_time_utc": "2013-06-11T01:00:00.000Z",
      "score": 1.3333333333333333
    }
  ]
]
```

<h3 id="search-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the parameters you provided. See response for more details|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="search-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Search Underwriters

<a id="opIdSearchUnderwriters"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/events/traffic/search-underwriters',
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

`GET /events/traffic/search-underwriters`

Return a list of underwriters matching the search string.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="search-underwriters-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|s|query|undefined|true|Find underwriters matching the search string|

> Example responses

> 200 Response

```json
[
  [
    "Blue Ribbon Farm",
    "Grant Farms",
    "Grant Family Farms",
    "The Farm"
  ]
]
```

<h3 id="search-underwriters-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the parameters you provided. See response for more details|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="search-underwriters-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

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
|---|---|---|
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
|---|---|---|
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
|---|---|---|
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
|---|---|---|
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
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

<h1 id="comrad-api-host-groups">Host Groups</h1>

## Create

<a id="opIdCreateHostGroup"></a>

> Code samples

```javascript
const inputBody = '{
  "users": [
    "5f35a3cf783e63454ccd7525",
    "5f35a651783e63454cd916d4",
    "5f35a651783e63454cd1425d",
    "5f35a651783e63454cd97ae4"
  ],
  "_id": "5f35abd3661c7f37f047dbcb",
  "on_air_name": "The Way Low Down",
  "__v": 0
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/host-groups/',
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

`POST /host-groups/`

Create a host group.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`

> Body parameter

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

<h3 id="create-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|[HostGroup](#schemahostgroup)|false|none|

> Example responses

> 200 Response

```json
{
  "users": [
    "5f720bae0504f73464bd83eb",
    "5f72114fab735642446f64ed"
  ],
  "_id": "5f8f42482d687150ac9373ba",
  "on_air_name": "Sean and Barry",
  "__v": 0
}
```

<h3 id="create-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="create-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Find by Hosts

<a id="opIdFindHostGroup"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/host-groups/',
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

`GET /host-groups/`

Gets host groups that exactly match all of the provided user ids.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="find-by-hosts-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|host[]|query|undefined|true|Ids of users that must be represented in the host group. Pass multiple values as ?host[]=IdOfFirstHost&host[]=IdOfSecondHost|

> Example responses

> 200 Response

```json
[
  {
    "users": [
      "5f720bae0504f73464bd83eb",
      "5f72114fab735642446f64ed"
    ],
    "_id": "5f8f42482d687150ac9373ba",
    "on_air_name": "Sean and Barry",
    "__v": 0
  }
]
```

<h3 id="find-by-hosts-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-by-hosts-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Find All

<a id="opIdFindHostGroup"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/host-groups/all',
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

`GET /host-groups/all`

Returns a list of all host groups, sorted by their on-air name.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

```json
[
  {
    "users": [
      "5f720bae0504f73464bd83eb",
      "5f72114fab735642446f64ed"
    ],
    "_id": "5f8f42482d687150ac9373ba",
    "on_air_name": "Sean and Barry",
    "__v": 0
  },
  {
    "users": [
      "5f720bae0504f73464bd83eb",
      "5f72114fab735642446f64ed"
    ],
    "_id": "5f8f42482d687150ac9373bb",
    "on_air_name": "The Way Low Down",
    "__v": 0
  }
]
```

<h3 id="find-all-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-all-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Find by Id

<a id="opIdFindHostGroupById"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/host-groups/{id}',
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

`GET /host-groups/{id}`

Gets host groups by its id.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="find-by-id-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|none|

> Example responses

> 200 Response

```json
{
  "users": [
    "5f720bae0504f73464bd83eb",
    "5f72114fab735642446f64ed"
  ],
  "_id": "5f8f42482d687150ac9373ba",
  "on_air_name": "Sean and Barry",
  "__v": 0
}
```

<h3 id="find-by-id-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-by-id-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Delete

<a id="opIdDeleteHostGroup"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/host-groups/{id}',
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

`DELETE /host-groups/{id}`

Deletes a host group by its id.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`

<h3 id="delete-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|none|

> Example responses

> 200 Response

```json
{
  "users": [
    "5f720bae0504f73464bd83eb",
    "5f72114fab735642446f64ed"
  ],
  "_id": "5f8f42482d687150ac9373ba",
  "on_air_name": "Prairie Lakes Band",
  "__v": 0
}
```

<h3 id="delete-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|The host group cannot be deleted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="delete-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Update

<a id="opIdUpdateHostGroup"></a>

> Code samples

```javascript
const inputBody = '{
  "on_air_name": "Prairie Lakes Band"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/host-groups/{id}',
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

`PUT /host-groups/{id}`

Updates a host group by its id.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`

> Body parameter

```json
{
  "on_air_name": "Prairie Lakes Band"
}
```

<h3 id="update-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|object|false|none|
|id|path|undefined|true|none|

> Example responses

> 200 Response

```json
{
  "users": [
    "5f720bae0504f73464bd83eb",
    "5f72114fab735642446f64ed"
  ],
  "_id": "5f8f42482d687150ac9373ba",
  "on_air_name": "Prairie Lakes Band",
  "__v": 0
}
```

<h3 id="update-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="update-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

<h1 id="comrad-api-library-albums-artists-tracks">Library (Albums, Artists, Tracks)</h1>

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
|---|---|---|
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
|type|query|string|false|If provided, this endpoint will only return the specified entity type|
|sortBy|query|undefined|false|If provided, the results will be sorted by this field name. Defaults to `updated_at`|
|sortDescending|query|undefined|false|Whether to sort the results in a descending manner. Defaults to `true`|
|page|query|undefined|false|The page number of results to return. Defaults to 1.|

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
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-all-responseschema">Response Schema</h3>

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
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|The Library records were created successfully|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|

<h3 id="create-many-responseschema">Response Schema</h3>

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
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="get-by-id-responseschema">Response Schema</h3>

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
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|404|[Not Found](https://tools.ietf.org/html/rfc7231#section-6.5.4)|Library item does not exist|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|

<h3 id="delete-responseschema">Response Schema</h3>

Status Code **200**

|Name|Type|Description|
|---|---|---|---|---|

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
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|

<h3 id="update-responseschema">Response Schema</h3>

Status Code **200**

|Name|Type|Description|
|---|---|---|---|---|

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
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="search-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

<h1 id="comrad-api-playlists">Playlists</h1>

## Saved Items - Add Comment

<a id="opIdPlaylistsAddCommentToSavedItems"></a>

> Code samples

```javascript
const inputBody = '{
  "description": "This is a comment entered into Saved Items"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists/:playlistId/saved-items/comment',
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

`PUT /playlists/:playlistId/saved-items/comment`

Adds a comment to the Saved Items of the specified playlist.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. The `DJ` role can also access the endpoint when the playlist spans a show where the user is a host.

> Body parameter

```json
{
  "description": "This is a comment entered into Saved Items"
}
```

<h3 id="saved-items---add-comment-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|object|false|none|
|playlistId|path|undefined|true|The id of the playlist to affect|

> Example responses

> 200 Response

```json
{
  "_id": "5fc9715ecd36da3cc05fafbf",
  "type": "comment",
  "description": "This is a comment entered into Saved Items",
  "executed_time_utc": "2020-12-03T23:14:38.020Z"
}
```

<h3 id="saved-items---add-comment-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="saved-items---add-comment-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Scratchpad - Add Comment

<a id="opIdPlaylistsAddCommentToScratchpad"></a>

> Code samples

```javascript
const inputBody = '{
  "description": "This is a comment entered into a playlist"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists/:playlistId/scratchpad/comment',
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

`PUT /playlists/:playlistId/scratchpad/comment`

Adds a comment to the scratchpad of the specified playlist.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. The `DJ` role can also access the endpoint when the playlist spans a show where the user is a host.

> Body parameter

```json
{
  "description": "This is a comment entered into a playlist"
}
```

<h3 id="scratchpad---add-comment-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|object|false|none|
|playlistId|path|undefined|true|The id of the playlist to affect|

> Example responses

> 200 Response

```json
{
  "_id": "5fc970f847ee2b5130d739c3",
  "type": "comment",
  "description": "This is a comment entered into a playlist"
}
```

<h3 id="scratchpad---add-comment-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="scratchpad---add-comment-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Saved Items - Add Track

<a id="opIdPlaylistsAddTrackToSavedItems"></a>

> Code samples

```javascript
const inputBody = '{
  "trackId": "5f721090ab735642446aa414"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists/:playlistId/saved-items/track',
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

`PUT /playlists/:playlistId/saved-items/track`

Adds a track to the scratchpad of the specified playlist.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. The `DJ` role can also access the endpoint when the playlist spans a show where the user is a host.

> Body parameter

```json
{
  "trackId": "5f721090ab735642446aa414"
}
```

<h3 id="saved-items---add-track-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|object|false|none|
|playlistId|path|undefined|true|The id of the playlist to affect|

> Example responses

> 200 Response

```json
{
  "_id": "5fc96ad8c276474f1c11db3d",
  "type": "track",
  "track": {
    "popularity": 0,
    "_id": "5f721090ab735642446aa414",
    "name": "The Up And Down",
    "album": {
      "popularity": 0,
      "_id": "5f720efe66320235249b9b04",
      "name": "Take One",
      "artist": "5f720eda663202352499f795",
      "label": "Destination Unknown",
      "genre": "5f720ef466320235249b2d93",
      "compilation": false,
      "custom": {
        "itunes_id": null,
        "old_comrad_id": "1111177433",
        "local": "0",
        "location": "Digital Library"
      },
      "type": "album",
      "created_at": "2020-09-28T16:27:42.994Z",
      "updated_at": "2020-09-28T16:27:42.994Z"
    },
    "track_number": 5,
    "disk_number": 1,
    "duration_in_seconds": 252,
    "custom": {
      "old_comrad_id": "279476"
    },
    "type": "track",
    "artists": [
      {
        "popularity": 0,
        "_id": "5f720eda663202352499f795",
        "name": "The Way Low Down",
        "type": "artist",
        "created_at": "2020-09-28T16:27:06.069Z",
        "updated_at": "2020-09-28T16:27:06.069Z"
      }
    ],
    "created_at": "2020-09-28T16:34:24.830Z",
    "updated_at": "2020-09-28T16:34:24.830Z"
  },
  "executed_time_utc": "2020-12-03T22:46:48.754Z"
}
```

<h3 id="saved-items---add-track-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="saved-items---add-track-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Scratchpad - Add Track

<a id="opIdPlaylistsAddTrackToScratchpad"></a>

> Code samples

```javascript
const inputBody = '{
  "trackId": "5fc96318bd1238436c43c74c"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists/:playlistId/scratchpad/track',
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

`PUT /playlists/:playlistId/scratchpad/track`

Adds a track to the scratchpad of the specified playlist.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. The `DJ` role can also access the endpoint when the playlist spans a show where the user is a host.

> Body parameter

```json
{
  "trackId": "5fc96318bd1238436c43c74c"
}
```

<h3 id="scratchpad---add-track-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|object|false|none|
|playlistId|path|undefined|true|The id of the playlist to affect|

> Example responses

> 200 Response

```json
{
  "_id": "5fc969a23000804d54f7b3de",
  "type": "track",
  "track": {
    "popularity": 0,
    "_id": "5f72105dab73564244691d04",
    "name": "JACKSON",
    "album": {
      "popularity": 0,
      "_id": "5f720ef966320235249b7172",
      "name": "The Legend of Johnny Cash",
      "artist": "5f720ed9663202352499be4a",
      "label": "Legacy",
      "genre": "5f720ef466320235249b2d9a",
      "compilation": false,
      "custom": {
        "itunes_id": null,
        "old_comrad_id": "36377",
        "local": "0",
        "location": "Gnu Bin",
        "library_number": "36377"
      },
      "type": "album",
      "created_at": "2020-09-28T16:27:37.791Z",
      "updated_at": "2020-09-28T16:27:37.791Z"
    },
    "track_number": 0,
    "disk_number": 1,
    "duration_in_seconds": 0,
    "custom": {
      "old_comrad_id": "79970"
    },
    "type": "track",
    "artists": [
      {
        "popularity": 61,
        "_id": "5f720ed9663202352499be4a",
        "name": "Johnny Cash",
        "type": "artist",
        "created_at": "2020-09-28T16:27:05.308Z",
        "updated_at": "2020-09-28T16:27:05.308Z"
      }
    ],
    "created_at": "2020-09-28T16:33:33.436Z",
    "updated_at": "2020-09-28T16:33:33.436Z"
  }
}
```

<h3 id="scratchpad---add-track-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="scratchpad---add-track-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Saved Items - Add Traffic

<a id="opIdPlaylistsAddTrafficToSavedItems"></a>

> Code samples

```javascript
const inputBody = '{
  "masterTimeId": "5f721090ab735642446aa414"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists/:playlistId/saved-items/traffic',
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

`PUT /playlists/:playlistId/saved-items/traffic`

Adds a traffic event to the scratchpad of the specified playlist.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. The `DJ` role can also access the endpoint when the playlist spans a show where the user is a host.

> Body parameter

```json
{
  "masterTimeId": "5f721090ab735642446aa414"
}
```

<h3 id="saved-items---add-traffic-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|object|false|none|
|playlistId|path|undefined|true|The id of the playlist to affect|

> Example responses

> 200 Response

```json
{
  "_id": "5fc97224f5f6b95288c7d241",
  "type": "traffic",
  "executed_time_utc": "2020-12-03T23:17:56.374Z",
  "traffic": {
    "repeat_rule": {
      "byweekday": [
        "TH"
      ],
      "repeat_start_date": "2020-03-19T15:30:00.000Z",
      "frequency": 2,
      "repeat_end_date": "2022-11-24T16:30:00.000Z"
    },
    "traffic_details": {
      "type": "Underwriting",
      "title": "Yellow Scene Magazine",
      "description": "<p>Please play from Underwriting folder on the Wavecart</p>",
      "producer": null,
      "custom": {
        "old_comrad_event_id": "215",
        "old_comrad_scheduled_event_ids": [
          "17451"
        ]
      },
      "underwriter_name": "Yellow Scene Magazine"
    },
    "status": "active",
    "_id": "5f7211f2ab735642446fefd9",
    "start_time_utc": "2020-12-03T16:30:00.000Z",
    "end_time_utc": "2020-12-03T16:31:00.000Z",
    "is_recurring": true,
    "created_at": "2020-09-28T16:40:18.890Z",
    "updated_at": "2020-09-28T16:40:18.890Z",
    "__v": 0,
    "master_event_id": "5f7211f2ab735642446fefd9"
  },
  "master_time_id": "5f7211f2ab735642446fefd9-1607013000000"
}
```

<h3 id="saved-items---add-traffic-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="saved-items---add-traffic-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Saved Items - Add Voice Break

<a id="opIdPlaylistsAddVoiceBreakToSavedItems"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists/:playlistId/saved-items/voice-break',
{
  method: 'PUT',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`PUT /playlists/:playlistId/saved-items/voice-break`

Adds a voice break to the Saved Items of the specified playlist.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. The `DJ` role can also access the endpoint when the playlist spans a show where the user is a host.

<h3 id="saved-items---add-voice-break-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|playlistId|path|undefined|true|The id of the playlist to affect|

> Example responses

> 200 Response

```json
{
  "_id": "5fc97059a8044c269c48f937",
  "type": "voice_break",
  "executed_time_utc": "2020-12-03T23:10:17.856Z"
}
```

<h3 id="saved-items---add-voice-break-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="saved-items---add-voice-break-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Scratchpad - Add Voice Break

<a id="opIdPlaylistsAddVoiceBreakToScratchpad"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists/:playlistId/scratchpad/voice-break',
{
  method: 'PUT',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`PUT /playlists/:playlistId/scratchpad/voice-break`

Adds a voice break to the scratchpad of the specified playlist.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. The `DJ` role can also access the endpoint when the playlist spans a show where the user is a host.

<h3 id="scratchpad---add-voice-break-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|playlistId|path|undefined|true|The id of the playlist to affect|

> Example responses

> 200 Response

```json
{
  "_id": "5fc96ff500dce7269c97ef58",
  "type": "voice_break"
}
```

<h3 id="scratchpad---add-voice-break-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="scratchpad---add-voice-break-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Scratchpad - Delete Item

<a id="opIdPlaylistsDeleteItemFromScratchpad"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists/:playlistId/scratchpad/:itemId',
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

`DELETE /playlists/:playlistId/scratchpad/:itemId`

Removes the specified item from the scratchpad of the specified playlist, if the item exists.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. The `DJ` role can also access the endpoint when the playlist spans a show where the user is a host.

<h3 id="scratchpad---delete-item-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|playlistId|path|undefined|true|The id of the playlist to affect|
|itemId|path|undefined|true|The id of the item to update|

> Example responses

<h3 id="scratchpad---delete-item-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="scratchpad---delete-item-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Scratchpad - Update Item

<a id="opIdPlaylistsUpdateScratchpadItem"></a>

> Code samples

```javascript
const inputBody = '{
  "occurs_before_time_utc": "2020-12-03T18:00:00Z"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists/:playlistId/scratchpad/:itemId',
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

`PUT /playlists/:playlistId/scratchpad/:itemId`

Updates the `occurs_after_time_utc` and/or `occurs_before_time_utc` properties of a scratchpad item. Scratchpad items appear in an ordered array within the playlist, and will display in that order. But, traffic events that occur in the playlist's timespan will also show in a track's playlist (unless that traffic event is already "executed" - meaning it is in the playlist's Saved Items list). The `occurs_after_time_utc` and `occurs_before_time_utc` inform Comrad's front-end how to display the scratchpad items relative to the unexecuted traffic items.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. The `DJ` role can also access the endpoint when the playlist spans a show where the user is a host.

> Body parameter

```json
{
  "occurs_before_time_utc": "2020-12-03T18:00:00Z"
}
```

<h3 id="scratchpad---update-item-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|object|false|none|
|playlistId|path|undefined|true|The id of the playlist to affect|
|itemId|path|undefined|true|The id of the item to update|

> Example responses

<h3 id="scratchpad---update-item-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="scratchpad---update-item-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Find One

<a id="opIdFindOnePlaylists"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists',
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

`GET /playlists`

Get a playlist based on its start and end time, if it exists.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="find-one-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|startTime|query|undefined|true|The time the playlist starts at|
|endTime|query|undefined|true|The time the playlist ends at|

> Example responses

> 200 Response

```json
{
  "_id": "5fc96303bd1238436c43c74b",
  "start_time_utc": "2020-12-03T16:30:00.000Z",
  "end_time_utc": "2020-12-03T19:06:00.000Z",
  "scratchpad": [
    {
      "_id": "5fc96318bd1238436c43c74c",
      "type": "track",
      "track": {
        "popularity": 0,
        "_id": "5f72109dab735642446ae259",
        "name": "Mile After Mile",
        "album": {
          "popularity": 0,
          "_id": "5f720f0b66320235249c156d",
          "name": "Easy Livin'",
          "artist": "5f720eda66320235249a336e",
          "label": "Blind Pig Records",
          "genre": "5f720ef466320235249b2d97",
          "compilation": false,
          "created_at": "2013-06-23T00:01:14.000Z",
          "custom": {
            "itunes_id": "596610087",
            "old_comrad_id": "44122",
            "local": null,
            "location": "Personal",
            "library_number": "44122"
          },
          "type": "album",
          "updated_at": "2020-09-28T16:27:55.577Z"
        },
        "track_number": 4,
        "disk_number": 1,
        "duration_in_seconds": 136,
        "custom": {
          "old_comrad_id": "295468"
        },
        "type": "track",
        "artists": [
          {
            "popularity": 0,
            "_id": "5f720eda66320235249a336e",
            "name": "Southern Hospitality",
            "type": "artist",
            "created_at": "2020-09-28T16:27:06.778Z",
            "updated_at": "2020-09-28T16:27:06.778Z"
          }
        ],
        "created_at": "2020-09-28T16:34:37.153Z",
        "updated_at": "2020-09-28T16:34:37.153Z"
      }
    }
  ],
  "saved_items": [
    {
      "_id": "5fc96329bd1238436c43c74e",
      "type": "traffic",
      "executed_time_utc": "2020-12-03T22:14:01.558Z",
      "traffic": {
        "repeat_rule": {
          "byweekday": [
            "TH"
          ],
          "repeat_start_date": "2020-03-19T15:30:00.000Z",
          "frequency": 2,
          "repeat_end_date": "2022-11-24T16:30:00.000Z"
        },
        "traffic_details": {
          "type": "Underwriting",
          "title": "Yellow Scene Magazine",
          "description": "<p>Please play from Underwriting folder on the Wavecart</p>",
          "producer": null,
          "custom": {
            "old_comrad_event_id": "215",
            "old_comrad_scheduled_event_ids": [
              "17451"
            ]
          },
          "underwriter_name": "Yellow Scene Magazine"
        },
        "status": "active",
        "_id": "5f7211f2ab735642446fefd9",
        "start_time_utc": "2020-12-03T16:30:00Z",
        "end_time_utc": "2020-12-03T16:31:00Z",
        "is_recurring": true,
        "created_at": "2020-09-28T16:40:18.890Z",
        "updated_at": "2020-09-28T16:40:18.890Z",
        "__v": 0,
        "master_event_id": {
          "_id": "5f7211f2ab735642446fefd9"
        },
        "master_time_id": "5f7211f2ab735642446fefd9-1607013000000"
      },
      "master_time_id": "5f7211f2ab735642446fefd9-1607013000000"
    },
    {
      "_id": "5fc9631abd1238436c43c74d",
      "type": "track",
      "track": {
        "popularity": 0,
        "_id": "5f721071ab7356424469d206",
        "name": "Mile After Mile",
        "album": {
          "popularity": 0,
          "_id": "5f720f0566320235249bb304",
          "name": "Apples Acre",
          "artist": "5f720eda663202352499feab",
          "label": "Dead Oceans",
          "genre": "5f720ef466320235249b2d91",
          "compilation": false,
          "created_at": "2011-08-11T13:51:14.000Z",
          "custom": {
            "itunes_id": null,
            "old_comrad_id": "42584",
            "local": "0",
            "location": "Library",
            "library_number": "42584"
          },
          "type": "album",
          "updated_at": "2020-09-28T16:27:49.052Z"
        },
        "track_number": 2,
        "disk_number": 1,
        "duration_in_seconds": 256,
        "custom": {
          "old_comrad_id": "225474"
        },
        "type": "track",
        "artists": [
          {
            "popularity": 0,
            "_id": "5f720eda663202352499feab",
            "name": "Nurses",
            "type": "artist",
            "created_at": "2020-09-28T16:27:06.158Z",
            "updated_at": "2020-09-28T16:27:06.158Z"
          }
        ],
        "created_at": "2020-09-28T16:33:53.258Z",
        "updated_at": "2020-09-28T16:33:53.258Z"
      },
      "executed_time_utc": "2020-12-03T22:13:46.733Z"
    },
    {
      "_id": "5fc9632dbd1238436c43c751",
      "type": "traffic",
      "executed_time_utc": "2020-12-03T22:14:05.985Z",
      "traffic": {
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
            "old_comrad_event_id": "2",
            "old_comrad_scheduled_event_ids": [
              "20"
            ]
          }
        },
        "status": "active",
        "_id": "5f7211f2ab735642446feeac",
        "start_time_utc": "2020-12-03T17:00:00Z",
        "end_time_utc": "2020-12-03T17:01:00Z",
        "is_recurring": true,
        "created_at": "2020-09-28T16:40:18.631Z",
        "updated_at": "2020-09-28T16:40:18.631Z",
        "__v": 0,
        "master_event_id": {
          "_id": "5f7211f2ab735642446feeac"
        },
        "master_time_id": "5f7211f2ab735642446feeac-1607014800000"
      },
      "master_time_id": "5f7211f2ab735642446feeac-1607014800000"
    }
  ],
  "created_at": "2020-12-03T22:13:23.386Z",
  "updated_at": "2020-12-03T22:13:23.386Z",
  "__v": 0
}
```

<h3 id="find-one-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-one-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Find or Create

<a id="opIdFindOrCreatePlaylist"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists',
{
  method: 'PUT',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`PUT /playlists`

Get a playlist based on its start and end time, if it exists. Otherwise, creates the playlist.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="find-or-create-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|startTime|query|undefined|true|The time the playlist starts at|
|endTime|query|undefined|true|The time the playlist ends at|

> Example responses

> 200 Response

```json
{
  "_id": "5fc96465070d4b34fc036fdf",
  "start_time_utc": "2020-12-03T19:06:00.000Z",
  "end_time_utc": "2020-12-03T20:00:00.000Z",
  "scratchpad": [],
  "saved_items": [],
  "created_at": "2020-12-03T22:19:17.824Z",
  "updated_at": "2020-12-03T22:19:17.824Z",
  "__v": 0
}
```

<h3 id="find-or-create-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-or-create-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Scratchpad - Move Item to Saved Items

<a id="opIdPlaylistsMoveItemFromSavedItemsToScratchpad"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists/:playlistId/saved-items/:itemId',
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

`DELETE /playlists/:playlistId/saved-items/:itemId`

Moves the specified item from Saved Items to Scratchpad, within a playlist.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. The `DJ` role can also access the endpoint when the playlist spans a show where the user is a host.

<h3 id="scratchpad---move-item-to-saved-items-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|playlistId|path|undefined|true|The id of the playlist to affect|
|itemId|path|undefined|true|The id of the item to move to the scratchpad|

> Example responses

> 200 Response

```json
{
  "_id": "5fc96318bd1238436c43c74c",
  "type": "track",
  "track": {
    "popularity": 0,
    "_id": "5f72109dab735642446ae259",
    "name": "Mile After Mile",
    "album": {
      "popularity": 0,
      "_id": "5f720f0b66320235249c156d",
      "name": "Easy Livin'",
      "artist": "5f720eda66320235249a336e",
      "label": "Blind Pig Records",
      "genre": "5f720ef466320235249b2d97",
      "compilation": false,
      "created_at": "2013-06-23T00:01:14.000Z",
      "custom": {
        "itunes_id": "596610087",
        "old_comrad_id": "44122",
        "local": null,
        "location": "Personal",
        "library_number": "44122"
      },
      "type": "album",
      "updated_at": "2020-09-28T16:27:55.577Z"
    },
    "track_number": 4,
    "disk_number": 1,
    "duration_in_seconds": 136,
    "custom": {
      "old_comrad_id": "295468"
    },
    "type": "track",
    "artists": [
      {
        "popularity": 0,
        "_id": "5f720eda66320235249a336e",
        "name": "Southern Hospitality",
        "type": "artist",
        "created_at": "2020-09-28T16:27:06.778Z",
        "updated_at": "2020-09-28T16:27:06.778Z"
      }
    ],
    "created_at": "2020-09-28T16:34:37.153Z",
    "updated_at": "2020-09-28T16:34:37.153Z"
  }
}
```

<h3 id="scratchpad---move-item-to-saved-items-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="scratchpad---move-item-to-saved-items-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Saved Items - Move Item to Scratchpad

<a id="opIdPlaylistsMoveItemFromScratchpadToSavedItems"></a>

> Code samples

```javascript
const inputBody = '{
  "itemId": "5fc96318bd1238436c43c74c"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists/:id/saved-items',
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

`PUT /playlists/:id/saved-items`

Moves the specified item from Scratchpad to Saved Items, within a playlist.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. The `DJ` role can also access the endpoint when the playlist spans a show where the user is a host.

> Body parameter

```json
{
  "itemId": "5fc96318bd1238436c43c74c"
}
```

<h3 id="saved-items---move-item-to-scratchpad-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|object|false|none|
|id|path|undefined|true|The id of the playlist to affect|

> Example responses

> 200 Response

```json
{
  "_id": "5fc96318bd1238436c43c74c",
  "type": "track",
  "track": {
    "popularity": 0,
    "_id": "5f72109dab735642446ae259",
    "name": "Mile After Mile",
    "album": {
      "popularity": 0,
      "_id": "5f720f0b66320235249c156d",
      "name": "Easy Livin'",
      "artist": "5f720eda66320235249a336e",
      "label": "Blind Pig Records",
      "genre": "5f720ef466320235249b2d97",
      "compilation": false,
      "created_at": "2013-06-23T00:01:14.000Z",
      "custom": {
        "itunes_id": "596610087",
        "old_comrad_id": "44122",
        "local": null,
        "location": "Personal",
        "library_number": "44122"
      },
      "type": "album",
      "updated_at": "2020-09-28T16:27:55.577Z"
    },
    "track_number": 4,
    "disk_number": 1,
    "duration_in_seconds": 136,
    "custom": {
      "old_comrad_id": "295468"
    },
    "type": "track",
    "artists": [
      {
        "popularity": 0,
        "_id": "5f720eda66320235249a336e",
        "name": "Southern Hospitality",
        "type": "artist",
        "created_at": "2020-09-28T16:27:06.778Z",
        "updated_at": "2020-09-28T16:27:06.778Z"
      }
    ],
    "created_at": "2020-09-28T16:34:37.153Z",
    "updated_at": "2020-09-28T16:34:37.153Z"
  }
}
```

<h3 id="saved-items---move-item-to-scratchpad-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="saved-items---move-item-to-scratchpad-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Saved Items - Rearrange Item

<a id="opIdPlaylistsSavedItemsRearrangeItem"></a>

> Code samples

```javascript
const inputBody = '{
  "toIndex": "0"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists/:playlistId/saved-items/:itemId/rearrange',
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

`PUT /playlists/:playlistId/saved-items/:itemId/rearrange`

Moves the specified Saved Items item to a new position within the Saved Items array

Calling this endpoint will not affect the `executed_time_utc` value of any items on the Saved Items list.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. The `DJ` role can also access the endpoint when the playlist spans a show where the user is a host.

> Body parameter

```json
{
  "toIndex": "0"
}
```

<h3 id="saved-items---rearrange-item-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|object|false|none|
|playlistId|path|undefined|true|The id of the playlist to affect|

> Example responses

<h3 id="saved-items---rearrange-item-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="saved-items---rearrange-item-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Scratchpad - Rearrange Item

<a id="opIdPlaylistsScratchpadRearrangeItem"></a>

> Code samples

```javascript
const inputBody = '{
  "toIndex": "0"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/playlists/:playlistId/scratchpad/:itemId/rearrange',
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

`PUT /playlists/:playlistId/scratchpad/:itemId/rearrange`

Moves the specified Scratchpad item to a new position within the Scratchpad array

If moving the item around other items that have `occurs_after_time_utc` or `occurs_before_time_utc` set, you will want to call the Scratchpad: Update Item endpoint after moving the item to adjust those values as well. The moved item should be updated with the `occurs_before_time_utc` value of the item after it in the sequence (after the move is complete). And, similarly, the moved item shoul dbe updated with the `occurs_after_time_utc` of the item before it in the sequence (after the move is complete).

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`. The `DJ` role can also access the endpoint when the playlist spans a show where the user is a host.

> Body parameter

```json
{
  "toIndex": "0"
}
```

<h3 id="scratchpad---rearrange-item-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|object|false|none|
|playlistId|path|undefined|true|The id of the playlist to affect|

> Example responses

<h3 id="scratchpad---rearrange-item-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was a problem with the data you submitted. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="scratchpad---rearrange-item-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Recent Plays

<a id="opIdRecentPlays"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/recent-plays',
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

`GET /recent-plays`

Returns the 10 most recents items played. Items can be tracks, voice breaks, comments, or traffic. Only returns items from the past 24 hours.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

```json
[
  {
    "type": "track",
    "track": {
      "name": "Morning Morning",
      "album": "Second Album",
      "album_artist": "The Fugs",
      "artists": [
        "The Fugs"
      ],
      "duration_in_seconds": 129
    }
  },
  {
    "type": "track",
    "track": {
      "name": "Watt",
      "album": "Floored by Four",
      "album_artist": "Floored by Four",
      "artists": [
        "Floored by Four"
      ],
      "duration_in_seconds": 237
    }
  },
  {
    "type": "traffic",
    "traffic": {
      "type": "Legal ID",
      "title": "Legal Id"
    }
  },
  {
    "type": "track",
    "track": {
      "name": "Restless",
      "album": "Toss Up",
      "album_artist": "Kevin Krauter",
      "artists": [
        "Kevin Krauter"
      ],
      "duration_in_seconds": 183
    }
  },
  {
    "type": "traffic",
    "traffic": {
      "type": "Legal ID",
      "title": "Legal Id"
    }
  },
  {
    "type": "voice_break"
  },
  {
    "type": "track",
    "track": {
      "name": "Clash",
      "album": "Fantasma",
      "album_artist": "Cornelius",
      "artists": [
        "Cornelius"
      ],
      "duration_in_seconds": 356
    }
  },
  {
    "type": "track",
    "track": {
      "name": "Clash",
      "album": "Night Wave",
      "album_artist": "Yuko Fujiyama",
      "artists": [
        "Yuko Fujiyama"
      ],
      "duration_in_seconds": 60
    }
  },
  {
    "type": "traffic",
    "traffic": {
      "type": "Announcement",
      "title": "Ongoing Promo"
    }
  },
  {
    "type": "comment",
    "description": "<p>test comment</p>"
  }
]
```

<h3 id="recent-plays-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Array of the 10 most recent items played|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="recent-plays-responseschema">Response Schema</h3>

Status Code **200**

|Name|Type|Description|
|---|---|---|---|---|
|Array of items played|[object]|Array of objects, where each object is an item played. Array appears in the order the items were played.|
|» type|string|The type of item. Possible values: `track`, `traffic`, `comment, `voice_break`|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

<h1 id="comrad-api-resources">Resources</h1>

## Create

<a id="opIdCreateResource"></a>

> Code samples

```javascript
const inputBody = '[
  {
    "category": "Announcements",
    "description": "ON-AIR - Online Ticket Giveaways",
    "link": "https://getcomrad.org/example/On-AirTicketGiveawayInstructions.pdf"
  }
]';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/resources',
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

`POST /resources`

Returns a list of all resources.

The following roles can access this API endpoint: `Admin`, `Full Access`

> Body parameter

```json
[
  {
    "category": "Announcements",
    "description": "ON-AIR - Online Ticket Giveaways",
    "link": "https://getcomrad.org/example/On-AirTicketGiveawayInstructions.pdf"
  }
]
```

<h3 id="create-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|[Resource](#schemaresource)|false|none|

> Example responses

> 200 Response

```json
{
  "_id": "5fc9260689069141b89a775c",
  "category": "Announcements",
  "description": "Sample Announcement Guidelines",
  "link": "https://getcomrad.org/announcements",
  "__v": 0
}
```

<h3 id="create-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="create-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Find All

<a id="opIdFindAllResources"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/resources',
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

`GET /resources`

Returns a list of all resources.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

> Example responses

> 200 Response

```json
[
  {
    "_id": "5f720bae0504f73464bd83b6",
    "category": "Announcements",
    "description": "ON-AIR - Online Ticket Giveaways",
    "link": "https://getcomrad.org/onlineticketgiveaways.pdf",
    "__v": 0
  },
  {
    "_id": "5f720bae0504f73464bd83b7",
    "category": "Policies",
    "description": "Non-commercial Policy",
    "link": "https://getcomrad.org/noncommercialpolicy.pdf",
    "__v": 0
  },
  {
    "_id": "5f720bae0504f73464bd83b8",
    "category": "Other Important Documents",
    "description": "2019-2023 Strategic Plan",
    "link": "https://getcomrad.org/other-important-documents",
    "__v": 0
  }
]
```

<h3 id="find-all-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-all-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Delete

<a id="opIdDeleteResource"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/resources/{id}',
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

`DELETE /resources/{id}`

Delete a resource

The following roles can access this API endpoint: `Admin`, `Full Access`

<h3 id="delete-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|none|

> Example responses

> 200 Response

```json
{
  "_id": "5fc928cc6df1040ae4bedf2c",
  "category": "Announcements",
  "description": "Sample Announcement Guidelines",
  "link": "https://getcomrad.org/announcements",
  "__v": 0
}
```

<h3 id="delete-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|404|[Not Found](https://tools.ietf.org/html/rfc7231#section-6.5.4)|Resource does not exist|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|A server error occurred. Check the error message for more details.|

<h3 id="delete-responseschema">Response Schema</h3>

Status Code **200**

|Name|Type|Description|
|---|---|---|---|---|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Update

<a id="opIdUpdateResource"></a>

> Code samples

```javascript
const inputBody = '{
  "category": "Other Important Documents"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/resouces/{id}',
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

`PUT /resouces/{id}`

Update a resource

The following roles can access this API endpoint: `Admin`, `Full Access`

> Body parameter

```json
{
  "category": "Other Important Documents"
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
  "_id": "5fc929663d904b472c5ba2c7",
  "category": "Other Important Documents",
  "description": "Sample Announcement Guidelines",
  "link": "https://getcomrad.org/announcements",
  "__v": 0
}
```

<h3 id="update-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|

<h3 id="update-responseschema">Response Schema</h3>

Status Code **200**

|Name|Type|Description|
|---|---|---|---|---|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

<h1 id="comrad-api-users">Users</h1>

## Check if Can Delete

<a id="opIdUsersCanDelete"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/users/{id}/can-delete',
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

`GET /users/{id}/can-delete`

Returns a value indicating whether the existing user can be deleted. Users can be deleted if they are not hosts of any shows.

The following roles can access this API endpoint: `Admin`

<h3 id="check-if-can-delete-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|none|

> Example responses

> 200 Response

```json
true
```

<h3 id="check-if-can-delete-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|A value indicating whether the user can be deleted|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

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
|---|---|---|
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
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-all-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Create API Key

<a id="opIdUsersCreateApiKey"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/users/{id}/api-key',
{
  method: 'POST',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});

```

`POST /users/{id}/api-key`

Creates an API key for the specified user. If the user already has an API key, calling this will replace the user's existing API key.

The following roles can access this API endpoint: `Admin`

<h3 id="create-api-key-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|none|

> Example responses

> 200 Response

```json
{
  "doc": {
    "api_key": {
      "last_used": "2020-11-19T21:04:08.704Z",
      "token": "exists"
    },
    "on_air_name": null,
    "primary_phone": null,
    "roles": [],
    "status": "Active",
    "_id": "5fb6dd9becaa3904742d2339",
    "email": "s2@getcomrad.org",
    "first_name": "Sean",
    "last_name": "Williams",
    "__v": 0,
    "can_delete": true
  },
  "api_key": "753713e1-9a8a-477a-a720-7fa761f4f37c"
}
```

<h3 id="create-api-key-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|The User's api key was created successfully|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="create-api-key-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Delete API Key

<a id="opIdUsersDeleteApiKey"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/users/{id}/api-key',
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

`DELETE /users/{id}/api-key`

Deletes the API key for the specified user.

The following roles can access this API endpoint: `Admin`

<h3 id="delete-api-key-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|none|

> Example responses

> 200 Response

```json
{
  "api_key": {
    "last_used": null,
    "token": null
  },
  "on_air_name": null,
  "primary_phone": null,
  "roles": [],
  "status": "Active",
  "_id": "5fb6dd9becaa3904742d2339",
  "email": "s2@getcomrad.org",
  "first_name": "Sean",
  "last_name": "Williams",
  "__v": 0,
  "can_delete": true
}
```

<h3 id="delete-api-key-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|The user's API key was deleted successfully|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="delete-api-key-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Find by ID

<a id="opIdUseresFindById"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/users/{id}',
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

`GET /users/{id}`

Gets a user by their ID

The following roles can access this API endpoint for any user: `Admin`

All users can use this endpoint for their own user ID.

<h3 id="find-by-id-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|none|

> Example responses

> 200 Response

```json
{
  "api_key": {
    "last_used": "2020-12-01T12:35:19.837Z",
    "token": "exists"
  },
  "on_air_name": "DJ Cool Software",
  "primary_phone": null,
  "roles": [
    "Admin"
  ],
  "status": "Active",
  "_id": "5f720bae0504f73464bd83eb",
  "email": "comrad.development@gmail.com",
  "first_name": "Comrad",
  "last_name": "Develpment",
  "__v": 0,
  "can_delete": false,
  "host_groups": [
    {
      "users": [
        "5f720bae0504f73464bd83eb",
        "5f72114fab735642446f64ed"
      ],
      "_id": "5f8f456552431919908e84b3",
      "on_air_name": "Sean and Barry",
      "__v": 0
    }
  ]
}
```

<h3 id="find-by-id-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Returns the user record matching the ID|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|404|[Not Found](https://tools.ietf.org/html/rfc7231#section-6.5.4)|There is no user with the specified id|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="find-by-id-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Delete

<a id="opIdDeleteUser"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/users/{id}',
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

`DELETE /users/{id}`

Deletes a user. Users cannot be deleted if they are hosts on a show.

There must always be at least one admin user in the database. This API endpoint will not allow you to delete the last Admin user.

The following roles can access this API endpoint for any user: `Admin`

<h3 id="delete-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|id|path|undefined|true|none|

> Example responses

> 200 Response

```json
null
```

<h3 id="delete-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|

<h3 id="delete-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Update

<a id="opIdUpdateUser"></a>

> Code samples

```javascript
const inputBody = '{
  "on_air_name": "DJ Coolest Software"
}';
const headers = {
  'Content-Type':'application/json',
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/users/{id}',
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

`PUT /users/{id}`

Update a user

The following roles can access this API endpoint for any user: `Admin`

All users can use this endpoint for their own user ID, but only to update password, first_name, last_name, email and on_air_name.

> Body parameter

```json
{
  "on_air_name": "DJ Coolest Software"
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
  "api_key": {
    "last_used": "2020-12-01T14:31:33.305Z",
    "token": "exists"
  },
  "on_air_name": "DJ Coolest Software",
  "primary_phone": null,
  "roles": [
    "Admin"
  ],
  "status": "Active",
  "_id": "5f720bae0504f73464bd83eb",
  "email": "comrad.development@gmail.com",
  "first_name": "Comrad",
  "last_name": "Develpment",
  "__v": 0,
  "can_delete": false
}
```

<h3 id="update-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|

<h3 id="update-responseschema">Response Schema</h3>

Status Code **200**

|Name|Type|Description|
|---|---|---|---|---|

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Search

<a id="opIdUsersSearch"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/users/search',
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

`GET /users/search`

Returns users whose email, first name or last name match the search string provided by the `q` parameter.

The following roles can access this API endpoint: `Admin`

<h3 id="search-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|q|query|undefined|true|The string to search for|
|status|query|string|false|If provided, this endpoint will only return users matching the specified status|

#### Enumerated Values

|Parameter|Value|
|---|---|
|status|Active|
|status|Inactive|

> Example responses

> 200 Response

```json
[
  {
    "api_key": {
      "last_used": "2020-11-19T21:32:46.261Z",
      "token": "exists"
    },
    "on_air_name": "DJ Cool Software",
    "primary_phone": null,
    "roles": [
      "Admin"
    ],
    "status": "Active",
    "_id": "5f720bae0504f73464bd83eb",
    "email": "comrad.development@gmail.com",
    "first_name": "Comrad",
    "last_name": "Develpment",
    "__v": 0
  },
  {
    "api_key": {
      "last_used": null,
      "token": null
    },
    "on_air_name": "DJ Show Producer",
    "primary_phone": null,
    "roles": [
      "Show Producer"
    ],
    "status": "Active",
    "_id": "5f720bae0504f73464bd83ee",
    "email": "show.producer@comrad.com",
    "first_name": "Show Producer",
    "last_name": "Test",
    "__v": 0
  },
  {
    "api_key": {
      "last_used": null,
      "token": null
    },
    "on_air_name": "DJ Full Access",
    "primary_phone": null,
    "roles": [
      "Full Access"
    ],
    "status": "Active",
    "_id": "5f720bae0504f73464bd83ed",
    "email": "full.access@comrad.com",
    "first_name": "Full Access",
    "last_name": "Test",
    "__v": 0
  },
  {
    "api_key": {
      "last_used": null,
      "token": null
    },
    "on_air_name": "DJ Underwriting",
    "primary_phone": null,
    "roles": [
      "Underwriting"
    ],
    "status": "Active",
    "_id": "5f720bae0504f73464bd83ef",
    "email": "underwriting@comrad.com",
    "first_name": "Underwriting",
    "last_name": "Test",
    "__v": 0
  },
  {
    "api_key": {
      "last_used": null,
      "token": null
    },
    "on_air_name": "DJ Admin",
    "primary_phone": null,
    "roles": [
      "Admin"
    ],
    "status": "Active",
    "_id": "5f720bae0504f73464bd83ec",
    "email": "admin@comrad.com",
    "first_name": "Admin",
    "last_name": "Test",
    "__v": 0
  },
  {
    "api_key": {
      "last_used": null,
      "token": null
    },
    "on_air_name": "DJ Guest",
    "primary_phone": null,
    "roles": [
      "Guest"
    ],
    "status": "Active",
    "_id": "5f720bae0504f73464bd83f1",
    "email": "guest@comrad.com",
    "first_name": "Guest",
    "last_name": "Test",
    "__v": 0
  },
  {
    "api_key": {
      "last_used": null,
      "token": null
    },
    "on_air_name": null,
    "primary_phone": null,
    "roles": [],
    "status": "Active",
    "_id": "5fb6dd9becaa3904742d2339",
    "email": "s2@getcomrad.org",
    "first_name": "Sean",
    "last_name": "Williams",
    "__v": 0
  }
]
```

<h3 id="search-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Returns a list of matching users, in no particular order|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="search-responseschema">Response Schema</h3>

<aside class="warning">
To perform this operation, you must be authenticated by means of one of the following methods:
ApiKeyAuth
</aside>

## Search Hosts

<a id="opIdUsersSearchHosts"></a>

> Code samples

```javascript

const headers = {
  'Accept':'application/json',
  'Authorization':'API_KEY'
};

fetch('/users/search-hosts',
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

`GET /users/search-hosts`

Returns users and host groups whose on-air name, first name or last name match the search string provided by the `q` parameter.

The following roles can access this API endpoint: `Admin`, `Full Access`, `Show Captain`, `Underwriting`, `DJ`, `Music Library Admin`

<h3 id="search-hosts-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|q|query|undefined|true|The string to search for|
|status|query|string|false|If provided, this endpoint will only return users matching the specified status|
|maxResults|query|undefined|false|The maximum number of results to provide. Defaults to 10.|

#### Enumerated Values

|Parameter|Value|
|---|---|
|status|Active|
|status|Inactive|

> Example responses

> 200 Response

```json
[
  {
    "on_air_name": "Sean Williams",
    "_id": "5f721174ab735642446f6583",
    "first_name": "Sean",
    "last_name": "Williams",
    "type": "User"
  },
  {
    "_id": "5f8f456552431919908e84b3",
    "on_air_name": "Sean and Barry",
    "type": "HostGroup"
  },
  {
    "on_air_name": "Indra, Sean, And Patrick",
    "_id": "5f721164ab735642446f6542",
    "first_name": "Indra,",
    "last_name": "Sean, And Patrick",
    "type": "User"
  },
  {
    "on_air_name": "Ean Parmar",
    "_id": "5f7211a9ab735642446f665a",
    "first_name": "Ean",
    "last_name": "Parmar",
    "type": "User"
  }
]
```

<h3 id="search-hosts-responses">Responses</h3>

|Status|Meaning|Description|
|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Returns a list of matching users and host groups, ordered by relevance to the search string|
|401|[Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)|The authentication you provided to access the API is invalid|
|403|[Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)|Your API key or account does not have permission to access this|
|422|[Unprocessable Entity](https://tools.ietf.org/html/rfc2518#section-10.3)|There was an issue with the data you provided. Check the response for more details.|
|500|[Internal Server Error](https://tools.ietf.org/html/rfc7231#section-6.6.1)|Server error. Check the response for more details.|

<h3 id="search-hosts-responseschema">Response Schema</h3>

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

A show. Can represent either a repeating series, or a single show instance, which could be within a series, or its own standalone instance. If a show instance is within a series, it will have a `master_event_id` value that references the series Show object.

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
|frequency|integer|true|A value specifying the interval at which events should repeat.<br /><br />Values:<br />0 = Repeat yearly<br />1 = Repeat monthly<br />2 = Repeat weekly<br />3 = Repeat daily<br />4 = Repeat hourly<br />5 = Repeat minutely<br />6 = Repeat secondly|
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

 

