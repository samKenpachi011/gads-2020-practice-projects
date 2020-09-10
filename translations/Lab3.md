# Google Africa Developer Scholarship-2020-Cloud-Practice-Projects

## Creating a GKE Cluster via Cloud Shell

## Objectives
  1. Create a Kubernetes cluster.
  2. Use kubectl to build and manipulate GKE clusters
  3. Use kubectl and configuration files to deploy Pods
  4. Use Container Registry to store and deploy containers

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
	
3. Create environment variables called MY_ZONE and MY_CLUSTER store the zone and cluster-name :

    ```bash
        export my_zone=us-central1-a
		export my_cluster=standard-cluster-1
    ```
4. Create a Kubernetes cluster using the environment variables above and provisioning 3 nodes and enabling ip-alias:	

	```bash
		gcloud container clusters create $my_cluster --num-nodes=3 --zone=$my_zone --enable-ip-alias
	```
	
5. Verify if the cluster has been created:	

	```bash
		gcloud container clusters --zone=$my_zone 
	```	
6.  Modify standard-cluster-1 to have four nodes:	

	```bash
		gcloud container clusters resize $my_cluster --zone=$my_zone --size=4
	```		
	
	note:When prompted with Do you want to continue (Y/n), press y to confirm
	
7.  Authenticate to a GKE cluster :

    ```bash
        gcloud container clusters get-credentials $my_cluster --zone $my_zone
    ```	
	
8.  Examine all of the authentication and endpoint configuration data stored in the kube config file:

    ```bash
        nano ~/.kube/config
    ```	
	
	note:To exit the editor use CTRL+X
	
## Use kubectl to inspect a GKE cluster
	
9.  Execute the following command to print out the content of the kubeconfig file:

    ```bash
        kubectl config view
    ```		
	
10. Execute the following command to print out the cluster information for the active context:

    ```bash
        kubectl cluster-info
    ```		
	
	The output describes the active context 
	
11. View the active current cluster content:

    ```bash
        kubectl config current-context
    ```		
	
	The output should look like this
	
	```bash
		gke_[PROJECT_ID]_us-central1-a_standard-cluster-1
	```
	
12. Execute the following command to print out some details for all the cluster contexts in the kubeconfig file.	

	```bash
		kubectl config get-contexts
	```

13. Execute the following command to change the active context:

    ```bash
        kubectl config use-context gke_${GOOGLE_CLOUD_PROJECT}_us-central1-a_standard-cluster-1
    ```		
	
	In this case you have only one cluster, so this command didn't change anything.
	
14. Execute the following command to view the resource usage across the nodes of the cluster:

    ```bash
        kubectl top nodes
    ```	
	
	The output should look like the following example.

	```bash
		NAME                            CPU(cores)   CPU%  MEMORY(bytes)  MEMORY%
		gke-standard-cluster-1-def...   29m          3%    431Mi          16%
		gke-standard-cluster-1-def...   45m          4%    605Mi          22%
		gke-standard-cluster-1-def...   40m          4%    559Mi          21%
		gke-standard-cluster-1-def...   34m          3%    488Mi          18%
	```
15. Execute the following command to enable bash autocompletion for kubectl:

    ```bash
        source <(kubectl completion bash)
    ```		
	
	This command produces no output.
	In Cloud Shell, type kubectl and press the Tab key twice
	note:The shell outputs all the possible commands.

16. Execute the following command to deploy the latest version of nginx as a Pod named nginx-1.	

	```bash
        kubectl create deployment nginx-1 --image=nginx:latest
    ```	
	
17. View all the deployed Pods in the active context cluster.	

	```bash
        kubectl get pods
    ```		
	
18. Create a environment variable for the pod-name above and call it my_nginx_pod	

	```bash
        export my_nginx_pod=<pod-name>
    ```		
	
19. View the complete details of the Pod you just created.	

	```bash
        kubectl describe pod $my_nginx_pod 
    ```		
	
	note:Details of the Pod, as well as its status and conditions and the events in its lifecycle, are displayed
	
## Push a file into a container	
	
	To be able to serve static content through the nginx web server, you must create and place a file into the container
		
20. Open a file named test.html in the nano text editor.

	```bash
        nano ~/test.html
    ```		

21. Add the following text (shell script) to the empty test.html file	

	```bash
        <html> <header><title>This is title</title></header>
		<body> Hello world </body>
		</html>
    ```		
	
	Press CTRL+O, then press Enter and press CTRL+X to save the file and exit the nano editor
	
22. Execute the following command to place the file into the appropriate location within the nginx container in the nginx Pod to be served statically.

	```bash
        kubectl cp ~/test.html $my_nginx_pod:/usr/share/nginx/html/test.html
    ```		

## Expose the Pod for testing

23. This command creates a LoadBalancer service, which allows the nginx Pod to accessed from internet addresses outside of the cluster.

	```bash
        kubectl expose pod $my_nginx_pod --port 80 --type LoadBalancer
    ```			

24. View details about services in the cluster.
	The kubernetes service is one of the default services created or used by the cluster.
	The nginx service that you created will also be displayed.

	```bash
        kubectl get services
    ```	
	
	note:You might have to repeat the command a few times before the new service has its external IP populated

25. Check the file contents of the test.html file created ealier use the external IP address from above.

	```bash
        curl http://[EXTERNAL_IP]/test.html
    ```		
	
    There should be a similar output as the one below
	
	```bash
		<html> <header><title>This is title</title></header>
		<body> Hello world </body>
		</html>
	```
26. View the resources being used by the nginx Pod

	```bash
        kubectl top pods
    ```		
## Deployment using a configuration file

27. Clone a repository to the cloud shell.

	```bash
        git clone https://github.com/GoogleCloudPlatformTraining/training-data-analyst
    ```		

28. Change directory to the folder that contains the needed sample file.

	```bash
        cd ~/training-data-analyst/courses/ak8s/04_GKE_Shell/
    ```		
	note: A sample manifest YAML file for a Pod called new-nginx-pod.yaml has been provided for you
	
	```bash
        apiVersion: v1
		kind: Pod
		metadata:
		  name: new-nginx
		  labels:
			name: new-nginx
		spec:
		  containers:
		  - name: new-nginx
			image: nginx
			ports:
			- containerPort: 80
    ```	
	
	
29. Deploy the configuration file.

	```bash
       kubectl apply -f ./new-nginx-pod.yaml
    ```	

30. View available pods.

	```bash
        kubectl get pods
    ```	
	
## Use shell redirection to connect to a Pod

31.  Start an interactive bash shell in the nginx container.

	```bash
        kubectl exec -it new-nginx /bin/bash
    ```		
	
	A new shell prompt appears
	
	```bash
        root@new-nginx:/#h
    ```	
	
32. Update dependencies and install the nano text editor.

	```bash
        apt-get update
		apt-get install nano
    ```			
	
33.  Execute the following commands to switch to the static files directory and create a test.html file.

	```bash
        cd /usr/share/nginx/html
		nano test.html
    ```			
	
34. In the nginx bash shell nano session, paste the following text:

	```bash
        <html> <header><title>This is title</title></header>
		<body> Hello world from the new deployment</body>
		</html>
    ```			
	Press CTRL+O, then press Enter and press CTRL+X to save the file and exit the nano editor.
	
35. Exit the nginx bash shell.

	```bash
        exit
    ```	

## Set up port forwarding from Cloud Shell to the nginx Pod (from port 10081 of the Cloud Shell VM to port 80 of the nginx container):	

36. Execute the following command to set up port forwarding .

	```bash
        kubectl port-forward new-nginx 10081:80
    ```		
	output:
	```bash
		Forwarding from 127.0.0.1:10081 -> 80
		Forwarding from [::1]:10081 -> 80
	```
	
37. Open another Cloud Shell instance to test.

	```bash
        curl http://127.0.0.1:10081/test.html
    ```	
	
	out should be:

	```bash
        <html> <header><title>This is title</title></header>
		<body> Hello world from the new deployment</body>
		</html>
    ```		

38. View pod logs.

	```bash
        kubectl logs new-nginx -f --timestamps
    ```	
	
	You will see the logs display in this new window

End








