# Experiment 7: CI/CD using Jenkins, GitHub and Docker Hub

---

## Introduction

---

### What is Jenkins?

Jenkins is a **web-based GUI automation server** used to:

- Build applications
- Test code
- Deploy software

It provides:

- Dashboard (browser-based UI)
- Plugin ecosystem (GitHub, Docker, etc.)
- Pipeline as Code using `Jenkinsfile`

### What is CI/CD?

- **Continuous Integration (CI):** Code is automatically built and tested after each commit
- **Continuous Deployment (CD):** Built artifacts (Docker images) are automatically delivered/deployed

### Workflow Overview

```
Developer → GitHub → Webhook → Jenkins → Build → Docker Hub
```

### Prerequisites

**Create a repository on GitHub:**

```
my-app: https://github.com/Ronak25034/my-app.git
```

**Project Structure**

```
my-app/
├── app.py
├── requirements.txt
├── Dockerfile
├── Jenkinsfile
```

---

## Hands-On

---

**Step 1: Create `app.py`**

```bash
nano app.py
```

Paste this:

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from CI/CD Pipeline!, my sapid is 500118984"

app.run(host="0.0.0.0", port=80)
```

![Create Python File](./Images/1.png)

---

**Step 2: Create `requirements.txt`**

```bash
nano requirements.txt
```

Paste this:

```
flask
```

![Create Requirements](./Images/2.png)

---

**Step 3: Create `Dockerfile`**

```bash
nano Dockerfile
```

Paste this:

```dockerfile
FROM python:3.10-slim

WORKDIR /app
COPY . .

RUN pip install -r requirements.txt

EXPOSE 80
CMD ["python", "app.py"]
```

![Create Dockerfile](./Images/3.png)

---

**Step 4: Create `Jenkinsfile`**

```bash
nano Jenkinsfile
```

Paste this:

```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = "ronak2503/myapp"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_TOKEN')]) {
                    sh 'echo $DOCKER_TOKEN | docker login -u ronak2503 --password-stdin'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push $IMAGE_NAME:latest'
            }
        }
    }
}
```

![Create Jenkinsfile](./Images/4.png)

---

**Step 5: Create `docker-compose.yml`**

```yaml
version: '3.8'

services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins
    restart: always
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
    user: root

volumes:
  jenkins_home:
```

![Create compose file](./Images/5.png)

---

**Step 6: Start Up Compose**

```bash
docker-compose up -d
```

![Up Compose](./Images/6.png)

---

**Step 7: Exec Jenkins Container to get Password**

```bash
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

![Exec Container](./Images/7-a.png)

---

**Step 8: Login to Jenkins Dashboard on Browser**

```
http://localhost:8080
```

![Jenkins Dashboard](./Images/7-b.png)

---

**Step 9: Install Suggested Plugins**

![Install Plugins](./Images/7-c.png)

---

**Step 10: Plugins List**

![Plugins List](./Images/7-d.png)

---

**Step 11: Create Admin User**

![Create Admin User](./Images/9.png)

---

**Step 12: Admin Dashboard Interface**

![Admin Dashboard](./Images/10.png)

---

**Step 13: Add `DockerHub Token` in Credentials**

![Token](./Images/11.png)
![Token](./Images/12.png)
![Token](./Images/13.png)

---

**Step 14: Create a Repository in DockerHub**

![Repo](./Images/16.png)

---

**Step 15: Repository will be listed**

![Repo List](./Images/20.png)

---

**Step 16: Create Pipeline Job**

- New Item: Pipeline
- Name: `ci-cd-pipeline`
- Definition: Pipeline Script from SCM

![Pipeline Job](./Images/14.png)

---

**Step 17: Build Pipeline & Track Stages in Console Output**

![Console](./Images/15.png)

---

**Step 18: Build Status Success will be shown with Green Tick**

![Build Status](./Images/18.png)

---

**Step 19: Repo will be consisting Image Now!**

![DockerHub Repo](./Images/19.png)

---