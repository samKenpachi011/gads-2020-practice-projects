# Google Africa Developer Scholarship-2020-Cloud-Practice-Projects

## Google Cloud Fundamentals: Getting Started with GKE

## Objectives
  1. Create a Kubernetes clusters.
  2. Create an nginx deployment.
  3. Exposed the application.
  4. Scale the application.

#### Enable these API's "Kubernetes API", "Container Registry API" and Start the cloud shell.

## Tasks
1.  Using the shell to confirm that the needed APIs are available and you can enable them:
   _-_ Check a list of services that you can enable in your project:
   
    ```bash
        gcloud services list --available
    ```
    _-_ If you don't see the API listed, that means you haven't been granted access to enable the API.
    | API                    | SERVICE_NAME                     |
    | ---------------------- | -------------------------------- |
    | Kubernetes API         | container.googleapis.com         |
    | Container Registry API | containerregistry.googleapis.com |
	
2. Use the service name from the previous step to enable each service:

    ```bash
        gcloud services enable SERVICE_NAME
    ```	
	
3. Create an environment variable called MY_ZONE and store the zone provided either by Qwiklabs:

    ```bash
        export MY_ZONE=<zone_provided>
    ```
4. Create a Kubernetes cluster called webfrontend with the zone tag using the environment variable above and provisioning 2 nodes:	

	```bash
		gcloud container clusters create webfrontend --zone $MY_ZONE --num-nodes 2
	```
	
5. The kubectl command line tool lets you control Kubernetes clusters now use it to get the version of Kubernetes:	

	```bash
		kubectl version
	```	
6. The kubectl command line tool lets you control Kubernetes clusters now use it to get the version of Kubernetes:	

	```bash
		kubectl version
	```		
	
7.  Check to see if the Kubernetes cluster is up and running:

    ```bash
        gcloud compute instances list
    ```	
	
8.  Launch a single instance of the nginx container. (Nginx is a popular web server.):

    ```bash
        kubectl create deploy nginx --image=nginx:1.17.10
    ```		
	
9.  View the pod running the nginx container:

    ```bash
        kubectl get pods
    ```		
	
10. Expose the nginx container to the Internet:

    ```bash
        kubectl expose deployment nginx --port 80 --type LoadBalancer
    ```		
	
11. View the service that you just created:

    ```bash
        kubectl get services
    ```		
	
12. Open a new web browser tab and paste your cluster's external IP address into the address bar. The default home page of the Nginx browser is displayed.	

13. Scale up the number of pods running on your service:

    ```bash
        kubectl scale deployment nginx --replicas 3
    ```		
	
14. View the pods running to confirm if Kubernetes udpated:

    ```bash
        kubectl get pods
    ```	
	
15. View if the service IP address has changed:

    ```bash
        kubectl get services
    ```		
16. Open a new web browser tab and paste your cluster's external IP address into the address bar. The default home page of the Nginx browser is displayed.	
	
