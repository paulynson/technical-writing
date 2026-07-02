# Performance Analysis: Product Dashboard Optimisation

## Overview

The Product Dashboard has experienced noticeable performance degradation as the product catalogue has grown. Users reported slow page loading, delayed image rendering, and poor responsiveness when searching and sorting products.

## Findings

### 1. Initial Data Loading

The dashboard currently fetches all available products during the initial page load. At the time of analysis, this resulted in approximately 500 products being requested in a single API call.

Impact

* Large API payloads increase page load time.
* Rendering hundreds of product cards delays the application's first meaningful interaction.
* Memory usage increases as the browser stores and renders unnecessary data.

Recommendation
Implement server side pagination and request only the data required for the current page.

Example:


GET /products?page=1&limit=20


### 2. Search Behaviour

The search feature triggers an API request on every keystroke.

#Current Behaviour


"P"      → Request
"Pi"     → Request
"Piz"    → Request
"Pizza"  → Request


This results in unnecessary network traffic and may produce race conditions where earlier responses overwrite more recent ones.

# Recommendation

* Implement input debouncing (300 to 500 ms).
* Cancel previous requests when a new search begins.
* Handle searching on the server rather than filtering large datasets on the client.



### 3. Sorting

Sorting is currently performed on the client after loading the full dataset.

# Impact

* Increased browser processing.
* Poor scalability as the catalogue grows.
* Unnecessary memory consumption.

# Recommendation
Move sorting to the backend and expose it through query parameters.

Example:


GET /products?sort=price&page=1&limit=20


### 4. Image Loading

Product images contribute significantly to perceived page load time.

# Recommendation

* Enable lazy loading for images outside the viewport.
* Serve responsive image sizes.
* Use compressed formats where supported.
* Display skeleton placeholders while images load.



### 5. Component Structure

The dashboard is implemented as a single large React class component responsible for rendering products, search, sorting, analytics, and pagination.

# Impact

* Difficult to maintain.
* Increased likelihood of unnecessary re-renders.
* Higher risk of merge conflicts when multiple developers modify the same component.

# Recommendation
Refactor the dashboard into smaller feature-based components while preserving existing functionality.

Proposed structure:


ProductDashboard
├── SearchBar
├── ProductTable
├── ProductRow
├── ProductImage
├── Pagination
└── AnalyticsPanel


## Expected Outcome

Implementing these recommendations is expected to:

* Reduce initial page load time.
* Decrease API payload size.
* Improve rendering performance.
* Reduce unnecessary network requests.
* Improve maintainability and support future feature development without requiring a full application rewrite.
