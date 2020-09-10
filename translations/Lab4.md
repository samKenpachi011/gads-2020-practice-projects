# Google Africa Developer Scholarship-2020-Cloud-Practice-Projects

## Upgrading Kubernetes Engine Clusters

## Objectives
  1. Use the GCP Shell to upgrade your GKE cluster version
  2. Upgrade the nodes version

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
	
##  Deploy a GKE cluster
3. Create environment variables for the projectID,zone and the cluster name of choice:

    ```bash
        export project_ID=$DEVSHELL_PROJECT_ID 
		export cluster_name=cluster-1
		export my_zone=us-central1-c
    ```
4. Execute the following command which sets up a cluster with the necessary resources such as cluster version set to "1.15.12-gke.2":	
	
	Don't copy this code:
	```bash
		gcloud beta container --project $project_ID clusters $cluster-name \
		--zone $my_zone \
		 --no-enable-basic-auth --cluster-version "1.15.12-gke.2" \ 
		 --machine-type "e2-medium" \
		 --image-type "COS" \
		 --disk-type "pd-standard" \
		 --disk-size "100" \
		 --metadata disable-legacy-endpoints=true \
		 --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" \
		 --num-nodes "3" \
		 --enable-stackdriver-kubernetes \
		 --enable-ip-alias \
		 --network "projects/$project_ID/global/networks/default" \
		 --subnetwork "projects/$project_ID/regions/us-central1/subnetworks/default" \
		 --default-max-pods-per-node "110" \
		 --no-enable-master-authorized-networks \
		 --addons HorizontalPodAutoscaling,HttpLoadBalancing \
		 --enable-autoupgrade \
		 --enable-autorepair \
		 --max-surge-upgrade 1 \
		 --max-unavailable-upgrade 0
	```

	Execute this code which is similar to the one above
	```bash
		gcloud beta container --project $project_ID clusters create $cluster_name --zone $my_zone --no-enable-basic-auth --cluster-version "1.15.12-gke.2" --machine-type "e2-medium" --image-type "COS" --disk-type "pd-standard" --disk-size "100" --metadata disable-legacy-endpoints=true --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --num-nodes "3" --enable-stackdriver-kubernetes --enable-ip-alias --network "projects/qwiklabs-gcp-00-c471ffd41dea/global/networks/default" --subnetwork "projects/qwiklabs-gcp-00-c471ffd41dea/regions/us-central1/subnetworks/default" --default-max-pods-per-node "110" --no-enable-master-authorized-networks --addons HorizontalPodAutoscaling,HttpLoadBalancing --enable-autoupgrade --enable-autorepair --max-surge-upgrade 1 --max-unavailable-upgrade 0
	```

5. Verify if the cluster was created and note that it might take a few minutes:	

	```bash
		gcloud container clusters list --zone $my_zone
	```		
	note: Display should be something like the output below
	
	```bash
		NAME       LOCATION       MASTER_VERSION  MASTER_IP       MACHINE_TYPE  NODE_VERSION   NUM_NODES  STATUS
		cluster-1  us-central1-c  1.15.12-gke.2   35.226.237.118  e2-medium     1.15.12-gke.2     3       RUNNING
	```
	
##  Upgrade your GKE cluster
	
5. Before upgrading check available and default version:	

	```bash
		gcloud container get-server-config --zone $my_zone
	```	
	note: You should see a list displayed of versions including available master versions
	
	```bash
		validMasterVersions:
		- 1.16.13-gke.400
		- 1.16.13-gke.1
		- 1.15.12-gke.17
		- 1.15.12-gke.16
		- 1.15.12-gke.13
		- 1.15.12-gke.9
		- 1.15.12-gke.2
		validNodeVersions:
		- 1.16.13-gke.400
		- 1.16.13-gke.1
		- 1.16.11-gke.5
	```
	
6. From the above step copy a version that is a master version and is greater than the current cluster version:	

	```bash
		export cluster_version=1.16.13-gke.1
	```		
	
7. Use the following to upgrade the cluster:

    ```bash
        gcloud container clusters upgrade $cluster_name --master --cluster-version $cluster_version --zone $my_zone
    ```	
	
	note: If prompted to continue say yes/y to continue
	
8. Check the cluster version if it upgraded by using then following commands:

    ```bash
        gcloud container clusters list --zone $my_zone
    ```	
	
	OR
	
	```bash
		gcloud container operations list --filter="TYPE:UPGRADE_MASTER AND TARGET:$cluster_name"
	```
	
	note: You should be able to see something similar to the output below with STATUS as done:

	```bash
		NAME       LOCATION       MASTER_VERSION  MASTER_IP       MACHINE_TYPE  NODE_VERSION     NUM_NODES  STATUS
		cluster-1  us-central1-c  1.16.13-gke.1   35.226.237.118  e2-medium     1.15.12-gke.2 *    3           RUNNING
	```

	OR
	
	```bash
		NAME                              TYPE            LOCATION       TARGET     STATUS_MESSAGESTATUS  START_TIME                      END_TIME
		operation-1599741918957-b40e2acb  UPGRADE_MASTER  us-central1-c  cluster-1   DONE    2020-09-10T12:45:18.957352583Z  2020-09-10T12:49:22.540563074Z
	```

## Upgrade the node pool in your cluster

	Check  the list of node pools only one default should be there	
	
9.  View the node-pool in the cluster:

    ```bash
        gcloud container node-pools list --cluster $cluster_name --zone $my_zone
    ```	
		
	Save the node-pool name from the display	
	
	```bash
		NAME          MACHINE_TYPE  DISK_SIZE_GB  NODE_VERSION
		default-pool  e2-medium     100           1.15.12-gke.2
	```
	
10. Create an environment variable for the node-pool:

    ```bash
        export pool_name=default-pool
    ```		
	
11. Use the following commands to describe the node-pool for further details:

    ```bash
        gcloud container node-pools describe $pool_name --cluster $cluster_name --zone=$my_zone
    ```	
	
	output
	
	```bash
			config:
			  diskSizeGb: 100
			  diskType: pd-standard
			  imageType: COS
			  machineType: e2-medium
			  metadata:
				disable-legacy-endpoints: 'true'
			  oauthScopes:
			  - https://www.googleapis.com/auth/devstorage.read_only
			  - https://www.googleapis.com/auth/logging.write
			  - https://www.googleapis.com/auth/monitoring
			  - https://www.googleapis.com/auth/service.management.readonly
			  - https://www.googleapis.com/auth/servicecontrol
			  - https://www.googleapis.com/auth/trace.append
			  serviceAccount: default
			  shieldedInstanceConfig:
				enableIntegrityMonitoring: true
			initialNodeCount: 3
			instanceGroupUrls:
			- https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-00-c471ffd41dea/zones/us-centr
			al1-c/instanceGroupManagers/gke-cluster-1-default-pool-5d38b348-grp
			locations:
			- us-central1-c
			management:
			  autoRepair: true
			  autoUpgrade: true
			maxPodsConstraint:
			  maxPodsPerNode: '110'
			name: default-pool
			podIpv4CidrSize: 24
			selfLink: https://container.googleapis.com/v1/projects/qwiklabs-gcp-00-c471ffd41dea/zones/us
			-central1-c/clusters/cluster-1/nodePools/default-pool
			status: RUNNING
			upgradeSettings:
			  maxSurge: 1
			version: 1.15.12-gke.2
	```
	
12. Execute the following commands as this will automatically upgrade the node-pool to the version of the cluster master.	

    ```bash
        gcloud container clusters upgrade $cluster_name --zone=$my_zone
    ```		
	OR
	
	```bash
		gcloud container clusters upgrade $cluster_name --node-pool $pool_name --zone=$my_zone
    ```		
	
	note: When prompted yes/y and continue
	
13. View the pods running to confirm if Kubernetes udpated:

    ```bash
        kubectl get pods
    ```	
	
14. Check if the upgrade worked by executing the following commands:

    ```bash
        gcloud container node-pools list --cluster $cluster_name --zone $my_zone
    ```		
	OR
	
	```bash
		gcloud container node-pools describe $pool_name --cluster $cluster_name  --zone=$my_zone
    ```		

End