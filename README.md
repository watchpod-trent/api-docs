# WatchPod API Documentation

RESTful API for accessing streaming media information including titles, sources, genres, networks, and cast/crew data.

## Base URL

```
http://localhost/api/v1
```

## Response Format

All responses are in JSON format with proper HTTP status codes:
- `200 OK` - Successful request
- `404 Not Found` - Resource not found
- `422 Unprocessable Entity` - Validation error

### Pagination

List endpoints return paginated results with the following structure:

```json
{
  "data": [...],
  "links": {
    "first": "http://localhost/api/v1/titles?page=1",
    "last": "http://localhost/api/v1/titles?page=10",
    "prev": null,
    "next": "http://localhost/api/v1/titles?page=2"
  },
  "meta": {
    "current_page": 1,
    "from": 1,
    "last_page": 10,
    "per_page": 15,
    "to": 15,
    "total": 150
  }
}
```

## Common Query Parameters

### Pagination
- `per_page` - Number of results per page (default: 15)
- `page` - Page number

### Sorting
- `sort` - Comma-separated list of fields to sort by. Prefix with `-` for descending order.
  - Example: `sort=title` (ascending)
  - Example: `sort=-year,title` (year descending, then title ascending)

### Including Relationships
- `include` - Comma-separated list of relationships to include in the response
  - Example: `include=genres,networks,sources`

---

## Titles

Endpoints for accessing movie and TV show information.

### List Titles

```http
GET /api/v1/titles
```

**Query Parameters:**
- `type` - Filter by type: `movie`, `tv_series`, `tv_special`, `tv_miniseries`, `short_film`
- `year` - Filter by release year
- `search` - Search titles by name (case-insensitive partial match)
- `include` - Include relationships: `genres`, `networks`, `sources`, `cast`
- `sort` - Sort by: `id`, `title`, `year`, `end_year`, `release_date`, `runtime_minutes`, `user_rating`, `critic_score`, `popularity_percentile`, `relevance_percentile`
- `per_page` - Items per page (default: 15)

**Default Sort:** `popularity_percentile` descending

**Example Request:**
```bash
GET /api/v1/titles?type=movie&year=2024&include=genres,sources&sort=-user_rating&per_page=20
```

**Example Response:**
```json
{
  "data": [
    {
      "id": 123456,
      "title": "Example Movie",
      "original_title": "Example Movie Original",
      "plot_overview": "A thrilling adventure...",
      "type": "movie",
      "runtime_minutes": 120,
      "year": 2024,
      "end_year": null,
      "release_date": "2024-03-15",
      "imdb_id": "tt1234567",
      "tmdb_id": 98765,
      "tmdb_type": "movie",
      "user_rating": 8.5,
      "critic_score": 85,
      "us_rating": "PG-13",
      "original_language": "en",
      "relevance_percentile": 95.5,
      "popularity_percentile": 98.2,
      "genres": [
        {
          "id": 1,
          "name": "Action",
          "tmdb_id": 28
        }
      ],
      "networks": [],
      "sources": [
        {
          "id": 203,
          "name": "Netflix",
          "type": "sub",
          "ios_appstore_url": "...",
          "android_playstore_url": "...",
          "android_scheme": "...",
          "ios_scheme": "..."
        }
      ],
      "links": {
        "self": "http://localhost/api/v1/titles/123456",
        "similar": "http://localhost/api/v1/titles/123456/similar",
        "cast": "http://localhost/api/v1/titles/123456/cast"
      }
    }
  ],
  "meta": { ... }
}
```

### Get Single Title

```http
GET /api/v1/titles/{id}
```

**Query Parameters:**
- `include` - Include relationships: `genres`, `networks`, `sources`, `cast`

**Example Request:**
```bash
GET /api/v1/titles/123456?include=genres,networks,sources,cast
```

### Get Similar Titles

```http
GET /api/v1/titles/{id}/similar
```

Returns titles similar to the specified title.

**Example Request:**
```bash
GET /api/v1/titles/123456/similar
```

### Get Title Cast & Crew

```http
GET /api/v1/titles/{id}/cast
```

Returns cast and crew credits for the specified title.

**Example Request:**
```bash
GET /api/v1/titles/123456/cast
```

**Example Response:**
```json
{
  "data": [
    {
      "id": 1,
      "title_id": 123456,
      "person_id": 789,
      "type": "cast",
      "role": "John Doe",
      "episode_count": null,
      "display_order": 1,
      "person": {
        "id": 789,
        "full_name": "Jane Smith",
        "first_name": "Jane",
        "last_name": "Smith",
        "main_profession": "actor",
        "date_of_birth": "1985-05-15",
        "date_of_death": null,
        "imdb_id": "nm0123456",
        "tmdb_id": 54321,
        "relevance_percentile": 92.5
      }
    }
  ]
}
```

---

## Genres

Endpoints for accessing genre information.

### List Genres

```http
GET /api/v1/genres
```

**Query Parameters:**
- `sort` - Sort by: `id`, `name`, `tmdb_id`

**Default Sort:** `name` ascending

**Note:** Returns all genres (not paginated)

**Example Request:**
```bash
GET /api/v1/genres?sort=name
```

**Example Response:**
```json
{
  "data": [
    {
      "id": 1,
      "name": "Action",
      "tmdb_id": 28,
      "links": {
        "self": "http://localhost/api/v1/genres/1",
        "titles": "http://localhost/api/v1/genres/1/titles"
      }
    }
  ]
}
```

### Get Single Genre

```http
GET /api/v1/genres/{id}
```

### Get Titles by Genre

```http
GET /api/v1/genres/{id}/titles
```

Returns all titles in the specified genre.

**Query Parameters:**
- `sort` - Sort by: `id`, `title`, `year`, `release_date`, `user_rating`, `critic_score`, `popularity_percentile`, `relevance_percentile`
- `per_page` - Items per page (default: 15)

**Default Sort:** `popularity_percentile` descending

**Example Request:**
```bash
GET /api/v1/genres/1/titles?sort=-user_rating&per_page=20
```

---

## Networks

Endpoints for accessing TV network information.

### List Networks

```http
GET /api/v1/networks
```

**Query Parameters:**
- `search` - Search networks by name (case-insensitive partial match)
- `sort` - Sort by: `id`, `name`, `origin_country`, `tmdb_id`
- `per_page` - Items per page (default: 15)

**Default Sort:** `name` ascending

**Example Request:**
```bash
GET /api/v1/networks?search=HBO&sort=name
```

**Example Response:**
```json
{
  "data": [
    {
      "id": 49,
      "name": "HBO",
      "origin_country": "US",
      "tmdb_id": 49,
      "links": {
        "self": "http://localhost/api/v1/networks/49",
        "titles": "http://localhost/api/v1/networks/49/titles"
      }
    }
  ],
  "meta": { ... }
}
```

### Get Single Network

```http
GET /api/v1/networks/{id}
```

### Get Titles by Network

```http
GET /api/v1/networks/{id}/titles
```

Returns all titles from the specified network.

**Query Parameters:**
- `sort` - Sort by: `id`, `title`, `year`, `release_date`, `user_rating`, `critic_score`, `popularity_percentile`, `relevance_percentile`
- `per_page` - Items per page (default: 15)

**Default Sort:** `popularity_percentile` descending

---

## Sources

Endpoints for accessing streaming source information (Netflix, Hulu, etc.).

### List Sources

```http
GET /api/v1/sources
```

**Query Parameters:**
- `type` - Filter by type: `sub` (subscription), `free`, `buy`, `rent`, `tve` (TV Everywhere)
- `search` - Search sources by name (case-insensitive partial match)
- `include` - Include relationships: `regions`, `titles`
- `sort` - Sort by: `id`, `name`, `type`
- `per_page` - Items per page (default: 15)

**Default Sort:** `name` ascending

**Example Request:**
```bash
GET /api/v1/sources?type=sub&include=regions&sort=name
```

**Example Response:**
```json
{
  "data": [
    {
      "id": 203,
      "name": "Netflix",
      "type": "sub",
      "ios_appstore_url": "https://apps.apple.com/...",
      "android_playstore_url": "https://play.google.com/...",
      "android_scheme": "nflx://",
      "ios_scheme": "nflx://",
      "regions": [
        {
          "id": 1,
          "name": "US"
        }
      ],
      "links": {
        "self": "http://localhost/api/v1/sources/203"
      }
    }
  ],
  "meta": { ... }
}
```

### Get Single Source

```http
GET /api/v1/sources/{id}
```

**Query Parameters:**
- `include` - Include relationships: `regions`, `titles`

**Example Request:**
```bash
GET /api/v1/sources/203?include=regions,titles
```

---

## People

Endpoints for accessing cast and crew information.

### List People

```http
GET /api/v1/people
```

**Query Parameters:**
- `search` - Search people by full name (case-insensitive partial match)
- `sort` - Sort by: `id`, `full_name`, `first_name`, `last_name`, `date_of_birth`, `date_of_death`, `relevance_percentile`, `main_profession`
- `per_page` - Items per page (default: 15)

**Default Sort:** `relevance_percentile` descending

**Example Request:**
```bash
GET /api/v1/people?search=Smith&sort=-relevance_percentile&per_page=20
```

**Example Response:**
```json
{
  "data": [
    {
      "id": 789,
      "full_name": "Jane Smith",
      "first_name": "Jane",
      "last_name": "Smith",
      "main_profession": "actor",
      "date_of_birth": "1985-05-15",
      "date_of_death": null,
      "imdb_id": "nm0123456",
      "tmdb_id": 54321,
      "relevance_percentile": 92.5,
      "links": {
        "self": "http://localhost/api/v1/people/789",
        "titles": "http://localhost/api/v1/people/789/titles"
      }
    }
  ],
  "meta": { ... }
}
```

### Get Single Person

```http
GET /api/v1/people/{id}
```

### Get Titles by Person

```http
GET /api/v1/people/{id}/titles
```

Returns all cast/crew credits for the specified person.

**Query Parameters:**
- `sort` - Sort by: `id`, `type`, `role`, `episode_count`, `display_order`
- `per_page` - Items per page (default: 15)

**Default Sort:** `display_order` ascending

**Example Request:**
```bash
GET /api/v1/people/789/titles?sort=display_order
```

---

## Data Model Reference

### Title Types
- `movie` - Feature film
- `tv_series` - TV series
- `tv_special` - TV special
- `tv_miniseries` - Limited series
- `short_film` - Short film

### Source Types
- `sub` - Subscription service
- `free` - Free streaming
- `buy` - Purchase
- `rent` - Rental
- `tve` - TV Everywhere (cable authentication)

### Cast/Crew Types
- `cast` - Actor/Actress
- `crew` - Director, Writer, Producer, etc.

### Professions
Common values: `actor`, `actress`, `director`, `writer`, `producer`, `composer`, `cinematographer`, etc.

---

## Example Use Cases

### Find all Netflix movies from 2024
```bash
GET /api/v1/titles?type=movie&year=2024&include=sources
# Then filter client-side for titles where sources includes Netflix
```

### Get top-rated action movies with cast
```bash
GET /api/v1/genres/1/titles?include=cast&sort=-user_rating&per_page=10
```

### Search for titles and see where to stream them
```bash
GET /api/v1/titles?search=Inception&include=sources
```

### Find all titles featuring a specific actor
```bash
GET /api/v1/people?search=Tom%20Hanks
# Use the person ID from response
GET /api/v1/people/{id}/titles
```

---

## Error Responses

### 404 Not Found
```json
{
  "message": "Resource not found."
}
```

### 422 Unprocessable Entity
```json
{
  "message": "The given data was invalid.",
  "errors": {
    "type": ["The selected type is invalid."]
  }
}
```

---

## Rate Limiting

Currently no rate limiting is enforced. This may change in future versions.

## CORS

CORS is enabled for all origins in development. Configure appropriately for production use.

---

## Notes

- All date fields are in ISO 8601 format (YYYY-MM-DD)
- Percentile scores are 0-100 decimal values
- User ratings are 0-10 decimal values
- Critic scores are 0-100 integer values
- IDs may be non-sequential as they come from external sources
- The API is read-only; no POST, PUT, PATCH, or DELETE operations are available

