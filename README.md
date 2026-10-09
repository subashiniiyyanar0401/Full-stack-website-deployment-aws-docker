# Full-Stack Website Deployment on AWS EC2 Using Docker

**Project Type:** DevOps Internship Project  
**Organization:** Uyartechnologies  
**Role:** Intern – DevOps Engineer

## 1. Project Overview

This project involved deploying and testing a full-stack photography website on an AWS EC2 instance running Amazon Linux 2023.

The application consisted of a React frontend, a Node.js and Express.js backend, and a PostgreSQL database. Docker was used to containerize the application services and connect them through a dedicated Docker network.

The project covered cloud infrastructure setup, database integration, frontend and backend deployment, administrator authentication, content management, file uploads, API testing, and troubleshooting.

**Confidentiality:** Client-identifying information and private application source code are excluded from this repository.

## 2. Objectives

- Deploy a full-stack web application on AWS EC2.
- Containerize application services using Docker.
- Configure communication between containers using a Docker network.
- Deploy PostgreSQL with persistent database storage.
- Integrate the frontend with backend REST APIs.
- Test administrator authentication and content management functionality.
- Verify file uploads and application responses.
- Troubleshoot deployment and configuration issues.

## 3. Technology Stack

| Component | Technology |
|---|---|
| Cloud Platform | AWS EC2 |
| Operating System | Amazon Linux 2023 |
| Frontend | React, TypeScript, Vite |
| Frontend Runtime | Node.js 22, serve |
| Backend | Node.js, Express.js, TypeScript |
| Database | PostgreSQL 15 |
| Containerization | Docker |
| Container Networking | Docker Network |
| Persistent Storage | Docker Volumes |
| Authentication | JWT, bcrypt |
| File Uploads | Multer |
| Testing | Linux CLI, Docker logs, curl |
| Version Control | Git, GitHub |

## 4. Application Architecture

The application was deployed using three primary containers.

```text
                 User / Browser
                       |
                       v
                React Frontend
                 Port 3008
                       |
                  REST API
                       |
                       v
              Node.js / Express
                 Port 5008
                       |
                       v
                 PostgreSQL
                  Port 5432

              AWS EC2 Instance
             Amazon Linux 2023

            Dedicated Docker Network
                       |
           Persistent Docker Volumes
```

### Architecture Explanation

1. The user accesses the website through a browser.
2. The React frontend displays the website interface.
3. The frontend communicates with the backend through HTTP API requests.
4. The Express.js backend processes application requests and interacts with PostgreSQL.
5. PostgreSQL stores application data.
6. Docker networking enables communication between containers.
7. Docker volumes preserve database data and uploaded files beyond the lifetime of individual containers.

**Note:** The frontend and backend ports shown above are container application ports. Actual public exposure depends on the Docker port mappings and EC2 security group configuration.

## 5. AWS EC2 Infrastructure Setup

The application was deployed on an AWS EC2 instance running Amazon Linux 2023.

### Activities Performed

- Connected to the EC2 instance using SSH.
- Verified the operating system and server environment.
- Installed or verified Docker.
- Started and enabled the Docker service.
- Cloned the application repositories.
- Organized the frontend and backend directories.
- Installed and verified the standalone Docker Compose CLI.

### Example Commands

Check the operating system:

```bash
cat /etc/os-release
```

Check Docker:

```bash
docker --version
```

Check Docker service status:

```bash
sudo systemctl status docker
```

Start Docker:

```bash
sudo systemctl start docker
```

Enable Docker at system startup:

```bash
sudo systemctl enable docker
```

Check Docker Compose:

```bash
docker-compose --version
```

These commands illustrate the infrastructure verification workflow. Commands should be adjusted to the server's actual configuration.

## 6. Docker Network Configuration

A dedicated Docker network was used to connect the application services.

Create the network:

```bash
docker network create website-network
```

Verify the network:

```bash
docker network ls
```

Inspect its configuration:

```bash
docker network inspect website-network
```

### Benefits

- Enables communication between connected containers.
- Allows services to use container names for network communication.
- Separates application networking from unrelated containers.
- Simplifies service configuration and troubleshooting.

The backend must use the PostgreSQL container's actual network name as the database hostname when both services are connected to the same Docker network.

## 7. PostgreSQL Database Deployment

PostgreSQL 15 was deployed as a Docker container.

### Configuration

| Setting | Value |
|---|---|
| Database Engine | PostgreSQL |
| Database Name | `Website-Database` |
| Container Name | `website-postgres` |
| Persistent Volume | `website-postgres-data` |
| Database Port | `5432` |

The database name remains an internal configuration value and is not a client name.

### Database Readiness Check

```bash
docker exec website-postgres \
  pg_isready -U postgres -d database name 
```

**Expected result:** PostgreSQL reports that it is accepting connections.

### Database Persistence

A Docker volume was configured to preserve database files across container restarts and recreation, provided the volume is retained.

List Docker volumes:

```bash
docker volume ls
```

Inspect the database volume:

```bash
docker volume inspect website-postgres-data
```

**Important:** Persistent volumes are not a replacement for database backups. Production deployments should include a backup and recovery plan.

## 8. Backend Deployment

The backend was developed using Node.js, Express.js, and TypeScript, with PostgreSQL providing persistent application data storage.

### Backend Configuration

| Setting | Value |
|---|---|
| Docker Image | `website-backend` |
| Container Name | `website-backend` |
| Application Port | `5008` |
| Database | PostgreSQL |
| File Upload Middleware | Multer |
| Upload Endpoint Path | `/uploads` |

### Backend Responsibilities

- Handle incoming API requests.
- Authenticate administrator login requests.
- Process content management operations.
- Communicate with PostgreSQL.
- Handle image and file uploads.
- Serve uploaded files through the configured uploads path.

### Backend API Verification

Test the home-content endpoint from the EC2 host:

```bash
curl -i http://localhost:5008/api/home-content
```

**Observed result during testing:** `HTTP/1.1 200 OK`

The endpoint returned JSON data containing home-page content.

A successful response verified that the tested endpoint was reachable and returning application data. It did not, by itself, prove that every backend endpoint was working correctly.

### Backend Logs

```bash
docker logs website-backend
```

Follow logs in real time:

```bash
docker logs -f website-backend
```

Logs were useful for investigating database connectivity, application startup, API requests, and runtime errors.

## 9. Frontend Deployment

The frontend was developed using React, TypeScript, and Vite.

A Docker build process generated production assets and packaged them into a container for serving the website.

### Frontend Configuration

| Setting | Value |
|---|---|
| Docker Image | `website-frontend` |
| Container Name | `website-frontend` |
| Application Port | `3008` |
| Build Tool | Vite |
| Production Assets | `dist/` |

### Build Process

Install dependencies:

```bash
npm install
```

Generate production assets:

```bash
npm run build
```

Build the Docker image:

```bash
docker build -t website-frontend .
```

### Frontend Verification

```bash
curl -I http://localhost:3008
```

**Observed result during testing:** `HTTP/1.1 200 OK`

The website was also opened in a browser to verify that the frontend loaded successfully.

### Frontend Logs

```bash
docker logs website-frontend
```

Logs can help identify startup errors and frontend serving issues.

## 10. Frontend and Backend Integration

The frontend used a Vite environment variable to configure the backend API base URL.

Example configuration:

```ini
VITE_API_URL=<configured-backend-api-base-url>
```

Vite environment variables prefixed with `VITE_` are generally embedded into the frontend bundle during the production build. Therefore, changes to this configuration require rebuilding the frontend assets and image.

### Integration Workflow

1. Configure the backend API base URL.
2. Build the frontend using the correct environment configuration.
3. Rebuild the frontend Docker image.
4. Start the frontend container with the intended port mapping and network.
5. Open the website in a browser.
6. Verify frontend API requests using browser developer tools.
7. Test the corresponding backend endpoints.

The administrator login component used the following endpoint:

```text
POST /api/admin/login
```

The frontend and backend must agree on the API URL, route, request format, and authentication response.

**Security note:** Browser-based frontend configuration is public to website visitors. Never place database passwords, private keys, or other secrets in Vite variables.

## 11. Admin Panel and Functional Testing

The administrator interface was tested after deployment.

The correct login route was identified by inspecting the frontend source code.

### Functional Test Results

| Test Case | Result |
|---|---|
| Frontend availability | Verified |
| Backend home-content API | Verified |
| Admin login | Verified |
| Admin authentication | Verified |
| Dashboard access | Verified |
| Add content | Verified |
| Edit content | Verified |
| Delete content | Verified |
| File upload | Verified |
| Image upload | Verified |

These results describe the functionality tested during the project. They should not be interpreted as a guarantee that all possible application scenarios have been tested.

### Authentication

JWT was used for administrator authentication, and bcrypt was used in administrator password handling.

Security considerations include:

- Store passwords as secure hashes rather than plaintext.
- Protect administrative endpoints with server-side authentication and authorization.
- Keep signing secrets outside source code.
- Avoid exposing credentials in screenshots, terminal output, or Git history.
- Use HTTPS for production traffic.

## 12. File Uploads and Persistent Storage

The backend used Multer to process uploaded files.

Uploaded files were served through the configured `/uploads` path, and a Docker volume was configured for persistent upload storage.

### Configuration

| Setting | Value |
|---|---|
| Upload Middleware | Multer |
| Upload Path | `/uploads` |
| Persistent Volume | `website-uploads` |

### Verification

The upload workflow was tested through the administrator interface.

The test involved uploading a file and verifying that the application processed the upload as expected.

Persistent storage helps prevent uploaded files from being lost when a container is replaced, provided the volume is mounted correctly and the application writes to the mounted location.

For production, file uploads should also include file-type and size validation, access control, and safe filename handling.

## 13. Troubleshooting and Resolutions

### Issue 1: PostgreSQL System Service Not Found

**Problem:** A PostgreSQL system service was unavailable on the EC2 host.

**Resolution:** PostgreSQL was deployed in a Docker container, and readiness was checked with `pg_isready`.

### Issue 2: Backend Database Connectivity

**Problem:** The backend needed to communicate with the PostgreSQL container.

**Resolution:** A dedicated Docker network was configured, and the backend database connection used the appropriate database container hostname.

### Issue 3: Frontend API Configuration

**Problem:** The frontend required the correct backend API base URL.

**Resolution:** The Vite environment variable was configured and the frontend image was rebuilt to incorporate the updated production configuration.

### Issue 4: Incorrect Admin Login Route

**Problem:** The initial login path did not match the actual administrator route.

**Resolution:** The frontend source code was inspected to identify the correct login route.

### Issue 5: Administrator Credentials

**Problem:** The initially seeded administrator password did not match the test password.

**Resolution:** The administrator account was verified, and test credentials were updated using bcrypt-based password handling. Login was then tested.

### Issue 6: Deployment Verification

**Problem:** The frontend, backend, database, and administrative functions needed separate verification.

**Resolution:** Docker status, application logs, HTTP responses, API requests, and browser checks were used to validate the deployment.

## 14. Useful Docker Commands

List running containers:

```bash
docker ps
```

List all containers:

```bash
docker ps -a
```

List images:

```bash
docker images
```

View backend logs:

```bash
docker logs website-backend
```

View frontend logs:

```bash
docker logs website-frontend
```

View database logs:

```bash
docker logs website-postgres
```

Inspect the Docker network:

```bash
docker network inspect website-network
```

Check the backend API:

```bash
curl -i http://localhost:5008/api/home-content
```

Check the frontend:

```bash
curl -I http://localhost:3008
```

**Note:** These commands assume that the containers use the generic names shown in this README. Adjust them to match the actual running resources.

## 15. Screenshots and Evidence

Create a `screenshots/` directory and add sanitized screenshots captured during the project.

Recommended directory structure:

```text
full-stack-website-deployment-aws-docker/
├── README.md
├── screenshots/
│   ├── ec2-docker-status.png
│   ├── containers-running.png
│   ├── website-homepage.png
│   ├── backend-api.png
│   ├── admin-login.png
│   ├── admin-dashboard.png
│   └── upload-test.png
└── docs/
    └── deployment-workflow.md
```

### Display Screenshots in the README

Docker containers:

```markdown
![Docker containers running](screenshots/containers-running.png)
```

Website homepage:

```markdown
![Website homepage](screenshots/website-homepage.png)
```

Backend API:

```markdown
![Backend API verification](screenshots/backend-api.png)
```

Administrator dashboard:

```markdown
![Administrator dashboard](screenshots/admin-dashboard.png)
```

Only add images that actually exist in the repository. Ensure screenshots do not expose passwords, tokens, private IP information when unnecessary, database connection strings, or confidential client details.

## 16. Key Learnings

This project provided hands-on experience with:

- AWS EC2 infrastructure and Linux administration.
- Docker image building and container lifecycle management.
- Docker networking and service communication.
- PostgreSQL container deployment and persistent volumes.
- React frontend production builds.
- Node.js and Express.js backend deployment.
- Frontend-to-backend REST API integration.
- Administrator authentication and content management.
- File upload handling and storage.
- Troubleshooting using Linux commands, Docker logs, and `curl`.
- Application testing and deployment documentation.

## 17. Deployment Flow

The deployment workflow followed for this full-stack website project is illustrated below.

```text
Source Code
     |
     v
Frontend Environment Configuration
     |
     v
Backend Environment Configuration
     |
     v
PostgreSQL Docker Container
     |
     v
Database Initialization
     |
     v
Backend Docker Image
     |
     v
Backend Docker Container
     |
     v
Backend API (Port 5008)
     |
     v
Frontend Dependency Installation
     |
     v
Production Build (npm run build)
     |
     v
Frontend Docker Image
     |
     v
Frontend Docker Container
     |
     v
Frontend (Port 3008)
     |
     v
Browser Access
     |
     v
Website Display
     |
     v
Admin Login
     |
     v
JWT Authentication
     |
     v
Content Management
(Add / Edit / Delete)
     |
     v
File and Image Uploads
     |
     v
Backend API Processing
     |
     v
PostgreSQL Database (Port 5432)
```

### Workflow Explanation

1. **Source Code:** Frontend and backend source code was obtained from the project repositories.
2. **Environment Configuration:** Frontend and backend environment variables were configured for application connectivity.
3. **Database Deployment:** PostgreSQL was started in a Docker container, and the database schema was initialized.
4. **Backend Deployment:** The backend Docker image was built and the container was started to serve API requests on port `5008`.
5. **Frontend Deployment:** Dependencies were installed, the production build was generated, and the frontend Docker image was built and started on port `3008`.
6. **Website Verification:** The deployed website was opened in a browser to verify frontend availability.
7. **Admin Authentication:** Administrator login and JWT-based authentication were tested.
8. **Content Management:** Add, edit, and delete operations were tested through the admin interface.
9. **File Uploads:** File and image upload functionality was tested through the application.
10. **Database Integration:** Backend requests interacted with PostgreSQL on port `5432` through the configured Docker network.

**Result:** The workflow documents the deployment and testing process for the frontend, backend, database, and administrative functionality.

These are proposed enhancements, not features claimed as already implemented.

## 18. Conclusion

This internship project strengthened my practical understanding of deploying a full-stack web application on AWS EC2 using Docker.

It provided hands-on exposure to containerization, Linux administration, database connectivity, frontend and backend integration, authentication testing, persistent storage, and troubleshooting.

The project demonstrates my developing skills in AWS cloud operations and DevOps practices, particularly application deployment, container networking, service verification, and technical documentation.

## 19. Confidentiality Notice

This repository contains sanitized technical documentation for professional portfolio and learning purposes.

It excludes client-identifying information and should not contain confidential source code, credentials, access tokens, private configuration values, or other restricted materials.

Only publish project details and evidence that you are authorized to share.
