# community-lost-found
A full-stack MERN application that connects people who have lost items with those who have found them — featuring real-time notifications, location-based search, secure in-app messaging, and claim verification.
🎯 About The Project

Lost & Found is a full-stack web application that bridges the gap between people who have lost items and those who have found them — creating a trusted community-driven platform for item recovery.

Whether it's a wallet left on a bus, keys dropped at a park, or a phone forgotten at a café — Lost & Found makes it effortless to post, search, and reconnect lost items with their rightful owners through smart matching, real-time notifications, and secure communication.

🌟 Features
FeatureDescription📢 Post Lost / Found ItemsEasily report lost or found items with photos, location, and description🔎 Smart Search & FilterSearch by category, date, location, and keywords🗺️ Location MappingVisual map view to spot items near you using Leaflet/Google Maps🔔 Real-Time NotificationsInstant alerts when a matching item is posted💬 Secure MessagingIn-app chat between finders and owners — no personal info shared🔐 Auth & ProfilesJWT-based authentication with user dashboards✅ Claim VerificationMulti-step ownership verification before handoff📊 Admin DashboardManage reports, users, and flagged items

🛠️ Tech Stack
<div align="center">
LayerTechnologyFrontendReact.js · Tailwind CSS · AxiosBackendNode.js · Express.jsDatabaseMongoDB (Mongoose ODM)AuthenticationJWT · bcryptReal-timeSocket.ioStorageCloudinary (image uploads)MapsLeaflet.js / Google Maps APIDeploymentVercel (Frontend) · Render (Backend)
</div>

🏗️ System Architecture
┌─────────────────────────────────────────────────────────────┐
│                        CLIENT LAYER                         │
│              React.js + Tailwind CSS + Axios                │
└─────────────────────┬───────────────────────────────────────┘
                      │  HTTP / WebSocket
┌─────────────────────▼───────────────────────────────────────┐
│                       API GATEWAY                           │
│                    Express.js (REST API)                     │
│         Auth Middleware │ Rate Limiter │ CORS                │
└──────┬──────────────┬──────────────────┬────────────────────┘
       │              │                  │
┌──────▼───┐   ┌──────▼───┐    ┌────────▼────────┐
│  Auth    │   │  Items   │    │  Notifications  │
│  Service │   │  Service │    │  Service        │
└──────┬───┘   └──────┬───┘    └────────┬────────┘
       │              │                  │
┌──────▼──────────────▼──────────────────▼────────────────────┐
│                     DATABASE LAYER                          │
│                 MongoDB Atlas (Mongoose)                     │
│        Users │ Items │ Claims │ Messages │ Notifications    │
└─────────────────────────────────────────────────────────────┘

🔄 Application Flow
                ┌─────────────────┐
                │ User Visits App │
                └────────┬────────┘
                         │
                         ▼
                ┌─────────────────┐
                │ Registered User?│
                └──────┬─────┬────┘
                       │Yes  │No
                       │     │
                       ▼     ▼
              ┌───────────┐ ┌───────────┐
              │  Login    │ │ Sign Up   │
              └─────┬─────┘ └─────┬─────┘
                    │             │
                    └──────┬──────┘
                           ▼
                ┌─────────────────┐
                │ JWT Token Issued│
                └────────┬────────┘
                         │
                         ▼
                ┌─────────────────┐
                │ Select Action   │
                └───┬─────────┬───┘
                    │         │
                    ▼         ▼
          ┌─────────────┐ ┌─────────────┐
          │ Lost Item   │ │ Found Item  │
          │ Report      │ │ Report      │
          └──────┬──────┘ └──────┬──────┘
                 │               │
                 └──────┬────────┘
                        ▼
          ┌─────────────────────────┐
          │ Fill Form + Upload Photo│
          └───────────┬─────────────┘
                      │
                      ▼
          ┌─────────────────────────┐
          │ Save Data in Database   │
          └───────────┬─────────────┘
                      │
                      ▼
          ┌─────────────────────────┐
          │ Notify Matching Users   │
          └───────────┬─────────────┘
                      │
                      ▼
          ┌─────────────────────────┐
          │ Search / Browse Items   │
          └───────────┬─────────────┘
                      │
                      ▼
              ┌────────────────┐
              │ Match Found ?  │
              └──────┬────┬────┘
                     │Yes │No
                     │    │
                     ▼    ▼
        ┌─────────────────┐  Continue Search
        │ Claim Request   │
        └──────┬──────────┘
               │
               ▼
        ┌─────────────────┐
        │ Owner Verifies  │
        │ Identity        │
        └──────┬────┬─────┘
               │Yes │No
               │    │
               ▼    ▼
 ┌───────────────────┐ ┌──────────────────┐
 │ In-App Chat /     │ │ Request More     │
 │ Connect Users     │ │ Proof            │
 └─────────┬─────────┘ └──────────────────┘
           │
           ▼
 ┌───────────────────┐
 │ Mark as Resolved  │
 └───────────────────┘

🗃️ Database Schema
┌──────────────────────┐       ┌──────────────────────┐
│       USERS          │       │        ITEMS          │
├──────────────────────┤       ├──────────────────────┤
│ _id: ObjectId        │──┐    │ _id: ObjectId         │
│ name: String         │  │    │ title: String         │
│ email: String        │  └───►│ postedBy: ObjectId    │
│ passwordHash: String │       │ type: lost | found    │
│ avatar: String       │       │ category: String      │
│ createdAt: Date      │       │ description: String   │
└──────────────────────┘       │ images: [String]      │
                               │ location: GeoJSON     │
                               │ status: open|resolved │
┌──────────────────────┐       │ date: Date            │
│       CLAIMS         │       └──────────────────────┘
├──────────────────────┤
│ _id: ObjectId        │       ┌──────────────────────┐
│ itemId: ObjectId     │       │      MESSAGES         │
│ claimedBy: ObjectId  │       ├──────────────────────┤
│ proof: String        │       │ _id: ObjectId         │
│ status: String       │       │ sender: ObjectId      │
│ createdAt: Date      │       │ receiver: ObjectId    │
└──────────────────────┘       │ itemId: ObjectId      │
                               │ content: String       │
                               │ timestamp: Date       │
                               └──────────────────────┘

📁 Project Structure
Lost_Found/
│
├── 📂 client/                    # React Frontend
│   ├── 📂 src/
│   │   ├── 📂 components/        # Reusable UI components
│   │   │   ├── ItemCard.jsx
│   │   │   ├── SearchBar.jsx
│   │   │   ├── MapView.jsx
│   │   │   └── Navbar.jsx
│   │   ├── 📂 pages/             # Route-based pages
│   │   │   ├── Home.jsx
│   │   │   ├── PostItem.jsx
│   │   │   ├── ItemDetail.jsx
│   │   │   └── Dashboard.jsx
│   │   ├── 📂 context/           # Global state (AuthContext)
│   │   └── 📂 services/          # Axios API calls
│   └── package.json
│
├── 📂 server/                    # Node.js Backend
│   ├── 📂 models/                # Mongoose schemas
│   ├── 📂 routes/                # Express route handlers
│   ├── 📂 controllers/           # Business logic
│   ├── 📂 middleware/            # Auth, error handling
│   └── server.js
│
├── 📄 .env.example
├── 📄 README.md
└── 📄 package.json

🚀 Installation & Setup
Prerequisites

Node.js v18+
MongoDB (local or Atlas URI)
npm or yarn

Clone the Repository
bashgit clone https://github.com/YOUR_USERNAME/Lost_Found.git
cd Lost_Found
Backend Setup
bashcd server
npm install

# Create .env file
cp .env.example .env
# Fill in: MONGO_URI, JWT_SECRET, CLOUDINARY_KEY, etc.

npm run dev
Frontend Setup
bashcd client
npm install
npm start

The app will run at http://localhost:3000 with the API at http://localhost:5000


🔌 API Endpoints
MethodEndpointDescriptionAuthPOST/api/auth/registerRegister new user❌POST/api/auth/loginLogin & get JWT❌GET/api/itemsGet all items❌POST/api/itemsPost a lost/found item✅GET/api/items/:idGet single item❌PUT/api/items/:idUpdate item✅DELETE/api/items/:idDelete item✅POST/api/claims/:itemIdSubmit a claim✅GET/api/messages/:userIdGet messages✅

📈 Project Stats
📦 Total Commits:       50+
🧩 Components:          15+
📡 API Routes:          20+
🗃️  DB Collections:     5
⏱️  Build Time:         ~2 mins
📱 Responsive:          ✅ Mobile Friendly

🤝 Contributing
Contributions are what make open source amazing! Here's how:
bash# 1. Fork the repository
# 2. Create your feature branch
git checkout -b feature/AmazingFeature

# 3. Commit your changes
git commit -m "Add some AmazingFeature"

# 4. Push to the branch
git push origin feature/AmazingFeature

# 5. Open a Pull Request

📄 License
Distributed under the MIT License. See LICENSE for more information.

👨‍💻 Author
<div align="center">
Your Name
Show Image
Show Image
Show Image
</div>
