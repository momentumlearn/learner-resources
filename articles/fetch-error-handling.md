# Thinking through different Fetch response outcomes

## What if I get a 200 response to a search request but there is no data?

If you plan to show something like search results on a page, but no results are returned to a GET request, then you should handle that case in your code and update the UI to let your user know what's going on.

That could look something like a check to see if you have the results you want.

Remember, the check you do will depend on the structure and content of the data that is returned in the response!

```js
fetch(searchUrl)
  .then(res => res.json())
  .then(data => {
    if (data.results.length > 0){
      // this condition assumes that data.results is an array of objects
      // If so, you can check to see if there is at least one data object in there.
      // You may need to do a different kind of check depending on the structure of the data returned!
      // If this condition is met, you have the data you need, so do stuff with it here!
    } else {
      // Plan B: you could update the DOM to show a message like "No search results to display"
    }
  })
```

## What if I get a 4xx or 5xx error?

We want to write code for the "happy path" where we get precisely the response we want back from a request, but we also need to handle unsuccessful requests.

Common responses like 404 (not found), 403 (forbidden), or 500 (internal server error) indicate that something went wrong, so we won't get the data we want. So what do we do about it?

Maybe we want to track or log this in some way to alert us to the problem, or retry the request, but we definitely want to make sure the UI responds appropriately.

Because the fetch will not throw an Error if one of these status codes shows up in the response (as other libraries you might use do), we have to handle it ourselves. We have [an `ok` method](https://developer.mozilla.org/en-US/docs/Web/API/Response/ok) we can use on the response object. It will return `true` if the response falls in the successful 2xx range. What we do next is up to us, but one possible solution is to throw your own Error and use `catch` to handle it.

```js
fetch('http://httpstat.us/404')
  .then(response => {
    if (!response.ok) {
      throw new Error(response.statusText)
    }
    return response.json() // this line won't run if we threw an error in the if block above
  })
  .then(data => {
    // do stuff with data
    // we would only get here if the response IS ok (that is, a 200 response)
  })
  .catch(error => {
    console.log(error)
    // maybe insert content in the DOM displaying the error
  })
```
