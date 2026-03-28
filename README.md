#  Jenkins Dynamic Docker Agent CI/CD

##  Project Overview
This project demonstrates a **Jenkins Master-Slave architecture** using **Docker-based dynamic agents** deployed across two AWS EC2 instances.

- Jenkins Master runs on one EC2 instance
- Docker daemon runs on another EC2 instance
- Agents (slaves) are created dynamically during job execution and destroyed after completion

---

##  Core Concept

This project implements **ephemeral agents**:

- Agent is **created (born)** when a pipeline starts  
- Agent executes the assigned job  
- Agent is **automatically destroyed (dies)** after execution  

This ensures **efficient resource utilization and scalability**

---

## Architecture

Jenkins Master (EC2)  
        ↓  
Docker Remote Host (EC2)  
        ↓  
Dynamic Docker Agent (Container)

---

##  Tech Stack

- Jenkins (CI/CD)
- Docker (Containerization)
- AWS EC2 (Infrastructure)
- Node.js (Sample Application)

---

##  Pipeline Workflow

1. Jenkins pipeline is triggered  
2. Jenkins connects to Docker host via remote API  
3. A new Docker container (agent) is created  
4. Job executes inside the container  
5. Container is automatically removed after completion  

---

## 📂 Project Structure

- `Dockerfile` → Defines application container  
- `app.js` → Sample Node.js application  
- `package.json` → Dependencies  
- `PROOF_OF_WORK/` → Screenshots and validation  

---

#  Challenges Faced & Solutions

## 1. Docker Agent Not Creating (Status 400 Error)
**Issue:** Jenkins failed to create containers via Docker  
**Cause:** Docker daemon was not properly exposed over TCP  
**Solution:**
- Configured `/etc/docker/daemon.json`
- Enabled Docker remote API on port `2375`
- Restarted Docker service  

---

## 2. Agent Stuck Offline
**Issue:** Containers were created but agents remained offline  
**Cause:** JNLP communication failure  
**Solution:**
- Enabled Jenkins inbound agent port (`50000`)
- Opened port in AWS security group  
- Verified connectivity using telnet  

---

## 3. Container Crashing Immediately (Exit Code 127)
**Issue:** Agent containers exited instantly  
**Cause:** Incorrect EntryPoint arguments  
**Solution:**
- Removed manual `${computer.jnlpmac}` arguments  
- Allowed Jenkins to handle agent startup internally  

---

## 4. Agent Unable to Connect to Jenkins
**Issue:** Containers couldn't reach Jenkins server  
**Cause:** Using public DNS instead of internal network  
**Solution:**
- Switched Jenkins URL to **private IP**
- Ensured both EC2 instances communicate within VPC  

---

## 5. Permission Denied Error (`/home/jenkins`)
**Issue:** Pipeline failed due to filesystem access  
**Cause:** Container user lacked write permissions  
**Solution:**
- Set container user to `root` in Jenkins configuration  

---

## 6. Agent Not Getting Destroyed After Build
**Issue:** Containers remained alive after job completion  
**Cause:** Idle timeout not configured properly  
**Solution:**
- Set `Idle timeout = 0`
- Enabled ephemeral agent behavior  

---

#  Key Learnings

- Configuring Jenkins with **Docker Cloud integration**
- Understanding **JNLP agent communication**
- Managing **Docker Remote API securely**
- Debugging real-world **CI/CD infrastructure issues**
- Handling **networking (public vs private IPs in AWS)**
- Implementing **ephemeral compute patterns**
- Importance of **logs and systematic debugging**

---

#  Outcome

✔ Dynamic agent provisioning  
✔ Ephemeral container lifecycle (born & die)  
✔ Fully functional Jenkins CI setup  
✔ Production-level debugging experience  

---

#  Future Enhancements

- Integrate GitHub Webhooks for auto-trigger  
- Push Docker images to Docker Hub  
- Deploy application using Kubernetes  
- Add monitoring (Prometheus + Grafana)  

---

#  Conclusion

This project demonstrates a real-world CI/CD setup using Jenkins and Docker, focusing on **dynamic scalability, automation, and efficient resource management**.

It also highlights hands-on experience in troubleshooting and resolving infrastructure-level issues.

Vaishnavi J P
Devops
