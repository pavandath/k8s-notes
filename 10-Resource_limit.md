## Kubernetes Resource Limits
#### 📌 Core Concept
- Resource limits in Kubernetes control how much CPU and memory containers can use in a Pod.

## 🔧 Two Key Parts
###1. Requests
- What it is: Minimum resources the container needs to run

- Used for: Scheduling Pods to nodes

#### What happens:

- Kubernetes reserves this amount for your container

- If no node has enough resources → Pod stays in Pending state

- This is your guaranteed resources

### 2. Limits
- What it is: Maximum resources the container can use

#### What happens when exceeded:

- CPU limit exceeded → Container gets slowed down (throttled)

- Memory limit exceeded → Container gets killed and restarts (OOMKilled)

### 📊 How to Measure Resources
#### Memory
- Measured in bytes

- Use these units:

- Mi = Megabytes (common size: 256Mi, 512Mi, 1Gi)

- Gi = Gigabytes

#### CPU
- Measured in CPU cores:

- 1 = One full CPU core

- 0.5 = Half a CPU core

- 500m = 0.5 CPU (500 millicores)

- 100m = 0.1 CPU (100 millicores)

Quick Kubernetes Memory Notes
🚨 Memory Limit Problem
App needs 1GB → You give 512MB limit → Container gets killed

Killed container = No service = 502/504 errors for users

🔍 How to Spot
Pod restarting constantly

"OOMKilled" in pod events

Users see "502 Bad Gateway"

💡 Solution
Set correct limits: Monitor app → See real usage → Set limit higher

Example: App uses 1GB → Set 1.2-1.5GB limit

Check with: kubectl describe pod (look for OOMKilled)

⚠️ Golden Rule
Low memory limit = Container killed = 502 errors

Always leave 20-30% buffer above what app actually uses


