# Google Africa Developer Scholarship-2020-Cloud-Practice-Projects

## Working with Cloud Build

## Objectives
  1. Use Cloud Build to build and push containers to the container registry 

#### Enable this API's "Cloudbuild API" and start the cloud shell.

## Tasks
1.  Using the shell to confirm that the needed API is available and you can enable it:
   _-_ Check a list of services that you can enable in your project:
   
    ```bash
        gcloud services list --available
    ```
    _-_ If you don't see the API listed, that means you haven't been granted access to enable the API.
    | API                    | SERVICE_NAME                     |
    | ---------------------- | -------------------------------- |
    | Cloudbuild API         | cloudbuild.googleapis.com       |
  
	
2. Use the service name from the previous step to enable each service:

    ```bash
        gcloud services enable SERVICE_NAME
    ```	
	
3. Create an empty quickstart.sh file using the nano text editor:

    ```bash
        nano quickstart.sh
    ```
4. Add the following lines in to the quickstart.sh file:	

	```bash
		#!/bin/sh
		echo "Hello, world! The time is $(date)."
	```
	
5. Save the file and close nano by pressing the CTRL+O, press enter and CTRL+X :	

6. Create an empty Dockerfile file using the nano text editor:	

	```bash
		nano Dockerfile
	```		
	
7.  Add the following command to the Dockerfile which instructs the build to use the Alpine Linux base image:

    ```bash
        FROM alpine
    ```	
	
8.  Add again to the Dockerfile which adds the quickstart.sh script created earlier to the / directory in the image.:

    ```bash
        COPY quickstart.sh /
    ```		
	
9.  Add the following to the Dockerfile which configures the image to execute the "/quickstart.sh" script when the associated container is created and run:

    ```bash
        CMD ["/quickstart.sh"]
    ```		
	
10. This is how the Dockerfile should look at the end:

    ```bash
        FROM alpine
		COPY quickstart.sh /
		CMD ["/quickstart.sh"]
    ```		
	
	Save the file and close nano by pressing the CTRL+O, press enter and CTRL+X
	
11. Use the following command to make the quickstart.sh script executable.:

    ```bash
        chmod +x quickstart.sh
    ```		
	
12. Use the following command to build the Docker container image in Cloud Build.	

    ```bash
        gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/quickstart-image .
    ```

13. Check if the image was created using the following command:

    ```bash
        gcloud container images list
    ```		
## Building Containers with a build configuration file and Cloud Build

	Cloud Build also supports custom build configuration files.
	In this task you will incorporate an existing Docker container using a custom YAML-formatted build file with Cloud Build.
	
14. Use the following command to clone the repository to the lab Cloud Shell:

    ```bash
        git clone https://github.com/GoogleCloudPlatformTraining/training-data-analyst
    ```	
	
15. Change to the directory that contains the sample files:

    ```bash
        cd ~/training-data-analyst/courses/ak8s/02_Cloud_Build/a
    ```		
	
16. View the contents of the configuration yaml file.	

    ```bash
        cat cloudbuild.yaml
    ```	
	
	This file instructs Cloud Build to use Docker to build an image using the Dockerfile specification in the current local directory,
	tag it with gcr.io/$PROJECT_ID/quickstart-image ($PROJECT_ID is a substitution variable automatically populated by Cloud Build with the 
	project ID of the associated project) and then push that image to Container Registry

17. Execute the following command to start a Cloud Build using cloudbuild.yaml as the build configuration file.	

    ```bash
        gcloud builds submit --config cloudbuild.yaml .
    ```
	
18. Check if two versions of quickstart-image are now in the list.	

    ```bash
        gcloud container images describe gcr.io/$PROJECT_ID/quickstart-image:tag
    ```

## Building and Testing Containers with a build configuration file and Cloud Build

19. Change to the directory that contains the sample files:

    ```bash
        cd ~/training-data-analyst/courses/ak8s/02_Cloud_Build/a
    ```		
	
	
20. View the contents of the configuration yaml file.	

    ```bash
        cat cloudbuild.yaml
    ```		
	
	In addition to its previous actions, this build configuration file runs the quickstart-image it has created.
	In this task, the quickstart.sh script has been modified so that 
	it simulates a test failure when an argument ['fail'] is passed to it

21. Execute the following command to start a Cloud Build using cloudbuild.yaml as the build configuration file.	

    ```bash
        gcloud builds submit --config cloudbuild.yaml .
    ```
	
	You will see output from the command that ends with text like this:

    ```bash
        Finished Step #1
		ERROR
		ERROR: build step 1 "gcr.io/qwiklabs-gcp-00-3c0a50b3c809/quickstart-image" failed: starting step container failed: Error response from daemon: OCI runtime create failed: container_linux.go:349: starting container process caused "exec: \"fail\": executable file not found in $PATH": unknown "
		----------------------------------------------------------------------------------------------------------------------------------------------------------------
		ERROR: (gcloud.builds.submit) build f3e94c28-fba4-4012-a419-48e90fca7491 completed with status "FAILURE"
    ```

22. Confirm if the shell knows that the build failed .The command will reply with a non-zero value

	```bash
		echo $?
	```
	
End of the Lab	










