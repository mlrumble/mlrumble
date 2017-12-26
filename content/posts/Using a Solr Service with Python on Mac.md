---
title: "Using a Solr Service with Python on Mac"
date: 2017-11-28T18:06:51+05:30
draft: false
keywords:
  - OSX
  - Solr
  - Apache
  - Python
description: Using a Solr Service with Python on Mac.

---

Started working on Solr for some search thing recently. It is difficult to find simple instructions to get it up and running. So here is my effort on the same.

## 1. Install Solr Server on your Laptop

I am using a Mac so the simplest way I could do that is by using

```bash
# Install Solr Server
brew install solr
# Start Solr Server with example configuration
solr -e techproducts
# Install solr for Python
pip install pysolr
```

Running the command `solr -e techproducts` gives us this message, and a whole lot of other things:

`
Waiting up to 180 seconds to see Solr running on port 8983 [\]
Started Solr server on port 8983 (pid=93066). Happy searching!
`
You can Direct your Web browser to http://localhost:8983/solr to visit the Solr Admin UI.
Now the fun part starts.

* * *

## 2. Use python to access the SolrServer

```python
import pysolr
solr = pysolr.Solr('http://localhost:8983/solr/techproducts',timeout=10)
# Clean up the Techproducts Solr Database(if that's what it is called)
solr.delete(q='*:*')
# Documents to be indexed. You can get that in any form but
# finally create a list of dicts
items = [{"id": "1","title": "text in action" },{"id": "2","title": "text in action party" }]
# Add documents to be indexed on the SolrCore
solr.add(items)
results = solr.search('party')
# See results
for r in results
    print r
```

Result :

`{u'_version_': 1585397928998993920, u'id': u'2', u'title': [u'text in action party']}`

Checkout "https://github.com/django-haystack/pysolr" for more options in pySolr

Hope that was helpful.
