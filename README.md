# National Rail Departures in Glance (Any UK Station)

I wanted to show National Rail departures from my local train station and feed it to my [Glance](https://github.com/glanceapp) Dashboard. However, I could not find any widget made for this. 
So, i made one and here i explain how to add **live National Rail departures for any UK station** to a [Glance](https://github.com/glanceapp) dashboard using [TransportAPI](https://www.transportapi.com), with safeguards to stay within the free API quota (default daily api calls are limited to 30)

<img width="671" height="431" alt="image" src="https://github.com/user-attachments/assets/aa17d642-9e74-4491-8250-9eba29f18838" />

<img width="676" height="433" alt="image" src="https://github.com/user-attachments/assets/abc2a3d7-4c29-44b2-bcde-d99e44795332" />


## What this does

- Displays **live National Rail departures** for a chosen station
- Works with **any UK station** (via CRS station codes)
- Uses Glance’s `custom-api` widget
- Limits API usage using caching to respect TransportAPI's free-tier limit of 30 calls a day


## Why TransportAPI (and not other options)

### TfL API
- Limited to TfL-operated services only
- Does not provide general National Rail departures

### National Rail Darwin (Push / OpenLDBWS)
- Push feed requires Kafka or similar infrastructure
- OpenLDBWS registration is often unavailable, at least I tried for 1 month and failed

If you manage to get access to [OpenLDBWS](https://realtime.nationalrail.co.uk/OpenLDBWSRegistration) then you don't need to worry about `cache: 60m` that is mentioned later. I am still not lucky enough to get in.

<img width="801" height="342" alt="image" src="https://github.com/user-attachments/assets/5ac5605d-2690-4e8c-8292-48dda2d041ff" />

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

1. Create an account at [TransportAPI](https://www.transportapi.com).
2. Create an application in the dashboard.
3. Copy your credentials:
   - `app_id`
   - `app_key`

## Step 2. Find your station CRS code

UK National Rail stations use **CRS codes** (3 letters).

How to find one:
1. Go to CRS lookup sites (for example, [crs.codes](https://crs.codes)).
2. Search your station name.
3. Copy the CRS code.

Examples:
- London Cannon Street → `CST`
- Brighton → `BTN`
- Inverness → `INV`

## Step 3. Add credentials to Glance

Add these environment variables to the Glance container:

```env
TRANSPORT_API_ID=your_app_id_here
TRANSPORT_API_KEY=your_app_key_here
```

## Step 4. Update your glance.yaml
