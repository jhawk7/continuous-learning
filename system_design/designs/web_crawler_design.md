# Web Crawler

- `web crawlers` basically traverse the open web by following links from page to page (think of `depth-first-search`)
and store data to do things like `indexing` for seach engines (e.g. google) or storing data for training models (e.g. openai)
  - they usually respect website crawling policies defined in `robots.txt` files
  - ideally complete the task within one week


## Capacity Estimations (back of envelope)
1. Around 1 billion web pages
2. Around 1MB to store the contents of a web page
3. 1B pages x 1MB of data = Petabytes of data (PB; 10^15)
4. 1 week -> 600k seconds, 1B/600k = 1500 requests per second
5. Maybe 500 threads to load 1B pages within in week => 100 servers (4 cores 8 threads)

## Process overview
1. Pull url to "crawl list"
2. Check if we've already crawled the page
3. Check if crawling is compliant with robots.txt file
4. Get the IP address of the host via DNS
5. Make an http request to load the contents of the site
6. Check if we have already processed a different URL with identical content
7. Parse the content
8. Store the results somewhere
9. Add any referenced URLs to crawl list