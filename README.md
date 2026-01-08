# Hubstaff API Support Assistant

A powerful internal tool designed to help support agents quickly answer customer questions about Hubstaff's API capabilities.

![Tool Preview](https://img.shields.io/badge/Status-Production%20Ready-green) ![Version](https://img.shields.io/badge/Version-2.0-blue)

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [How It Works](#how-it-works)
- [Using the Tool](#using-the-tool)
- [Understanding Results](#understanding-results)
- [Data Structure](#data-structure)
- [Adding New Capabilities](#adding-new-capabilities)
- [Maintenance](#maintenance)

---

## Overview

The **Hubstaff API Support Assistant** helps support agents instantly determine:

- ✅ **What CAN be done** via the API (and how)
- ❌ **What CAN'T be done** (and why)
- ⚠️ **What requires a workaround** (indirect approaches)
- 📋 **What plans/add-ons are required**

Instead of agents needing to memorize API documentation, they can simply ask natural questions like:
- "Can the customer create projects via the API?"
- "Is check-in/check-out supported?"
- "Can they sync timesheets with their ERP?"

---

## Features

### 1. Smart Question Analysis
The tool understands natural language queries and maps them to specific API capabilities. It recognizes variations like:
- "create", "add", "new", "make" → CREATE operations
- "delete", "remove", "destroy" → DELETE operations
- "get", "view", "fetch", "export" → READ operations
- "edit", "update", "modify" → UPDATE operations

### 2. Three Answer Types

| Answer | Meaning |
|--------|---------|
| ✅ **YES** | Directly supported with a specific endpoint |
| ❌ **NO** | Not possible via API (may have alternatives) |
| ⚠️ **INDIRECT** | Achievable through a different approach |

### 3. Data Capabilities Matrix
An expandable table showing READ/CREATE/UPDATE/DELETE capabilities for every data type at a glance.

### 4. Capability Cards
Visual cards showing:
- What operations are supported (Read/Create/Update/Delete)
- Sync direction (Hubstaff → External, Bi-directional, etc.)
- Limitations
- Common use cases

### 5. Endpoint Reference
Collapsible sections with all relevant API endpoints, organized by category.

### 6. Support Tips
Always-visible reminders about:
- Plan requirements
- Rate limits
- Authentication
- Useful documentation links

---

## How It Works

### Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    USER INTERFACE                           │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Search Input: "Can I create a project?"            │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                   QUERY ANALYSIS                            │
│                                                             │
│  1. checkCanDo() - Matches query to capabilities object     │
│  2. findRelevantCapabilities() - Finds related data types   │
│  3. analyzeQuery() - Matches to endpoint categories         │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                   RESULT RENDERING                          │
│                                                             │
│  • renderCanDoResult() - YES/NO/INDIRECT display            │
│  • renderCapabilitiesSection() - Capability cards           │
│  • displayResults() - Full results with endpoints           │
└─────────────────────────────────────────────────────────────┘
```

### Key Data Objects

#### 1. `capabilities` (inside `checkCanDo()`)
Contains detailed information for specific "can I do X?" questions:

```javascript
'create project': { 
    answer: 'YES',                    // YES, NO, or INDIRECT
    question: 'Can I create...',      // Human-readable question
    howTo: [...],                     // Endpoints to use
    notes: [...],                     // Additional context
    requirements: [...],              // Plan/add-on requirements
    limitations: [...],               // What can't be done
    alternatives: [...]               // Workarounds (for NO/INDIRECT)
}
```

#### 2. `dataCapabilities`
Defines CRUD capabilities for each data type:

```javascript
timesheets: {
    name: "Timesheets",
    icon: "📊",
    read: true,
    create: false,
    update: false,
    delete: false,
    syncDirection: "outbound",        // outbound, inbound, both, push
    description: "...",
    limitations: [...],
    useCases: [...],
    relatedCategories: [...]
}
```

#### 3. `apiKnowledge`
Contains endpoint details for each API category:

```javascript
projects: {
    title: "Projects",
    icon: "📁",
    description: "...",
    endpoints: [
        { method: "GET", path: "/v2/...", desc: "..." },
        { method: "POST", path: "/v2/...", desc: "..." }
    ],
    details: [...],
    notes: "..."
}
```

---

## Using the Tool

### Quick Search Buttons
Click any quick search button to instantly search common topics:
- ⏱️ Time Tracking
- 📋 Attendance
- 📁 Projects & Tasks
- 📸 Screenshots
- 🔐 Authentication
- 🔔 Webhooks
- ⚡ Rate Limits
- 👥 Users & Teams
- 🛠️ Dev Resources

### Natural Language Queries
Type questions naturally:
- "Can the customer sync timesheets with Odoo?"
- "Is it possible to delete screenshots?"
- "How do I create a webhook?"
- "Can they track check-in and check-out?"

### Data Capabilities Matrix
Click "🔄 API Data Capabilities Matrix" to expand a table showing:
- Every data type
- Read/Create/Update/Delete support
- Sync direction

Click any row to search for that data type.

### Knowledge Base Reference
Click "📚 API Knowledge Base Reference" to see all API categories. Click any card to search for that topic.

---

## Understanding Results

### ✅ YES Response
```
┌─────────────────────────────────────────┐
│              ✅ YES                      │
├─────────────────────────────────────────┤
│ 📋 What you're asking:                  │
│    Can I create projects via the API?   │
├─────────────────────────────────────────┤
│ ✅ How to achieve this:                 │
│    POST /v2/organizations/{org}/projects│
│    ↳ Create a new project               │
├─────────────────────────────────────────┤
│ 📝 Notes:                               │
│    • Full CRUD supported for projects   │
├─────────────────────────────────────────┤
│ 📋 Requirements:                        │
│    • Team or Enterprise plan            │
└─────────────────────────────────────────┘
```

### ❌ NO Response
```
┌─────────────────────────────────────────┐
│              ❌ NO                       │
├─────────────────────────────────────────┤
│ 📋 What you're asking:                  │
│    Can I delete projects via the API?   │
├─────────────────────────────────────────┤
│ ❌ Why this isn't possible:             │
│    • No DELETE endpoint exists          │
├─────────────────────────────────────────┤
│ 💡 Alternative approach:                │
│    PUT /v2/projects/{project_id}        │
│    ↳ Archive the project instead        │
└─────────────────────────────────────────┘
```

### ⚠️ INDIRECT Response
```
┌─────────────────────────────────────────┐
│           ⚠️ INDIRECT                   │
├─────────────────────────────────────────┤
│ 📋 What you're asking:                  │
│    Can I track check-in/check-out?      │
├─────────────────────────────────────────┤
│ ⚠️ Why this is indirect:                │
│    • No explicit check-in endpoint      │
├─────────────────────────────────────────┤
│ ✅ How to achieve this instead:         │
│                                         │
│    GET /v2/.../activities               │
│    ↳ Option 1: Activity-based           │
│                                         │
│    GET /v2/.../job_site_visits          │
│    ↳ Option 2: GPS Geofencing           │
└─────────────────────────────────────────┘
```

---

## Data Structure

### Sync Directions

| Direction | Icon | Meaning |
|-----------|------|---------|
| `outbound` | → | Hubstaff → External Only (read-only) |
| `inbound` | ← | External → Hubstaff Only |
| `both` | ↔ | Bi-directional Sync |
| `push` | 📤 | Push Notifications (Webhooks) |

### API Categories Covered

| Category | Endpoints | CRUD Support |
|----------|-----------|--------------|
| Activities | 10 | Read only |
| Timesheets | 1 | Read only |
| Time Entries | 2 | Read + Create |
| Projects | 5 | Read + Create + Update |
| Tasks | 8 | Full CRUD |
| Users | 2 | Read only |
| Members | 7 | Read + Update |
| Teams | 8 | Full CRUD |
| Screenshots | 4 | Read only |
| Job Sites | 7 | Read + Create + Update |
| Job Site Visits | 2 | Read only |
| Locations | 2 | Read only |
| Webhooks | 9 | Read + Create + Delete |
| Clients | 5 | Read + Create + Update |
| Invites | 6 | Full CRUD |
| Time Off | 2 | Read only |
| Invoices | 2 | Read only |
| And more... | | |

---

## Adding New Capabilities

### Adding a New "Can I...?" Question

1. Open the file and find the `checkCanDo()` function
2. Add a new entry to the `capabilities` object:

```javascript
'your new capability': { 
    answer: 'YES',  // or 'NO' or 'INDIRECT'
    question: 'Can I do X via the API?',
    howTo: [
        { method: 'POST', endpoint: '/v2/...', desc: 'Description' }
    ],
    notes: ['Note 1', 'Note 2'],
    requirements: ['Team or Enterprise plan'],
    limitations: ['Limitation 1'],
    alternatives: []  // Only for NO/INDIRECT answers
}
```

3. Optionally add fuzzy matching in the `fuzzyMatches` array:

```javascript
{ patterns: ['create', 'add'], subject: 'your thing', key: 'your new capability' }
```

### Adding a New Data Type to Capabilities Matrix

Add to the `dataCapabilities` object:

```javascript
yourDataType: {
    name: "Your Data Type",
    icon: "🆕",
    read: true,
    create: true,
    update: false,
    delete: false,
    syncDirection: "both",
    description: "Description here",
    limitations: [
        "Limitation 1",
        "Limitation 2"
    ],
    useCases: ["Use case 1", "Use case 2"],
    relatedCategories: ["relatedCategory1"]
}
```

### Adding New Endpoints

Add to the `apiKnowledge` object:

```javascript
yourCategory: {
    title: "Your Category",
    icon: "🆕",
    description: "Description",
    endpoints: [
        { method: "GET", path: "/v2/...", desc: "Description" },
        { method: "POST", path: "/v2/...", desc: "Description" }
    ],
    details: ["Detail 1", "Detail 2"],
    notes: "Optional notes",
    status: "supported"
}
```

---

## Maintenance

### Keeping Endpoints Up-to-Date

Use the **Hubstaff API Updater Tool** (`hubstaff-api-updater-smart.html`) to:

1. Compare current tool endpoints against live API documentation
2. Identify missing endpoints
3. Get copy-paste instructions for adding new ones

### Checking for API Changes

1. Download latest API docs: 
   ```bash
   curl -o hubstaff-api-docs.json https://developer.hubstaff.com/openapi.json
   ```
2. Open the updater tool
3. Drop in the JSON file
4. Paste your current index.html (or upload it)
5. Click "Compare"

### Common Maintenance Tasks

| Task | Where to Edit |
|------|---------------|
| Add new endpoint | `apiKnowledge` object |
| Add new "Can I?" answer | `capabilities` object in `checkCanDo()` |
| Add new data type | `dataCapabilities` object |
| Update plan requirements | `requirements` array in relevant capability |
| Fix incorrect information | Find relevant object and update |

---

## Technical Notes

### Browser Compatibility
- Modern browsers (Chrome, Firefox, Safari, Edge)
- No external dependencies (vanilla JavaScript)
- Self-contained single HTML file

### Performance
- All data is client-side (no API calls)
- Instant search results
- Lightweight (~100KB total)

### Hosting
Can be hosted on:
- GitHub Pages
- Any static file server
- Local file system (file://)
- Internal company wiki/intranet

---

## Support

For questions or issues with this tool, contact the development team or raise an issue in the repository.

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.0 | Jan 2026 | Added enhanced capability analysis with YES/NO/INDIRECT responses, requirements, and alternatives |
| 1.5 | Jan 2026 | Added Data Capabilities Matrix and capability cards |
| 1.0 | Jan 2026 | Initial release with endpoint reference and search |
