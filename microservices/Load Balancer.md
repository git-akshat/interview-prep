# Load Balancer  
  
**Receives incoming traffic and distributes it across multiple backend servers/services** so no single instance gets overwhelmed.  
  
## ✅ Why Load Balancers Are Used  
* Prevent overload on a single server  
* Improve availability (no single point of failure)  
* Enable horizontal scaling (add/remove servers easily)  
* Perform health checks and remove unhealthy instances  
* Enable zero-downtime deployments  
  
## 🔑 What a Load Balancer Does Internally  
**1️⃣ Receives request**  
Client hits **one public endpoint**  
  
**2️⃣ Chooses a backend (routing algorithm)**  
Common strategies:  
* Round Robin  
* Least Connections  
* Weighted routing  
* IP hash  
  
**3️⃣ Health checks**  
Continuously pings servers:  
```

/health

```
If one fails → removed from rotation.  
  
**4️⃣ Forwards request**  
Sends traffic only to **healthy instances**.  
  
  
## ⭐ Real Interview One-Liner (memorize)  
A load balancer distributes incoming traffic across multiple service instances, performs health checks, and routes requests only to healthy nodes to achieve scalability, high availability, and fault tolerance.  
  
  
****NGINX****  
  
> **NGINX is a web server and reverse proxy that also provides Layer-7 (HTTP/HTTPS) and Layer-4 (TCP/UDP) load balancing.**  
>   
> **✅ What NGINX does as a load balancer**  
> 
> When placed in front of multiple servers/services:  
>   
> Client → NGINX → Service1  
> 
>                 Service2  
> 
>                 Service3  
> 
> NGINX will:  

* Distribute traffic (round-robin / least connections / hashing)  
* Perform health checks  
* Remove unhealthy backends  
* Support sticky sessions  
* Terminate SSL  
* Act as a reverse proxy  
> All of this = **load balancing + traffic management**.  
