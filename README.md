# Scrape the Web!

This repo contains scripts I've written to scrape the web:


## scrapeYelp.py
  
As a result of Yelp's API limitations (can only pull 3 reviews per business), a scraper was developed to crawl yelp's web pages. 
This crawler uses lxml to identify where the reviews are located, and then exports these reviews into a csv. 

```
def fetch_reviews(link):
    """gathers the reviews from the link/business provided using NUM PAGES as the limit of pages to crawl. NOTE: 1
    page = 20 reviews. """
    biz_url = link.rsplit('biz/', 1)
    business = biz_url[1]
    start_url = ['http://www.yelp.com/biz/%s' % business]
    biz_reviews = []

    for url in start_url:
        for pg in range((NUM_PAGES + 1)):
            page = requests.get(url + ("?start=%s" % pg))
            tree = html.fromstring(page.text)
            reviews = tree.xpath('//p[@itemprop="description"]/text()')

            if reviews:
                for rev in reviews:
                    biz_reviews.append(str(rev))
            else:
                print('There are no reviews for this business: %s' % business)

    return biz_reviews, str(business)
```

This program takes 2 arguments: 

```
parser.add_argument('-p', '--num_pages',
                        default=NUM_PAGES, type=int, required=False,
                        help='enter the number of pages you would like reviews to be pulled from or leave it blank to get all of them. NOTE: 20 reviews '
                             'per page.')

parser.add_argument('-l', '--link',
                        default=False, type=str, required=True,
                        help='enter link to business...')
```

* **Number of Pages** (not required)
  - In order for the program to know how many reviews to pull, the number of we pages must be specified or leave it blank to get all of them. There are 20 reviews per page until the end of the reviews. This defaults on 1 page or 0.
* **Link** (required)
  - This program requires a link to be passed as an argument before executing. The code uses this link to specify which business to pull reviews from.
