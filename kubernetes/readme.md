
Explanation of Kubernetes Files:


 deployment.yaml: Defines your application's deployment on the KinD cluster, specifying the number of replicas, image to be used, and port configuration.
 
 service.yaml: Exposes your application as a LoadBalancer service, making it accessible from outside the KinD cluster via port 80.


Verifying Deployment to KinD

Apply Kubernetes files with:

kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml

Check running pods and services:

kubectl get pods
kubectl get services

To access your app, use:

kubectl port-forward svc/online-shop-service 8080:80

Then, visit: http://localhost:8080

Verification

Confirm all stages are successfully executed from console logs.

Check your email inbox for Jenkins success/failure notifications.

Verify application accessibility via port-forwarding.
#