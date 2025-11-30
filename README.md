# IoT Sensor Dashboard

A full-stack IoT monitoring application designed to visualize sensor data from ESP8266 devices. This project features a decoupled architecture with a modern Bootstrap 5 front-end (`public/`) and a secure PHP API back-end (`server/`).

## 📋 Features

  * **Dual Interface:**
      * **Admin Dashboard:** Comprehensive view for managing sensors and users.
      * **Local Dashboard:** Simplified view for specific sensor readings.
  * **Modern UI:** Built with **Bootstrap 5** and the Inter font, featuring a responsive design and an animated, translucent login card.
  * **Secure Authentication:** Implements `password_hash()` (Bcrypt). Includes an auto-migration system that upgrades legacy MD5 passwords to secure hashes upon the first successful login.
  * **API-First Design:** Front-end communicates via JSON APIs; no server-side HTML rendering or redirects.
  * **IoT Integration:** Dedicated endpoint (`api.php`) for ESP8266 devices to POST temperature and humidity data.

## 📂 Project Structure

The repository is organized to separate public assets from server logic:

```text
odisha/
├── public/                 # Client-side code (Browser accessible)
│   ├── assets/             # CSS and JS files
│   ├── login.html          # Entry point (Animated Auth)
│   ├── admin_dashboard.html
│   └── local_dashboard.html
├── server/                 # Server-side logic (API Endpoints)
│   ├── api.php             # ESP8266 Ingestion Endpoint
│   ├── login.php           # Auth Handler (JSON response)
│   ├── admin_dashboard.php # Admin Data Provider
│   └── ...
└── README.md
```

## 🛠️ Prerequisites

  * **Server Environment:** MAMP, XAMPP, or any LAMP stack.
  * **PHP:** Version 7.4 or higher.
  * **Database:** MySQL (or MariaDB).

## ⚙️ Installation & Setup

### 1\. Deploy Files

Copy the `odisha` folder into your server's document root:

  * **MAMP (macOS):** `/Applications/MAMP/htdocs/odisha`
  * **XAMPP (Windows):** `C:\xampp\htdocs\odisha`

### 2\. Database Configuration

Open **phpMyAdmin** or your MySQL client and execute the following SQL scripts to set up the required databases and tables.

**Step A: User Authentication**

```sql
CREATE DATABASE IF NOT EXISTS login_system;
USE login_system;

CREATE TABLE IF NOT EXISTS users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  username VARCHAR(100) UNIQUE NOT NULL,
  password VARCHAR(255) NOT NULL,
  role ENUM('admin','local') NOT NULL DEFAULT 'local'
);

-- Note: You must insert your first user manually or via a script using password_hash().
-- Example (Pseudo-code): INSERT INTO users ... VALUES ('admin', '$2y$10$hash...', 'admin');
```

**Step B: Sensor Data**

```sql
CREATE DATABASE IF NOT EXISTS sensor_data;
USE sensor_data;

CREATE TABLE IF NOT EXISTS sensor_id (
  id INT AUTO_INCREMENT PRIMARY KEY,
  lab_name VARCHAR(255) NOT NULL,
  esp8266_id INT NOT NULL,
  sensor_short_name VARCHAR(255) DEFAULT NULL,
  data_interval INT DEFAULT 60
);

CREATE TABLE IF NOT EXISTS readings (
  id INT AUTO_INCREMENT PRIMARY KEY,
  esp8266_id INT NOT NULL,
  temperature DOUBLE,
  humidity DOUBLE,
  recorded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 3\. Database Connection

Ensure your PHP files in `server/` are configured with the correct DB credentials (`localhost`, `root`, `root` or `password`).

## 🚀 Usage

### Accessing the Dashboard

1.  Start your MAMP/Apache server.
2.  Open your browser to:
    `http://localhost:8888/odisha/public/login.html`
    *(Adjust port `8888` if your server uses port `80`)*.
3.  Log in using your database credentials. You will be redirected to the Admin or Local dashboard based on your role.

### IoT Device Integration

Configure your ESP8266 to send data to the API endpoint.

**API Endpoint:**
`http://<YOUR_IP>:8888/odisha/server/api.php`

**cURL Test Example:**

```bash
curl -X POST -H "Content-Type: application/json" \
  -d '{"id":123, "temp":24.5, "rh":55.1}' \
  http://localhost:8888/odisha/server/api.php
```

## 🔒 Security Notes

  * **Password Hashing:** This system uses `password_verify()`. If you are importing an old database with MD5 passwords, the system will automatically upgrade them to Bcrypt hashes seamlessly upon login.
  * **Production Deployment:**
      * Disable `display_errors` in your `php.ini`.
      * Ensure the site is served over **HTTPS**.
      * Ideally, move the `server/` directory outside the public web root and update include paths for better security.

## 📜 License

This project is open-source and available for educational and personal use.
