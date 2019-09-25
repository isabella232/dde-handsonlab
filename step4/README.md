# Link Extractor: Step 4 - Docker App

Our application is working perfectly and we're well on our way to Unicorn status! It is time to leverage
[Docker App](https://github.com/docker/app) to make our Application even more portable, and to use a parameters file to make it environment agnostic! You saw parameters exposed in the Template you used in Application Designer earlier.

## Changes from the previous step

You will notice the Application Designer created a directory `step3.dockerapp`. This is something we haven't used previously and provides a way for us to distribute our entire application at once, as it includes all of our microservices and the parameters that can be used for deployment.

We have copied over a `linkextractor.dockerapp` into the `step4` directory for you to complete the lab with.

The `linkextractor.dockerapp` directory is composed of 3 files:

- `docker-compose.yml` - This file is something we have worked with in the previous steps. It contains instructions on how to deploy each service, and how each service will interact with the others. This Docker Compose file is slightly different though as it contains variables for tuneable parameters.

- `metadata.yml` - This file contains identification metadata such as name, version, description and maintainers of your containerised application.

- `parameters.yml` - This final section specifies default values for application parameters, these values will be inserted into the Docker Compose file at runtime.

As the application is nearly ready for production, we have removed the mounted php file for live editing. The www is now version controlled with a Docker Image tag `v1` and `v2`.

## Try it out

1. Using the `docker app` CLI syntax we are able to deploy and modify our containerised application.

First let's inspect our Docker app to find out more information about what services it will deploy and the default variables for those services.
   
Within your terminal, navigate to the step4 directory and run:

   ```powershell   
   > docker app inspect linkextractor.dockerapp
   linkextractor 0.1.0
   
   Maintained by: Gordon the Turtle
   
   sample description
   
   Services (2) Replicas Ports Image
   ------------ -------- ----- -----
   api          1        5000  ollypom/ee-templates-api:step4-v1
   www          1        80    ollypom/ee-templates-web:step4-v1
   
   Parameters (4) Value
   -------------- -----
   api.port       5000
   api.replicas   1
   www.port       80
   www.replicas   1
   ```

As shown in the previous exercise (step3), we can see our Docker application is composed of an API tier and web tier. We can also see the default ports each container will expose itself on.

2. Deploy a Docker App on to Docker Desktop Enterprise.

A Docker App can be deployed on top of a Docker Swarm, a Kubernetes Cluster, or as standalone containers. We will explore Kubernetes in step5, but first let’s familiarise our self with Docker App using Docker Swarm.

Create a one node Docker Swarm cluster on Docker Desktop Enterprise

   ```powershell
   > docker swarm init
   Swarm initialized: current node (qx8qqtlyhory19tr9xw15loxg) is now a manager
   ```

You can view the one node cluster with the following command:

   ```powershell
   > docker node list
   
   ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
   qx8qqtlyhory19tr9xw15loxg *   docker-desktop      Ready               Active              Leader              19.03.2
   ```

It's time to deploy the Docker App on to the swarm cluster to start taking advantage of that wonderful packaging. Navigate into the `linkextractor.dockapp` directory if you have not already done so, and run:

   ```powershell
   > docker app install --name linkextractor .
   Creating network linkextractor_default
   Creating service linkextractor_www
   Creating service linkextractor_api
   Application "linkextractor" installed on context "default"
   ```
  
   Once again open the web browser and inspect our application. http://localhost

   NOTE: If you get an error, make sure you have stopped your application from the previous exercise. Open the Application Designer window and click the "Stop" button. You also need to be in the `linkextractor.dockerapp` directory when you run the `docker app install` command (or change the last option in the command to `./linkextractor.dockerapp`)

3. You can use Docker App to upgrade a running application. An upgrade could be to a new version of the image, changing      parameters or changing the number of containers within the service.

   In our situation we have bundle two different services, just as we had before: an apache-based `www` (webserver) and python/flask `api` service.

   First, we are going to manually upgrade the Docker application by scaling the number of copies of the webserver.

   ```powershell
   > docker app upgrade linkextractor --set www.replicas=2
   ```
   
   a successul upgrade will return similar output to the following
   
   ```
   Updating service linkextractor_api (id: 2iyw5pctwgxuzhtg1amldcyan)
   Updating service linkextractor_www (id: zmbo7wz1dn49ca3dnu1cz8e41)
   Application "linkextractor" upgraded on context "default"
   ```

   Now if you get the status of the link extractor application, you can see that the application state has changed. We now have two web servers running the `www` service.

   ```powershell
   > docker app status linkextractor
   INSTALLATION
   ------------
   Name:         linkextractor
   Created:      2 minutes
   Modified:     35 seconds
   Revision:     01DNCR847E2JEM1BT6FTB6QA77
   Last Action:  upgrade
   Result:       SUCCESS
   Orchestrator: swarm
   
   APPLICATION
   -----------
   Name:      linkextractor
   Version:   0.1.0
   Reference:
   
   PARAMETERS
   ----------
   api.port:     5000
   api.replicas: 1
   www.port:     80
   www.replicas: 2
   
   STATUS
   ------
   ID                  NAME                MODE                REPLICAS            IMAGE                               PORTS
   h1czrmxu3geu        linkextractor_www   replicated          2/2                 ollypom/ee-templates-web:step4-v1   *:80->80/tcp
   rstihiqfr98p        linkextractor_api   replicated          1/1                 ollypom/ee-templates-api:step4-v1   *:5000->5000/tcp
   ```

4. Finally, we are going to show how to use an alternative parameters file with our application. In this scenario the application has been installed with the "testing" parameters file, but we bundle a "production" parameters file into the  application bundle as well to be used when this application gets near the production clusters.

   We can use the `upgrade` sub command again to change the application from the testing parameters file to the production file.

   ```powershell
   > docker app upgrade linkextractor --parameters-file .\parameters\production.yml 
   Updating service linkextractor_api (id: rstihiqfr98pyys8j0bvar55i)
   Updating service linkextractor_www (id: h1czrmxu3geu37lqq6f39wc73)
   Application "linkextractor" upgraded on context "default"
   ```
   
   NOTE: on macOS or Linux use the following command
   
   ```bash
   $ docker app upgrade linkextractor --parameters-file ./parameters/production.yml
   ```

   This production parameters file contains different variables for the ports our application runs on. It is quite common that port `80` will be unavailable in production clusters.

   If we check the status of our application again, we can see what port the `www` service is running in production.

   ```powershell
   > docker app status linkextractor 
   INSTALLATION
   ------------
   Name:         linkextractor
   Created:      7 minutes
   Modified:     2 seconds
   Revision:     01DNCRJGFBXMMYYKSEJY5XEC3R
   Last Action:  upgrade
   Result:       SUCCESS
   Orchestrator: swarm
   
   APPLICATION
   -----------
   Name:      linkextractor
   Version:   0.1.0
   Reference:
   
   PARAMETERS
   ----------
   api.port:     5000
   api.replicas: 1
   www.port:     8080
   www.replicas: 1
   
   STATUS
   ------
   ID                  NAME                MODE                REPLICAS            IMAGE                               PORTS
   h1czrmxu3geu        linkextractor_www   replicated          1/1                 ollypom/ee-templates-web:step4-v1   *:8080->80/tcp
   rstihiqfr98p        linkextractor_api   replicated          1/1                 ollypom/ee-templates-api:step4-v1   *:5000->5000/tcp
   ```

   `www.port: 8080`. Gives us the bit of information we need, if we now browse to http://localhost:8080 we can now use our application again.

## Clean-up

For the next exercise we will be deploying our application slightly differently so will need to clean up the application and the swarm.

```powershell
   > docker app uninstall linkextractor
   Removing service linkextractor_api
   Removing service linkextractor_www
   Removing network linkextractor_default
   Application "linkextractor" uninstalled on context "default"

   docker swarm leave --force
   Node left the swarm.
```
## Proceed to Step 5
