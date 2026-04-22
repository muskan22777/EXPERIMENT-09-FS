Experiment 9: Containerizing a Full-Stack Application using Docker & Docker Compose
📌 Objective

To containerize and run a complete full-stack application consisting of:

Spring Boot backend
React frontend
MySQL database

using Docker and Docker Compose, and verify that the application runs successfully through a browser.

🛠 Technologies Used
Docker
Docker Compose
Spring Boot
React.js
MySQL
Maven
Nginx (for serving frontend)
📂 Project Structure
fullstack-docker-exp/
│
├── backend/
│   ├── Dockerfile
│   └── Spring Boot Application
│
├── frontend/
│   ├── Dockerfile
│   └── React Application
│
├── docker-compose.yml
└── README.md
⚙️ Backend Containerization (Spring Boot)

A Dockerfile was created to:

build the project using Maven
generate the executable JAR file
run the application inside a container

Example:

FROM maven:3.9.6-eclipse-temurin-17 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jdk
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8081
ENTRYPOINT ["java","-jar","app.jar"]
⚙️ Frontend Containerization (React + Nginx)

The frontend Dockerfile:

builds React app
creates production build
serves using Nginx

Example:

FROM node:20 AS build
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 3000
CMD ["nginx", "-g", "daemon off;"]
⚙️ Docker Compose Setup

Docker Compose was used to run:

frontend container
backend container
MySQL container

Example:

version: "3.8"

services:

  mysql-db:
    image: mysql:8
    container_name: mysql-db
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: userdb
    ports:
      - "3307:3306"

  backend:
    build: ./backend/backend
    container_name: spring-backend
    ports:
      - "8081:8081"
    depends_on:
      - mysql-db

  frontend:
    build: ./frontend
    container_name: react-frontend
    ports:
      - "3000:80"
    depends_on:
      - backend
▶️ Steps to Run the Application
Step 1: Clone repository
git clone <repo-link>
Step 2: Navigate to project directory
cd fullstack-docker-exp
Step 3: Build and start containers
docker compose up --build
🌐 Application Access

Frontend:

http://localhost:3000

Backend API:

http://localhost:8081/api/users
✅ Output Verification

The application was successfully verified by:

Running all containers simultaneously
Accessing frontend via browser
Adding users through UI
Storing data in MySQL database
Fetching stored users via backend API
🎯 Conclusion

In this experiment, a complete full-stack application was successfully containerized using Docker. Docker Compose was used to orchestrate frontend, backend, and database services with proper networking and environment configuration. The application ran successfully inside containers and was accessible through the browser.
