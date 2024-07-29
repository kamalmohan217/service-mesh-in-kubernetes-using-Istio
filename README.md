# service mesh in kubernetes using Istio
![image](https://github.com/user-attachments/assets/efe7786c-edf4-4bbd-8d3d-2229a003dbca)
When considering microservice without service mesh, every service can communicate with every other service and this commmunication happens in plain text. 

<br><br/>
![image](https://github.com/user-attachments/assets/0e711d86-af1c-41db-aa24-f182d36cbbeb)
Istiod imparts service discovery, configuration and certificate management. Communication configuration (which microservices are allowed to communicate and which services are not allowed to communicate), metrics, security and tracing will be taken care by sidecar proxy container. After implementation of service mesh using istio no services will be allowed to communicate directly it will be communicated through the **sidecar proxy envoy container** as shown in the diagram above.

<br><br/>
**Architecture diagram for Service mesh using istio is as shown below.**
![image](https://github.com/user-attachments/assets/9e49fefa-a407-484e-b79a-7ce3525ff8e5)

```
Explanation of different components for istio.
1. Pilot, Pilot is responsible for traffic management and routing.
2. Citadel, Citadel is responsible for secure communication between the microservices.
3. Galley, Galley is responsible for configuration management.
4. Mixer, Mixer collects telemetry data.
```

For the demonstration purpose I have used the microservice as provided on the Isio site https://istio.io/latest/docs/setup/getting-started/ in the URL https://raw.githubusercontent.com/istio/istio/release-1.22/samples/bookinfo/platform/kube/bookinfo.yaml. Details regarding the microservice is available on https://istio.io/latest/docs/examples/bookinfo/.

I had deployed the microservice on the eks-cluster and eks cluster was created using the terraform script as available with the Repository https://github.com/kamalmohan217/terraform-eks-withaddons.git. Istio is not yet installed on the eks cluster. 

As shown in the screenshot below at present only one out of one container is ready. It means sidecar proxy envoy container is not yet created in the pod. As I mentioned I had not installed Istio till now.
![image](https://github.com/user-attachments/assets/0d949adf-dee1-420d-ae2a-08c5f63ccb35)

Now delete the entire namespace and create a fresh one (namespace) then install isto and deploy the microservice as shown in the screenshot below.
![image](https://github.com/user-attachments/assets/cf339b07-cdcf-4d8d-9832-9d51948d3b59)

```
You must label the namespace with istio-injection=enabled as written below
kubectl label namespace microservice istio-injection=enabled

Now check the labels using the command as shown below
kubectl get ns --show-labels
```
Finally you will see two out of two containers are ready as shown in the screenshot below. The second container is for sidecar proxy envoy container. 
![image](https://github.com/user-attachments/assets/da29fa9f-202f-416f-9dd9-c227aa440e26)

You can observe that in namespace istio-system there is a service named as istio-ingressgateway as shown in the screenshot below. Istio ingress gateway provides the similar functionality as that of ingress-controller. In ingress controller you need to create ingress rule however here you need to create gateway as well as virtual service.
![image](https://github.com/user-attachments/assets/774f7986-e4ce-45ae-aa73-516c9160a770)

I had provided the SSL certificate through the kubernetes secret as shown below.
![image](https://github.com/user-attachments/assets/6738f206-2a57-4ec7-b94a-bea4eb2a054d)

```
kubectl create secret tls ecommerce-tls-secret -n istio-system --key mykey.key --cert STAR_singhritesh85_com.crt
```

Screenshot for yaml file to create gateway and virtual service is as shown below.
![image](https://github.com/user-attachments/assets/8fbbbf9b-6f2d-4230-8ec1-94ca6c3706a1)
![image](https://github.com/user-attachments/assets/1fadf73a-24f1-4154-b083-3df61d93b30f)
![image](https://github.com/user-attachments/assets/cd0ea360-6acc-489a-95c9-3d15f6850c78)
![image](https://github.com/user-attachments/assets/fa8fdcde-d1a0-4181-9122-81e08d35018e)

Now do the entry in Rout53 to create record set for DNS Name of isto-ingressgateway service and create the URL and access your application through that URL.
![image](https://github.com/user-attachments/assets/203d1ee5-2be5-4475-ae73-1cc13f1b91bb)
![image](https://github.com/user-attachments/assets/f998d0c1-2c9a-40a4-97c4-ffc41c101d0b)

### Istio supports traffic spliting and hence canary deployment.
