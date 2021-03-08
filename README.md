# Object Detection React App (detect hat)

You can find an in depth walkthrough for training a TensorFlow.js model [here](https://github.com/cloud-annotations/training/).


# Prerequisites
You need to have the following installed to complete the steps in this code pattern:

* [Docker](https://www.docker.com/products/docker-desktop)
* [IBM Cloud Kubernetes Service Provisioned](https://www.ibm.com/cloud/container-service)

For running these services locally without Docker containers, you need:

* [Node.js v10 or later](https://nodejs.org/en/download/)
<details><summary><strong>Tip: Use Node Version Manager (nvm)</strong></summary>

> nvm is a simple bash script to manage multiple active Node.js versions.

> recommend: using `Node Version Manager (NVM)` to control the version of Node.js that you use.

> Why? The system or operating system installed Node.js version is fixed. You may need different versions of Node for other projects.  

> NVM allows you to choose and switch to the version of Node.js that suits your needs.

> Install via command line:

```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.2/install.sh | bash
```

[Learn more about NVM](https://github.com/nvm-sh/nvm) and find the latest installation instructions.

</details>

* [Relevant Node.js packages](package.json): Use `npm install`



## Setup
`git clone` the repo and `cd` into it by running the following command:

```bash
$ git clone https://github.com/y-leaf/object-detection-react-hat.git
$ cd object-detection-react-hat
```

# Steps 

Follow these steps to set up and run this code pattern locally and on the cloud. The steps are described in detail below.

1. [Run the application locally](#1-run-the-application-locally)
2. [Build a docker image, then run it locally](#2-Build-a-docker-image-then-run-it-locally)
3. [Deploy to IBM Cloud Kubernetes Service](#3-deploy-to-ibm-cloud-kubernetes-service)


--------------------

### 1. Run the application locally

1. Install packages with NPM by running `npm install`.

> **Note: You’ll need to have Node 8.10.0 or later on your local development machine.** You can use [nvm](https://github.com/creationix/nvm#installation) (macOS/Linux) or [nvm-windows](https://github.com/coreybutler/nvm-windows#node-version-manager-nvm-for-windows) to easily switch Node versions between different projects.

2. (Optional) Add TensorFlow.js Model to the App

if you have your own TensorFlow.js model, copy the `model_web` directory generated from the object detection walkthrough and paste it into the `public` folder of this repo.

3. Start the app by running `npm start`.

4. Open [http://localhost:3000](http://localhost:3000) to view it in the browser.


--------------------

### 2. Build a docker image, then run it locally

1. Make sure you are at the root of this application.
2. Note your docker-hub username
<details><summary><strong>How to find your docker hub credentials</strong></summary>

> To download Docker desktop you must create a Docker hub account.

> To find the username, you can click on at your Docker desktop icon (mac) toolbar 

</details>

3. Build the docker image by running:

```bash
export DOCKERHUB_USERNAME=<your-dockerhub-username>
docker build -t $DOCKERHUB_USERNAME/object-detection-react:v0.0.1 .
```


Great!  So, now lets run the image locally!

```bash
 docker run -p 4001:4001 grantsteinfeldibm/currencyexchange:v0.0.1
```


You should now see the object-detection microservice up and running

Explore the microservice at
>  [http://localhost:3000](http://localhost:3000) for documentation


--------------------

### 3. Deploy to IBM Cloud Kubernetes Service

1. To allow changes to the this microservice, create a repo on [Docker Cloud](https://cloud.docker.com/) where you can push the new modified containers. 

> NOTE: If a new repo is used for the Docker containers, the container `image` will need to be modified to the name of the new repo used in [object-detection-deploy.yaml](object-detection-deploy.yaml).

```bash
export DOCKERHUB_USERNAME=<your-dockerhub-username>

docker build -t $DOCKERHUB_USERNAME/object-detection-react:v0.0.1 .

docker login

docker push $DOCKERHUB_USERNAME/object-detection-react:v0.0.1

```

2. Provision an [IBM Cloud Kubernetes Service (Lite)](https://cloud.ibm.com/kubernetes/catalog/cluster).

* Login to the IBM Cloud using the [Developer Tools CLI](https://www.ibm.com/cloud/cli):
> NOTE use `--sso` if you have a single sign on account, or delete for username/password login

```bash
ibmcloud login --sso
```

* Set the Kubernetes environment to work with your cluster:

```bash
ibmcloud cs cluster-config --cluster <$CLUSTER_NAME OR $CLUSTER_ID>
```


#### Lite Cluster Instructions

3. Run `bx cs workers --cluster <$CLUSTER_NAME>` and locate and take note of the `Public IP`. This IP is used to access the currency service API. 

Update the `env` values `HOST_IP` and `SCHEME` in [object-detection-deploy.yaml](object-detection-deploy.yaml) 
to the `<PUBLIC_IP>:32001` and `http`.

4. To deploy the services to the IBM Cloud Kubernetes Service, run:

```bash
kubectl apply -f object-detection-deploy.yaml


## Confirm the services are running - this may take a minute
kubectl get pods
```

5. Use `http://<PUBLIC_IP>:32001` to access the microservice