# service mesh in kubernetes using Istio
![image](https://github.com/user-attachments/assets/efe7786c-edf4-4bbd-8d3d-2229a003dbca)
When considering microservice without service mesh, every service can communicate with every other service and this commmunication happens in plain text. 

<br><br/>
![image](https://github.com/user-attachments/assets/0e711d86-af1c-41db-aa24-f182d36cbbeb)
Istiod imparts service discovery, configuration and certificate management. Communication configuration, metrics, security and tracing will be taken care by sidecar proxy container.

<br><br/>
**Architecture diagram for Service mesh using istio is as shown below.**
![image](https://github.com/user-attachments/assets/f2ffe416-de03-4c7f-8c25-43fd99ca0c0c)

```
Explanation of different components for istio.
1. Pilot, Pilot is responsible for traffic management and routing.
2. Citadel, Citadel is responsible for secure communication between the microservices.
3. Galley, Galley is responsible for configuration management.
4. Mixer, Mixer collects telemetry data.
```
