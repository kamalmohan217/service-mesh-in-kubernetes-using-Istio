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

In above screenshot of gateway you can see the selector which I have used is **istio: ingressgateway** matched with the istio-ingressgateway deployment as shown in the screenshot below. 
![image](https://github.com/user-attachments/assets/c78d37ef-cfc9-4e28-a97a-cb2f4618afd8)

Now do the entry in Rout53 to create record set for DNS Name of isto-ingressgateway service and create the URL and access your application through that URL.
![image](https://github.com/user-attachments/assets/203d1ee5-2be5-4475-ae73-1cc13f1b91bb)
![image](https://github.com/user-attachments/assets/0c095a5b-5df1-46f8-9556-7f252d70c8de)

### Istio supports traffic spliting and hence canary deployment.
To demonstrate canary deployment using Istio I have installed Argo Rollout and deleted earlier deployment, gateway and virtualservice using the command kubectl delete -f kubernetes-original-manifests.yaml and kubectl delete -f gateway-original.yaml.
![image](https://github.com/user-attachments/assets/51b88a67-b591-49e0-815d-ac01a52de925)
![image](https://github.com/user-attachments/assets/260c5203-1ce7-45ce-8e91-b0a128f19b8e)
![image](https://github.com/user-attachments/assets/c44b0d60-42ca-474a-be0a-f571b7b0005c)
![image](https://github.com/user-attachments/assets/6758251f-e83b-4175-b655-39f35fc0254f)
![image](https://github.com/user-attachments/assets/f87460ef-2f06-4e74-8957-16710d401972)

In **canary deployment** a newer version of Application pods will be created, the older version of application is named as stable version and newer version is named as canary version. A certain percentage (weight) of traffic will be routed to the canary version and rest of the traffic will be routed to the stable version and perform the testing with newer version of Application pods. If every thing is fine with newer version of application then perform rollout and move to newer version of applications.

In the Original manifests file as present in the link https://raw.githubusercontent.com/istio/istio/release-1.22/samples/bookinfo/platform/kube/bookinfo.yaml. I did change in productpage-v1 deployment as shown in the screenshot below and updated manifests file is available with this Repository.
![image](https://github.com/user-attachments/assets/0f8d5666-26f2-4f16-9191-0231ce6f2cab)
![image](https://github.com/user-attachments/assets/d8900bb6-bcb3-4efb-b8a7-a1dcdd92b43d)
![image](https://github.com/user-attachments/assets/c749e008-cfb8-4721-9fbd-be07595e709e)
![image](https://github.com/user-attachments/assets/ee438599-01a7-4955-b977-f5b7897b76ce)
![image](https://github.com/user-attachments/assets/f200e45c-ad4b-477b-af46-435b0e6f6f18)

Now change the image version of application productpage from 1.19.1 to 1.20.1
![image](https://github.com/user-attachments/assets/f3a38a1b-4129-4677-866e-8bb59aa9a9f3)
And check if the newer version of application is working properly. If it is working properly then perform rollout.
```
Below commands can be used to perform rollout and check the status after rollout

kubectl argo rollouts promote productpage-v1 -n microservice

kubectl argo rollouts get rollout productpage-v1 -n microservice --watch
```
![image](https://github.com/user-attachments/assets/63336b37-f183-43a2-987b-6ffcd48b9e00)
![image](https://github.com/user-attachments/assets/6c2524a6-b4d4-4824-ac59-339c58ef6fe1)

![image](https://github.com/user-attachments/assets/66dfb99b-8265-4d44-a6c8-f4b1d58bd55f)

You can also perform installation of jaeger, kiali, prometheus and grafana with istio as shown in below screenshots.
![image](https://github.com/user-attachments/assets/938f9b98-673d-4f78-82e5-efcc5b4e8b2c)
![image](https://github.com/user-attachments/assets/29c9cb2e-486c-42e9-977d-ce1610acf5d4)

Directory istio-1.22.3 will be created automatically after installation of Istio.

Now create virtualservice for grafana and kiali as shown in the screenshot below.
![image](https://github.com/user-attachments/assets/da29376d-0c28-4528-8bbc-bc167238ba74)
![image](https://github.com/user-attachments/assets/13684ee4-c567-42bb-a40b-00c5b705d309)
![image](https://github.com/user-attachments/assets/7a591f5f-3abc-488e-b295-78ea94599675)

I have added Destination Rule here as mTLS was enabled and kiali and grafana doesn't have a sidecar container so it is necessary to add destination rule with mTLS disabled as shown in the above screenshot.
<br><br/>
Finally create the record set in Route53 with DNS Name of the istio-ingressgateway service and access the created URL.
![image](https://github.com/user-attachments/assets/795e0d6f-7dfc-4157-b5ea-13461aa51027)
![image](https://github.com/user-attachments/assets/6e75ce61-faee-4bd0-8054-e2bd56446d2e)
![image](https://github.com/user-attachments/assets/c0101930-96c2-4201-94fa-607e45b15abb)
