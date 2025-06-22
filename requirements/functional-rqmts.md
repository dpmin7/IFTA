This document contains the functional requirements we elicited for the Intelligent Flight Tracking Assistant, based on the SolveIt's project documents provided to all teams.

You will find the _actors_ we identified, and how they interact with the system in major scenarios.

# ***Actor***
- Student
- ADS-B Hub system
- Google BigQuery system

<br>

# ***Student***

## Flight Tracking 
- Real-time Reception of Data from Raspberry PI on the RUI 
- Connecting to ADSBHub.org for Global Track Reception 
- Storing Raw/SBS Data in local files or BigQuery 
- Playback of stored data in local files or BigQuery
- Map-based Visualization of Aircraft Positions with real-time Updated Map Tile
- Display aircraft details on click 
- Computing the Distance and Time to the Closest Point of Approach (CPA) Between Aircraft 
- Polygon Area Selection on Map and Track Filtering 
- Switching Between Google/VFR/IFR Map Modes 
- GUI interactions such as buttons, sliders and dropdowns 
- Real-time Display of Message Count, Position, Time, and Track Count 
- Detection and Recovery in case of WI-FI or SDR Failure 
- Retrieving Flight and Aircraft Information via API 
- Flight history and Dead Reckoning simulation 
- Improved functionality/style and expanded 
- Compare contrast a flight’s SBS or ADS-B with planned route

<br>


### Notes
<p><sup>1</sup>RUI is a native Windows application.</p>
