### Good rule is

- Independent entity → separate collection.
- Small data that belongs closely to one entity → embed it.

### Use of separate collections 
1. Every update touching nested data needs positional operators / arrayFilters — as we saw earlier, you can't just updateOne the field directly; you have to locate the matching array element first ("items.$[item].price", arrayFilters: [...]), which is more verbose and easier to get wrong (miss a filter condition, update the wrong array index, etc.).

2. If the same embedded data appears across multiple documents/collections (like the product example — embedded in orders, carts, wishlists), a single real-world change means writing and running the same messy update once per collection, and you have to remember to do it everywhere it's duplicated. Miss one, and your data goes inconsistent (e.g. cart shows old price, wishlist shows new price).

3. Whole-document rewrites — MongoDB treats a document as the atomic unit; updating one small nested field still means the database touches/rewrites that entire (possibly large) document, which is slower than updating one small standalone document in a separate collection.

4. Growing complexity of queries as nesting gets deeper — if you ever needed to filter/query based on embedded array contents (e.g. "find all orders containing this specific product"), the query itself gets more complex ($elemMatch, nested $in, etc.) compared to a simple find({ productId }) on a separate collection.

### The decision framework you've landed on:

If an entity/data is small, fixed-size, and always used together with its parent → embed it directly (e.g. shippingAddress inside order — it's small, bounded, and you'd never query addresses independently of their order).
If an entity can grow large, needs independent querying/updating, or is shared across multiple parents → give it its own collection, and just store a reference (ObjectId) to connect it back (e.g. orderItems, reviews, returns, offers all reference back to orders/products rather than living nested inside them).

- And you're right that this isn't a one-time decision made only at the start — it's something you evaluate per entity, based on:

Will this grow unbounded? (reviews, order items, logs → yes → separate collection)
Does it need to be queried/updated on its own? (yes → separate collection)
Is it shared/referenced from multiple other collections? (yes → separate collection, avoid duplication)
Is it small and permanently tied to one parent, never touched independently? (embed)
