# VSRS — Vehicle Service Registration System

A full-stack vehicle service booking app built with **React + Vite** (frontend) and **Express + SQLite** (backend).

## Quick Start

```bash
# Install dependencies
npm install

# Development (runs both frontend + backend)
npm run dev

# Production (builds frontend, starts server)
npm start
```

- Frontend: http://localhost:5173
- Backend API: http://localhost:3001

---

## Viewing Backend Database Records

The database file is at `server/database.sqlite`. Always run commands from the **W22 project folder**.

### Option 1: Terminal Queries

```bash
cd c:\Users\thars\OneDrive\Documents\dumps\W22
```

**View all users:**
```powershell
node -e "import('./server/db.js').then(m => console.table(m.default.prepare('SELECT name,email,role,created_at FROM users').all()))"
```

**View all vehicles:**
```powershell
node -e "import('./server/db.js').then(m => console.table(m.default.prepare('SELECT vehicle_number,brand,model,year FROM vehicles').all()))"
```

**View all bookings:**
```powershell
node -e "import('./server/db.js').then(m => console.table(m.default.prepare('SELECT user_name,service_type,service_center_name,date,status,price_quote FROM bookings').all()))"
```

**View everything at once:**
```powershell
node -e "import('./server/db.js').then(m => { const d = m.default; console.log('=== USERS ==='); console.table(d.prepare('SELECT name,email,role FROM users').all()); console.log('=== VEHICLES ==='); console.table(d.prepare('SELECT vehicle_number,brand,model,year FROM vehicles').all()); console.log('=== BOOKINGS ==='); console.table(d.prepare('SELECT user_name,service_type,status,date FROM bookings').all()); })"
```

### Option 2: VS Code Extension (Recommended)

1. Install the **"SQLite Viewer"** extension in VS Code
2. Open `server/database.sqlite` in VS Code
3. Browse tables visually with a GUI

### Option 3: API Endpoints (while server is running)

```powershell
# Health check
Invoke-RestMethod http://localhost:3001/api/health

# View registered service centers (no auth needed)
Invoke-RestMethod http://localhost:3001/api/auth/service-centers
```

---

## Database Tables

| Table | Description |
|-------|-------------|
| `users` | All registered users (customers, service-centers, admins) |
| `vehicles` | Vehicles registered by customers |
| `bookings` | Service booking records |

### Custom Queries

You can run any SQL query:

```powershell
# Count users by role
node -e "import('./server/db.js').then(m => console.table(m.default.prepare('SELECT role, COUNT(*) as count FROM users GROUP BY role').all()))"

# Find bookings by status
node -e "import('./server/db.js').then(m => console.table(m.default.prepare('SELECT * FROM bookings WHERE status = ?').all('pending')))"

# Delete all data (⚠️ resets everything)
node -e "import('./server/db.js').then(m => { m.default.exec('DELETE FROM bookings; DELETE FROM vehicles; DELETE FROM users;'); console.log('All data cleared.'); })"
```

---

## Environment Variables

Copy `.env.example` to `.env` and configure:

```
PORT=3001
CORS_ORIGIN=http://localhost:5173
JWT_SECRET=your_secret_here
JWT_EXPIRES_IN=7d
DB_PATH=./server/database.sqlite
```

## User Roles

| Role | Access |
|------|--------|
| `customer` | Add vehicles, book services, view own history |
| `service-center` | View/manage bookings assigned to them |
| `admin` | View all users, all bookings, system-wide access |
