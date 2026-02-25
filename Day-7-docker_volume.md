# Docker Volume – Complete Practical Guide

## 📌 What is Docker Volume?

Docker Volume is a persistent storage mechanism used to store data outside the container lifecycle.

Containers are **ephemeral (temporary)**.  
If a container is deleted, its internal data is lost.

Volumes solve this problem by storing data on the host machine.

---

# 🧠 Why We Use Docker Volumes?

- To prevent data loss
- To persist database data
- To store logs permanently
- To maintain data during container recreation
- To support CI/CD redeployments

---

# 🏗 Without Volume

Container → Data  
Delete Container → ❌ Data Lost  

---

# 🏗 With Volume

Container → Volume → Host Storage  
Delete Container → ✅ Data Safe  

---

# 🔹 Step-by-Step Practical

## 1️⃣ Create a Volume

```bash
docker volume create practice-volume
