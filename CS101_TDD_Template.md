# Technical Design Document: ASP Buddy

## 1. The Problem
* **Project Title:** ASP Buddy: Smart NYC Alternate Side Parking Assistant
* **Problem Statement:** Drivers in residential NYC neighborhoods frequently receive \$65 parking tickets or spend 30–45 minutes circling for spots because Alternate Side Parking (ASP) street-cleaning schedules differ by street side and are often suspended unexpectedly for holidays or severe weather.
* **Who It Affects:** NYC car owners and daily commuters, local neighborhood residents, and DSNY street-sweeper operators whose routes are obstructed by unmoved vehicles.

---

## 2. The Solution
* **One-Sentence Pitch:** A smart parking assistant that combines block-specific street-cleaning schedules with live NYC DOT suspension updates to alert drivers before enforcement begins or confirm when they can remain parked.
* **Inputs:**
  * Street name and side of street (`string`)
  * Scheduled cleaning day and current day of the week (`string`)
  * Scheduled cleaning start/end times and current time in 24-hour format (`int`)
  * Live NYC ASP suspension status (`bool`)
  * User confirmation that the vehicle has been moved (`bool`)
* **Outputs:**
  * Real-time parking status message (Safe to park, 30-minute move warning, active cleaning alert, or ASP suspension notice).
* **Key C++ Variables:**
  * `string cleaningDay;` (e.g., `"Tuesday"`)
  * `string currentDay;` (e.g., `"Tuesday"`)
  * `int currentTime;` (e.g., `835` for 8:35 AM)
  * `int cleaningStartTime;` (e.g., `900` for 9:00 AM)
  * `int cleaningEndTime;` (e.g., `1030` for 10:30 AM)
  * `bool isASPSuspended;`
  * `bool carMoved;`

---

## 3. The Core Logic (Pseudocode)

START PROGRAM
  INPUT cleaningDay, cleaningStartTime, cleaningEndTime
  INPUT currentDay, currentTime, isASPSuspended
  SET carMoved = false

  WHILE (carMoved == false) DO
    IF (isASPSuspended == true) THEN
      OUTPUT "ASP is suspended today! No need to move your car."
      SET carMoved = true
    ELSE IF (currentDay == cleaningDay) THEN
      IF (currentTime >= cleaningStartTime - 30 AND currentTime < cleaningStartTime) THEN
        OUTPUT "WARNING: Street cleaning starts in under 30 minutes. Move your car now!"
      ELSE IF (currentTime >= cleaningStartTime AND currentTime <= cleaningEndTime) THEN
        OUTPUT "ALERT: Street cleaning is active right now! Risk of \$65 ticket."
      ELSE
        OUTPUT "Safe to park right now. Cleaning is outside the current window."
      END IF
    ELSE
      OUTPUT "No street cleaning on this side of the street today."
      SET carMoved = true
    END IF

    IF (carMoved == false) THEN
      INPUT "Did you move your car? (true/false): " INTO carMoved
      INPUT "Enter updated time (e.g., 915 for 9:15 AM): " INTO currentTime
    END IF
  END WHILE

  OUTPUT "You're all set! Parking check complete."
END PROGRAM

---

## 4. The C++ Connection
* **Loop Control (`WHILE` $\rightarrow$ `while`):** The pseudocode `WHILE (carMoved == false)` translates directly into a C++ `while (!carMoved)` loop, which continues checking the parking status until the user moves their car or the rule is bypassed.
* **Boolean Conditionals (`IF` $\rightarrow$ `if`):** Checking `IF (isASPSuspended == true)` translates to `if (isASPSuspended)` using a `bool` variable to immediately exit the warning cycle on holidays or snow days.
* **Compound Time Checks (`AND` $\rightarrow$ `&&`):** The range check `currentTime >= cleaningStartTime - 30 AND currentTime < cleaningStartTime` maps to C++ relational and logical operators (`if (currentTime >= cleaningStartTime - 30 && currentTime < cleaningStartTime)`) using `int` values for 24-hour time.
* **Console I/O (`INPUT` / `OUTPUT` $\rightarrow$ `cin` / `cout`):** User prompts and status alerts map directly to standard `<iostream>` operations (`cout <<` for displaying warnings and `cin >>` for updating `carMoved` and `currentTime`).
