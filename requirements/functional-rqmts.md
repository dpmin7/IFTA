This document contains the functional requirements we elicited for the Intelligent Flight Tracking Assistant, based on the SolveIt's project documents provided to all teams.

You will find the _actors_ we identified, and how they interact with the system in major scenarios.

# ***Actor***
- Air traffic control student
- SDR system
- ADS-B Hub/Local system
- Google BigQuery system

<br>

# ***Air traffic control student on the RUI*** <sup>1</sup>

## Aircraft Tracking
- Display aircraft received from ADS-B server or SDR system
    - Real-time display of message count, position, time, and track count 
- GUI interactions such as buttons, sliders and dropdowns 
- Display aircraft details on click 
- Retrieving flight and aircraft information via API 
- Flight history and dead reckoning simulation 
- Compare and contrast the planned route with the actual location of the aircraft
- Detection and recovery in case of network failure 

## Area management to monitor
- Polygon area selection on map and track filtering 

## Collision risk detection
- Computing the distance and time to the Closest Point of Approach (CPA) between aircraft 

## Record and Playback
- Storing Raw/SBS data in local files or BigQuery 
- Playback of stored data in local files or BigQuery

## Select Map
- Switching between Google/VFR/IFR Map Modes 
- Map-based visualization of aircraft location with real-time updated map tile
- Select various map providers

<br>

# ***SDR system*** <sup>2</sup>
- Our system:
    - Real-time reception of aircraft raw data from SDR 
    - Provide raw data to client programs
    - Detection and Recovery SDR failure 

<br>

# ***ADS-B Hub/Local system***
- Provide aircraft SBS data to client programs

<br>

# ***Google BigQuery system***
- Save large amount of aircraft data in CSV format
- Provide CSV file to client programs

<br>

### Notes
<p><sup>1</sup>RUI is a native windows GUI application of Intelligent Flight Tracking Assistant system.</p>
<p><sup>2</sup>SDR (Software Defined Radio) system (a.k.a Flight Tracker) is a linux server running on Raspberry Pi which gathers aircraft raw data and provides them.</p>
