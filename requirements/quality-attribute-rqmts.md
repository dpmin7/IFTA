# Quality attribute requirements of the Intelligent Flight Tracking Assistant

A proper elicitation of quality attribute requirements (aka architecture characteristics, non-functional requirements) 
requires a conversation with various system stakeholders. That conversation would allow: *(i)* specifying these requirements 
in a testable/measurable format; and *(ii)* prioritizing the requirements. 

In this document, we listed a few requirements that we interviewed with SolveIt and *assumed* would be important for the system.  

## *Performance - response time* 
- When a user selects(hook) an aircraft on the Remote User Interface (RUI) during SBS connect mode, the system must display the result to the user within 0.1 seconds1 even aircrafts increases to up to 5000.

## *Availability - resilience* 
- Once the Raspberry Pi flight tracker or ADSB-hub returns to a normal state from failure, the RUI must resume service continuity within a minute without requiring any manual intervention.

## *Modifiability - map provider substitution* 
- When a developer adds or replaces other map providers, the system must integrate the changes within 3 days, and the program must continue to operate correctly.

## *Usability - visible feedback*
- When a failure occurs in the Raspberry pi flight tracker or Adsb-Hub connection, the status color is changed to yellow from green within 10 seconds.
