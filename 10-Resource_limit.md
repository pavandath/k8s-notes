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

# Quick Kubernetes Memory Notes

## 🚨 Memory Limit Problem
* **Scenario:** App needs 1GB → You give 512MB limit → **Container gets killed.**
* **Impact:** Killed container = **No service** = **502/504 errors** for users.

---

## 🔍 How to Spot
* **Behavior:** Pod restarting constantly.
* **Status:** `OOMKilled` in pod events or container status.
* **User Experience:** Users see "502 Bad Gateway" or "504 Gateway Timeout."
* **Command:** Run `kubectl describe pod <pod-name>` and look at the `Last State` section.

---

## 💡 Solution
* **Right-sizing:** Monitor app → Observe real-world usage → Set limit higher than peak usage.
* **Example:** If app uses 1GB at peak → Set **1.2GB - 1.5GB** limit.
* **Automation:** Use a Vertical Pod Autoscaler (VPA) if you want K8s to recommend these values for you.

---

## ⚠️ Golden Rule
> **Low memory limit = Container killed = 502 errors.**

* Always leave a **20-30% buffer** above what the app actually consumes to handle unexpected spikes or traffic bursts.


