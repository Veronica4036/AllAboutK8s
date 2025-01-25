## What is a socket? Understanding with [The default docker CRI socket changed, in Kubernetes 1.24 and beyond](https://github.com/kubernetes-sigs/cri-tools/issues/868)

1. What is a Socket?
- A socket is a communication endpoint that allows two processes to communicate, either on the same machine (Unix/Linux sockets) or across a network (network sockets)
- In this context, we're talking about Unix Domain Sockets (UDS), which are files on the filesystem that enable inter-process communication (IPC)
- Think of it like a special file that acts as a "phone line" between different software components

```
Process A (e.g. Kubernetes)          Process B (e.g. Docker)
+-------------------+                +-----------------+
|                   |                |                 |
|  Wants to run     |                |  Actually runs  |
|  containers       |                |  containers     |
|                   |                |                 |
+--------+----------+                +-------+---------+
         |                                   |
         |           SOCKET                  |
         |        +----------+               |  
         +------->|          |<--------------+
                  | /var/run |
                  | /docker. |
                  | sock     |
                  +----------+
```

Think of a socket like a phone line:
a. Process A (Kubernetes) wants to tell Process B (Docker) "hey, start a container"
b. But processes can't directly talk to each other
c. They need a communication channel - this is what the socket is

Real-world analogy:
- Imagine two offices in different buildings
- They can't directly talk to each other
- They install a dedicated phone line between them
- The phone line is like the socket
- The phone number (location) would be like `/var/run/docker.sock`

What happens in practice:
```
a. Kubernetes: "I need to start a container"
   ↓
b. Writes message to socket (/var/run/docker.sock)
   ↓
c. Docker is listening to this socket
   ↓
d. Docker reads the message
   ↓
e. Docker executes the container
   ↓
f. Docker writes response back to socket
   ↓
g. Kubernetes reads the response
```

2. The Container Runtime Architecture:
```
Kubernetes --> CRI (Container Runtime Interface) --> Container Runtime (Docker/containerd/CRI-O)
     ^                    ^                                ^
     |                    |                                |
Manages clusters    Standardized API              Actually runs containers
```

3. Historical Context:
- Originally, Kubernetes had built-in Docker support via "dockershim"
- Dockershim was a built-in component that translated Kubernetes commands to Docker commands
- The socket `/var/run/dockershim.sock` was where this communication happened

4. The Change (Kubernetes 1.24):
- Kubernetes removed dockershim from its core code
- If users still wanted to use Docker, they needed to install "cri-dockerd" (a separate project)
- This meant the communication path (socket) changed from:
  - Old: `/var/run/dockershim.sock` (built into Kubernetes)
  - New: `/var/run/cri-dockerd.sock` (external adapter)

5. Why This Matters:
- Tools like `crictl` (a debugging tool for container runtimes) need to know where to find the runtime
- They do this by connecting to these sockets
- When the socket location changed, these tools needed updating

6. Real-world Impact:
```
Before:
Kubernetes → dockershim (internal) → Docker
           ↓
   /var/run/dockershim.sock

After:
Kubernetes → cri-dockerd (external) → Docker
           ↓
   /var/run/cri-dockerd.sock
```

7. Solutions:
a. For containerd users:
```bash
crictl config --set runtime-endpoint=unix:///run/containerd/containerd.sock
```

b. For Docker users with cri-dockerd:
```bash
crictl config --set runtime-endpoint=unix:///var/run/cri-dockerd.sock
```

8. Why The Change Happened:
- Kubernetes wanted to simplify its codebase
- Docker isn't CRI-compatible by default
- Containerd (which Docker actually uses under the hood) is more lightweight and CRI-compatible
- This change encouraged users to move to more kubernetes-native runtimes

This change represents Kubernetes' evolution toward more standardized and maintainable container runtime interfaces, though it required some adaptation from users and tools in the ecosystem.

The issue in #868 was about this "phone line" moving to a new location:
- Old location: `/var/run/dockershim.sock`
- New location: `/var/run/cri-dockerd.sock`

So tools needed to be updated with the new "phone number" to call Docker at its new location.
