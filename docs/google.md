# What happens when you navigate to google.com?

1. Browser checks its own DNS cache for a corresponding IP address, then your OS' DNS cache, then (most likely) checks the default router's DNS cache, then ISP / configured DNS server until it gets an answer.
2. Browser builds HTTP GET string with "[http://[url]"](http://[url]”) as the requested URL
3. Browser sends request over the network (if asked how that works, mention the Border Gateway Protocol and say you don't know how it works)
4. (Possible interaction with proxy server / load balancer / CDN / etc.)
5. Server parses request string and routes it using Regex on the request path
6. Application layer assembles a response, possibly via a connection to a DB server
7. Response goes back over the network
8. Browser parses the response
9. Browser checks the headers, in particular the status code.
10. Browser goes down each HTML element and either paints the DOM or executes the tag
11. The browser builds a new GET request for each CSS or JS tag, goes through the same steps as above, and runs the code before proceeding.

Read all of these answers to the question (the more you read, the better you'll understand the important parts of the process):

- [Link 1 (short)][quora scrape]
- [Link 2 (medium)][igoro]
- [Link 3 (long)][what happens repo]

[igoro]: http://igoro.com/archive/what-really-happens-when-you-navigate-to-a-url/comment-page-4/
[quora scrape]: https://jiangchengl.wordpress.com/2015/08/20/what-happens-when-you-type-www-example-com-in-the-browser-address-and-enter-press-button/
[what happens repo]: https://github.com/alex/what-happens-when
