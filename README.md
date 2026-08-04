<div align="center">

# 🗺️ TilePlanner

**A tile-based trip planning web application — diploma project inspired by xTiles**

[![React](https://img.shields.io/badge/React-18-61DAFB?style=flat-square&logo=react)](https://react.dev/)
[![Vite](https://img.shields.io/badge/Vite-4-646CFF?style=flat-square&logo=vite)](https://vitejs.dev/)
[![ASP.NET Core](https://img.shields.io/badge/ASP.NET_Core-7-512BD4?style=flat-square&logo=dotnet)](https://dotnet.microsoft.com/)
[![MongoDB](https://img.shields.io/badge/MongoDB-6-47A248?style=flat-square&logo=mongodb)](https://www.mongodb.com/)
[![Leaflet](https://img.shields.io/badge/Leaflet-1.9-199900?style=flat-square&logo=leaflet)](https://leafletjs.com/)

<br/>

> Organize your trips with freely positioned, resizable tiles.  
> Add maps, task lists, budgets, weather, notes and files — all on one canvas.

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Architecture](#-architecture)
- [Tile Types](#-tile-types)
- [Getting Started](#-getting-started)
- [Environment Variables](#-environment-variables)
- [API Reference](#-api-reference)
- [Project Structure](#-project-structure)

---

## 🔍 Overview

TilePlanner is a simplified derivative of [xTiles](https://xtiles.app), built as a **diploma project**.  
It focuses on **trip planning**: users create projects (workspaces) and populate them with freely placed, resizable information tiles — maps, checklists, budgets, notes, images and more.

The system supports three subscription tiers (BASIC / ADVANCED / FULL) with payments processed via **Braintree / PayPal**, and authentication via **JWT** or **Google OAuth**.

---

## ✨ Features

| Category | Details |
|---|---|
| **Auth** | Email/password login, Google OAuth, JWT tokens |
| **Projects** | Create multiple trip workspaces; each is a freeform canvas |
| **Tiles** | Freely drag and resize any tile on the canvas |
| **Map** | Interactive Leaflet map; add named points with planned visit dates |
| **Weather** | Current conditions + 5-day forecast via OpenWeatherMap for any map point |
| **Budget** | Track expenses by purpose and location; running total per tile |
| **Tasks** | To-do items with deadlines and completion status |
| **Notes** | Free-form rich text notes |
| **Files & Images** | Upload and display files; stored in MongoDB GridFS |
| **Calendar** | Built-in calendar component for date-based planning |
| **Notifications** | In-app notification system |
| **Subscriptions** | Three access tiers; payment via Braintree/PayPal (Sandbox) |
| **Responsive** | Separate Desktop and Mobile layouts for all screens |

---

## 🛠 Tech Stack

### Frontend

| Technology | Purpose |
|---|---|
| React 18 + Vite | UI framework and build tool |
| React Router v6 | Client-side routing |
| Axios | HTTP client |
| Leaflet / React-Leaflet | Interactive maps |
| react-google-places-autocomplete | Location search |
| react-draggable + react-resizable | Draggable/resizable tiles |
| react-calendar | Calendar component |
| React Hook Form | Form validation |
| @react-oauth/google | Google OAuth button |
| braintree-web-drop-in-react | Payment UI |
| MUI Icons | Icon set |
| react-responsive | Responsive layout (Desktop / Mobile) |
| CSS Modules + Animate.css | Styling |

### Backend

| Technology | Purpose |
|---|---|
| ASP.NET Core 7 Web API | REST API server |
| MongoDB + MongoDB.Driver | Primary database |
| MongoDB GridFS | Binary file storage (avatars, uploads) |
| JWT Bearer | Authentication tokens |
| Braintree SDK | Payment processing |
| Swagger / OpenAPI | API documentation |

---

## 🏗 Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Browser / Mobile                         │
│                                                                  │
│   React 18 (Vite)                                               │
│   ├── / ─────── Landing page (public)                          │
│   ├── /login ── Login (email + Google OAuth)                   │
│   ├── /registration                                             │
│   ├── /home ─── Dashboard: project list, profile, notifications│
│   ├── /project/:id ── Tile canvas (drag / resize)              │
│   └── /change_plan ── Subscription & Braintree payment UI      │
│                                                                  │
│   Services: item.service │ user.service │ weather.service       │
│             map.service  │ payment.service                      │
└────────────────────────────┬────────────────────────────────────┘
                             │ HTTPS / JSON (Axios)
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                    ASP.NET Core 7 REST API                       │
│                                                                  │
│   Controllers                                                    │
│   ├── /login  /register ── AuthorizationController             │
│   ├── /user/* ──────────── UserCRUDController                  │
│   ├── /item/* ──────────── ItemCRUDController                  │
│   ├── /file/* ──────────── FileCRUDController                  │
│   ├── /notify/* ────────── NotificationsController             │
│   ├── /role/* ──────────── RoleController                      │
│   └── /payment/* ───────── BrainTreePaymentController          │
│                                                                  │
│   Middleware: JWT Auth │ CORS │ Swagger                         │
└────────────────┬───────────────────────┬────────────────────────┘
                 │                       │
         ┌───────▼──────┐    ┌──────────▼────────┐
         │   MongoDB    │    │   Braintree API   │
         │  (TilePlanner│    │  (PayPal Sandbox) │
         │   database)  │    └───────────────────┘
         │  + GridFS    │
         └──────────────┘
```

---

## 🧩 Tile Types

Each tile on the canvas is a `BasicItem` document in MongoDB.  
The `Itemtype` enum defines what a tile renders and what fields it uses:

| Type | Description |
|---|---|
| `SCREEN` | A project workspace (the canvas itself) |
| `TEXT` | Plain text block |
| `URL` | Clickable link |
| `IMAGE` | Uploaded image, displayed inline |
| `FILE` | Attached file (stored in GridFS) |
| `TASK` | Single to-do item with deadline and `isDone` flag |
| `TASKLIST` | Collection of `TASK` items in one tile |
| `COORDINATE` | Interactive Leaflet map with named points; each point has coordinates, a planned visit date, and a `isVisited` flag |
| `BUDGET` | Expense tracker — list of `{ purpose, place, price }` entries with a running total |
| `NOTES` | Free-form multi-line notes |

All tiles share a common base — position (`TilePosX/Y`), size (`TileSizeX/Y`), background color, and background image.

---

## 🚀 Getting Started

### Prerequisites

- [.NET 7.0 SDK](https://dotnet.microsoft.com/en-us/download/dotnet/7.0)
- [MongoDB](https://www.mongodb.com/try/download/community) running on `localhost:27017`
- [Node.js 18+](https://nodejs.org/) and [Yarn](https://yarnpkg.com/)
- A free [OpenWeatherMap API key](https://openweathermap.org/api)
- (Optional) [Braintree Sandbox account](https://www.braintreepayments.com/sandbox) for payments

---

### Backend

```bash
cd TilePlanner_Server_RESTAPI/TilePlanner_Server_RESTAPI
```

Set your configuration in `appsettings.json` (see [Environment Variables](#-environment-variables)), then:

```bash
dotnet run
```

The API starts on `https://localhost:7029`.  
Swagger UI is available at `https://localhost:7029/swagger` in Development mode.

---

### Frontend

```bash
cd tileplanner-front
yarn install
```

Create a `.env` file in `tileplanner-front/`:

```env
VITE_APP_OPENWHEATER_API_KEY=your_openweathermap_key
```

Then start the dev server:

```bash
yarn dev
```

The app opens at `http://localhost:5173`.  
API calls are proxied to `https://localhost:7029` via the `proxy` field in `package.json`.

---

## ⚙️ Environment Variables

### Backend — `appsettings.json`

```json
{
  "ConnectionStrings": {
    "MongoDBConnection": "mongodb://localhost:27017"
  },
  "DataBaseName": "TilePlanner",
  "StorageFolder": "C:\\path\\to\\UserAvatars",
  "JWT": {
    "Issuer": "WayDo",
    "Audience": "WayDoClient",
    "Key": "your-secret-key-min-32-chars",
    "Lifetime": 10
  },
  "BraintreeGateway": {
    "Environment": "SANDBOX",
    "MerchantId": "your_merchant_id",
    "PublicKey": "your_public_key",
    "PrivateKey": "your_private_key"
  },
  "CORS": {
    "Origin": [ "any" ],
    "Method": [ "any" ],
    "Header": [ "any" ]
  }
}
```

### Frontend — `.env`

```env
VITE_APP_OPENWHEATER_API_KEY=your_openweathermap_api_key
```

---

## 📡 API Reference

> Full interactive documentation available via Swagger at `/swagger` when running in Development mode.

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/login` | Authenticate user, returns JWT + User ID |
| `POST` | `/register` | Create a new user account |
| `GET` | `/user/{id}` | Get user profile |
| `PUT` | `/user/{id}` | Update user profile |
| `DELETE` | `/user/{id}` | Soft-delete user |
| `GET` | `/item/{id}` | Get a single item (tile or project) |
| `GET` | `/items/{userId}` | Get all projects for a user |
| `GET` | `/items/children/{parentId}` | Get all tiles inside a project |
| `POST` | `/item` | Create a new tile or project |
| `PUT` | `/item/{id}` | Update tile content, position, or size |
| `DELETE` | `/item/{id}` | Soft-delete a tile |
| `POST` | `/file` | Upload a file (GridFS) |
| `GET` | `/file/{id}` | Download a file by ID |
| `GET` | `/notify/{userId}` | Get notifications for a user |
| `GET` | `/role/{userId}` | Get user's subscription role |
| `POST` | `/payment/checkout` | Process Braintree payment & upgrade role |

---

## 📁 Project Structure

```
TilePlanner/
├── TilePlanner_Server_RESTAPI/          # ASP.NET Core 7 Backend
│   └── TilePlanner_Server_RESTAPI/
│       ├── Auth/                        # JWT authentication logic
│       ├── BrainTreePayPalPayment/      # Braintree service + interface
│       ├── Controllers/                 # REST API controllers
│       │   ├── AuthorizationController
│       │   ├── ItemCRUDController
│       │   ├── UserCRUDController
│       │   ├── FileCRUDController
│       │   ├── NotificationsController
│       │   ├── RoleController
│       │   └── BrainTreePaymentController
│       ├── DBConnection/
│       │   └── MongoContext.cs          # All MongoDB operations
│       ├── ORM/                         # Data models
│       │   ├── BasicItem.cs             # Universal tile model
│       │   ├── ItemType.cs              # Tile type enum
│       │   ├── User.cs                  # User model
│       │   ├── Supplementary.cs         # DTOs and value types
│       │   ├── Notification.cs
│       │   └── Roles/
│       │       ├── AccessLevel.cs       # BASIC / ADVANCED / FULL
│       │       └── Role.cs
│       └── Program.cs                   # App configuration & startup
│
└── tileplanner-front/                   # React 18 Frontend (Vite)
    ├── public/                          # Static assets & SVG icons
    │   └── weather_icons/               # OpenWeatherMap icon set
    └── src/
        ├── components/
        │   └── screens/
        │       ├── authorize/           # Login & Registration (Desktop + Mobile)
        │       └── home/
        │           ├── home-item/       # Landing page
        │           ├── authorize-item/  # Dashboard (projects, profile, notifications)
        │           └── project-item/    # Tile canvas
        │               └── UI/
        │                   ├── map-tile-item/       # Leaflet map tile
        │                   ├── budget-tile-item/    # Budget tracker tile
        │                   ├── taskList-tile-item/  # Task list tile
        │                   ├── notes-tile-item/     # Notes tile
        │                   ├── image-tile-item/     # Image tile
        │                   └── resizable-component-item/ # Drag & resize wrapper
        └── services/
            ├── item.service.js          # Tile / project API calls
            ├── user.service.js          # User & auth API calls
            ├── weather.service.js       # OpenWeatherMap integration
            ├── map.service.js           # Map utilities
            └── payment.service.js       # Braintree payment calls
```

---

<div align="center">

Diploma project · React 18 · ASP.NET Core 7 · MongoDB · Leaflet · Braintree

</div>
