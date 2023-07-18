We have deployed insurance application in capstone project2 using jenkins, github, dockerhub,docker in AWS EC2 Machine, we will take the same application and deploy through Azure Devops, Azure Repos, Azure container registry, docker in EC2 machine.

1. Import the resository:

We will create a new project in Azure DevOps and then go to Repo and click on Import a Repository:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.001.png)

We have already created gihub repo for the capstone2 project: 

<https://github.com/kotianrakshith/CapstoneProject2>

We will import this:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.002.png)

We can see after the import all the files are imported:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.003.png)

(Later we will delete all the files not needed ex. Jenkinsfile,ansible playbook)

1. Build the java application

`           `We wiill go to pipeline and create a new pipeline:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.004.png)

Then select your repo and the code:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.005.png)

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.006.png)

Then we will chose maven and only chose package:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.007.png)

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.008.png)

So our code till the build stage will be as below: (I have used self hosted pool instead of microsoft as Im not yet approved for it)

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.009.png)

Now lets save and run:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.010.png)

As Im using self hosted agent it will ask for permision:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.011.png)

Once you permit, it will run and we can see that it has sucessfully run and maven has build package to be deployed.

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.012.png)

1. Build and push the image to azure container registry

First let us create a container registry:

Go to container registries in azure portal and click create container registry:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.013.png)

Give all the details and click  review+create

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.014.png)

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.015.png)

Now we will go the pipeline and add the build and push to container regstry steps:

Edit the pipeline and add the task buildandpush docker:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.016.png)

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.017.png)

The code will be as below:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.018.png)

We have made sure docker is running locally and we will save the pipeline and it should run successfully:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.019.png)

Now let us check the Azure Container Registry if the image has been stored or not.

Go to the registry and navigate to Repositories, you should be able to see newly create ‘insuranceapp’ image

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.020.png)

1. Create a virtual machine and install docker:

Now since we have to deploy the docker container in a virtual machine, we will create a virtual machine. Go to virtual machine in azure and click create new:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.021.png)

We will give suffcicietn details:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.022.png)

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.023.png)

Then we will review and create.

Once created, we will go to the networking and all inbound rule:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.024.png)

Then we will add inbound 8081 as this is the port we will be exposing our docker container application.

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.025.png)

Now there should be SSH port and 8081 port :

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.026.png)

Now lets connect to the VM and install docker.

In this case we are connecting through bastion as its safer. In the bastion, it may ask you to setup bastion, you can click on deploy bastion which automatically will set it up and add new subnet:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.027.png)

Once deployed you can connect using your ssh key

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.028.png)

Your vm will open in the new browser tab:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.029.png)

Now let us install docker.

First we upgrade the system

sudo apt update && sudo apt upgrade -y

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.030.png)

Now we install the packages, keys, required repositories:

sudo apt install ca-certificates curl gnupg lsb-release

sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb\_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.031.png)

Now run the update command again(sudo apt update)

Now install the docker ce:

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.032.png)

Now check the docker version:

docker -v

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.033.png)

If you check the docker you see it is running:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.034.png)

`           `Then you can add your user for docker group(This may not be needed, but if you use different user it is helpful)

sudo usermod -aG docker $USER

Then reload the session using command: newgrp docker

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.035.png)

Now we will also do docker login to login to azure container registry:

First we will go to access keys and  enable admin user which will give us username and password

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.036.png)

We wll use this to login to login server.

docker login kotianrakshith.azurecr.io

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.037.png)

1. Add SSH service connection in Azure DevOps to the VM:

Go to the Project Settings-> Service connections-> click New service connection:

Search for SSH:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.038.png) 

Give all the required details:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.039.png)

Then save and it should be added:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.040.png)

1. Update pipeline to deploy docker contianer in VM:

We will deploy docker container through docker commands given SSH connections to the VM.

Go to the pipline and edit the pipeline. Search for SSH:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.041.png)

We will select the service connection we added and add following command as a scirpt:

docker stop insuranceapp 2>/dev/null

docker rm insuranceapp 2>/dev/null

docker run -itd -p 8081:8081 --name insuranceapp kotianrakshith.azurecr.io/insuranceapp 

(We need stop and rm if we want to run this pipeline repeatedly as once run there will be container present and we are using  2>/dev/null so it can ignore the errors)

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.042.png)![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.043.png)

Now lets save and it will run:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.044.png)

We see that it has run sucessfully.

1. Check if app is deployed sucessfully:

First lets go and check the VM if the app is deployed in docker:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.045.png)

We can see from ‘docker ps’ command that it has been created and running correctly.

Now let us access the app and check. To access it we need to use ip:port so here it is

<http://20.115.123.210:8081/>

We can see that we are able to access it correctly:

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.046.png)

![](/readmeimages/Images/Aspose.Words.c66ae028-478c-44e6-9bf2-5ceed97b39a7.047.png)

That completes this project. We have used the same code from capstone1 but we have used azure tools like Azure Devops, Azure Repos, Azure container registry, docker in EC2 machine to achive the same results much easily.
