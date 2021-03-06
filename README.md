# Reddit Comment Checker [![npm][npm-image]][npm-url] [![downloads][downloads-image]][downloads-url]

[npm-image]: https://img.shields.io/npm/v/reddit-comment-checker.svg
[npm-url]: https://npmjs.org/package/reddit-comment-checker
[downloads-image]: https://img.shields.io/npm/dm/reddit-comment-checker.svg
[downloads-url]: https://npmjs.org/package/reddit-comment-checker

Polls Reddit comments from a post and sends an IFTTT webhook if a regular expression is matched.

## Installation:
```
npm install reddit-comment-checker
```

## Usage:

First import the `RedditCommentChecker` class from the module:
```
import { RedditCommentChecker } from 'reddit-comment-checker';
```

Then create a `RedditCommentChecker` by passing in the following data:
```
const commentChecker = new RedditCommentChecker({
    username: 'karenSmith',             // Reddit username to access API with
    password: 'hunter2',                // Reddit password for provided username
    appId: 'q-werTYuiOPas',              // Reddit App ID to access API with
    appSecret: 'asd-FGhjK8lZXCvB3Mqw',  // Reddit appSecret for provided App ID
    userAgent: 'platform:com.example.app:v1.0.0', // User Agent string to provide to Reddit API
    subreddit: 'funny',                 // Subreddit of provided post
    post: 'i0tgj4',                     // Post ID to poll comments of
    regex: /lol/,                       // Regular Expression to look for in comments
    stopOnMatch: true,                  // (Default = false) If true, stops polling after finding match
    stopOnNotify: false,                // (Default = false) If true, stops polling after triggering webhook (Equivalent to stopOnMatch if shouldNotify is not provided)
    iftttApiKey: 'asSFDidsijd8fsdfsdj', // API Key for IFTTT Webhook
    iftttEvent: 'doAThing',             // Event for IFTTT Webhook
    shouldNotify: undefined,            // (Default = _ => true) Only send a notification/stop when this function returns true
    generateIftttValues: matches => ({ value1: matches.length }) // (Optional) Function to generate query params to send to IFTTT webhook
});
```

For more information on the first 5 properties, see the usage instructions for the npm module [`reddit`](https://github.com/feross/reddit).

To get an IFTTT API Key for webhooks, go to [IFTTT's Webhooks page](https://ifttt.com/maker_webhooks) and follow their instructions. Once you have an API key, you can find the documentation at https://maker.ifttt.com/use/your-api-key.

The property `shouldNotify` is a function that takes the list of matches as a parameter. If it returns `true`, the IFTTT webhook will be triggered. If it returns false, the IFTTT webhook will not be triggered. If a function is not provided, the webhook will be called every time a match is found.

The final property: `generateIftttValues` is a callback that will run whenever matches are found. This takes in the array of matches, and returns an object with up to 3 properties, `value1`, `value2`, and `value3`, which will be provided to the IFTTT webhook if provided.

The matches used in `generateIftttValues` and `shouldNotify` are the return value from the `RegExp.exec()` function. Each element of the array will represent the match in a single comment. This match will be an array of strings, whose first index will be the full match for your Regular Expression, and the remaining items will be any capturing groups you specify in your expression. There are additional properties for maintaining state if you use a global or sticky expression. See the [Mozilla Developer Network documentation for `RegExp.prototype.exec()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec) for more information.

Once you have a `RedditCommentChecker` object, you can call the `startPolling(intervalInSeconds, stopAfter)` function to start polling for matches. Its first parameter is the interval (in seconds) at which to poll for matches. This parameter defaults to 3600 seconds, or 1 hour, and it will throw an error if you provide a value less than 1 second to prevent you from exceeding Reddit's rate limit of 60 calls per minute.

If you provided `stopOnMatch: true` in the `RedditCommentChecker` constructor, polling will automatically stop when a match is found. If `stopOnNotify` is set to `true`, polling will stop when a match is found and it causes `shouldNotify` to return true. If `shouldNotify` is not provided, `stopOnMatch` and `stopOnNotify` are equivalent. Otherwise, or if you want to stop early, you can either call the `stopPolling()` function to stop polling for matches immediately, or you can provide the optional second parameter to `startPolling(intervalInSeconds, stopAfter)` to automatically stop polling after that many seconds.

#### Full example:
```
const commentChecker = new RedditCommentChecker({
    username: 'karenSmith',
    password: 'hunter2',
    appId: 'q-werTYuiOPas',
    appSecret: 'asd-FGhjK8lZXCvB3Mqw',
    userAgent: 'platform:com.example.app:v1.0.0',
    subreddit: 'funny',
    post: 'i0tgj4',
    regex: /lol/,
    stopOnMatch: true,
    iftttApiKey: 'asSFDidsijd8fsdfsdj',
    iftttEvent: 'doAThing',
    generateIftttValues: matches => ({ value1: matches.length })
});

commentChecker.startPolling(10800, 604800); // Check once every 3 hours, give up after 3 days
```

Note: All API keys, app IDs, app secrets, usernames, passwords, etc in this documentation are intended to be fake and nonfunctional. If you discover that any of these do in face correspond to a real app or account that is currently in use, please create an Issue on this project so I can correct the mistake.
