# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **single-file static web application** for planning student internship and graduation project visits. The entire application is contained in [index.html](index.html) - there is no build process, no dependencies to install, and no server backend required.

**Purpose**: Plan and coordinate physical/online visits to 13 students (6 graduation projects + 7 internships) across multiple locations in the Netherlands, optimizing travel routes from Ravenstein.

**Tech Stack**:
- Vanilla JavaScript (no framework)
- Tailwind CSS (via CDN)
- Leaflet.js v1.9.4 for interactive maps (via CDN)
- OpenStreetMap tiles for base mapping

## Development Workflow

### Running the Application
```bash
# Option 1: Open directly in browser
open index.html  # macOS
xdg-open index.html  # Linux
start index.html  # Windows

# Option 2: Serve via local web server (recommended for development)
python -m http.server 8000
# Then visit http://localhost:8000

# Option 3: Use any static file server
npx http-server
```

### Making Changes
1. Edit [index.html](index.html) directly
2. Refresh browser to see changes immediately
3. No compilation, bundling, or build step required

### Git Workflow
- Commit messages are descriptive of what changed
- Recent commits show pattern: content updates (student data, dates, addresses)
- Main branch is `main`

## Architecture

### Single-File Application Pattern
All code exists in one HTML file with embedded:
- **HTML Structure** (lines 1-111): Layout using Tailwind utility classes
- **JavaScript Logic** (lines 113-521): Data, rendering, and interactivity
- **CSS** (lines 13-25): Minimal custom styles (only for map and card hover effects)

### Data Model
Student data is hardcoded in the `studenten` array (lines 117-264). Each student object contains:

```javascript
{
  naam: "Student Name",
  plaats: "City Name",
  type: "afstuderen" | "praktijk",  // Graduation or internship
  voorkeur: "fysiek" | "online" | "op locatie" | "geen voorkeur",
  coords: [latitude, longitude],
  status: "match" | "bespreken" | "conflict" | "praktijk",
  datum: "Day XX Month HH:MM-HH:MM",
  opmerking: "Additional notes",
  online: true | false
}
```

**Home Location**: Ravenstein at [51.7953, 5.6442] (line 302)

### Key Functions

**Map Rendering**:
- `applyJitter(studenten)` (lines 267-298): Prevents marker overlap by adding small coordinate offsets when multiple students share the same location
- Leaflet map initialization (lines 302-360): Creates interactive map with custom markers, calculates distances and travel times
- Distance calculation: Approximate straight-line distance using Pythagorean theorem (lines 328-334)
  - Latitude factor: 111 km per degree
  - Longitude factor: 85 km per degree
  - Travel time estimate: distance / 70 km/h

**Student List Rendering**:
- `renderStudents()` (lines 448-502): Generates student cards filtered by current selection
- `filterStudents(type)` (lines 504-515): Applies filter and updates UI button states

**Schedule Planning**:
- `suggestions` array (lines 362-426): Pre-planned optimal 5-day schedule with:
  - Geographic clustering (e.g., Lieshout → Uden → Boxmeer)
  - Travel time calculations between locations
  - Visit duration tracking (typically 1h15 per student, 2h for paired visits)
  - Color-coded by day

### UI Components

**Filter System** (lines 84-106):
- "Alle (13)" - Shows all students
- "Afstuderen (6)" - Shows only graduation projects
- "Praktijk (7)" - Shows only internships

**Status Color Coding**:
- Blue (#3b82f6): Matched visits (graduation students with confirmed dates)
- Yellow (#f59e0b): To be discussed
- Red (#ef4444): Date conflicts
- Purple (#9333ea): Internship stages

**Map Markers**:
- Green home marker (🏠): Starting location (Ravenstein)
- Graduation (🎓): Blue circles for matched visits
- Internship (📚): Purple circles
- Custom emoji-based markers with border styling

## Common Modifications

### Updating Student Data
Edit the `studenten` array (lines 117-264):
- Coordinates can be found via Google Maps (right-click → "What's here?")
- Date format: "Day DD month HH:MM-HH:MM" (e.g., "Ma 27 okt 09:00-10:15")
- Status affects map marker color

### Updating Schedule
Edit the `suggestions` array (lines 362-426):
- Each day object contains: dag, studenten, kleur, beschrijving, tijd
- Gradient colors follow pattern: `bg-gradient-to-r from-{color}-600 to-{color}-600`
- Emoji conventions: 🎓 (graduation), 📚 (internship), 🚗 (travel), 💻 (online), ⏰ (break)

### Adjusting Map View
- **Center point**: Line 302 - `setView([lat, lng], zoom)`
- **Zoom level**: Line 302 - Default is 8 (province-level view)
- **Auto-fit bounds**: Lines 357-360 - Automatically adjusts to show all markers

### Modifying Colors
Status colors defined in two places:
- **Map markers**: Lines 320-326 (`colors` object)
- **Student cards**: Lines 456-461 (`statusColors` object)

## Language and Conventions

- **Primary language**: Dutch (Nederlands)
- **Common terms**:
  - "Plaats" = Location/City
  - "Voorkeur" = Preference
  - "Opmerking" = Remark/Note
  - "Reistijd" = Travel time
  - "Dag" = Day
  - "Afstuderen" = Graduation
  - "Praktijk" = Internship/Practice

## Deployment

No build step required - simply deploy [index.html](index.html) to any static hosting:
- GitHub Pages
- Netlify (drag and drop)
- Vercel
- Any web server (Apache, Nginx, etc.)

The application is completely standalone and will work offline once loaded (except for CDN dependencies and map tiles).
