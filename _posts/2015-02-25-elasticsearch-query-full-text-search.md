---
layout: post
title: The Missing Defaults for ElasticSearch Full-Text Search
date: 2015-02-25 19:45
---

[ElasticSearch](http://elasticsearch.org/) is a widely-used application for indexing and searching both structured
and unstructured data. I've recently been working on improving how it's used for the search on the [PMG.org.za](https://pmg.org.za)
website and I'm surprised at how few examples there are of search queries for full-text search across
a collection of documents. So here's a breakdown of what I believe to be a reasonable default full-text
search query for ElasticSearch.

*(Update 2015-03-12: add ``operator``, set ``cutoff_frequency`` in ``multi_match`` query; increase body fragments to 2)*

My goal is to provide a **reasonable default** which others can build upon. If you know how to improve this for the general case, please let me know.

Caveats
=======

Search is complicated and I am not an expert. ElasticSearch has [extensive API documentation](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/index.html) and [a detailed guide](http://www.elasticsearch.org/guide/) which are worth reading. Not all queries will work in all cases and you may need to tweak things. This is a starting point, something that works reasonably well.

Assumptions
===========

The queries below make the following basic assumptions which cover many general use cases:

* We have a collection of documents that have a ``title`` and a ``body``, both of which are searchable.
* We want to provide a one-box search experience that "just works", like Google.

The Query
=========

Here's the query, then I'll dig into how it works:

{% highlight json %}
{
  "query": {
    "bool": {
      "must": {
        "multi_match": {
          "query": "THE QUERY TEXT",
          "type": "best_fields",
          "cutoff_frequency": 0.0007,
          "operator": "and",
          "fields": ["title", "body"]
        }
      },
      "should": {
        "multi_match": {
          "query": "THE QUERY TEXT",
          "type": "phrase",
          "fields": ["title", "body"]
        }
      }
    }
  },
  "highlight": {
    "pre_tags": ["<mark>"],
    "post_tags": ["</mark>"],
    "fragment_size": 80,
    "no_match_size": 0,
    "fields": {
      "title": {"number_of_fragments": 0},
      "body": {"number_of_fragments": 2}
    }
  },
  "_source": {"exclude": ["body"]},
  "size": 20,
  "sort": {
    "_score": {"order": "desc"}
  }
}
{% endhighlight %}

And here's how it works.

Term Query
==========

The first part is the actual query.  Here we have two queries joined using the
``bool`` operator, let's dig into the first one:

{% highlight json %}
"query": {
  "bool": {
    "must": {
      "multi_match": {
        "query": "THE QUERY TEXT",
        "type": "best_fields",
        "cutoff_frequency": 0.0007,
        "operator": "and",
        "fields": ["title", "body"]
      }
    },
{% endhighlight %}

This is a basic term query across both the ``title`` and ``body`` fields
using the ``best_fields`` type.  The ``must`` means that documents **must**
match this portion of the query. So they have to have at least some of the query words.
Queries with multiple words will have each searched for separately (that is, not as a phrase).

A [best-fields multi-match](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-multi-match-query.html#type-best-fields)
query searches across multiple fields independently and uses whichever has the
best score. So if the user roughly knows the title of a document they can
do a regular search for it and it'll rank highly, which is more convenient
than explicitly choosing a title search.

The ``cutoff_frequency`` is a crucial bit of magic which is hidden in the [common terms section of the ElasticSearch guide](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-common-terms-query.html#_examples_3).
Put simply, it means that words that are really common (like "is" and "the")
will be ignored unless they shouldn't be, such as when they make up most of the query.

Phrase Query
============

{% highlight json %}
"should": {
  "multi_match": {
    "query": "THE QUERY TEXT",
    "type": "phrase",
    "fields": ["title", "body"]
  }
}
{% endhighlight %}

The second query is also a ``multi_match`` query across both fields,
but this time it's a ``phrase`` query. This means it's searched
for as a complete phrase in each field. The ``should`` means that documents
don't **have** to match this, but those that do will have higher scores.

By combining the **must** term-based query with this **should** phrase-based
query, the user gets a combined phrase and keyword query at the same time
without having to ask for it. A document that has the full phrase will score
higher than one that just has all the words scattered around.

Highlights
==========

{% highlight json %}
"highlight": {
  "pre_tags": ["<mark>"],
  "post_tags": ["</mark>"],
  "fragment_size": 80,
  "no_match_size": 0,
  "fields": {
    "title": {"number_of_fragments": 0},
    "body": {"number_of_fragments": 1}
  }
},
{% endhighlight %}

This portion hightlights the matches that are found in each field. If a field
didn't match, it's not highlighted at all. We always want to show the entire
title field if there's a match, so we don't fragment it. If the body matches,
only show the best match and only enough for the user to check the context. We
use the HTML5 ``mark`` element to mark the matches, which has good styling in
modern browsers.

Sorting and other stuff
=======================

{% highlight json %}
"_source": {"exclude": ["body"]},
"size": 20,
"sort": {
  "_score": {"order": "desc"}
}
{% endhighlight %}

Here we tell ElasticSearch not to send back the full body of all our documents. That's
useful because it's unlikely we're going to show it in the search results and
it could potentially be really big.

Finally, we only want 20 matches and they must be sorted best to worst.

Even Lego includes Instructions
===============================

ElasticSearch is like Lego: lots of small components that can be combined in a
variety of amazing and powerful ways. There are many, many knobs to tweak and
different approaches to getting the best results for your scenario.

There's no true one-size-fits-all solution.

That doesn't mean we can't have a collection of good query blueprints
for common scenarios. Starting points from which to understand what's
possible and jump-start new directions. You can still build something
entirely your own, but if you're keen to get the basics working quickly,
you've got something to try out.
