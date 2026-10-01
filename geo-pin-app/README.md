# Geo-Pin App — Backend (AWS RDS / PostgreSQL)

A group-built location "pin" app where users drop public or private pins on a map
within a watch radius. **My focus was the backend:** hosting the app and its data on
AWS RDS PostgreSQL and designing the schema and data access.

## What I owned

- **AWS RDS (PostgreSQL) hosting** for the app and its data.
- **Schema design** — users, private pins, public pins, a validity/endorsement table
  (arrays of endorsed pin IDs), and a verification-code table, keyed on string
  UIDs/PIDs with `ON DELETE CASCADE` relationships.
- **Stored functions** — e.g. a `clean_and_fetch_public_pins()` that prunes stale
  pins and returns the current set in one call.
- **Concurrency / integrity** — reasoning about transaction isolation levels so
  concurrent, non-atomic operations couldn't overwrite or corrupt records.

## Documentation

- [`docs/schema-and-tables.pdf`](./docs/schema-and-tables.pdf) — full table layout,
  schema, stored functions, and backend notes.
- **Final presentation (view-only):**
  <https://docs.google.com/presentation/d/1FtzhcnCTl3_Xkk43bzHJMJRyg-5oK3iS-Z4Bp4VqKcQ/edit?usp=sharing>

## Skills

PostgreSQL (pSQL) · relational schema design · stored functions / `plpgsql` ·
transaction isolation & data integrity · AWS RDS.
