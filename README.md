GitHub Community Visualisation
==============================

## Goal

make a nice visualisation of all members from a given GitHub community, by loading them directly via the the API.

## Concept

1. Make a static HTML site, displaying all names / portraits / locations / bio, in pretty™
2. Make it dynamic by loading all members through GitHub's API
3. Encourage others to fork it, remix it, and have their own visualization

## Example

Let's take [@github's members](https://github.com/github?tab=members) as an example. Getting the members via the API (using jQuery):

```js
$.getJSON('https://api.github.com/orgs/github/public_members').then( 
  function(members) { 
    /* ... */ 
  }
)
```


Result looks like

```json
[
    {
        "login": "defunkt",
        "id": 2,
        "avatar_url": "https://0.gravatar.com/avatar/b8dbb1987e8e5318584865f880036796?d=https%3A%2F%2Fidenticons.github.com%2Fc81e728d9d4c2f636f067f89cc14862c.png&r=x",
        "gravatar_id": "b8dbb1987e8e5318584865f880036796",
        "url": "https://api.github.com/users/defunkt",
        "html_url": "https://github.com/defunkt",
        "type": "User",
        "site_admin": true
    },
    ...
]
```

Problem is that we only get the username and avatar URL, we don't get the name, location or bio with this request.
Getting all the details is not a problem code wise:

```js
getMemberDetails = function(members) {
  var promises = members.map( function(member) { return $.getJSON(member.url )})
  return $.when.apply($, promises)
}
normalizeData = function() {
  var array = Array.prototype.slice.call(arguments)
  return array.map( function(args) { return args[0] } )
}

$.getJSON('https://api.github.com/orgs/hoodiehq/public_members')
.then( getMemberDetails )
.then( normalizeData )
.then( function( memberDetails ) {
  /* ... */ 
})
```

Result now looks like

```json
[
  {
    "login": "defunkt",
    "id": 2,
    "avatar_url": "https://1.gravatar.com/avatar/b8dbb1987e8e5318584865f880036796?d=https%3A%2F%2Fidenticons.github.com%2Fc81e728d9d4c2f636f067f89cc14862c.png&r=x",
    "gravatar_id": "b8dbb1987e8e5318584865f880036796",
    "url": "https://api.github.com/users/defunkt",
    "html_url": "https://github.com/defunkt",
    "type": "User",
    "name": "Chris Wanstrath",
    "company": "GitHub",
    "blog": "http://chriswanstrath.com/",
    "location": "San Francisco",
    "email": "chris@github.com",
    "hireable": true,
    "bio": "",
    "public_repos": 101,
    "followers": 12819,
    "following": 208,
    "created_at": "2007-10-20T05:24:19Z",
    "updated_at": "2013-11-06T09:49:04Z",
    "public_gists": 284
  },
  ...
]
```

But the problem lies in [GitHub's API request rate limitations](http://developer.github.com/v3/#rate-limiting).
The script above sends on initial request to get all members plus an extra request per member. 
But GitHub allows only for **60 requests per hour** from one IP.

This could be a deal breaker … 

Ways to work around it:

1. Do only show login/avatar at first, and load details en demand when the visitor clicks on it.
2. Make the visitor sign in with his GitHub account, to bump the rate to 5000 / hour
3. <YOUR IDEA HERE>
