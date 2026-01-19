Kubernetes Resource Limits
📌 Core Concept
Resource limits in Kubernetes control how much CPU and memory containers can use in a Pod.

🔧 Two Key Parts
1. Requests
What it is: Minimum resources the container needs to run

Used for: Scheduling Pods to nodes

What happens:

Kubernetes reserves this amount for your container

If no node has enough resources → Pod stays in Pending state

This is your guaranteed resources

2. Limits
What it is: Maximum resources the container can use

What happens when exceeded:

CPU limit exceeded → Container gets slowed down (throttled)

Memory limit exceeded → Container gets killed and restarts (OOMKilled)

📊 How to Measure Resources
Memory
Measured in bytes

Use these units:

Mi = Megabytes (common size: 256Mi, 512Mi, 1Gi)

Gi = Gigabytes

CPU
Measured in CPU cores:

1 = One full CPU core

0.5 = Half a CPU core

500m = 0.5 CPU (500 millicores)

100m = 0.1 CPU (100 millicores)

