# Timetable Generator

An automated academic timetable scheduling system built with Node.js, Express, and MongoDB. Supports multi-branch scheduling, elective synchronization, cross-branch course sharing, lab room pairing, and semester half splits.

---

## Prerequisites

Before installing, ensure you have the following:

- **Node.js** v16 or higher — [nodejs.org](https://nodejs.org)
- **MongoDB** v5 or higher (running locally) — [mongodb.com](https://www.mongodb.com/try/download/community)
- **npm** (bundled with Node.js)

---

## Installation

### 1. Clone or download the project

```bash
git clone <your-repo-url>
cd timetable-generator
```

Or simply place all project files into a folder and navigate to it.

### 2. Install dependencies

```bash
npm install
```

This installs: `express`, `mongoose`, `multer`, `csv-parser`, `body-parser`, `ejs`, and `path`.

If you don't have a `package.json` yet, initialize one first and install manually:

```bash
npm init -y
npm install express mongoose multer csv-parser body-parser ejs
```

### 3. Start MongoDB

Make sure MongoDB is running locally on the default port (`27017`):

```bash
# macOS / Linux
mongod

# Windows (if installed as a service)
net start MongoDB

# Or via Homebrew (macOS)
brew services start mongodb-community
```

The app connects to `mongodb://localhost:27017/timetable` automatically.

### 4. Start the server

```bash
node server.js
```

You should see:

```
MongoDB Connected
Server running on http://localhost:3000
```

---

## Usage

Open your browser and navigate to **http://localhost:3000**.

### Step 1 — Upload Rooms

Upload a `rooms.csv` file with the following columns:

| Column | Description |
|--------|-------------|
| `number` | Room identifier (e.g. `C001`, `C207`) |
| `capacity` | Seating capacity |
| `type` | Room type (e.g. `classroom`, `lab`) |

> **Note:** Rooms `C207`, `C208`, `C106`, `C107`, `C206`, and `C105` are reserved exclusively for labs. Do not assign regular lecture courses to these rooms — the system enforces this automatically.

### Step 2 — Upload Faculty

Upload a `faculty.csv` file with the following columns:

| Column | Description |
|--------|-------------|
| `name` | Faculty member's full name |
| `department` | Department name |
| `availability` | Availability string (optional) |

### Step 3 — Upload Courses

Upload a `courses.csv` file. This triggers automatic timetable generation if faculty and rooms are already loaded.

**Required columns:**

| Column | Type | Description |
|--------|------|-------------|
| `code` | string | Course code (e.g. `CS101`) |
| `name` | string | Full course name |
| `faculty` | string | Assigned faculty name (must match faculty data) |
| `duration` | number | Session duration in minutes |
| `type` | string | `Lecture`, `Tutorial`, or `Lab` |
| `branch` | string | Branch (e.g. `CSE`, `ECE`, `DSAI`) |
| `section` | string | Section identifier, or `ALL` |
| `year` | number | Academic year (1–4) |
| `credits` | number | Credit hours |
| `semesterHalf` | string | `0` = both halves, `1` = first half only, `2` = second half only |
| `basket` | number | Elective basket group number |
| `isElective` | boolean | `1` / `true` / `TRUE` to mark as elective |
| `sharedWith` | string | Comma-separated branch/section targets for cross-branch scheduling |

**Cross-branch scheduling (`sharedWith`):**

To schedule the same course session for two branches simultaneously (same room, same time), set `sharedWith` on both course entries to reference each other. For example, for a shared lecture between CSE and ECE:

- CSE entry: `sharedWith = ECE`
- ECE entry: `sharedWith = CSE`

### Step 4 — View Timetable

After uploading courses, navigate to:

| Path | Description |
|------|-------------|
| `/view` | Branch/year/section timetable view |
| `/view-faculty` | Faculty timetable view |
| `/view-classrooms` | Room-by-room timetable view |

### Downloading

| Path | File |
|------|------|
| `/download` | `timetable.csv` |
| `/download-faculty` | `faculty-timetable.csv` |
| `/download-classrooms` | `classroom-timetable.csv` |

---


## API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| GET | `/` | Upload interface |
| POST | `/upload/courses` | Upload courses CSV |
| POST | `/upload/faculty` | Upload faculty CSV |
| POST | `/upload/rooms` | Upload rooms CSV |
| POST | `/generate` | Manually trigger timetable generation |
| GET | `/view` | View branch timetable |
| GET | `/view-faculty` | View faculty timetable |
| GET | `/view-classrooms` | View classroom timetable |
| GET | `/download` | Download timetable CSV |
| GET | `/download-faculty` | Download faculty CSV |
| GET | `/download-classrooms` | Download classroom CSV |
| GET | `/clear-db` | Clear timetable and course data |
| GET | `/force-clear` | Drop all collections (full reset) |

---

## Project Structure

```
.
├── server.js          # Main application (routes + scheduling logic)
├── views/             # EJS templates
│   ├── index.ejs
│   ├── timetable.ejs
│   ├── faculty-timetable.ejs
│   └── classroom-timetable.ejs
├── public/            # Static assets (CSS, JS)
├── uploads/           # Temporary CSV upload directory (auto-created)
└── package.json
```

---

## License

MIT
