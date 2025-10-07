# Rail-Saathi API Documentation

## Table of Contents
- [Overview](#overview)
- [API Endpoints](#api-endpoints)
- [Authentication](#authentication)
- [Train Search API](#train-search-api)
- [Seat Availability API](#seat-availability-api)
- [Response Structure](#response-structure)
- [Field Descriptions](#field-descriptions)
- [Error Handling](#error-handling)
- [Code Examples](#code-examples)

---

## Overview

Rail-Saathi integrates with the **IRCTC RapidAPI** to provide real-time train search and seat availability information. This document describes the API endpoints, request/response structures, and implementation details.

**Base URL**: `https://irctc1.p.rapidapi.com`

**API Provider**: RapidAPI - IRCTC API

---

## API Endpoints

### 1. Train Search Between Stations
**Endpoint**: `/api/v3/trainBetweenStations`

**Method**: `GET`

**Description**: Fetches all available trains running between two stations on a specific date.

### 2. Seat Availability Check
**Endpoint**: `/api/v1/checkSeatAvailability`

**Method**: `GET`

**Description**: Checks seat availability for a specific train, class, and date.

---

## Authentication

All API requests require RapidAPI authentication headers:

```python
headers = {
    "x-rapidapi-key": "YOUR_RAPIDAPI_KEY",
    "x-rapidapi-host": "irctc1.p.rapidapi.com"
}
```

**Environment Variable**: Store your API key in `.env` file:
```
RAPIDAPI_KEY=your_actual_api_key_here
```

---

## Train Search API

### Request Parameters

| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `fromStationCode` | string | Yes | Origin station code | `LKO` (Lucknow) |
| `toStationCode` | string | Yes | Destination station code | `CNB` (Kanpur Central) |
| `dateOfJourney` | string | Yes | Date of travel (YYYY-MM-DD) | `2024-12-22` |

### Example Request

```python
url = "https://irctc1.p.rapidapi.com/api/v3/trainBetweenStations"
querystring = {
    "fromStationCode": "LKO",
    "toStationCode": "CNB",
    "dateOfJourney": "2024-12-22"
}

response = requests.get(url, headers=headers, params=querystring)
```

### Response Structure

```json
{
    "status": true,
    "message": "Success",
    "timestamp": "2024-12-22T10:30:00Z",
    "data": [
        {
            "train_number": "12345",
            "train_name": "Lucknow Mail",
            "from_station_name": "Lucknow Junction",
            "to_station_name": "Kanpur Central",
            "from_std": "06:00",
            "to_sta": "08:30",
            "duration": "02:30",
            "run_days": ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"],
            "class_type": ["SL", "3A", "2A", "1A"],
            "distance": "75",
            ...
        }
    ]
}
```

---

## Seat Availability API

### Request Parameters

| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `classType` | string | Yes | Class of travel | `CC`, `SL`, `3A`, `2A`, `1A` |
| `fromStationCode` | string | Yes | Origin station code | `LKO` |
| `toStationCode` | string | Yes | Destination station code | `CNB` |
| `trainNo` | string | Yes | Train number | `12345` |
| `date` | string | Yes | Date (DD-MM-YYYY) | `22-12-2024` |
| `quota` | string | Yes | Quota type | `GN` (General) |

### Class Types

| Code | Description |
|------|-------------|
| `SL` | Sleeper Class |
| `3A` | AC 3 Tier |
| `2A` | AC 2 Tier |
| `1A` | AC First Class |
| `CC` | Chair Car |
| `EC` | Executive Chair Car |
| `2S` | Second Sitting |

### Example Request

```python
url = "https://irctc1.p.rapidapi.com/api/v1/checkSeatAvailability"
querystring = {
    "classType": "CC",
    "fromStationCode": "LKO",
    "quota": "GN",
    "toStationCode": "CNB",
    "trainNo": "12345",
    "date": "22-12-2024"
}

response = requests.get(url, headers=headers, params=querystring)
```

### Response Structure

```json
{
    "status": true,
    "message": "Success",
    "timestamp": "2024-12-22T10:30:00Z",
    "data": [
        {
            "date": "22-12-2024",
            "current_status": "AVAILABLE-042",
            "confirm_probability": "High"
        }
    ]
}
```

---

## Field Descriptions

### Train Data Fields

| Field Name | Type | Description |
|------------|------|-------------|
| `train_number` | string | Unique train identification number |
| `train_name` | string | Official name of the train |
| `run_days` | array | Days of the week train operates (Mon, Tue, etc.) |
| `from_std` | string | Scheduled departure time from origin (HH:MM) |
| `from_sta` | string | Scheduled arrival time at origin (usually empty) |
| `to_sta` | string | Scheduled arrival time at destination (HH:MM) |
| `to_std` | string | Scheduled departure time from destination |
| `from` | string | Origin station code |
| `to` | string | Destination station code |
| `from_station_name` | string | Full name of origin station |
| `to_station_name` | string | Full name of destination station |
| `distance` | string | Distance between stations in kilometers |
| `duration` | string | Total journey time (HH:MM format) |
| `halt_stn` | integer | Number of intermediate stops |
| `class_type` | array | Available classes (e.g., ["SL", "3A", "2A"]) |
| `train_type` | string | Train category code (e.g., "VBEX" for Vande Bharat) |
| `special_train` | boolean | Whether it's a special/temporary train |
| `has_pantry` | boolean | Indicates if train has pantry car |
| `is_monsoon_timing_applicable` | boolean | Special monsoon schedule applies |

### Scoring Fields

| Field Name | Type | Description |
|------------|------|-------------|
| `score` | number | Overall train score based on multiple factors |
| `score_train_type` | number | Score based on train type/category |
| `score_booking_frequency` | number | Score based on booking popularity |
| `score_duration` | number | Score based on journey time |
| `score_std` | number | Score based on departure/arrival times |
| `score_user_preferred` | number | Score based on user preferences |
| `duration_rating` | number | Rating of journey duration |
| `frequency_perc` | number | Percentage of days train operates |

### Distance & Location Fields

| Field Name | Type | Description |
|------------|------|-------------|
| `local_train_from_sta` | string | Distance from primary station if departing from satellite station |
| `from_distance_text` | string | Human-readable distance from origin (e.g., "0 Kms from LKO") |
| `to_distance_text` | string | Human-readable distance to destination |

### Date & Time Fields

| Field Name | Type | Description |
|------------|------|-------------|
| `train_date` | string | Date of journey (YYYY-MM-DD format) |
| `timestamp` | string | API response generation time (ISO 8601) |

---

## Error Handling

### Common HTTP Status Codes

| Status Code | Description |
|-------------|-------------|
| `200` | Success - Request completed successfully |
| `400` | Bad Request - Invalid parameters |
| `401` | Unauthorized - Invalid API key |
| `403` | Forbidden - API quota exceeded |
| `404` | Not Found - Endpoint doesn't exist |
| `429` | Too Many Requests - Rate limit exceeded |
| `500` | Internal Server Error - API service issue |

### Example Error Response

```json
{
    "status": false,
    "message": "Invalid station code",
    "timestamp": "2024-12-22T10:30:00Z"
}
```

### Implementation in Code

```python
try:
    response = requests.get(url, headers=headers, params=querystring)
    response.raise_for_status()  # Raises HTTPError for bad responses
    return response.json()
except requests.exceptions.HTTPError as e:
    logging.error(f"HTTP Error: {e}")
    return None
except requests.exceptions.RequestException as e:
    logging.error(f"Request Error: {e}")
    return None
except Exception as e:
    logging.error(f"Unexpected Error: {e}")
    return None
```

---

## Code Examples

### Complete Train Search Implementation

```python
import os
import requests
import logging
from dotenv import load_dotenv

# Load environment variables
load_dotenv()

def fetch_live_station_data(from_station_code, to_station_code, date):
    """
    Fetch live station data from IRCTC RapidAPI.
    
    Args:
        from_station_code (str): Origin station code (e.g., 'LKO')
        to_station_code (str): Destination station code (e.g., 'CNB')
        date (str): Date of journey in YYYY-MM-DD format
        
    Returns:
        dict: API response containing train data
        None: If request fails
    """
    url = "https://irctc1.p.rapidapi.com/api/v3/trainBetweenStations"
    
    querystring = {
        "fromStationCode": from_station_code,
        "toStationCode": to_station_code,
        "dateOfJourney": date
    }
    
    headers = {
        "x-rapidapi-key": os.getenv("RAPIDAPI_KEY"),
        "x-rapidapi-host": "irctc1.p.rapidapi.com"
    }
    
    try:
        response = requests.get(url, headers=headers, params=querystring)
        response.raise_for_status()
        return response.json()
    except requests.exceptions.HTTPError as e:
        logging.error(f"HTTP Error: {e}")
        return None
    except Exception as e:
        logging.error(f"Unexpected Error: {e}")
        return None


def fetch_live_seat_availability_data(class_type, from_station_code, quota, 
                                     to_station_code, train_number, date):
    """
    Fetch seat availability data from IRCTC RapidAPI.
    
    Args:
        class_type (str): Travel class (CC, SL, 3A, 2A, 1A, etc.)
        from_station_code (str): Origin station code
        quota (str): Quota type (GN for General)
        to_station_code (str): Destination station code
        train_number (str): Train number
        date (str): Date in DD-MM-YYYY format
        
    Returns:
        dict: API response containing seat availability
        None: If request fails
    """
    url = "https://irctc1.p.rapidapi.com/api/v1/checkSeatAvailability"
    
    querystring = {
        "classType": class_type,
        "fromStationCode": from_station_code,
        "quota": quota,
        "toStationCode": to_station_code,
        "trainNo": train_number,
        "date": date
    }
    
    headers = {
        "x-rapidapi-key": os.getenv("RAPIDAPI_KEY"),
        "x-rapidapi-host": "irctc1.p.rapidapi.com"
    }
    
    try:
        response = requests.get(url, headers=headers, params=querystring)
        response.raise_for_status()
        return response.json()
    except requests.exceptions.HTTPError as e:
        logging.error(f"HTTP Error: {e}")
        return None
    except Exception as e:
        logging.error(f"Unexpected Error: {e}")
        return None


# Usage Example
if __name__ == "__main__":
    # Search for trains
    train_data = fetch_live_station_data("LKO", "CNB", "2024-12-22")
    
    if train_data and train_data.get('status'):
        print(f"Found {len(train_data['data'])} trains")
        
        # Check seat availability for first train
        first_train = train_data['data'][0]
        seat_data = fetch_live_seat_availability_data(
            "CC", "LKO", "GN", "CNB", 
            first_train['train_number'], 
            "22-12-2024"
        )
        
        if seat_data and seat_data.get('status'):
            print(f"Seat Status: {seat_data['data'][0]['current_status']}")
```

### Flask Route Integration

```python
from flask import Blueprint, request, render_template, flash

search_bp = Blueprint('search', __name__)

@search_bp.route('/search', methods=['GET'])
def search():
    # Get query parameters
    from_station = request.args.get('from_station')
    to_station = request.args.get('to_station')
    date = request.args.get('date')
    travel_class = request.args.get('travel_class')
    
    # Validate parameters
    if not all([from_station, to_station, date, travel_class]):
        flash("Invalid search parameters", "danger")
        return render_template('error.html')
    
    # Fetch train data
    response = fetch_live_station_data(from_station, to_station, date)
    
    if not response or not response.get('status'):
        flash("Unable to fetch train data", "danger")
        return render_template('error.html')
    
    # Filter trains by class
    train_data = [
        train for train in response['data']
        if travel_class in train.get('class_type', [])
    ]
    
    # Render results
    return render_template('search.html', results=train_data)
```

---

## Rate Limits

- **RapidAPI Free Tier**: 500 requests/month
- **RapidAPI Pro Tier**: Varies based on subscription

**Recommendation**: Implement caching for frequently searched routes to reduce API calls.

---

## Testing

### Sample Test Data

**Common Station Codes:**
- Delhi: `NDLS` (New Delhi)
- Mumbai: `CSTM` (Mumbai CST)
- Bangalore: `SBC` (Bangalore City)
- Chennai: `MAS` (Chennai Central)
- Kolkata: `HWH` (Howrah)
- Lucknow: `LKO` (Lucknow)
- Kanpur: `CNB` (Kanpur Central)

### cURL Example

```bash
curl -X GET \
  'https://irctc1.p.rapidapi.com/api/v3/trainBetweenStations?fromStationCode=LKO&toStationCode=CNB&dateOfJourney=2024-12-22' \
  -H 'x-rapidapi-host: irctc1.p.rapidapi.com' \
  -H 'x-rapidapi-key: YOUR_API_KEY'
```

---

## Support & Resources

- **RapidAPI Documentation**: https://rapidapi.com/hub
- **IRCTC Official**: https://www.irctc.co.in
- **Station Code Finder**: https://indiarailinfo.com/

---

## Changelog

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2024-12-22 | Initial API documentation |

---

**Note**: This documentation is based on the IRCTC RapidAPI integration used in Rail-Saathi. API structure and fields may change based on the API provider's updates.
