# service mesh in kubernetes using Istio
![image](https://github.com/user-attachments/assets/efe7786c-edf4-4bbd-8d3d-2229a003dbca)
When considering microservice without service mesh, every service can communicate with every other service and this commmunication happens in plain text. 

<br><br/>
![image](https://github.com/user-attachments/assets/0e711d86-af1c-41db-aa24-f182d36cbbeb)
Istiod imparts service discovery, configuration and certificate management. Communication configuration (which microservices are allowed to communicate and which services are not allowed to communicate), metrics, security and tracing will be taken care by sidecar proxy container. After implementation of service mesh using istio no services will be allowed to communicate directly it will be communicated through the **sidecar proxy envoy container** as shown in the diagram above.

<br><br/>
**Architecture diagram for Service mesh using istio is as shown below.**
![image](https://github.com/user-attachments/assets/44b43b0c-7478-47f0-8ab8-b9dd5b367f52)

```
Explanation of different components for istio.
1. Pilot, Pilot is responsible for traffic management and routing.
2. Citadel, Citadel is responsible for secure communication between the microservices.
3. Galley, Galley is responsible for configuration management.
4. Mixer, Mixer collects telemetry data.
```
