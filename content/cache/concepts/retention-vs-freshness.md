---
title: Retention vs Freshness (TTL)
pcx_content_type: concept
---

# Retention versus Freshness (TTL)

In the context of Cloudflare CDN (Content Delivery Network), retention and freshness refer to two separate but related concepts. For an object in cache, freshness is how long it should be considered valid without consulting its source, while retention refers to how long it stays in cache before being removed.

## Retention

When a file or resource is requested from a website, Cloudflare caches it to avoid having to ask the origin server for it again the next time it is requested, reducing latency and improving delivery speed. But if an object in cache does not get requested again, eventually it will be removed to make room for newer, more popular objects. This process is called eviction and is a standard part of cache management. When the cache wants to store a new object but does not have room, it uses an algorithm called Least Recently Used, or LRU, to nominate an object to evict and replace it with the new one. An object’s cache retention period refers to the duration the object is stored in Cloudflare’s cache before being evicted. It is worth noting that an object’s retention period is a function of its relative popularity and the size of Cloudflare’s caches, and therefore is not configurable.

## Freshness (TTL)

The time window that an object should be considered safe for a cache to use is dictated by its freshness, also known as Time to Live (TTL). If an object has a TTL of five minutes that means that, starting from the moment the cache first receives the object, for the next five minutes the cache can use that object without checking with the origin again. After five minutes have passed, if Cloudflare gets another request for that object, we cannot use what is stored in the cache without first checking the origin to see if the object is still valid. Those first five minutes in this object’s case are its freshness period. There are a few ways to configure TTLs for resources served through Cloudflare’s Content Delivery Network:

- Include [Origin Cache Control](/cache/concepts/cache-control/) or [CDN Cache Control](/cache/concepts/cache-control/) directives, like `max-age` or `s-maxage`, in the origin cache-control response header.

- Use [Cache Rules](/cache/how-to/cache-rules/), [Page Rules](/cache/how-to/edge-browser-cache-ttl/create-page-rules/) (deprecated), or [Workers](/cache/concepts/interact-with-workers/).

If an object in cache is no longer fresh and Cloudflare receives a request for it, we ask the origin to revalidate the object we have in cache. The Origin can then either send a new version of the object which will replace the old in cache, or tell us the object we have is valid and to refresh its TTL. This revalidation will happen any time an object’s retention period is greater than its freshness period.