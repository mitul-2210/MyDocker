# 🚀 Docker Network Experiment  

This repository demonstrates how to create and test a custom Docker network (`net-bridge`) that enables seamless inter-container communication. The experiment showcases how containers interact within a user-defined bridge network.  

---  

## 🛠 Experiment Overview  

Docker provides powerful networking features that allow containers to communicate securely and efficiently. This experiment covers:  

1. **Creating a custom bridge network** for isolated communication.  
2. **Running multiple containers** within the network.  
3. **Inspecting and verifying connectivity** between containers.  
4. **Testing inter-container communication** using `ping` and name resolution.  

---  

## 🔧 Experiment Setup  

### **1️⃣ Create a Custom Docker Network**  
```sh
docker network create --driver bridge --subnet 172.20.0.0/16 --ip-range 172.20.240.0/20 net-bridge
```  

### **2️⃣ Run Containers and Attach to the Network**  

#### 🗄 **Run Database Container**  
```sh
docker run -itd --net=net-bridge --name=cont_database redis
```  

#### 🖥 **Run Server Container**  
```sh
docker run -dit --name server-A --network net-bridge busybox
```  

### **3️⃣ Inspect Network and Containers**  

#### 🔍 **Check Network Details**  
```sh
docker network inspect net-bridge
```  

#### 🔎 **Check Container Details**  
```sh
docker inspect cont_database
```  

#### 🌐 **Get Container IP Address**  
```sh
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' cont_database
```  

### **4️⃣ Testing Connectivity**  

#### 🖥 **Access Container Shell**  
```sh
docker exec -it server-A sh
```  

#### 🔗 **Ping Another Container by IP**  
```sh
ping 172.20.240.1  # Replace with actual container IP
```  

#### 🔗 **Ping Another Container by Name**  
```sh
ping cont_database
```  

---  

## 📌 Observations  

✅ The custom bridge network enables direct communication between containers.  

✅ Containers can interact using assigned IP addresses.  

✅ Name resolution may not always work with BusyBox due to its minimal nature.  

✅ Using `docker inspect` helps retrieve network details and assigned IPs.  

---  

## 🏁 Conclusion  

This experiment demonstrates Docker's networking capabilities, showing how inter-container communication works using a custom bridge network. Understanding Docker networking is essential for deploying microservices and containerized applications effectively.  

---  

## 📸 Docker Network Connectivity Test  

Below is an example of successful container communication:  

![Docker Ping Test](docker-pingtest.jpg)  

✅ The above image confirms that containers can communicate via ping.  

---  


