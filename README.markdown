# Evently

Evently is a lightweight, community-focused web platform for creating, managing, and attending small-scale events like game nights, study groups, and online meetups. Designed for micro-communities, Evently offers a simple alternative to Eventbrite, emphasizing free RSVPs, pre-event discussions, and intuitive event management.

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Running Locally](#running-locally)
- [Running with Docker](#running-with-docker)
- [Populating Sample Data](#populating-sample-data)
- [API Documentation](#api-documentation)
- [Testing](#testing)
- [Deployment](#deployment)
- [Contributing](#contributing)
- [License](#license)

## Features

Evently provides a streamlined experience for organizing and joining small events (<100 attendees). Key features include:

- **User Authentication & Roles**:
  - Regular users: Create, join, and manage their own events.
  - Admins: Moderate all events and users.
- **Event Creation & Management**:
  - Create, edit, or delete events with details like title, description, date/time, and location (physical or virtual).
  - Support for timezone handling and privacy settings (public/private).
- **RSVP System**:
  - Users request to join events; owners approve/decline.
  - Displays RSVP status (pending, approved, declined).
- **Comment System**:
  - Pre-event discussion for approved attendees, with moderation by owners/admins.
- **Event Status**:
  - Mark events as completed and view attendee lists.
- **Notifications**:
  - Frontend alerts for invites and RSVP updates, with dismissible UI.

*Note*: The current implementation includes a minimal homepage with event listing and RSVP button functionality (toast notifications). Full features like authentication and RSVP management require additional development.

## Tech Stack

- **Frontend**: Next.js (React-based, with App Router for SSR/SSG)
- **Backend**: Express (Node.js framework with REST API)
- **Database**: MongoDB
- **Styling**: Tailwind CSS for responsive, utility-first design
- **Authentication**: JWT (JSON Web Tokens), with optional OAuth (Google)
- **Other Tools**:
  - `date-fns` for timezone handling
  - `react-toastify` for notifications
  - `Jest` for testing
- **Containerization**: Docker and Docker Compose
- **Deployment**: Vercel (frontend), Heroku (backend)

## Project Structure

```
evently/
├── frontend/                 # Next.js frontend
│   ├── app/                  # Next.js App Router
│   ├── components/           # React components
│   ├── lib/                  # Utility functions
│   ├── public/               # Static assets
│   ├── package.json          # Frontend dependencies
│   ├── Dockerfile            # Docker configuration
│   └── ...
├── backend/                  # Express backend
│   ├── routes/               # API routes
│   ├── models/               # MongoDB models
│   ├── middleware/           # Authentication middleware
│   ├── package.json          # Backend dependencies
│   ├── Dockerfile            # Docker configuration
│   └── ...
├── docker-compose.yml        # Docker Compose configuration
├── .env.example              # Environment variables template
└── README.md                 # Project documentation
```

## Prerequisites

- **Node.js**: v18 or higher
- **MongoDB**: Local installation or MongoDB Atlas
- **Docker**: Required for containerized setup
- **npm** or **yarn**: Package manager
- **Vercel CLI**: Optional, for frontend deployment
- **Heroku CLI**: Optional, for backend deployment

## Installation

1. **Clone the Repository** (or create the structure manually):
   ```bash
   mkdir evently
   cd evently
   mkdir frontend backend
   ```

2. **Set Up Frontend**:
   - Navigate to `frontend/`:
     ```bash
     cd frontend
     npm install
     ```
   - Create `frontend/.env` based on `.env.example`:
     ```env
     NEXT_PUBLIC_API_URL=http://localhost:5000/api
     ```

3. **Set Up Backend**:
   - Navigate to `backend/`:
     ```bash
     cd ../backend
     npm install
     ```
   - Create `backend/.env` based on `.env.example`:
     ```env
     PORT=5000
     MONGO_URI=mongodb://localhost:27017/evently
     JWT_SECRET=your_jwt_secret_key
     ```
   - Replace `your_jwt_secret_key` with a secure random string.

4. **Set Up MongoDB**:
   - Install MongoDB locally or use MongoDB Atlas.
   - Ensure MongoDB is running (`mongod`) or update `MONGO_URI` with your Atlas connection string.

## Running Locally

1. **Start MongoDB**:
   - Run `mongod` locally or ensure MongoDB Atlas is accessible.

2. **Start the Backend**:
   - In `backend/`:
     ```bash
     npm run dev
     ```
   - The backend will run on `http://localhost:5000`.

3. **Start the Frontend**:
   - In `frontend/`:
     ```bash
     npm run dev
     ```
   - The frontend will run on `http://localhost:3000`.

4. **Access the Application**:
   - Open `http://localhost:3000` in your browser.
   - You’ll see the Evently homepage with a list of public events (if any exist). Click the RSVP button to trigger a toast notification.

## Running with Docker

1. **Ensure Docker and Docker Compose are Installed**:
   - Install [Docker](https://www.docker.com/get-started) and [Docker Compose](https://docs.docker.com/compose/install/).

2. **Verify Files**:
   - Ensure `frontend/Dockerfile`, `backend/Dockerfile`, and `docker-compose.yml` are in place.

3. **Update Environment Variables**:
   - The `docker-compose.yml` includes environment variables. Ensure `JWT_SECRET` is set to a secure random string:
     ```yaml
     JWT_SECRET=your_jwt_secret_key
     ```

4. **Build and Run**:
   - In the `evently/` directory:
     ```bash
     docker-compose up --build
     ```
   - This starts:
     - Frontend on `http://localhost:3000`
     - Backend on `http://localhost:5000`
     - MongoDB on `mongodb://localhost:27017`

5. **Access the Application**:
   - Open `http://localhost:3000` to view the frontend.
   - Use `http://localhost:5000/api/events` to test the API.

6. **Stop Containers**:
   - Press `Ctrl+C`, then:
     ```bash
     docker-compose down
     ```
   - To remove MongoDB data:
     ```bash
     docker-compose down -v
     ```

## Populating Sample Data

To display events on the homepage, add sample data to the MongoDB `events` collection:

1. **Using MongoDB Shell**:
   ```javascript
   use evently;
   db.events.insertMany([
     {
       title: "Game Night",
       description: "Join us for a fun evening of board games!",
       date: new Date("2025-04-30T19:00:00Z"),
       location: "Online via Zoom",
       privacy: "public",
       owner: "mockUserId",
       attendees: [],
       rsvpRequests: [],
       comments: [],
       status: "active"
     },
     {
       title: "Study Group",
       description: "Collaborative study session for finals.",
       date: new Date("2025-05-01T14:00:00Z"),
       location: "Library, Room 101",
       privacy: "public",
       owner: "mockUserId",
       attendees: [],
       rsvpRequests: [],
       comments: [],
       status: "active"
     }
   ]);
   ```

2. **Using a POST Request**:
   ```bash
   curl -X POST http://localhost:5000/api/events \
   -H "Content-Type: application/json" \
   -d '{
     "title": "Game Night",
     "description": "Join us for a fun evening of board games!",
     "date": "2025-04-30T19:00:00Z",
     "location": "Online via Zoom",
     "privacy": "public"
   }'
   ```

3. **Seed on Startup**:
   - Modify `backend/server.js` to include a seeding function (see project documentation for details).

After adding data, refresh `http://localhost:3000` to see event cards.

## API Documentation

The backend exposes a REST API. Key endpoints include:

- **GET /api/events**: Fetch all public events.
  - Response: `[{ _id, title, description, date, location, privacy, ... }]`
- **POST /api/events**: Create a new event.
  - Body: `{ title, description, date, location, privacy }`
  - Response: Created event object

For full API documentation, consider adding Swagger in a production environment.

## Testing

1. **Frontend Tests**:
   - In `frontend/`:
     ```bash
     npm test
     ```
   - Uses Jest and React Testing Library.

2. **Backend Tests**:
   - In `backend/`:
     ```bash
     npm test
     ```
   - Uses Jest and Supertest.

*Note*: Test suites are not fully implemented in the minimal setup. Add test files to `frontend/__tests__` and `backend/__tests__` as needed.

## Deployment

- **Frontend (Vercel)**:
  1. Push `frontend/` to a Git repository.
  2. Connect to Vercel via the Vercel CLI or dashboard.
  3. Set `NEXT_PUBLIC_API_URL` to your backend URL (e.g., Heroku URL).

- **Backend (Heroku)**:
  1. Push `backend/` to a Git repository.
  2. Deploy to Heroku using the Heroku CLI.
  3. Set `MONGO_URI`, `JWT_SECRET`, and `PORT` in Heroku Config Vars.

## Contributing

1. Fork the repository.
2. Create a feature branch:
   ```bash
   git checkout -b feature/YourFeature
   ```
3. Commit changes:
   ```bash
   git commit -m "Add YourFeature"
   ```
4. Push to the branch:
   ```bash
   git push origin feature/YourFeature
   ```
5. Open a pull request.

## License

MIT License. See `LICENSE` file for details.