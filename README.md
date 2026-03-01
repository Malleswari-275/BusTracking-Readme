# College Bus GPS Tracking — Requirements (Web First, App Later)

## Project Overview

The final product will include:
- A **web portal for management**
- A **mobile app for students**

For the current phase, we are building a **single web application** with **role-based logins**:
- **Students** see the student UI
- **Management (Super Admin / Supervisor)** see the admin UI

Later, the **student module** will be migrated into a mobile app with the same features.

---

## Admin Module (Management Portal)

### Roles and Access

#### 1) Super Admin
The Super Admin has full control across the college, including:
- Adding and managing **Supervisors**
- Adding and managing **Buses**
- Adding and managing **Students**
- Viewing **overall analytics**, such as:
  - Region-wise bus logs
  - Region-wise delays
  - College-wide bus statistics

#### 2) Supervisor
Supervisors can access and manage data **only for their assigned region**, including:
- Region-wise bus logs and statistics
- Updating bus status (active/inactive)
- Handling bus and driver changes
- Sending notices and updates to students

---

## Admin Dashboard (Key Features)

### 1) Dashboard Highlights
The dashboard should show quick insights such as:
- **Buses delayed today** (displayed as cards/widgets)
- **Daily bus logs summary**

### 2) Bus Logs (Search and View)
A Bus Logs section should provide:
- A **search bar** to search by **bus number**
- A detailed log view for a selected bus, including (as needed):
  - Date
  - Scheduled arrival time vs actual arrival time
  - Arrival status: **On time / Delayed**
  - If delayed: optional **reason**
  - Driver name and driver phone number (optional)

### 3) Bus Active / Inactive Control
When a bus is unavailable, it should be marked **inactive** so that:
- It is **not shown in the student portal**
- Students who have it as a default bus can be informed clearly

This action should primarily be handled by the **Supervisor**.

---

## Special Operational Scenarios (Supervisor Actions)

### 1) Reduced Buses on Every 3rd Saturday
On every third Saturday, buses are reduced and the college shares a notice (usually via WhatsApp) containing:
- Bus route
- Bus number
- Driver name and phone number
- Bus arrival time

The Supervisor should be able to **publish this notice directly to the student portal**, via:
- **Notices** and/or **Notifications**

### 2) Bus Change for a Student (Temporary or Permanent)
Sometimes a student’s bus may change. In that case, the Supervisor should be able to:
- Notify students who were using the previous bus
- Inform them of the **new bus number** (and any supporting details)

### 3) Driver Change
Supervisors (and Super Admins) should be able to update:
- Driver name
- Driver contact number
- Bus-to-driver assignment for a given day/event

---

## Summary of Admin Features

### Supervisor Login
- Bus change options (special events, 3rd Saturdays)
- Driver change for a bus
- Area/region-wise buses:
  - Bus logs
  - Bus statistics
- Update bus status:
  - Active / Inactive

### Super Admin Login
- Dashboard:
  - Today’s delays
  - Bus statistics and reports
  - Bus logs (search by bus number and view details)
- Full CRUD operations:
  - Students
  - Buses
  - Supervisors
- Bus change options (special events, 3rd Saturdays)
- Driver change for a bus

---

## Student Module (Student Portal)

### Login and First-Time Setup

#### 1) Credentials
Students will log in using:
- College roll number or college email
- A system-generated random password

#### 2) Mandatory Password Change
On the first login:
- Students must **change their password**
- Until the password is changed, the system should **block access** to the rest of the portal/app

### Default Bus Configuration

After changing the password, students should set a **default bus number**:
- Provide a search/dropdown list of all active buses
- The selected bus becomes the student’s default bus

On future logins:
- The student should directly see their **default bus tracking screen**, without selecting again.

---

## Student Map and Tracking Features

### 1) Main Map View
The map should display:
- The bus’s live location
- The route/path towards the **college (destination)**

Along with the map, show:
- Driver name
- Driver phone number (for that day)

If the student’s default bus is **inactive**, the UI should clearly indicate it (e.g., red badge/label or animation).

### 2) Student Stop Selection and ETA
The student should be able to set their stop:
- Provide a button below the map
- On click, allow the student to tap:
  - On the route line (blue path), or
  - Anywhere on the map
- Save the selected spot as the student’s stop

After selecting the stop, show:
- Distance from bus to the stop
- Estimated time for the bus to reach the stop (ETA)

---

## Student Notifications and Alerts

Students should receive notifications for:

### 1) Bus Start Notification
When the bus starts in the morning:
- Notify: **“Your bus has started.”**

### 2) Approaching Stop (Under 5 Minutes)
If ETA to the student’s stop becomes less than 5 minutes:
- Notify: **“Bus arriving in 5 minutes.”** (or similar)

### 3) Bus Reached Stop
When the bus reaches the student’s selected stop:
- Notify: **“Bus has reached your stop.”**

### 4) Bus Crossed the Stop
If the bus has already crossed the stop:
- Notify: **“Bus has already passed your stop.”**

This can be detected by monitoring ETA:
- If ETA starts **increasing** after decreasing, the bus may have crossed the stop.

### 5) Bus Change Notification
Whenever a student’s bus is changed by management:
- Notify the student with the new bus details.

---

## Student Portal Pages

- **Profile**
  - Change password
  - Theme toggle
  - Default bus number
  - Default stop
  - Logout
- **Map**
  - Live bus tracking
  - Stop selection
  - ETA/distance
- **Notifications**
  - Alerts and updates from management/system
- **Home**
  - (To be finalized later)

---

## Rule-Based Chatbot (Decision Support Bot)

A simple rule-based chatbot will help students quickly get information without free-text input.

### Predefined Options (No Free Search)
Students will choose from fixed prompts such as:
- Where is my bus?
- Can I catch any bus near me?
- Is my bus delayed?
- Bus change today?
- Missed my bus — what now?

---

## Example Flow: “Can I catch any bus near me?”

### Step 1: Student Selects an Option
Example:
- “Can I catch any bus near me?”

### Step 2: Chatbot Sends Request to Backend
```json
{
  "studentId": "ST123",
  "lat": 16.9932,
  "lng": 82.2504
}
```

### Step 3: Backend Logic
Backend should:
- Fetch all active buses
- Compute distance between the student and nearby buses
- Check direction (approaching vs already passed)
- Filter buses within a walkable radius

### Step 4: Backend Response
```json
{
  "availableBuses": [
    {
      "busNumber": "27",
      "distance": "120m",
      "eta": "3 mins"
    },
    {
      "busNumber": "12",
      "distance": "180m",
      "eta": "5 mins"
    }
  ]
}
```

### Step 5: Chatbot Message to Student
Example response:
- You can catch these buses nearby:
  - Bus 27 — 120m away (ETA: 3 mins)
  - Bus 12 — 180m away (ETA: 5 mins)
- Please move towards the road.

---

## Context Awareness (Without AI Complexity)

To make the chatbot feel intelligent while staying rule-based, it should use:
- The student’s **default bus**
- The student’s **current location**
- Current bus status (active/inactive, delayed/on-time)
