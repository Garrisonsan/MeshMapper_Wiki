# Coverage API

The Coverage API provides programmatic access to MeshMapper coverage grid-square data. Use it to draw coverage grids on your own maps or integrate MeshMapper data into external tools and dashboards.

## Authentication

Access requires a **Coverage** API key provisioned by a Master administrator. Each key is scoped to a specific region or multiregion group and has a daily rate limit.

To request a key, contact a Master administrator.

## Endpoint

```
GET https://meshmapper.net/coverage.php?key=YOUR_API_KEY
```

## Response Format

```json
{
  "success": true,
  "region": "[IATA]",
  "grid_size": { "lat": 0.0027, "lon": 0.00384 },
  "generated_at": 1710547200,
  "total_squares": 1234,
  "grid_squares": [
    {
      "grid_id": "16816_-19718",
      "bounds": {
        "south": 45.4032,
        "west": -75.7177,
        "north": 45.4059,
        "east": -75.7138
      },
      "coverage_type": "BIDIR",
      "fill_color": "#1e7e34",
      "border_color": "#14522d",
      "snr": 8.5,
      "timestamp": 1710547200
    }
  ]
}
```

## Field Reference

### Top-Level Fields

| Field | Type | Description |
| --- | --- | --- |
| `success` | boolean | `true` if the request succeeded. |
| `region` | string | The region or group code this key is scoped to. |
| `grid_size` | object | Grid square dimensions in degrees (`lat` and `lon`). |
| `generated_at` | integer | Unix timestamp of when the response was generated. |
| `total_squares` | integer | Number of grid squares returned. |
| `grid_squares` | array | Array of grid square objects (see below). |

### Grid Square Fields

| Field | Type | Description |
| --- | --- | --- |
| `grid_id` | string | Unique identifier for the grid cell in `"latIndex_lonIndex"` format. |
| `bounds` | object | Bounding box with `south`, `west`, `north`, `east` in decimal degrees. |
| `coverage_type` | string | One of: `BIDIR`, `TX`, `RX`, `DISC`, `DEAD`, `DROP`. |
| `fill_color` | string | Hex fill color matching MeshMapper's map rendering. |
| `border_color` | string | Hex border color matching MeshMapper's map rendering. |
| `snr` | float or null | Signal-to-noise ratio of the dominant ping, if available. |
| `timestamp` | integer or null | Unix timestamp of the ping colouring this grid square. |

## Coverage Types

When multiple pings exist in the same grid square, the highest-priority type wins.

| Type | Color | Priority | Description |
| --- | --- | --- | --- |
| `BIDIR` | Green (`#1e7e34`) | 6 | Two-way confirmed link. |
| `DISC` | Cyan (`#17a2b8`) | 5 | Discovery or trace packet. |
| `TX` | Orange (`#fd7e14`) | 4 | Transmitted but not heard back. |
| `RX` | Purple (`#6f42c1`) | 3 | Heard traffic, without transmitting. |
| `DEAD` | Grey (`#6c757d`) | 2 | Repeater heard but no route. |
| `DROP` | Red (`#bd2130`) | 1 | No connection. |

## Drawing Grid Squares

Each grid square can be reconstructed as a rectangle using the `bounds` object:

```javascript
// Leaflet.js example
data.grid_squares.forEach(sq => {
    L.rectangle(
        [[sq.bounds.south, sq.bounds.west], [sq.bounds.north, sq.bounds.east]],
        {
            fillColor: sq.fill_color,
            color: sq.border_color,
            fillOpacity: 0.6,
            weight: 1
        }
    ).addTo(map);
});
```

The grid uses fixed cell sizes of **0.0027 degrees latitude** by **0.00384 degrees longitude** (approximately 300m squares). These match MeshMapper's Simplified Mode rendering.

## Rate Limits

Each API key has a daily request limit. Counters reset daily.

When you exceed your limit, the API returns HTTP 429:

```json
{
  "success": false,
  "error": "rate_limit_exceeded",
  "message": "Daily request limit reached",
  "limit": 100,
  "used": 100,
  "resets_in_hours": 12.5
}
```

## Error Responses

| HTTP Code | Error | Description |
| --- | --- | --- |
| 400 | `missing_key` | No API key provided. |
| 400 | `invalid_region` | Region code on key not found. |
| 401 | `invalid_key` | API key not found. |
| 403 | `no_region` | No region assigned to this key. |
| 429 | `rate_limit_exceeded` | Daily request limit reached. |
| 500 | `server_error` | Internal error (database not found, etc.). |

## Managing Your Key

If you have a Coverage API key assigned to your admin account, you can view your current usage and regenerate your key from the **User Settings** tab in your region's Admin Portal. Regenerating a key invalidates the old one immediately.
