# Multi Search Engine

[![PyPI version](https://badge.fury.io/py/multi-search-engine.svg)](https://badge.fury.io/py/multi-search-engine)
[![Python Versions](https://img.shields.io/pypi/pyversions/multi-search-engine.svg)](https://pypi.org/project/multi-search-engine/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![CI](https://github.com/ssannssarr/SearchEngine/actions/workflows/ci.yml/badge.svg)](https://github.com/ssannssarr/SearchEngine/actions/workflows/ci.yml)
[![codecov](https://codecov.io/gh/yourusername/multi-search-engine/branch/main/graph/badge.svg)](https://codecov.io/gh/yourusername/multi-search-engine)
[![Downloads](https://pepy.tech/badge/multi-search-engine)](https://pepy.tech/project/multi-search-engine)

A powerful Python library for performing searches across multiple search engines with a unified interface. Supports Google, Bing, DuckDuckGo, Yahoo, Mojeek, and Brave with built-in caching, rate limiting, and proxy support.

## Features

- **6 Search Engines**: Google, Bing, DuckDuckGo, Yahoo, Mojeek, Brave
- **Unified Interface**: Same API for all search engines
- **1-Line Search**: `quick_search()` for ease of use
- **Visit URL**: Fetch page content from search results
- **Caching**: File-based and in-memory caching
- **Rate Limiting**: Built-in rate limiter with exponential backoff
- **Proxy Support**: Works with custom proxies and ScraperAPI
- **Result Filtering**: Filter by keywords, domain, or limit count
- **Export Options**: Export to JSON or dictionary format
- **Type Hints**: Full type annotations for better IDE support
- **Error Handling**: Comprehensive exception handling

## Prerequisites

- **Python 3.9** or higher 
- **pip** (usually installed with Python)

Check Python version:
```bash
python --version  # Must be 3.9+
```

## Installation

```bash
pip install xnoxs-engine
```

## Quick Start

### Easiest Way (1 Line!)

```python
from SearchEngine import quick_search

# Quick search in 1 line
results = quick_search("Python programming")

# With engine options
results = quick_search("machine learning", engine="brave", num_results=5)
```

### Standard Way

```python
from SearchEngine import DuckDuckGoSearch

# Create search engine instance
ddg = DuckDuckGoSearch()

# Perform search
results = ddg.search("Python programming", num_results=10)

# Process results
for result in results:
    print(f"Title: {result.title}")
    print(f"URL: {result.url}")
    print(f"Description: {result.description}")
    print()
```

### With Context Manager

```python
from SearchEngine import DuckDuckGoSearch, FileCache

# Auto cleanup after completion
with DuckDuckGoSearch(cache=FileCache()) as ddg:
    results = ddg.search("Python tutorial")
    for result in results:
        print(result.title)
```

## Supported Search Engines

| Engine | Class | Without Proxy | With ScraperAPI | Recommendation |
|--------|-------|---------------|-----------------|----------------|
| DuckDuckGo | `DuckDuckGoSearch` | Stable | Stable | Direct (no proxy) |
| Yahoo | `YahooSearch` | Stable | Stable | Direct (no proxy) |
| Mojeek | `MojeekSearch` | Stable | Stable | Direct (no proxy) |
| Brave | `BraveSearch` | Stable | Stable | Direct (no proxy) |
| Bing | `BingSearch` | May need captcha | OK | Use ScraperAPI |
| Google | `GoogleSearch` | Blocked | OK | ScraperAPI required |

> **Note:** Google and Bing actively block automated requests. Use ScraperAPI for reliable results.

## Usage Examples

### Basic Search

```python
from SearchEngine import DuckDuckGoSearch, BingSearch, GoogleSearch

# DuckDuckGo (no proxy)
ddg = DuckDuckGoSearch()
results = ddg.search("Python programming", num_results=10)

# With search parameters
results = ddg.search(
    query="machine learning",
    page=1,
    num_results=10,
    language="id",
    country="ID",
    safe_search=True
)
```

### Using ScraperAPI (for Google/Bing)

```python
from SearchEngine import GoogleSearch

google = GoogleSearch(scraper_api_key="YOUR_API_KEY")
results = google.search("Python programming")

for result in results:
    print(f"{result.title}: {result.url}")
```

### Caching Results

```python
from SearchEngine import DuckDuckGoSearch, FileCache, MemoryCache

# File-based cache (persistent)
file_cache = FileCache(cache_dir=".search_cache", default_ttl=3600)
ddg = DuckDuckGoSearch(cache=file_cache)

# Memory cache (session only)
mem_cache = MemoryCache(default_ttl=600)
ddg = DuckDuckGoSearch(cache=mem_cache)

# Results are automatically cached
results = ddg.search("Python Tutorial")
```

### Rate Limiting

```python
from SearchEngine import BingSearch, RateLimiter

limiter = RateLimiter(
    requests_per_minute=10,
    min_delay=1.0,
    max_delay=60.0
)

bing = BingSearch(rate_limiter=limiter)
results = bing.search("web development")
```

### Using Custom Proxy

```python
from SearchEngine import YahooSearch

yahoo = YahooSearch(proxy="http://proxy.example.com:8080")
results = yahoo.search("tech news")
```

### Filtering Results

```python
from SearchEngine import BraveSearch

brave = BraveSearch()
results = brave.search("programming tutorial", num_results=20)

# Filter by keyword
python_results = brave.filter_by_keyword("python")

# Filter by domain
github_results = brave.filter_by_domain("github.com")

# Limit results
top_5 = brave.limit_results(5)
```

### Exporting Results

```python
from SearchEngine import DuckDuckGoSearch

ddg = DuckDuckGoSearch()
results = ddg.search("data science")

# Export to list of dictionaries
data = ddg.to_dict_list()

# Export to JSON string
json_str = ddg.to_json(indent=2)

# Save to file
with open("results.json", "w") as f:
    f.write(ddg.to_json())
```

### Search All Engines at Once

```python
from SearchEngine import search_all_engines

# Search across all engines (DuckDuckGo, Yahoo, Mojeek, Brave)
result = search_all_engines("Python tutorial")

# Iterate successful results
for engine, items in result.items():
    print(f"{engine}: {len(items)} results")

# Check and handle errors per engine
if result.has_errors():
    for engine, error in result.errors.items():
        print(f"{engine} failed: {error}")

# Select specific engines
result = search_all_engines("AI", engines=["duckduckgo", "brave"])

# Raise exception if any error occurs
result = search_all_engines("AI", raise_on_error=True)
```

### View Available Engines

```python
from SearchEngine import get_available_engines

engines = get_available_engines()
print(engines)  # ['google', 'bing', 'duckduckgo', 'yahoo', 'mojeek', 'brave']
```

### Visit URL from Search Results

New feature to fetch content from found URLs:

```python
from SearchEngine import quick_search, visit_url

# Method 1: From search results
results = quick_search("Python tutorial")
page = results[0].visit()  # Visit first URL

print(f"Title: {page.title}")
print(f"Content: {page.text[:500]}")  # First 500 characters

# Method 2: Directly with URL
page = visit_url("https://python.org")
print(f"Title: {page.title}")
print(f"Status: {page.status_code}")

# Check error
if not page.success:
    print(f"Error: {page.error}")

# Preview text with specific length
print(page.get_text_preview(200))  # 200 characters
```

**PageContent Properties:**

| Property | Type | Description |
|----------|------|-------------|
| `url` | str | Page URL |
| `title` | str | Page title |
| `text` | str | Clean text (without HTML) |
| `html` | str | Raw HTML |
| `status_code` | int | HTTP status code |
| `success` | bool | True if successful |
| `error` | str | Error message (if failed) |

### Error Handling

```python
from SearchEngine import (
    DuckDuckGoSearch,
    NetworkException,
    ParseException,
    BlockedException
)

ddg = DuckDuckGoSearch()

try:
    results = ddg.search("query")
except NetworkException as e:
    print(f"Network error: {e}")
except ParseException as e:
    print(f"Failed to parse results: {e}")
except BlockedException as e:
    print(f"Blocked by search engine: {e}")
```

## API Reference

### SearchEngine Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `user_agent` | str | Random | Custom user agent string |
| `proxy` | str | None | Proxy URL |
| `timeout` | int | 30 | Request timeout in seconds |
| `delay` | float | 1.0 | Delay between requests |
| `cache` | CacheInterface | None | Cache instance |
| `rate_limiter` | RateLimiter | None | Rate limiter instance |
| `scraper_api_key` | str | None | ScraperAPI key |

### Search Method Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `query` | str | Required | Search query |
| `page` | int | 1 | Page number |
| `num_results` | int | 10 | Results per page |
| `language` | str | None | Language code (e.g., 'id', 'en') |
| `country` | str | None | Country code (e.g., 'ID', 'US') |
| `safe_search` | bool | True | Enable safe search |
| `use_cache` | bool | True | Use cached results |

### SearchResult Properties

| Property | Type | Description |
|----------|------|-------------|
| `title` | str | Result title |
| `url` | str | Result URL |
| `description` | str | Result description/snippet |
| `position` | int | Position in search results |
| `engine` | str | Search engine name |
| `extra` | dict | Additional metadata |

## Project Structure

```
SearchEngine/
├── SearchEngine/
│   ├── __init__.py
│   ├── base.py              # Base class and SearchResult
│   ├── cache.py             # FileCache and MemoryCache
│   ├── rate_limiter.py      # RateLimiter
│   ├── exceptions.py        # Custom exceptions
│   └── engines/
│       ├── __init__.py
│       ├── google.py
│       ├── bing.py
│       ├── duckduckgo.py
│       ├── yahoo.py
│       ├── mojeek.py
│       └── brave.py
├── tests/
├── .github/
│   └── workflows/
├── pyproject.toml
├── README.md
├── LICENSE
├── CHANGELOG.md
└── CONTRIBUTING.md
```

## Example Output

```python
>>> from SearchEngine import quick_search
>>> results = quick_search("Python", num_results=2)
>>> results[0].title
'Welcome to Python.org'
>>> results[0].url
'https://www.python.org/'
>>> results[0].description
'The official home of the Python Programming Language...'

>>> # Visit page
>>> page = results[0].visit()
>>> page.success
True
>>> page.title
'Welcome to Python.org'
```

## Troubleshooting

| Problem | Cause | Solution |
|---------|--------|----------|
| `ModuleNotFoundError: No module named 'SearchEngine'` | Library not installed | Run `pip install xnoxs-engine` |
| `NetworkException: Connection error` | No internet connection | Check your internet connection |
| `BlockedException: Captcha detected` | IP blocked by search engine | Use proxy or ScraperAPI |
| Empty results from Google | Google blocks requests | Use `scraper_api_key` parameter |
| `TimeoutError` | Server too slow | Increase `timeout=60` parameter |
| `ParseException` | HTML format changed | Update library to latest version |

**Tips:**
- Start with DuckDuckGo or Brave (no proxy needed)
- Use caching to reduce requests
- Add delays between searches to avoid rate limiting

## Contributing

Contributions are very welcome! Please read the [Contributing Guide](CONTRIBUTING.md) for details on how to submit pull requests, report issues, and contribute to the project.

## Security

For security issues, please read the [Security Policy](SECURITY.md).

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for a list of changes.

## Acknowledgements

- Thanks to all contributors
- Inspired by the need for a unified search interface
- Built with Python and love
