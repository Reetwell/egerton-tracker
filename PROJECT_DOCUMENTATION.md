# Egerton U15 Lions - Web Tracker Documentation

## Project Overview

A comprehensive team management and player tracking web application for the Egerton U15 Lions football club. The application serves dual roles:

1. **Player/Supporter Portal** (`index.html`) - View team statistics, fixtures, results, lineups, and receive manager messages
2. **Manager Portal** (`egerton-manager.html`) - Update match results, manage lineups, message players, and maintain team data
3. **Quick Admin** (`admin.html`) - Lightweight interface for quickly updating match results

**Architecture:** Single-page application (SPA) using vanilla JavaScript with real-time data synchronization via Google Sheets API and Apps Script backend

---

## File Structure

```
/Egerton Web Tracker/
├── index.html              # Player/Supporter Portal (main public interface)
├── egerton-manager.html    # Manager Portal (admin dashboard)
├── admin.html              # Quick Admin Interface
├── egerton-tracker.png     # Team Badge/Logo
└── PROJECT_DOCUMENTATION.md # This file
```

---

## Configuration

All three HTML files share the same configuration:

```javascript
const CONFIG = {
    SPREADSHEET_ID: '1sMHiLgwRAsnu2zWRCfdHWwuEMfefpOe30BIo_Zv01IE',
    API_KEY: 'AIzaSyAkb76g25dIADs9Ty5CD3V7BguWe3ahLPs',
    BADGE_IMG: 'egerton-tracker.png',
    APPS_SCRIPT_URL: 'https://script.google.com/macros/s/AKfycby6a_XWdPHAjEcId0-A57dRG26w_VJlF38lsVuBqnPGzeXAo3RTM76phbB7RA5n9zuwAA/exec',
    ADMIN_PASSCODE: '1505',
    TEAM_NAME: 'Egerton U15 Lions',
    SEASON: '2025/26'
}
```

---

## Google Sheets Database Schema

### 1. Fixtures Sheet
| Column | Description |
|--------|-------------|
| ID | Unique match identifier |
| Date | Match date |
| KickOffTime | Kick-off time |
| Opponent | Opposition team name |
| Venue | Home/Away |
| Competition | League/Cup name |
| OurScore | Egerton's score |
| TheirScore | Opposition score |
| GoalScorers | Comma-separated scorer names |
| Notes | Match notes |
| Status | Scheduled/Played/Postponed/Cancelled |
| MOTM | Man of the Match |

### 2. Players Sheet
| Column | Description |
|--------|-------------|
| ShirtNumber | Player's squad number |
| Name | Full name |
| Position | Playing position |
| Goals | Total goals scored |
| MOTM | Man of the Match awards |
| Foot | Preferred foot |
| Appearances | Total appearances |

### 3. Managers Sheet
| Column | Description |
|--------|-------------|
| ID | Unique identifier |
| Name | Manager's display name |
| Username | Login username |
| Password | Login password |

### 4. Messages Sheet
| Column | Description |
|--------|-------------|
| ID | Unique message ID |
| From | Sender name |
| FromType | 'manager' or 'player' |
| To | Recipient name |
| Message | Message content |
| Timestamp | Date/time sent |

### 5. Lineups Sheet
| Column | Description |
|--------|-------------|
| MatchID | Links to Fixtures.ID |
| Formation | e.g., '4-3-3', '4-4-2' |
| Starting11 | Comma-separated player names |
| Subs | Comma-separated substitute names |
| Notes | Tactical notes |
| CreatedBy | Manager who created it |
| Timestamp | When created |

---

## index.html - Player Portal

### Purpose
Main public-facing interface for players and supporters to track team performance.

### App States
```javascript
const APP_STATE = {
    LOGIN: 'login',
    WELCOME: 'welcome',
    BADGE: 'badge',
    MAIN: 'main'
}
```

### Navigation Tabs
- **Overview** - Dashboard with next match, personal stats, team stats, top scorers, recent results
- **Fixtures** - Upcoming matches with lineup status
- **Results** - Past matches with filtering (Win/Draw/Loss)
- **Squad** - Player roster with statistics
- **Messages** - Player-manager messaging
- **Admin** - Passcode-protected match result entry (code: 1505)

### Key Functions

#### Authentication
| Function | Description |
|----------|-------------|
| `findPlayerMatch()` | Fuzzy search matching for player login |
| `handleLoginInput()` | Processes login input with suggestions |
| `attemptLogin()` | Validates and initiates welcome sequence |
| `loginAsGuest()` | Guest mode for supporters |
| `logout()` | Clears session |

#### Data
| Function | Description |
|----------|-------------|
| `fetchSheetData()` | Fetches all data from Google Sheets |
| `getPlayerMatches()` | Gets matches where player scored |
| `getPlayerMessages()` | Gets conversation history |

#### UI
| Function | Description |
|----------|-------------|
| `setActiveTab()` | Switches between tabs |
| `openPlayerProfile()` | Opens player statistics overlay |
| `openMatchDetail()` | Opens match result overlay |
| `renderLineupGraphic()` | Visual team lineup display |
| `handleSearch()` | Live search across players/matches |

#### Admin (requires passcode)
| Function | Description |
|----------|-------------|
| `enterPasscode()` | Numeric keypad entry |
| `selectMatch()` | Select match for editing |
| `saveMatch()` | Save result to backend |

---

## egerton-manager.html - Manager Portal

### Purpose
Comprehensive manager dashboard for team administration.

### Navigation Tabs
- **Dashboard** - Quick overview, next match, squad stats
- **Lineup** - Formation selector, player selection, tactical notes
- **Messages** - Send messages to players
- **Fixtures** - View upcoming matches
- **Squad** - Full squad statistics

### Key Functions

#### Authentication
| Function | Description |
|----------|-------------|
| `attemptLogin()` | Validates manager credentials |
| `startWelcomeSequence()` | Welcome animation |
| `logout()` | Manager logout |

#### Lineup Builder
| Function | Description |
|----------|-------------|
| `selectMatchForLineup()` | Select fixture for lineup |
| `setFormation()` | Change formation |
| `togglePlayerSelection()` | Add/remove from Starting/Sub |
| `saveLineup()` | Save to backend |
| `renderLineupGraphic()` | Visual lineup card |

**Available Formations:** 4-3-3, 4-4-2, 4-2-3-1, 3-5-2, 3-4-3, 5-3-2, 4-5-1

#### Messaging
| Function | Description |
|----------|-------------|
| `openChat()` | Open conversation with player |
| `sendMessage()` | Send message via Apps Script |
| `getConversation()` | Get message history |

---

## admin.html - Quick Admin

### Purpose
Lightweight panel for quickly updating match results without full authentication.

### Features
- Match selector dropdown
- Status selector (Scheduled/Played/Postponed/Cancelled)
- Score entry fields
- Goal scorers input (comma-separated)
- Goals per scorer input (comma-separated numbers)

---

## Architecture & Data Flow

### Read Operations
```
Google Sheets → Google Sheets API v4 → JavaScript fetch() → State → UI Render
```

### Write Operations
```
User Action → Apps Script Endpoint → Google Sheets → Local State Update → UI Re-render
```

### Startup Sequence
1. Page load shows spinner
2. `fetchSheetData()` called
3. Data parsed into JavaScript objects
4. State initialized
5. `renderApp()` generates UI
6. App ready for interaction

---

## Styling

### Framework
- **Tailwind CSS** via CDN
- **Design Pattern:** Glass Morphism with backdrop filters

### Color Scheme
| Element | Color |
|---------|-------|
| Primary | Yellow (#FACC15) |
| Success/Win | Green (#22c55e) |
| Neutral | Gray shades |
| Home | Dark green (#15803d) |
| Away | Blue (#1d4ed8) |

### Responsive Design
- **Mobile-first** approach
- **Breakpoint:** md (768px) activates desktop layout
- **Mobile:** Bottom navigation, sticky header
- **Desktop:** Fixed left sidebar, top header

---

## Security Notes

### Current Implementation
- Google Sheets API key is read-only
- Admin passcode: `1505` (hardcoded)
- Manager passwords stored in spreadsheet
- Input sanitized via `escapeHtml()` function

### Recommendations for Production
1. Move API keys to environment variables
2. Hash manager passwords
3. Implement proper authentication (OAuth)
4. Add CSRF protection
5. Consider dedicated backend

---

## Dependencies

### External Services
- **Google Sheets API v4** - Data storage and retrieval
- **Google Apps Script** - Backend write operations
- **Tailwind CSS CDN** - Styling

### No Additional Libraries
- Pure vanilla JavaScript
- No build process required
- Static HTML files can be hosted anywhere

---

## How to Use

### For Players/Supporters
1. Open `index.html`
2. Enter your name (or proceed as guest)
3. Navigate using tabs to view fixtures, results, squad, messages
4. Click on matches or players for detailed views

### For Managers
1. Open `egerton-manager.html`
2. Login with username and password
3. Use Dashboard for overview
4. Use Lineup tab to set team selection
5. Use Messages to communicate with players

### For Quick Admin Updates
1. Open `admin.html`
2. Select match from dropdown
3. Enter scores and scorers
4. Click Save

---

## Future Improvements

1. **Real-time Updates** - WebSockets or polling for live sync
2. **Offline Support** - Service worker for offline functionality
3. **Native App** - Consider mobile app wrapper
4. **Analytics** - Usage tracking and insights
5. **Accessibility** - Improve keyboard navigation, ARIA labels
6. **Testing** - Add automated tests
