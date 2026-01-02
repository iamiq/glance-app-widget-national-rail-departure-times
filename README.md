# National Rail Departures in Glance (Any UK Station)

This repository explains how to add **live National Rail departures for any UK station** to a **Glance** dashboard using **TransportAPI**, with deliberate safeguards to stay within the free API quota.


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

For personal dashboards, TransportAPI is the most practical option.


## Limitations (important)

TransportAPI free tier limits:
- **30 requests per day**
- Hard failures (403) if exceeded

Glance has no built-in scheduling or time windows. This setup relies on caching to keep calls low.

Recommended:
- `cache: 60m` (about 15 calls/day, safely below quota)

## Step 1. Register for TransportAPI

1. Create an account at TransportAPI.
2. Create an application in the dashboard.
3. Copy your credentials:
   - `app_id`
   - `app_key`

## Step 2. Find your station CRS code

UK National Rail stations use **CRS codes** (3 letters).

How to find one:
1. Go to CRS lookup sites (for example, crs.codes).
2. Search your station name.
3. Copy the CRS code.

Examples:
- Deptford → `DEP`
- London Cannon Street → `CST`
- Brighton → `BTN`

## Step 3. Add credentials to Glance

Add these environment variables to the Glance container (Komodo stack env vars or `.env` file):

```env
TRANSPORT_API_ID=your_app_id_here
TRANSPORT_API_KEY=your_app_key_here
