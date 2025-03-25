# 🚀 Deploying a Streamlit App with PostgreSQL using Docker

This project showcases how to deploy a **Streamlit** application that connects to a **PostgreSQL** database using **Docker**. The setup includes creating a custom Docker network, running a PostgreSQL container, populating it with sample data, and deploying the Streamlit app to visualize the data.

---

## 📦 Prerequisites

- **Docker** installed ([Install Docker](https://docs.docker.com/get-docker/))
- Familiarity with Docker, PostgreSQL, and Python

---

## 📂 Project Structure

```bash
.
├── Dockerfile      # Dockerfile for the Streamlit application
├── stream.py       # Streamlit application script
└── README.md       # Project documentation
```

---

## 🛠️ Step 1: Create a Custom Docker Network

To allow seamless communication between the containers, create a dedicated Docker network:

```bash
docker network create my_network
```

---

## 🛠️ Step 2: Launch PostgreSQL Container

Start a PostgreSQL container with the required credentials:

```bash
docker run -d \
  --name postgres_container \
  --network my_network \
  -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=admin123 \
  -e POSTGRES_DB=mydatabase \
  -p 5432:5432 \
  postgres
```

---

## 🛠️ Step 3: Insert Sample Data into PostgreSQL

Access the PostgreSQL CLI inside the running container:

```bash
docker exec -it postgres_container psql -U admin -d mydatabase
```

Run the following SQL commands to create a table and add data:

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100) UNIQUE
);

INSERT INTO users (name, email) VALUES
('Alice Doe', 'alice@example.com'),
('Bob Smith', 'bob@example.com'),
('Charlie Brown', 'charlie@example.com');

SELECT * FROM users;
```

Exit PostgreSQL with `\q`.

---

## 🛠️ Step 4: Create the Streamlit App

Create a `stream.py` file with the following content:

```python
import streamlit as st
import psycopg2

def get_connection():
    return psycopg2.connect(
        dbname="mydatabase",
        user="admin",
        password="admin123",
        host="postgres_container",
        port="5432"
    )

st.title("📊 User Data Viewer")

try:
    conn = get_connection()
    cur = conn.cursor()
    cur.execute("SELECT * FROM users;")
    data = cur.fetchall()
    
    st.write("### 👥 Registered Users")
    for user in data:
        st.write(f"**ID:** {user[0]} | **Name:** {user[1]} | **Email:** {user[2]}")
    
    cur.close()
    conn.close()
except Exception as e:
    st.error(f"Connection Error: {e}")
```

---

## 🛠️ Step 5: Create a Dockerfile for Streamlit

Create a `Dockerfile` with the following content:

```dockerfile
FROM python:3.9
WORKDIR /app
COPY . .
RUN pip install --no-cache-dir streamlit psycopg2
EXPOSE 8501
CMD ["streamlit", "run", "stream.py", "--server.port=8501", "--server.address=0.0.0.0"]
```

---

## 🛠️ Step 6: Build and Run the Streamlit Container

### 🔨 Build the Docker Image

```bash
docker build -t streamlit_app .
```

### 🚀 Run the Streamlit Container

```bash
docker run -d \
  --name streamlit_container \
  --network my_network \
  -p 8501:8501 \
  streamlit_app
```

---

## 🌐 Access the Application

Open your browser and go to: [http://localhost:8501](http://localhost:8501)

You should see the Streamlit app displaying the user data from PostgreSQL.

---

## 🧹 Cleanup Resources

To stop and remove the containers and network:

```bash
docker stop postgres_container streamlit_container

docker rm postgres_container streamlit_container

docker network rm my_network
```

---

## 📜 License

This project is licensed under the **MIT License**. Feel free to modify and use it for your needs.

---

🚀 **Enjoy Building with Streamlit, PostgreSQL, and Docker!** 🚀

