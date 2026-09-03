# Spotify Clone

A full-stack Spotify-inspired music streaming application with real-time chat, user activity tracking, and an admin dashboard for content management.

## Overview

This project includes:

- A React + TypeScript frontend with music playback controls, song/album browsing, and a responsive 3-column layout
- An Express backend with REST APIs for songs, albums, users, stats, and admin operations
- MongoDB with Mongoose for data persistence
- Socket.IO for real-time 1-on-1 chat, online status, and music activity tracking between users
- Clerk authentication with Google OAuth integration
- Cloudinary for audio and cover image file storage
- An admin dashboard for creating/deleting songs and albums with upload dialogs and aggregate stats

## Tech Stack

### Frontend
- React 18
- TypeScript
- Vite
- React Router DOM v6
- Zustand
- Tailwind CSS
- shadcn/ui (Radix UI primitives)
- Socket.IO Client
- Clerk React
- Axios
- React Hot Toast
- lucide-react
- react-resizable-panels

### Backend
- Node.js
- Express
- MongoDB + Mongoose
- Socket.IO
- Clerk Express
- Cloudinary
- express-fileupload
- CORS
- dotenv
- node-cron

## Project Structure

```text
spotify-modified-app/
├── backend/
│   ├── src/
│   │   ├── controller/        # API request handlers
│   │   ├── lib/               # DB, socket, cloudinary setup
│   │   ├── middleware/        # Auth + admin middleware
│   │   ├── models/            # Mongoose schemas
│   │   ├── routes/            # Express route definitions
│   │   ├── seeds/             # Initial data seed scripts
│   │   └── index.js           # Server entry point
│   └── package.json
├── frontend/
│   ├── public/                # Static assets (songs, covers, albums)
│   ├── src/
│   │   ├── components/        # Reusable UI components + skeletons
│   │   ├── layout/            # Main layout, sidebar, player, activity
│   │   ├── lib/               # axios + utility helpers
│   │   ├── pages/             # Page-level route components
│   │   ├── providers/         # Auth context provider
│   │   ├── stores/            # Zustand state stores
│   │   ├── types/             # TypeScript type definitions
│   │   └── App.tsx            # Root routes
│   └── package.json
├── package.json               # Root build scripts
└── README.md
```

## Features

### Music & Playback
- Browse Featured, Made For You, and Trending song sections
- View album details with all tracks
- Play, pause, skip next/previous song controls
- Volume slider and audio player with seek bar
- Album queue management

### Real-Time Features (Socket.IO)
- 1-on-1 instant messaging chat between users
- Online/offline user status indicators
- Live "now playing" activity updates (see what friends are currently listening to)
- Real-time message persistence via MongoDB

### Admin Dashboard
- Create new songs with audio + cover image uploads (Cloudinary)
- Create new albums with cover image uploads
- Delete songs and albums with cascading cleanup
- View aggregate stats: total songs, albums, users, and artists

### Authentication
- Clerk-powered sign-up and login with Google OAuth
- Protected API routes via Clerk middleware
- Admin role gate based on authorized email address

## Environment Variables

Create a `.env` file in the `backend/` folder:

```env
PORT=5000
MONGODB_URI=your_mongodb_connection_string
ADMIN_EMAIL=admin@example.com
NODE_ENV=development

CLOUDINARY_API_KEY=your_cloudinary_api_key
CLOUDINARY_API_SECRET=your_cloudinary_api_secret
CLOUDINARY_CLOUD_NAME=your_cloudinary_cloud_name

CLERK_PUBLISHABLE_KEY=your_clerk_publishable_key
CLERK_SECRET_KEY=your_clerk_secret_key
```

Create a `.env` file in the `frontend/` folder:

```env
VITE_CLERK_PUBLISHABLE_KEY=your_clerk_publishable_key
```

## Installation

### 1. Install backend dependencies

```bash
cd backend
npm install
```

### 2. Install frontend dependencies

```bash
cd ../frontend
npm install
```

## Running Locally

### Terminal 1 — Start the backend (from project root or backend folder)

```bash
cd backend
npm run dev
```

The backend runs on:

```text
http://localhost:5000
```

### Terminal 2 — Start the frontend

```bash
cd frontend
npm run dev
```

The frontend Vite app runs on:

```text
http://localhost:3000
```

### Seeding initial data (optional)

```bash
cd backend
npm run seed:songs
npm run seed:albums
```

## Available Scripts

### Root scripts

```bash
npm run build    # install deps in both folders and build frontend
npm start        # start the backend in production mode
```

### Backend scripts

```bash
npm run dev           # start backend with nodemon
npm start             # start backend with node
npm run seed:songs    # run the songs seed script
npm run seed:albums   # run the albums seed script
```

### Frontend scripts

```bash
npm run dev       # start Vite dev server
npm run build     # type-check + production build
npm run lint      # run ESLint
npm run preview   # preview production build locally
```

## API Routes

All `/api/*` routes except public album/song listing endpoints require a valid Clerk auth session.

### Auth
- `GET  /sso-callback`              — Clerk OAuth redirect callback
- `GET  /auth-callback`             — Post-OAuth sync handler

### Songs
- `GET  /api/songs/featured`        — Get featured songs
- `GET  /api/songs/made-for-you`   — Get made-for-you songs
- `GET  /api/songs/trending`       — Get trending songs
- `GET  /api/songs`                — Get all songs (admin only)

### Albums
- `GET  /api/albums`                — Get all albums
- `GET  /api/albums/:albumId`       — Get album by ID with songs

### Users & Chat
- `GET  /api/users`                 — Get all users
- `GET  /api/users/messages/:userId` — Get chat history with a user

### Stats (admin only)
- `GET  /api/stats`                 — Get platform aggregate stats

### Admin (all require admin)
- `GET    /api/admin/check`         — Verify current user is admin
- `POST   /api/admin/songs`         — Create a new song (with files)
- `DELETE /api/admin/songs/:id`     — Delete a song
- `POST   /api/admin/albums`        — Create a new album (with cover image)
- `DELETE /api/admin/albums/:id`    — Delete an album and its songs

## Production Build

```bash
npm run build
npm start
```

When `NODE_ENV=production`, the Express server automatically serves the compiled frontend assets from `frontend/dist`.

## Notes

- The frontend socket client connects to `http://localhost:5000` in development and uses the same origin in production.
- A cron job runs hourly to clean up temporary upload files in `backend/tmp`.
- Admin access is controlled by matching the signed-in user's primary email to `ADMIN_EMAIL`.
