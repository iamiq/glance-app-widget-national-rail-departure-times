# National Rail Departures in Glance (Any UK Station)

This repository explains how to add **live National Rail departures for any UK station** to a **Glance** dashboard using **TransportAPI**, with deliberate safeguards to stay within the free API quota.

The setup avoids heavyweight rail data platforms and is suitable for personal dashboards and home servers.


## What this does

- Displays **live National Rail departures** for a chosen station
- Works with **any UK station** (via CRS station codes)
- Uses Glance’s `custom-api` widget
- Limits API usage using caching to respect free-tier limits


## Why TransportAPI (and not other options)

### TfL API
- Limited to TfL-operated services only
- Does **not** provide general National Rail departures

### National Rail Darwin (Push / OpenLDBWS)
- Push feed requires Kafka or similar infrastructure
- OpenLDBWS registration is often unavailable
- Excessively complex for a single dashboard widget

### TransportAPI
- Simple REST-based JSON API
- Uses Network Rail data for live services
- No background services or message queues required
- Integrates cleanly with Glance

For personal or household dashboards, TransportAPI is the most practical choice.


## Limitations (important)

TransportAPI **free tier** limits:

- **30 requests per day**
- Hard API failures (403) if exceeded
- No burst allowance

This configuration is intentionally designed around those constraints.


## Rate-limiting strategy used here

Glance has no built-in scheduling or time windows.  
Instead, this setup relies on **long caching**.

```yaml
cache: 60m
