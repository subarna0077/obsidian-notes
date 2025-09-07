                 ┌────────────────────────────┐
                 │   Client requests list     │
                 │   GET /products/           │
                 └─────────────┬─────────────┘
                               │
                               ▼
                     ┌─────────────────┐
                     │ Check Redis cache│
                     │  cache.get(KEY)  │
                     └─────┬───────────┘
                  Cache Hit? │
                   ┌─────────┘
                   │Yes
                   ▼
            ┌───────────────────┐
            │ Return cached data │
            │  (super fast!)    │
            └───────────────────┘
                   │
                   │
                   │
                   ▼
                   No
             ┌─────────────┐
             │ Query DB    │
             │ Product.objects.all() │
             └─────┬───────┘
                   │
                   ▼
             ┌─────────────┐
             │ Serialize   │
             │ data        │
             └─────┬───────┘
                   │
                   ▼
             ┌─────────────┐
             │ Store in    │
             │ Redis cache │
             │ cache.set(KEY, data, timeout) │
             └─────┬───────┘
                   │
                   ▼
             ┌─────────────┐
             │ Return data │
             └─────────────┘
             

## Cache invalidation flow


   ┌─────────────┐
   │ Product is  │
   │ created/    │
   │ updated/    │
   │ deleted     │
   └─────┬───────┘
         │
         ▼
 ┌─────────────────────────┐
 │ Django signal triggers  │
 │  (post_save / post_delete) │
 └─────┬───────────────────┘
         │
         ▼
 ┌─────────────────────────┐
 │ Delete Redis cache key  │
 │ cache.delete(CACHE_KEY) │
 └─────┬───────────────────┘
         │
         ▼
 ┌─────────────────────────┐
 │ Next GET /products/     │
 │ sees cache miss → rebuild│
 └─────────────────────────┘
