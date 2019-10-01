---
title: Beacon API
---

HSL Beacon API can be used to fetch locations of [Bluetooth beacons](https://en.wikipedia.org/wiki/Bluetooth_low_energy_beacon) that have been installed to vehicles and stops in HSL area.

The API is a simple REST interface that returns data in JSON format.

## Endpoint

```
https://api.lij.hsl.fi/beacon-info-api/v1.0/beacons/active
```

### Usage

Data returned by the API should be cached and subsequent queries to the API should use HTTP header `If-Modified-Since` as beacon data is rarely updated.

### Response

The API returns an JSON array that contains beacon data.  
Each beacon has following fields:

| Field            | Type      | Description |
|------------------|-----------|-------------|
| `serial`         | String    | Serial number of the beacon. Unique for each beacon.
| `uuid`           | String    | UUID of the beacon. All HSL beacons use shared UUID.<br/>Note that `uuid` does not contain dashes.
| `major`          | String    | Major value identifying the beacon. Major values are shared between multiple beacons. See descriptions for major values [below](#major-values).
| `minor`          | String    | Minor value identifying the beacon. Minor value can be shared between multiple beacons with different major values.
| `location`       | Integer   | Location type of the beacon. Possible values: <ul><li>`1` = vehicle</li><li>`2` = stop</li></ul>
| `status`         | Integer   | Status of the beacon. Status of the beacon is always `2` (active) as the public API returns only active beacons.
| `stop`           | Integer   | ID of the stop where the beacon is installed. Corresponds to `stop_id` in GTFS and `Stop.gtfsId` in [Digitransit API](https://digitransit.fi/en/developers/apis/1-routing-api/stops/).<br/>`null` if location type is `1`.
| `operator`       | String    | Operator ID of the vehicle where the beacon is installed. Corresponds to `operator_id` in [HFP API](https://digitransit.fi/en/developers/apis/4-realtime-api/vehicle-positions/).<br/>`null` if location type is `2`.
| `vehicle_number` | String    | Vehicle number of the vehicle where the beacon is installed. Corresponds to `vehicle_number` in HFP API.<br/>`null` if location type is `2`.
| `responsible`    | String    | ID of the organization responsible for the beacon.
| `modified_on`    | Integer   | Timestamp (in milliseconds) when the beacon data was modified.

#### Major values

Major value of a beacon depends on where it is installed.  

List of major values:

| Major  | Description    |
|--------|----------------|
| `0001` | Stop
| `0002` | Tram
| `0003` | Metro
| `0004` | Train
| `0005` | Train station platform
| `0007` | Ferry
| `0010` | Bus
| `0011` | Bus
| `0012` | Bus
| `0013` | Bus
| `0014` | Bus
| `0015` | Bus
| `0020` | Test beacon

### Identifying beacons

Beacons are identified by combination of their `uuid`, `major` and `minor` properties.  

Libraries such as [Core Location (iOS)](https://developer.apple.com/documentation/corelocation/) and [Android Beacon Library](https://altbeacon.github.io/android-beacon-library/) can be used to detect nearby beacons, which can then be matched to a corresponding beacon from the API with their `uuid`, `major` and `minor` values. 