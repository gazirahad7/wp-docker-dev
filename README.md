# WordPress Docker Development Environment

A lightweight Docker-based WordPress setup for plugin development, with MySQL, phpMyAdmin (auto-login), and WP-CLI.

## 🚀 Services Included

- WordPress (latest version)
- MySQL 8 (database)
- phpMyAdmin (auto-login enabled)
- WP-CLI (WordPress command-line interface)
- Local plugin development via volume mapping

## 📋 Prerequisites

Before you begin, ensure you have:

- Docker (v20.10+)
- Docker Compose (v2.0+)
- Git

```bash
docker --version
docker compose version
```

## 📁 Project Structure

```
wp-docker-dev/
├── docker-compose.yml          # Docker services configuration
├── .env-simple                 # Environment template (committed)
├── .env                        # Your local config (ignored by git)
├── .gitignore                  # Git ignore rules
├── .dockerignore               # Docker ignore rules
├── wp-data/                    # WordPress core (auto-generated, ignored)
├── db_data/                    # MySQL data (ignored)
└── plugins/                    # Your plugin development folder
    └── my-first-plugin/        # Example plugin (your repo)
```

## ⚙️ Setup (First Time)

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/wp-docker-dev.git
cd wp-docker-dev
```

### 2. Create Environment File

```bash
cp .env-simple .env
```

### 3. Configure Environment Variables

Edit `.env` file with your preferred settings:

**Database Configuration**

```env
MYSQL_ROOT_PASSWORD=your_secure_root_password
MYSQL_DATABASE=wordpress_db
MYSQL_USER=wp_user
MYSQL_PASSWORD=your_secure_password
```

**WordPress Configuration**

```env
WORDPRESS_DB_HOST=db:3306
WORDPRESS_DB_NAME=wordpress_db
WORDPRESS_DB_USER=wp_user
WORDPRESS_DB_PASSWORD=your_secure_password
```

**Ports**

```env
WORDPRESS_PORT=8080
PHPMYADMIN_PORT=8081
```

### 4. Start Docker Containers

```bash
docker compose up -d
```

### 5. Wait for Services to Initialize

## 🌐 Access Your Development Environment

| Service    | URL                   | Credentials           |
| ---------- | --------------------- | --------------------- |
| WordPress  | http://localhost:8080 | Set up on first visit |
| phpMyAdmin | http://localhost:8081 | Auto-login enabled    |

### First-Time WordPress Setup

1. Visit http://localhost:8080
2. Select your language
3. Create admin account
4. Complete installation

## 🧩 Plugin Development

### Adding Your Plugin

**Option 1: Create New Plugin**

```bash
cd plugins
mkdir my-first-plugin
cd my-first-plugin
# Create plugin files
```

**Option 2: Clone Existing Plugin**

```bash
cd plugins
git clone https://github.com/yourusername/my-plugin.git
```

**Option 3: Add Plugin as Git Submodule**

```bash
git submodule add https://github.com/yourusername/my-plugin.git plugins/my-plugin
```

Update submodules after cloning the repository:

```bash
git submodule update --init --recursive
```

To pull latest changes from all submodules:

```bash
git submodule foreach git pull origin main
```

To update a specific submodule:

```bash
cd plugins/my-plugin
git pull origin main
cd ../..
```

To remove a submodule:

```bash
git submodule deinit -f plugins/my-plugin
rm -rf .git/modules/plugins/my-plugin
git rm -f plugins/my-plugin
```

### Plugin Structure Example

```
plugins/my-first-plugin/
├── my-first-plugin.php         # Main plugin file
├── includes/                   # Core functionality
├── assets/                     # CSS, JS, images
└── README.md
```

### Activating Your Plugin

**Via WordPress Admin:**

1. Go to http://localhost:8080/wp-admin
2. Navigate to Plugins → Installed Plugins
3. Activate your plugin

**Via WP-CLI:**

```bash
docker compose run --rm wpcli plugin activate my-first-plugin
```

## 🛠 WP-CLI Usage

WP-CLI allows you to manage WordPress from the command line.

### Common Commands

**List all plugins:**

```bash
docker compose run --rm wpcli plugin list
```

**Install and activate a plugin:**

```bash
docker compose run --rm wpcli plugin install contact-form-7 --activate
```

**Database backup:**

```bash
docker compose run --rm wpcli db export backup.sql
```

**Database import:**

```bash
docker compose run --rm wpcli db import backup.sql
```

**Create a new admin user:**

```bash
docker compose run --rm wpcli user create newadmin admin@example.com --role=administrator --user_pass=password123
```

**Flush rewrite rules:**

```bash
docker compose run --rm wpcli rewrite flush
```

**Update WordPress core:**

```bash
docker compose run --rm wpcli core update
```

### User Management Examples

**Create Admin User with Custom Password:**

```bash
docker compose run --rm wpcli user create adminuser admin@yourdomain.com --role=administrator --user_pass=SecurePassword123!
```

**Create Editor User:**

```bash
docker compose run --rm wpcli user create editoruser editor@yourdomain.com --role=editor --user_pass=EditorPass456!
```

**Create Author User:**

```bash
docker compose run --rm wpcli user create authoruser author@yourdomain.com --role=author --user_pass=AuthorPass789!
```

**List all users:**

```bash
docker compose run --rm wpcli user list
```

**Update user password:**

```bash
docker compose run --rm wpcli user update adminuser --prompt=user_pass
```

**Delete a user:**

```bash
docker compose run --rm wpcli user delete username
```

**Change user role:**

```bash
docker compose run --rm wpcli user set-role username administrator
```

## 🐳 Docker Management

### View Container Status

```bash
docker compose ps
```

### View Logs

```bash
# All services
docker compose logs -f

# Specific service
docker compose logs -f wordpress
docker compose logs -f db
```

### Stop Containers

```bash
docker compose stop
```

### Start Stopped Containers

```bash
docker compose start
```

### Restart Containers

```bash
docker compose restart
```

### Remove Containers and Volumes

```bash
docker compose down

# This removes volumes including database data
docker compose down -v
```

## 🔧 Troubleshooting

### Container Won't Start

```bash
docker compose down
docker compose up -d --force-recreate
```

### Clear Docker Cache

```bash
docker compose down
docker system prune -a --volumes
docker compose up -d
```

## 🔐 Security Notes

### For Development Only

⚠️ This setup is for local development only. Do not use in production without:

- Strong passwords
- Proper firewall rules
- SSL certificates
- Security hardening

### Sensitive Files

These files contain secrets and are automatically ignored by Git:

- `.env` (your local environment variables)
- `wp-data/` (WordPress installation)
- `db_data/` (MySQL database files)

### What Gets Committed to Git

✅ **Committed:**

- `docker-compose.yml`
- `.env-simple` (template only)
- `.gitignore`
- `.dockerignore`
- Plugin code in `plugins/`
- This README

❌ **Not Committed:**

- `.env`
- `wp-data/`
- `db_data/`
- `*.log` files
- OS/editor files (`.DS_Store`, `.idea/`, etc.)

## 📦 Backup & Migration

### Database Backup

```bash
docker compose run --rm wpcli db export backup-$(date +%Y%m%d).sql
```

### Full Backup

```bash
# Database
docker compose run --rm wpcli db export backup.sql

# Uploads and plugins
tar -czf wp-content-backup.tar.gz wp-data/wp-content/
```

### Restore Database

```bash
docker compose run --rm wpcli db import backup.sql
```

### Restore Uploads

```bash
tar -xzf wp-content-backup.tar.gz
```

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 🙋 Support

If you encounter any issues:

- Check the [Troubleshooting](#troubleshooting) section
- Search existing [GitHub issues](https://github.com/yourusername/wp-docker-dev/issues)
- Create a new issue with:
  - Your Docker version
  - Your OS
  - Error messages
  - Steps to reproduce

## 🔗 Useful Links

- [WordPress Documentation](https://wordpress.org/support/)
- [WP-CLI Documentation](https://developer.wordpress.org/cli/commands/)
- [Docker Documentation](https://docs.docker.com/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
