## Specification for `llm.txt` for pypi python packages

### 1. Overview

This document specifies the structure and content of an `llm.txt` file. The purpose of this file is to provide a comprehensive, machine-readable, and human-friendly context about a software library to a Large Language Model (LLM). Adherence to this specification will enable the LLM to generate more accurate, idiomatic, and safe code.

The file format is **Markdown** with a **YAML Frontmatter** block.

### 2. File Structure

The file must be organized into the following top-level sections in this specific order:

1.  **YAML Frontmatter** (Required)
2.  **Core Concepts & Design Philosophy** (Highly Recommended)
3.  **Pitfalls & Common Errors** (Recommended)
4.  **Index** (Required)
5.  **Module Sections** (Required)
6.  **Global Examples** (Highly Recommended)

### 3. Section Specifications

#### 3.1. YAML Frontmatter

* **Status:** Required
* **Purpose:** To provide structured, machine-readable metadata about the package.
* **Format:** A valid YAML block enclosed by `---` at the very beginning of the file.
* **Fields:**
    * `package` (string, required): The official name of the package (e.g., `requests`, `my-awesome-library`).
    * `version` (string, required): The semantic version number of the package documentation (e.g., `2.28.1`).
    * `language` (string, required): The programming language. Must be `python`.
    * `summary` (string, required): A concise, one-sentence description of the package's purpose.
* **Example:**
    ```yaml
    ---
    package: text-formatter
    version: 0.1.0
    language: python
    summary: A simple utility for creating URL-friendly slugs from strings.
    ---
    ```

#### 3.2. Core Concepts & Design Philosophy

* **Status:** Highly Recommended
* **Purpose:** To give the LLM a high-level understanding of the library's architecture and intended usage patterns.
* **Format:** A block of prose text using standard Markdown.
* **Content:**
    * Identify the 1-3 primary abstractions or classes (e.g., `Pipeline`, `Step`).
    * Describe the most common workflow or sequence of operations.
    * Explain any fundamental design patterns the library relies on (e.g., immutability, dependency injection, factory pattern).
* **Example:**
    ```markdown
    The library is centered around the `slugify()` function, which performs the core logic. All customization, such as setting a custom separator or changing the case, is handled by creating and passing an instance of the `FormatConfig` class. The design is purely functional; functions do not maintain state or have side effects.
    ```

#### 3.3. Pitfalls & Common Errors

* **Status:** Recommended
* **Purpose:** To guide the LLM away from common mistakes and anti-patterns.
* **Format:** A bulleted list using standard Markdown.
* **Content:**
    * List common mistakes (e.g., "Forgetting to call `.build()` before `.run()`).
    * Describe known anti-patterns (e.g., "Creating monolithic `Step` classes instead of composing smaller ones").
    * Explicitly mention any classes or functions intended only for testing or internal use.
* **Example:**
    ```markdown
    - **Error:** Using a multi-character string (e.g., `'--'`) for the `separator` in `FormatConfig` will raise an `InvalidSeparatorError`. Only single characters are permitted.
    - **Behavior Note:** By default, non-ASCII characters are transliterated to their closest ASCII equivalent (e.g., 'é' becomes 'e'). To preserve them, you must set `allow_unicode=True` in the `FormatConfig`.
    - **Anti-Pattern:** Do not call internal helper functions like `_clean_text()`. They are not part of the public API and may change without notice.
    ```

#### 3.4. Index

* **Status:** Required
* **Purpose:** To provide a quick, scannable table of contents of the public API.
* **Format:** A nested bulleted list. The top level represents modules, and the second level categorizes the contents of that module.
* **Content:**
    * List all public modules and submodules.
    * Under each module, list its public `Classes`, `Functions`, and `Exceptions`.
* **Example:**
    ```markdown
    - **`text_formatter.main`**
      - **Classes:** `FormatConfig`
      - **Functions:** `slugify`
      - **Exceptions:** `InvalidSeparatorError`
    ```

#### 3.5. Module Sections

* **Status:** Required
* **Purpose:** The detailed API reference for every public object in the library.
* **Format:** Each module listed in the Index must have a corresponding Level 3 Markdown header (`### Module: module.name`). Underneath, each public object gets its own Level 4 header.
* **Example:**
    ```markdown
    ### Module: `text_formatter.main`

    #### class FormatConfig
    A configuration object to control the behavior of the `slugify` function.

    - `__init__(self, separator: str = '-', to_lower: bool = True, allow_unicode: bool = False)`
      - Creates a new configuration. Raises `InvalidSeparatorError` if the separator is not a single character.
      - *Example:* `config = FormatConfig(separator='_', to_lower=False)`

    #### Function: slugify(text: str, config: FormatConfig | None = None) -> str
    Converts a string into a URL-friendly slug.
    - *Example:* `slug = slugify("Hello World!")`

    #### exception InvalidSeparatorError(ValueError)
    Raised when an invalid separator is provided to `FormatConfig`.
    ```

#### 3.6. Global Examples

* **Status:** Highly Recommended
* **Purpose:** To demonstrate a complete, end-to-end, idiomatic use case of the library.
* **Format:** One or more fenced code blocks using ` ```python `.
* **Content:**
    * A realistic, runnable code snippet that imports multiple components from the library and shows how they work together to solve a problem.
    * The code should be well-commented to explain the steps.
* **Example:**
    ```python
    from text_formatter.main import slugify, FormatConfig, InvalidSeparatorError

    # --- Basic Usage ---
    # Use the default settings (lowercase, hyphen separator)
    basic_slug = slugify("This is a Test Title!")
    print(f"Basic: '{basic_slug}'")  # Output: 'this-is-a-test-title'

    # --- Advanced Usage with a Config Object ---
    # Create a custom configuration to use underscores and preserve case.
    custom_config = FormatConfig(separator='_', to_lower=False)
    advanced_slug = slugify("Another Example with Custom_Settings", config=custom_config)
    print(f"Advanced: '{advanced_slug}'") # Output: 'Another_Example_with_Custom_Settings'

    # --- Handling Unicode ---
    unicode_config = FormatConfig(allow_unicode=True)
    unicode_slug = slugify("你好世界", config=unicode_config)
    print(f"Unicode: '{unicode_slug}'") # Output: '你好世界'

    # --- Handling Expected Errors ---
    try:
        # This will fail because '--' is not a single character
        error_config = FormatConfig(separator='--')
    except InvalidSeparatorError as e:
        print(f"Caught expected error: {e}")
    ```

## Real Example - geopy==2.4.1 - llm.txt

```
---
package: geopy
version: 2.4.1
language: python
summary: A Python client for several popular geocoding web services.
---
```

### Core Concepts & Design Philosophy

The `geopy` library provides a unified interface to a variety of third-party geocoding web services. The core of the library is the `geopy.geocoders.Geocoder` abstract class, with specific implementations for each service (e.g., `Nominatim`, `GoogleV3`, `Bing`).

The typical workflow is to:
1.  Instantiate a specific geocoder with any required credentials (like an API key or a `user_agent`).
2.  Use the `geocode()` method to convert a string address into geographic coordinates (`latitude`, `longitude`).
3.  Use the `reverse()` method to convert coordinates into a human-readable address.

A second key feature is the `geopy.distance` module, which provides tools to calculate the distance between two points using either `geodesic` (default, highly accurate ellipsoid model) or `great_circle` (simpler spherical model) calculations.

For bulk operations, the library provides `geopy.extra.rate_limiter.RateLimiter` to gracefully handle API usage limits and prevent being blocked by service providers.

### Pitfalls & Common Errors

- **`user_agent` is Mandatory for Nominatim**: The `Nominatim` geocoder requires a custom `user_agent` string identifying your application (e.g., `Nominatim(user_agent="my-awesome-app")`). Using the default will result in an error to prevent violating the service's terms of use.
- **Each Service has its Own Terms**: `geopy` is only a client library. Each geocoding service has its own terms, pricing, and rate limits. The LLM should advise users to consult the specific service's documentation.
- **Handling No Results**: If a query returns no results, the `geocode()` or `reverse()` methods will return `None`. Code should always handle this case.
- **`exactly_one=True` Default**: By default, geocoding methods return a single `geopy.location.Location` object. If multiple results are possible and desired, you must explicitly pass `exactly_one=False`, which will return a list of `Location` objects.
- **Coordinate Order is (latitude, longitude)**: Throughout `geopy`, points are represented in `(latitude, longitude)` order.
- **Use `RateLimiter` for Bulk Geocoding**: When geocoding many addresses, wrap the `geocode` method in a `RateLimiter` to automatically add delays and retry on errors, respecting the provider's usage policy.

### Index

- **`geopy.geocoders`**
  - **Classes:** `Nominatim`, `GoogleV3`, `Bing`, `ArcGIS`, `Pelias`, `Photon` (and many others)
  - **Functions:** `get_geocoder_for_service`
- **`geopy.distance`**
  - **Classes:** `Distance`, `geodesic`, `great_circle`
- **`geopy.point`**
  - **Classes:** `Point`
- **`geopy.location`**
  - **Classes:** `Location`
- **`geopy.exc`**
  - **Exceptions:** `GeocoderServiceError`, `GeocoderTimedOut`, `GeocoderQuotaExceeded`, `ConfigurationError`, `GeocoderNotFound`
- **`geopy.extra.rate_limiter`**
  - **Classes:** `RateLimiter`, `AsyncRateLimiter`

---

### Module: `geopy.geocoders`

#### Function: get_geocoder_for_service(service: str) -> Geocoder
Returns the geocoder class for a given service string (e.g., "nominatim"). Raises `GeocoderNotFound` if the service is not recognized.

#### class Nominatim
Geocoder for OpenStreetMap data.
- **`__init__(self, user_agent: str, domain: str = 'nominatim.openstreetmap.org', ...)`**
  - A `user_agent` is required.
  - *Example:* `geolocator = Nominatim(user_agent="my_app/1.0")`
- **`geocode(self, query: str | dict, *, exactly_one: bool = True, timeout: int = 1, limit: int = None, addressdetails: bool = False, language: str = 'en', country_codes: list = None, ...)`**
  - Converts an address string or structured dict to a location.
- **`reverse(self, query: Point | str, *, exactly_one: bool = True, timeout: int = 1, language: str = 'en', zoom: int = 18, ...)`**
  - Converts coordinates to an address.

#### class GoogleV3
Geocoder using the Google Maps v3 API.
- **`__init__(self, api_key: str, ...)`**
  - Requires a Google Maps API key.
  - *Example:* `geolocator = GoogleV3(api_key="YOUR_API_KEY")`
- **`geocode(self, query: str = None, *, components: dict = None, bounds: list = None, region: str = None, language: str = None, ...)`**
  - Geocodes a query or a structured `components` dictionary.
- **`reverse(self, query: Point | str, *, language: str = None, ...)`**
  - Converts coordinates to an address.

---

### Module: `geopy.distance`

#### class Distance
Represents a distance that can be expressed in various units. Arithmetic and comparisons are supported.
- **`__init__(self, kilometers: float = 0, miles: float = 0, ...)`**
- Access units as properties:
  - *Example:* `d = Distance(miles=10); print(d.km)` # 16.09344

#### class geodesic(Distance)
Calculates the geodesic distance between two points using a specified ellipsoid model (default WGS-84). This is the most accurate method.
- **`__init__(self, *args, ellipsoid='WGS-84', **kwargs)`**
- *Usage:* `distance.geodesic(point1, point2)`

#### class great_circle(Distance)
Calculates the great-circle distance using a simpler spherical model of the Earth. It's faster but less accurate than `geodesic`.
- **`__init__(self, *args, radius=EARTH_RADIUS, **kwargs)`**
- *Usage:* `distance.great_circle(point1, point2)`

---

### Module: `geopy.point`

#### class Point
A data structure representing a geographic point.
- **`__init__(self, latitude: float, longitude: float, altitude: float = 0)`**
  - Can also be created from a sequence `(lat, lon, alt)` or a string `'lat, lon'`.
  - *Example:* `p = Point(41.5, -81.0)`
- Access values by index or name: `p[0]` or `p.latitude`.

---

### Module: `geopy.location`

#### class Location
The object returned by a successful `geocode` or `reverse` call.
- **Properties:**
  - `address` (str): The full, human-readable address.
  - `latitude` (float): The latitude of the location.
  - `longitude` (float): The longitude of the location.
  - `altitude` (float): The altitude of the location.
  - `point` (Point): A `Point` object for the location.
  - `raw` (dict): The original, unprocessed response from the geocoding service.

---

### Global Examples

**1. Basic Geocoding and Reverse Geocoding**
```python
from geopy.geocoders import Nominatim
from geopy.exc import GeocoderTimedOut

# Always set a user_agent
geolocator = Nominatim(user_agent="my-geocoding-app")

# Geocode an address
try:
    location = geolocator.geocode("175 5th Avenue NYC")
    if location:
        print(f"Address: {location.address}")
        print(f"Coordinates: ({location.latitude}, {location.longitude})")
except GeocoderTimedOut:
    print("Error: Geocoder service timed out.")

# Reverse geocode coordinates
try:
    location = geolocator.reverse("40.7410861, -73.9896297")
    if location:
        print(f"\nReverse Geocoded Address: {location.address}")
except GeocoderTimedOut:
    print("Error: Geocoder service timed out.")

2. Calculating Distancefrom geopy.distance import geodesic, great_circle
from geopy.point import Point

newport_ri = Point(41.49008, -71.312796)
cleveland_oh = Point(41.499498, -81.695391)

# Most accurate method
dist_geodesic = geodesic(newport_ri, cleveland_oh)
print(f"\nGeodesic distance: {dist_geodesic.miles:.2f} miles")

# Faster, less accurate method
dist_great_circle = great_circle(newport_ri, cleveland_oh)
print(f"Great-circle distance: {dist_great_circle.miles:.2f} miles")
3. Bulk Geocoding with Rate Limitingimport pandas as pd
from geopy.geocoders import Nominatim
from geopy.extra.rate_limiter import RateLimiter

# Create a sample DataFrame
df = pd.DataFrame({'address': ['Eiffel Tower, Paris, France', 'Colosseum, Rome, Italy', 'Big Ben, London, UK']})

geolocator = Nominatim(user_agent="my-bulk-geocoding-app")

# 1. Create a geocoder instance
# 2. Wrap the geocode method with RateLimiter to avoid API errors
#    This adds a 1-second delay between calls.
geocode = RateLimiter(geolocator.geocode, min_delay_seconds=1)

# Apply the geocoder to the 'address' column
# The RateLimiter will handle the delays automatically
df['location'] = df['address'].apply(geocode)

# Extract coordinates into a 'point' column
df['point'] = df['location'].apply(lambda loc: (loc.latitude, loc.longitude) if loc else None)

print("\n--- Bulk Geocoding Results ---")
print(df)
