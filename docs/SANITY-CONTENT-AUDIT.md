# Sanity & Content Modeling Audit

Audit against **Content Modeling Best Practices** and **Sanity Best Practices** (GROQ, schema, images, TypeGen). Items are ordered by impact (critical → low).

---

## Critical

### 1. GROQ: Order before slice (Sanity rule)

**Rule:** Always apply `order()` before slice notation. Slicing before ordering returns arbitrary results.

**Current:** `FEATURED_PROPERTIES_QUERY` uses `[0...6]` with no `order()`:

```groq
*[_type == "property" && featured == true && status == "active"][0...6] { ... }
```

**Fix:** Order then slice so “featured” results are deterministic (e.g. latest first):

```groq
*[_type == "property" && featured == true && status == "active"] | order(createdAt desc)[0...6] { ... }
```

**Status:** Fixed in this pass.

---

## High

### 2. Images: Use `_key` for array items (Sanity rule)

**Rule:** Use Sanity’s `_key` as React `key` for array items (e.g. `images[]`), not index or `asset._id`.

**Current:** `ImageGallery` uses `key={image.asset?._id || index}` and the image projection does not select `_key`.

**Fix:**

- In GROQ, include `_key` in every `images[]` (and any other array) projection.
- In components, use `key={image._key}` (with fallback only if needed).

**Status:** Fixed in this pass.

### 3. Images: Query hotspot and crop (Sanity rule)

**Rule:** Enable `hotspot: true` in schema (done) and **query** `hotspot` and `crop` so the URL builder can use them.

**Current:** Schema has `hotspot: true` on image fields. The shared `imageFragment` only projects `asset->{ ... }, alt`; it does not project `hotspot` or `crop`.

**Fix:** Add `hotspot` and `crop` to the image fragment so `urlFor(image)` can apply editor-defined cropping and focal point.

**Status:** Fixed in this pass.

### 4. Images: Use LQIP for blur placeholders (Sanity rule)

**Rule:** Query `asset->metadata.lqip` (and dimensions) and use them with Next.js `Image` for blur-up.

**Current:** GROQ already requests `metadata { lqip, dimensions }`. Components use `urlFor(...)` but do **not** pass `placeholder="blur"` or `blurDataURL` to Next.js `Image`.

**Fix:** Where you render Sanity images (e.g. `PropertyCard`, `ImageGallery`, `AgentCard`), pass `placeholder={metadata?.lqip ? 'blur' : 'empty'}` and `blurDataURL={metadata?.lqip}` when available.

**Status:** Fixed in this pass for PropertyCard and ImageGallery.

---

## Medium

### 5. Content model: Amenities as references vs strings (Content modeling)

**Rule:** Use references when content is reusable and should stay in sync (single source of truth).

**Current:** `property.amenities` is `array of string`. Amenity taxonomy lives in the `amenity` document type (value, label, icon, order). Storing only strings on the property means:

- Renaming or fixing an amenity in the Amenities collection does not update existing properties.
- No single source of truth between “amenity definition” and “property amenities”.

**Options:**

- **A (recommended long-term):** Change `amenities` to `array of reference` to `amenity`. Query with `amenities[]->{ value, label, _id }`. Listings and filters use the referenced docs. One place to update labels/values.
- **B (minimal change):** Keep strings but add validation (e.g. custom rule) so only values from the Amenities collection are allowed.

### 6. Schema: Slug validation (Sanity rule)

**Rule:** Validate slug format (e.g. lowercase, hyphens only) and optionally uniqueness.

**Current:** `property.slug` has `Rule.required()` only.

**Suggestion:** Add a custom rule (e.g. regex `^[a-z0-9-]+$`) and, if needed, async uniqueness check against other documents.

### 7. Studio structure (Sanity rule)

**Current:** Single flat list of document types via `S.documentTypeListItems()`.

**Suggestion:** Group by domain for editors, e.g.:

- **Listings** – Property
- **People** – User, Agent
- **Leads** – Lead
- **Data** – Amenity

Use `S.listItem().child(S.documentTypeList())` or structure builder groups so the Studio is easier to navigate.

---

## Low / Already good

### 8. GROQ: Optimizable filters and no joins in filters

**Status:** Queries use `_type == "..."`, `agent._ref == $agentId`, `_id == $id`, `clerkId == $clerkId`, `userId == $userId`. No `->` in filter conditions. Good.

### 9. GROQ: Project only needed fields

**Status:** Queries use explicit projections; no `*` or `{...}` without field list. Good.

### 10. GROQ: defineQuery and TypeGen

**Status:** Queries use `defineQuery` from `next-sanity`; types are generated and used across the app. Good.

### 11. Schema: Data over presentation

**Status:** Field names are semantic (title, status, address, description, etc.). No presentation-heavy names (e.g. “bigHeroText”, “redButton”). Good.

### 12. Schema: Reference vs object

**Status:** Agent and property/lead use `reference` where content is reusable; address is an embedded object where it’s document-specific. Good.

### 13. Schema: Validation

**Status:** Required fields, `.email()`, `.min()`/`.max()`/`.positive()` used. Optional: slug format and cross-field (e.g. price reduced) validation.

### 14. Schema: Image hotspot

**Status:** `hotspot: true` is set on image fields in property, agent, and user schemas. Good (querying hotspot/crop is covered in #3).

---

## Summary

| Priority   | Item                          | Action                          |
|-----------|--------------------------------|---------------------------------|
| Critical  | Order before slice            | Fixed (FEATURED_PROPERTIES)     |
| High      | Array `_key` in queries & UI   | Fixed (images)                  |
| High      | Query hotspot/crop            | Fixed (image fragment)           |
| High      | Use LQIP in Next/Image        | Fixed (PropertyCard, ImageGallery) |
| Medium    | Amenities references vs strings| Consider refactor or validation  |
| Medium    | Slug validation                | Optional schema improvement     |
| Medium    | Studio structure               | Optional grouping               |
| Low       | Filters, projections, TypeGen  | Already aligned with practices  |

After applying the fixes in this repo, run `npm run typegen` (or your typegen script) so generated types stay in sync with the updated queries.
