# CricPulse Development Guide

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                   Frontend (React)                       │
│  ├─ Components                                          │
│  ├─ Pages                                               │
│  ├─ Services (API calls)                                │
│  ├─ Hooks (WebSocket, State)                            │
│  └─ Types (TypeScript interfaces)                       │
└────────────────┬────────────────────────────────────────┘
                 │ HTTP + WebSocket
┌────────────────▼────────────────────────────────────────┐
│                Backend (Express + Socket.io)             │
│  ├─ Routes (API endpoints)                              │
│  ├─ Controllers (Business logic)                        │
│  ├─ Models (MongoDB schemas)                            │
│  ├─ Middleware (Auth, CORS, etc)                        │
│  └─ Services (Match updates, notifications)             │
└────────────────┬────────────────────────────────────────┘
                 │ MongoDB Connection
┌────────────────▼────────────────────────────────────────┐
│              Database (MongoDB)                         │
│  ├─ Matches Collection                                  │
│  ├─ Users Collection (future)                           │
│  └─ Scores Collection (future)                          │
└─────────────────────────────────────────────────────────┘
```

---

## 📂 Frontend Project Structure

```
frontend/
├── src/
│   ├── components/          # Reusable React components
│   │   ├── MatchCard.tsx    # Individual match card
│   │   ├── Dashboard.tsx    # Main dashboard
│   │   ├── Header.tsx       # Top navigation
│   │   └── ScoreCard.tsx    # Detailed scorecard
│   ├── pages/               # Page-level components
│   │   ├── Home.tsx
│   │   ├── MatchDetail.tsx
│   │   └── About.tsx
│   ├── services/            # API services
│   │   ├── api.ts          # Axios instance
│   │   ├── matchAPI.ts     # Match endpoints
│   │   └── wsService.ts    # WebSocket service
│   ├── hooks/               # Custom React hooks
│   │   ├── useWebSocket.ts
│   │   ├── useMatches.ts
│   │   └── useScores.ts
│   ├── types/               # TypeScript interfaces
│   │   └── index.ts
│   ├── App.tsx              # Root component
│   ├── main.tsx             # Entry point
│   └── index.css            # Global styles
├── public/                  # Static assets
├── index.html               # HTML template
├── vite.config.ts           # Vite configuration
├── tailwind.config.js       # Tailwind configuration
├── postcss.config.js        # PostCSS configuration
├── tsconfig.json            # TypeScript configuration
└── package.json             # Dependencies
```

---

## 📂 Backend Project Structure

```
backend/
├── src/
│   ├── routes/              # API routes
│   │   ├── matches.ts       # Match routes
│   │   ├── users.ts         # User routes (future)
│   │   └── index.ts         # Route aggregator
│   ├── controllers/         # Request handlers
│   │   ├── matchController.ts
│   │   └── userController.ts
│   ├── models/              # MongoDB schemas
│   │   ├── Match.ts
│   │   └── User.ts
│   ├── middleware/          # Express middleware
│   │   ├── auth.ts          # Authentication
│   │   ├── errorHandler.ts  # Error handling
│   │   └── validation.ts    # Request validation
│   ├── services/            # Business logic
│   │   ├── matchService.ts
│   │   ├── liveUpdateService.ts
│   │   └── wsService.ts
│   ├── types/               # TypeScript interfaces
│   │   └── index.ts
│   ├── config/              # Configuration files
│   │   ├── database.ts      # DB connection
│   │   └── env.ts           # Env variables
│   └── server.ts            # Express + Socket.io setup
├── dist/                    # Compiled JavaScript
├── .env.example             # Environment template
├── tsconfig.json            # TypeScript configuration
├── package.json             # Dependencies
└── nodemon.json             # Nodemon config (optional)
```

---

## 🚀 Frontend Development

### Adding a New Component

1. **Create Component File**
```typescript
// frontend/src/components/MatchCard.tsx
import React from 'react';
import type { Match } from '../types';

interface MatchCardProps {
  match: Match;
  onClick: () => void;
}

export const MatchCard: React.FC<MatchCardProps> = ({ match, onClick }) => {
  return (
    <div onClick={onClick} className="bg-slate-900 border border-slate-800 rounded-xl p-4 cursor-pointer">
      <h3>{match.teamA} vs {match.teamB}</h3>
      <p className="text-emerald-400">{match.score.runs}/{match.score.wickets}</p>
    </div>
  );
};
```

2. **Import in App.tsx**
```typescript
import { MatchCard } from './components/MatchCard';
```

3. **Use Component**
```typescript
<MatchCard match={match} onClick={handleClick} />
```

### Adding a Custom Hook

```typescript
// frontend/src/hooks/useMatches.ts
import { useState, useEffect } from 'react';
import { matchesAPI } from '../services/api';
import type { Match } from '../types';

export const useMatches = () => {
  const [matches, setMatches] = useState<Match[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchMatches = async () => {
      try {
        const response = await matchesAPI.getAll();
        setMatches(response.data);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Failed to fetch');
      } finally {
        setLoading(false);
      }
    };

    fetchMatches();
  }, []);

  return { matches, loading, error };
};
```

---

## 🔧 Backend Development

### Adding a New Route

1. **Create Route File**
```typescript
// backend/src/routes/matches.ts
import express from 'express';
import * as matchController from '../controllers/matchController';

const router = express.Router();

router.get('/', matchController.getAllMatches);
router.get('/:id', matchController.getMatchById);
router.post('/', matchController.createMatch);
router.put('/:id', matchController.updateMatch);

export default router;
```

2. **Create Controller**
```typescript
// backend/src/controllers/matchController.ts
import type { Request, Response } from 'express';

export const getAllMatches = async (req: Request, res: Response) => {
  try {
    // Logic here
    res.json({ message: 'All matches' });
  } catch (error) {
    res.status(500).json({ error: error instanceof Error ? error.message : 'Error' });
  }
};

export const getMatchById = async (req: Request, res: Response) => {
  try {
    const { id } = req.params;
    // Logic here
    res.json({ message: `Match ${id}` });
  } catch (error) {
    res.status(500).json({ error: error instanceof Error ? error.message : 'Error' });
  }
};

export const createMatch = async (req: Request, res: Response) => {
  try {
    // Logic here
    res.status(201).json({ message: 'Match created' });
  } catch (error) {
    res.status(500).json({ error: error instanceof Error ? error.message : 'Error' });
  }
};

export const updateMatch = async (req: Request, res: Response) => {
  try {
    const { id } = req.params;
    // Logic here
    res.json({ message: `Match ${id} updated` });
  } catch (error) {
    res.status(500).json({ error: error instanceof Error ? error.message : 'Error' });
  }
};
```

3. **Register in server.ts**
```typescript
import matchRoutes from './routes/matches';

app.use('/api/matches', matchRoutes);
```

### WebSocket Implementation

```typescript
// backend/src/services/wsService.ts
import type { Server } from 'socket.io';

export const setupWebSocket = (io: Server) => {
  io.on('connection', (socket) => {
    console.log(`Client connected: ${socket.id}`);

    // Join match room
    socket.on('match:join', ({ matchId }) => {
      socket.join(`match:${matchId}`);
      console.log(`Client ${socket.id} joined match ${matchId}`);
    });

    // Broadcast match update
    socket.on('match:update', (data) => {
      io.to(`match:${data.matchId}`).emit('match:update', data);
    });

    socket.on('disconnect', () => {
      console.log(`Client disconnected: ${socket.id}`);
    });
  });
};
```

---

## 🧪 Testing

### Frontend Testing
```bash
cd frontend
npm test

# Run specific test
npm test -- MatchCard.test.tsx

# Coverage
npm test -- --coverage
```

### Backend Testing
```bash
cd backend
npm test

# Watch mode
npm test -- --watch

# Coverage
npm test -- --coverage
```

---

## 🐛 Debugging

### Frontend
1. Use React DevTools Chrome Extension
2. Use console.log() or debugger
3. Use Vite's built-in HMR (Hot Module Replacement)

### Backend
```typescript
// Add debugging
console.log('Debug info:', data);
console.error('Error:', error);

// Or use node debugger
node --inspect dist/server.js
```

---

## 📦 Adding Dependencies

### Frontend
```bash
cd frontend
npm install package-name

# Or with yarn
yarn add package-name
```

### Backend
```bash
cd backend
npm install package-name

# Dev dependencies
npm install --save-dev package-name
```

---

## ✅ Code Quality

### Linting
```bash
# Frontend
cd frontend && npm run lint

# Backend
cd backend && npm run lint
```

### Type Checking
```bash
# Frontend
cd frontend && npx tsc --noEmit

# Backend
cd backend && npx tsc --noEmit
```

---

## 🚀 Performance Tips

1. **Frontend**: Use React.memo for expensive components
2. **Frontend**: Lazy load components with React.lazy()
3. **Backend**: Use pagination for large datasets
4. **Backend**: Cache frequently accessed data
5. **Both**: Use compression middleware (gzip)

---

## 📚 Useful Resources

- [React Documentation](https://react.dev)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [Express.js Guide](https://expressjs.com/)
- [Socket.io Documentation](https://socket.io/docs/)
- [Tailwind CSS](https://tailwindcss.com/docs)
- [MongoDB](https://docs.mongodb.com/)

---

**Happy Coding! 🚀**
