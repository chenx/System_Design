# Web Crawler

The basic algorithm of a web crawler:
1. Given a set of URLs, download all the web pages addressed by the URLs.
2. Extract URLs from these web pages
3. Add new URLs to the list of URLs to be downloaded. Repeat these 3 steps.

## Clarifying questions

- main purpose of the crawler? Is it used for search engine indexing, data mining, or something else?
- How many web pages does the web crawler collect per month?  1B pages.
- What content types are included? HTML only or other content types such as PDFs and images as well
- Do we need to store HTML pages crawled from the web? up to 5 years
- How do we handle web pages with duplicate content? Ignore

### characteristics of a good web crawler

- Scalability
- Robustness
- Politeness
- Extensibility

### Estimation

Assume 1 billion web pages are downloaded every month.
- QPS: 1,000,000,000 / 30 days / 24 hours / 3600 seconds = ~400 pages per second.
- Peak QPS = 2 * QPS = 800
- Assume the average web page size is 500k.
- 1-billion-page x 500k = 500 TB storage per month.
- Assuming data are stored for five years, 500 TB * 12 months * 5 years = 30 PB. A 30 PB storage is needed to store five-year content.

## High Level Design

### Workflow

Seed URLs -> URL Frontier -> HTML Downloader (check DNS resolver) -> Content Parser -> (Content Storage) -> Link Extractor -> URL Filter (back to URL Frontier -> URL Storage 

## Deep Dive

### DFS vs BFS

BFS is more commonly used. DFS may have stack overflow.

### URL frontier

- Politeness
- Priority
  - Prioritizer, Queue selector
- Freshness / re-crawl
- Storage for URL frontier
  - hundreds of millions of URLs
  - Majority on HD, current in Memory

### HTML Downloader

- Robots.txt
- Performance optimization
  - Distributed crawl
  - Cache DNS resolver
  - Locality
  - Short timeout

### Robustness

- Consistent hashing: distribute load of download servers
- Save crawl states and data
- Exception handling
- Data validation

### Extensibility

- Support new content types to download. E.g., image, PDF
- Web monitor: prevent copyright and trademark infringements

### Detect and avoid problematic content

- Redundant content
  - hash, checksum
- Spider trap
  - set max depth or length of links
- Data noise
  - advertisements, code snippets, spam URLs, etc.

### Others

- Server-side rendering
- Filter out unwanted pages (spam)
- Database replication and sharding
- Horizontal scaling (keep servers stateless)
- Availability, consistency, and reliability
- Analytics

