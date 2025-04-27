
# 🚀 Microservices Orchestration with Docker Swarm

## 📌 Overview  
This project demonstrates **Microservices Orchestration** using **Docker Swarm**, which is Docker's built-in tool for clustering and managing containers. The architecture consists of two services:

1. **API Gateway** - The entry point for all user requests.
2. **Backend Service** - Provides necessary data to the API Gateway.

With **Docker Swarm**, these services will be deployed, managed, scaled, and updated seamlessly.

---

## 🛠 Prerequisites  
Before getting started, ensure the following tools are installed on your system:

- **Docker** (Latest version)
- **Docker Swarm** initialized (`docker swarm init`)
- **Python** (for creating microservices)

---

## 📂 Project Structure  
The project follows this structure:
```
📂 Microservices-Docker-Swarm/
│── backend/
│   ├── backend.py        # Code for Backend service
│   ├── Dockerfile        # Dockerfile for Backend
│── api_gateway/
│   ├── api_gateway.py    # Code for API Gateway
│   ├── Dockerfile        # Dockerfile for API Gateway
│── docker-compose.yml    # Definition for Swarm stack
```

---

## 🖥️ Microservices Development  

### 1️⃣ Backend Service  
#### 📜 `backend.py`
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Greetings from the Backend Service!"

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5000)
```
#### 📜 Dockerfile (Backend)
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY backend.py /app
RUN pip install flask
CMD ["python", "backend.py"]
```

---

### 2️⃣ API Gateway  
#### 📜 `api_gateway.py`
```python
from flask import Flask
import requests

app = Flask(__name__)

@app.route('/')
def hello():
    backend_response = requests.get('http://backend-service:5000')
    return f"API Gateway: {backend_response.text}"

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=8080)
```
#### 📜 Dockerfile (API Gateway)
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY api_gateway.py /app
RUN pip install flask requests
CMD ["python", "api_gateway.py"]
```

---

## 🐳 Building Docker Images  
To build the Docker images for both services, navigate to the project directory and run the following commands:
```bash
# Build the backend service
docker build -t backend-service ./backend

# Build the API Gateway
docker build -t api-gateway ./api_gateway
```

---

## 🏗️ Deploying with Docker Swarm  

### 1️⃣ Initialize Docker Swarm  
```bash
docker swarm init
```
This will set your machine as a Swarm Manager.

### 2️⃣ Create the Docker Swarm Stack  
#### 📜 `docker-compose.yml`
```yaml
version: '3.7'
services:
  backend-service:
    image: backend-service
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
    networks:
      - app-network
    ports:
      - "5000:5000"

  api-gateway:
    image: api-gateway
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
    networks:
      - app-network
    ports:
      - "8080:8080"
    depends_on:
      - backend-service

networks:
  app-network:
    driver: overlay
```

### 3️⃣ Deploy the Stack  
```bash
docker stack deploy -c docker-compose.yml microservices-stack
```
This will deploy the stack in your Swarm environment.

### 4️⃣ Verify Running Services  
```bash
docker stack services microservices-stack
```

---

## 🌐 Accessing the Application  
- **API Gateway**: [http://localhost:8080](http://localhost:8080)
- The API Gateway dynamically fetches data from the Backend Service.
- ![alt text](image-4.png)

To view logs for debugging:
```bash
docker service logs microservices-stack_api-gateway
```

---

## 📈 Scaling Services  
Scale the backend service up or down:
```bash
docker service scale microservices-stack_backend-service=5
```
This increases the number of replicas for the backend service to **5**.

---

## 🔄 Updating Services  
If there are changes in the backend service code, rebuild the image and deploy the updated version:
```bash
docker service update --image backend-service:latest microservices-stack_backend-service
```
This will update the backend service without downtime.

---

## ❌ Removing the Stack  
Once you're done testing, you can remove the stack:
```bash
docker stack rm microservices-stack
```
This will stop and remove all services from the stack.

---

## 🎯 Summary  
✔ Built **API Gateway** and **Backend Service** microservices.  
✔ Dockerized both services for containerization.  
✔ Defined a **Docker Compose for Swarm** to manage and deploy the services.  
✔ Deployed using **Docker Swarm** for scalability and fault tolerance.  
✔ Demonstrated **scaling** and **updating** the services.

---

## 🚀 Next Steps  
✅ **Deploy the stack to cloud platforms like AWS/GCP** using managed Swarm clusters.  
✅ **Integrate a database** (e.g., MySQL or MongoDB) for persistent storage.  
✅ **Add authentication and rate-limiting** features to the API Gateway.

⚡ **Happy Coding & Orchestrating!** 🐳🎯

--- 

