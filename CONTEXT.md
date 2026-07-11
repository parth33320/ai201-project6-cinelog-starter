# CONTEXT.md — Ubiquitous Language for CineLog Watchlist

This document establishes the Ubiquitous Language and domain glossary for the CineLog Watchlist feature, ensuring alignment across all layers of the application (Database Schema, Service Layer, REST API Endpoints, and Test Suite).

## Domain Glossary

* **User**: A registered account on the CineLog platform. Each User is uniquely identified by a UUID string.
* **Film**: A catalog item representing a movie. Following the database refactor, each Film is uniquely identified by a UUID string (post-rebase ritual) and contains metadata such as title, director, year, genre, and poster URL.
* **Collection**: A public-facing log of films a User has already watched and rated. It serves as a social record and is sorted newest-first.
* **Watchlist**: A personal, private-by-default queue of films a User intends to watch in the future. It acts as an aspirational backlog.
* **Watchlist Entry**: A discrete database record linking a User to a Film via foreign keys. It includes a `public` visibility flag (defaulting to `False` / Private) and a `date_added` timestamp.

## Architectural Boundaries and Mappings

| Domain Concept | Database Model | Service Functions | API Endpoints | Default Behavior |
| :--- | :--- | :--- | :--- | :--- |
| **Watchlist Entry** | `WatchlistEntry` | `add_to_watchlist()` / `remove_from_watchlist()` | `POST /watchlist/<user_id>/add` / `DELETE /watchlist/<user_id>/remove` | Private (`public=False`), Sort: Newest-First |
| **Collection Entry** | `CollectionEntry` | `add_to_collection()` / `remove_from_collection()` | `POST /collection/<user_id>/add` / `DELETE /collection/<user_id>/remove` | Public, Sort: Newest-First |
