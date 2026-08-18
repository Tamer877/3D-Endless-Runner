# 3D-Endless-Runner
A run game developed with Unity.

# 3D Endless Runner - System Architecture and Development Plan

This project is developed to simulate how a 3D Endless Runner game operates with a full-fledged client-server architecture. To optimize the development and testing processes, the **Unity Client, IIS Web Server, SQL Database, and File Server are all configured on the same Virtual Machine (VM)**.

## 🏗️ System Architecture (3-Tier Architecture)

The project consists of 3 main server tiers and 1 client:

### 1. Unity Client (Frontend)
The interface the player interacts with.
* Communicates with the IIS via REST APIs.
* Listens to the File Server for asset updates and downloading new models.
* **Development Environment:** Unity 3D (C#)

### 2. IIS Server (Web & API Tier - Business Logic)
The backend server that handles HTTP/HTTPS REST API requests coming from mobile or desktop clients.
* **Login / Auth:** User authentication and JWT (JSON Web Token) generation.
* **Leaderboard & Score Management:** Validates end-of-game scores using basic anti-cheat logic and writes them to the database.
* **In-Game Store (Market):** Handles balance checks and executes the logic for purchasing new skins or characters.
* **Development Environment:** .NET Core Web API (C#)

### 3. SQL Server (Database Tier)
The layer where user data is stored securely in a relational manner.
* **Users:** `User_ID`, `Username`, `PasswordHash`, `CreatedDate`
* **PlayerStats:** `High Score`, `Total Gold`, `Total Diamonds`, `Matches Played`
* **Inventory:** List of character/skin IDs owned by the player.
* **Development Environment:** SQL Server

### 4. File Server (Asset & Patch Management)
The static file server where 3D models (FBX), Asset Bundles, textures, and audio files are stored.
* **Why a File Server?** Large media files are stored here to prevent database bloat. The database only stores the file path (e.g., `\\fileserver\assets\runner_v2.bundle`).
* **Versioning:** Files for different versions (patches) of the game are kept in separate folders. The client downloads only the missing or updated files.

---

## 📂 Project Folder Structure

The root directory structure of the repository is organized as follows to ensure decoupled development of the layers:

```text
📦 EndlessRunner-Architecture
 ┣ 📂 BackendAPI               # C# .NET REST API project hosted on IIS
 ┃ ┣ 📂 Controllers            # Login, Leaderboard, Store endpoints
 ┃ ┣ 📂 Models                 # C# representations of database tables
 ┃ ┗ 📂 Services               # Business logic (Score validation, purchase logic)
 ┣ 📂 Database                 # SQL Server files
 ┃ ┣ 📜 CreateTables.sql       # SQL scripts to create tables
 ┃ ┗ 📜 StoredProcedures.sql   # Required database procedures
 ┣ 📂 FileServer_Mock          # File Server directory structure (Example)
 ┃ ┣ 📂 v1.0                   # Base assets for the initial release
 ┃ ┗ 📂 v1.1                   # Patch assets (new characters, etc.)
 ┣ 📂 UnityClient              # Unity 3D project folder
 ┃ ┣ 📂 Scripts                # Game mechanics and logic
 ┃ ┣ 📂 Network                # HTTP Request scripts to communicate with the API
 ┃ ┗ 📂 AssetManagement        # Scripts to fetch bundles from the File Server
 ┗ 📜 README.md                # Project documentation (This file)
