# CricPulse Setup Guide

## ✅ Option 1: Quick Start (Local Development)

### 1. Clone the Repository
```bash
git clone https://github.com/pallalakshmidevi600-oss/Cric-pulse.git
cd Cric-pulse
```

### 2. Start Backend
```bash
cd backend
npm install
cp .env.example .env
npm run dev
```

Output should show:
```
🚀 Server running on port 5000
📊 WebSocket ready for live updates
```

### 3. Start Frontend (New Terminal)
```bash
cd frontend
npm install
npm run dev
```

Visit: http://localhost:5173

---

## 🐳 Option 2: Docker Setup

### Prerequisites
- Docker Desktop installed
- Docker Compose

### Run with One Command
```bash
docker-compose up -d
```

Access:
- Frontend: http://localhost:5173
- Backend: http://localhost:5000
- MongoDB: localhost:27017

### Stop Services
```bash
docker-compose down
```

---

## 📊 Project Features

### Frontend Features ✨
- ✅ React 18 + TypeScript
- ✅ Tailwind CSS styling
- ✅ Real-time updates with Socket.io
- ✅ Responsive design
- ✅ Dark theme UI
- ✅ League filtering (All/International/National)
- ✅ Live match grid
- ✅ Match detail view (coming soon)

### Backend Features 🔧
- ✅ Express.js server
- ✅ Socket.io for WebSocket
- ✅ TypeScript support
- ✅ MongoDB integration (ready)
- ✅ CORS configured
- ✅ Environment-based config

---

## 🚀 Deployment

### Deploy Frontend to Vercel
```bash
npm i -g vercel
cd frontend
vercel
```

### Deploy Backend to Heroku
```bash
npm i -g heroku
cd backend
heroku login
heroku create your-app-name
git push heroku main
```

---

## 📝 Development Tips

### Frontend - Add New Component
1. Create file in `frontend/src/components/`
2. Use TypeScript interfaces from `types/index.ts`
3. Import in App.tsx
4. Style with Tailwind classes

### Backend - Add New Route
1. Create route file in `backend/src/routes/`
2. Export router
3. Import in server.ts
4. Add to app.use()

### WebSocket Events
- Frontend: `import { io } from 'socket.io-client'`
- Backend: `io.on('connection', (socket) => {})`

---

## 🐛 Troubleshooting

### Port Already in Use
```bash
# Find process on port
lsof -i :5000  # Backend
lsof -i :5173  # Frontend

# Kill process
kill -9 <PID>
```

### MongoDB Connection Error
- Ensure MongoDB is running locally
- Or update MONGODB_URI in .env

### CORS Error
- Check CORS_ORIGIN in backend .env
- Should match frontend URL

---

## 📚 Useful Commands

```bash
# Frontend
npm run dev      # Start dev server
npm run build    # Build for production
npm run lint     # Run linter

# Backend
npm run dev      # Start with ts-node
npm run build    # Compile TypeScript
npm start        # Run compiled JS

# Docker
docker-compose up     # Start all services
docker-compose down   # Stop all services
docker-compose logs   # View logs
```

---

## 🤝 Contributing

1. Create a feature branch: `git checkout -b feature/your-feature`
2. Make changes and commit: `git commit -m 'Add feature'`
3. Push: `git push origin feature/your-feature`
4. Create Pull Request

---

## 📞 Support

- GitHub Issues: https://github.com/pallalakshmidevi600-oss/Cric-pulse/issues
- Discussions: https://github.com/pallalakshmidevi600-oss/Cric-pulse/discussions

---

**Happy Coding! ⚡🏏**
