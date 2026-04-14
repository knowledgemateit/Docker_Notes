# 🌐 helloworld-devops-pipeline

> A Java Servlet application demonstrating a full local DevOps pipeline using **Maven → SonarQube → Nexus → Tomcat**, all orchestrated with Docker Compose.

---

## 🏗️ Architecture Overview

```
┌──────────────────────────────────────────────────────────────────────┐
│                        Developer Machine                             │
│                                                                      │
│   src/main/java/HelloWorldServlet.java                               │
│              │                                                       │
│              ▼                                                       │
│   ┌─────────────────┐                                                │
│   │  Maven (Docker) │  mvn clean package / sonar:sonar / deploy     │
│   └────────┬────────┘                                                │
│            │                                                         │
│   ┌────────▼──────────────────────────────────────────────────┐     │
│   │                   Docker Compose Network                  │     │
│   │                                                           │     │
│   │  ┌─────────────┐  ┌──────────────┐  ┌────────────────┐  │     │
│   │  │  SonarQube  │  │    Nexus     │  │    Tomcat      │  │     │
│   │  │  :9000      │  │   :8081      │  │    :8080       │  │     │
│   │  │             │  │              │  │                │  │     │
│   │  │ Code        │  │  Artifact    │  │  Serves the    │  │     │
│   │  │ Quality     │  │  Repository  │  │  WAR file      │  │     │
│   │  │ Analysis    │  │  (WAR store) │  │  live          │  │     │
│   │  └─────────────┘  └──────────────┘  └────────────────┘  │     │
│   └───────────────────────────────────────────────────────────┘     │
└──────────────────────────────────────────────────────────────────────┘

Pipeline Flow:
  Code  →  Build WAR  →  Analyze (Sonar)  →  Deploy (Nexus)  →  Serve (Tomcat)
```

---

## 🧰 Tech Stack

| Tool | Role | Port |
|---|---|---|
| **Maven** | Build & dependency management | — |
| **SonarQube** | Static code analysis & quality gate | `9000` |
| **Nexus** | Artifact repository (WAR storage) | `8081` |
| **Tomcat** | Java web server (WAR deployment) | `8080` |
| **Docker Compose** | Orchestrates all services locally | — |

---

## 📋 Prerequisites

- [Docker](https://www.docker.com/) & Docker Compose installed
- No local Maven or Java installation needed — Maven runs inside Docker

---

## 🚀 Quick Start

### Step 1 – Start All Services

```bash
docker-compose up -d
```

Wait ~60 seconds for SonarQube and Nexus to fully initialize before proceeding.

---

## 🗄️ Phase 1: Nexus Setup (One-Time)

### 1. Access Nexus
Open `http://localhost:8081` and log in:

```
Username: admin
Password: admin123
```

### 2. Create Repositories
Create two **Maven hosted** repositories:

| Repository Name | Type |
|---|---|
| `maven-snapshots` | Maven2 (hosted) – Snapshot |
| `maven-releases` | Maven2 (hosted) – Release |

### 3. Configure Maven Credentials
Add the following to your `~/.m2/settings.xml`:

```xml
<servers>
  <server>
    <id>nexus</id>
    <username>admin</username>
    <password>admin123</password>
  </server>
</servers>
```

---

## 🔨 Phase 2: Build, Analyze & Deploy

All Maven commands run inside a Docker container — no local Java/Maven needed.

### Step 1 – Build the WAR

```bash
docker run --rm \
  -v "$PWD":/usr/src/app \
  -w /usr/src/app \
  maven:3.8.7-openjdk-17 \
  mvn clean package
```

> Output: `target/helloworld-servlet.war`

---

### Step 2 – SonarQube Code Analysis

```bash
docker run --rm \
  -v "$PWD":/usr/src/app \
  -w /usr/src/app \
  maven:3.8.7-openjdk-17 \
  mvn sonar:sonar -Dsonar.host.url=http://localhost:9000
```

> View the report at: `http://localhost:9000`

---

### Step 3 – Deploy Artifact to Nexus

```bash
docker run --rm \
  -v "$PWD":/usr/src/app \
  -w /usr/src/app \
  maven:3.8.7-openjdk-17 \
  mvn deploy
```

> The WAR is uploaded to the Nexus repository at `http://localhost:8081`

---

### Step 4 – Deploy to Tomcat

Tomcat automatically maps `./target/` as its `webapps/` directory via Docker Compose volume.
The WAR is **redeployed automatically** — no manual step needed.

---

## ✅ Test the Application

Open your browser and go to:

```
http://localhost:8080/helloworld-servlet/hello
```

**Expected Response:**

```
Hello World via Maven, Nexus, SonarQube, Tomcat!
```

---

## 📂 Project Structure

```
helloworld-devops-pipeline/
├── src/
│   └── main/
│       ├── java/
│       │   └── HelloWorldServlet.java
│       └── webapp/
│           └── WEB-INF/
│               └── web.xml
├── target/                      ← Generated WAR (auto-deployed to Tomcat)
│   └── helloworld-servlet.war
├── docker-compose.yml           ← Orchestrates all services
├── pom.xml                      ← Maven project config
└── README.md
```

---

## 🌐 Service URLs

| Service | URL | Credentials |
|---|---|---|
| **App (Tomcat)** | `http://localhost:8080/helloworld-servlet/hello` | — |
| **SonarQube** | `http://localhost:9000` | `admin / admin` |
| **Nexus** | `http://localhost:8081` | `admin / admin123` |

---

## ⚠️ Troubleshooting

| Problem | Cause | Fix |
|---|---|---|
| SonarQube not reachable | Container still starting | Wait 60s after `docker-compose up -d` |
| Nexus 401 Unauthorized | Credentials not in `settings.xml` | Add the `<server>` block to `~/.m2/settings.xml` |
| WAR not deploying to Tomcat | Volume mapping issue | Ensure `./target` is mounted correctly in `docker-compose.yml` |
| `mvn deploy` fails | Nexus repos not created | Complete the Nexus one-time setup first |
| App returns 404 | WAR not yet extracted | Wait 10s after build, then refresh |

---

## 🔄 Full Pipeline at a Glance

```
git clone / edit code
        │
        ▼
mvn clean package       →   target/*.war
        │
        ▼
mvn sonar:sonar         →   Quality Report @ :9000
        │
        ▼
mvn deploy              →   WAR stored in Nexus @ :8081
        │
        ▼
Tomcat auto-deploys     →   Live App @ :8080  🎉
```
