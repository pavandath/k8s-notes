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

---

# Kubernetes Memory: 502 vs 504 Explained

## 🔄 What Actually Happens

### Scenario 1: Fast Death → 502 Bad Gateway
* **The Flow:** App starts → Immediately tries to allocate >512MB → **Killed instantly.**
* **The Result:** The Load Balancer (or Ingress) tries to send traffic to the Pod, finds no active process, and reports: "Backend dead."
* **Status:** `502 Bad Gateway`

---

### Scenario 2: Slow Death → 504 Gateway Timeout
* **The Flow:** App is running fine → Gradually leaks or consumes memory → Hits the 512MB limit **while processing an active request.**
* **The Result:** The process is killed mid-request. The connection hangs because the Load Balancer is waiting for a response that will never come.
* **Status:** `504 Gateway Timeout`

---

### Scenario 3: Mixed Errors
* **The Chaos:** You see a mix of both errors in your logs.
* **Why:** * Some users hit a Pod that just died (**502**).
    * Some users were in the middle of a transaction when the Pod hit its limit (**504**).
* **Symptom:** Users report "random" instability or intermittent errors.

---

## 🛠 Summary Table

| Error | Cause | Timing |
| :--- | :--- | :--- |
| **502** | Process is already dead or refused connection | Instant |
| **504** | Process was killed mid-execution or hung | Delayed (Timeout) |

