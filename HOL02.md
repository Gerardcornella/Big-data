---
---
---

# [**HOL 02: DEPLOYING A HYBRID INFRASTRUCTURE FOR RESEARCHERS IN AWS**]{.underline}

## [**INTRODUCTION**]{.underline}

</p align="center">

Jupyter Notebooks have become an essential tool for analyzing data and disseminating findings in data science. This hands-on lab guides you through setting up a private Jupyter Notebook server on AWS for your research team. Furthermore, we will deploy a public Nginx web server using Voila to share your team's findings with the public.

</p>

## [**OBJECTIVES**]{.underline}

</p align="center">

This hands-on lab aims to introduce you to the basics of cloud computing by deploying a hybrid infrastructure for researchers in AWS. The infrastructure will include a private Jupyter Notebook server accessible via VPN and a public Voila server accessible from the Internet.

</p>

## [**ARCHITECTURE DIAGRAM**]{.underline}

</p align="center">

The figure provides a visual representation of the proposed hybrid infrastructure. It depicts a Virtual Private Cloud (VPC) on AWS, segmented into three subnets: Production, Research, and DMZ. The Production Subnet hosts an EC2 instance named HOL02-VoilaServer, which is Internet-accessible. The Research Subnet accommodates an EC2 instance named HOL02-Jupyter," accessible via VPN. The DMZ Subnet contains an EC2 instance named HOL02-VPN, which facilitates secure SSH connections from your team to the Research subnet.

</p>

![](Imatges%20markdown/Imagen1.png)

*Figure 1. Architecture diagram*

## [**SET UP THE ARCHITECTURE**]{.underline}

### [**IMPORT KEY PAIR**]{.underline}

The first step to build the infrastructure for researchers in AWS is to import the public key and name it **HOL02.**

![](Imatges%20markdown/Imagen2.png)

*Figure 2. IMPORT KEY PAIR*

### [**VPC**]{.underline}

The second step was to create a VPC named HOL02-VPC with the CIDR: 10.0.0.0/16.

![](Imatges%20markdown/Imagen3.png)

*Figure 3. VPC CREATION*

### [**SUBNETS**]{.underline}

</p align="center">

The following step was to create the three different subnets (*Production, Research, and DMZ*), that will be segmented the VPC.

</p>

![](Imatges%20markdown/Imagen4.png)

*Figure 4. SUBNETS CREATION*

### [**INTERNET GATEWAY**]{.underline}

</p align="center">

To ensure the instances that will be deployed have an internet connection, for their different porpouses such as to configure the VPN, install Jupyter and connecting through internet to the instance we have created an internet gateway that have been to attached to our VPC.

</p>

![](Imatges%20markdown/Imagen5.png){width="567"}

*Figure 5. INTERNETGATEWAY CREATION*

### [**ROUTE TABLES**]{.underline}

</p align="center">

By default, a route table automatically comes with your VPC. It controls the routing for all subnets that are not explicitly associated with any other route table. However, we are going to create three different route tables that will be associated with the three different subnets. The main function is to specify the rules for the traffic leaving the subnet. In order, to enable your subnet to access the internet through an internet gateway, we need to add the following route. The destination for the route is 0.0.0.0/0, which represents all IPv4 addresses. The target is the internet gateway that's attached to our VPC.

</p>

![](Imatges markdown/Imagen6.png)

*Figure 6. ROUTE TABLES CREATION*

</p align="center">

To sum up, what we have been developed so far we can look up to the resource map, to get a reminder how is the infrastructure been built.

</p>

![](Imatges markdown/Imagen7.png)

*Figure 7. RESOURCE MAP*

</p align="center">

We can notice, our VPC that has three different subnets associated each one with one different route tables. Also, we appreciate there is the default routing table with no associations. Finally, the internet gateway has been associated the different routing tables to grant internet access.

</p>

### [**SECURITY GROUPS**]{.underline}

</p align="center">

The next step we configure the different security groups for the three different instances, in order to define the traffic for the multiple instances.

</p>

![](Imatges markdown/Imagen8.png)

*Figure 8. SECURITY GROUPS*

</p align="center">

-   **HOL02-DMZ-Security Group:** Will be associated with the DMZ Subnet which will contain an EC2 instance named HOL02-VPN and has five inbound rules, which will facilitate secure SSH connections from your team to the Research subnet.

    </p>

    </p align="center">

-   **HOL02-Research- Security Group:** Will be associated with the Research Subnet accommodates an EC2 instance named HOL02-Jupyter and has two inbound rules, in order to be accessible via VPN.

    </p>

    </p align="center">

-   **HOL02-Production-Security Group:** Will be associated with the Production Subnet which will host an EC2 instance named HOL02-VoilaServer and has three inbound rules, which will facilitate that is Internet-accessible.

    </p>

### [**INSTANCES**]{.underline}

The three different instances required for the infrastructure were deployed.

![](Imatges markdown/Imagen9.png)

*Figure 9. LAUNCHING INSTANCES*

Let's take a look in detail each one of the instances and how were configured:

#### [**HOL02-VPN:**]{.underline}

</p align="center">

This will be the first instance deployed and configured, in order to be able to connect to other instances through VPN and configure them properly. The instance will be associated with subnet HOL02-DMZ, with the Ubuntu 22.04 LTS  AMI and will be the only instance associated with an elastic IP, due its functionality that must act such as a VPN and its VPN can not change because will disconfigure every time we run the instance and we should change the VPN configuration. Also will be associated with
its security group defined previously.

</p>

![](Imatges markdown/Imagen10.png)

*Figure 10. HOL02-VPN INSTANCE*

[CONFIGURING VPN]{.underline}

</p align="center">

The first step to be able to connect to configure the VPN, we must be able to connect to the instance through the security group.

</p>

![](Imatges markdown/Imagen11.png)

*Figure 11. CONNECTING TO THE VPN INSTANCE*

Once, we are in we can execute the piece of code provided to download the OpenVPN.

![](Imatges markdown/Imagen12.png)

*Figure 12. OPENVPN DOWNLOAD*

</p align="center">

After, it has been installed successfully we can configure the VPN. We are going to connect to the OpenVPN through internet with this path into our browser: *https://\<elastic-ip\>:943*.

</p>

![](Imatges markdown/Imagen13.png)

*Figure 13. ACCES TO OPENVPN*

</p align="center">

We enter our credentials and configure the network settings changing its hostname with our elastic IP, 54.147.17.131. We save the changes and we configure the VPN settings in order to add our subnets IDR one per line and don't let the client internet traffic be routed through the VPN. Finally, we enter the OpenVPN as client to download our user-locked profile to be able to connect properly in the VPN.

</p>

![](Imatges markdown/Imagen14.png)

*Figure 14. NETWORK SETTINGS*

![](Imatges markdown/Imagen15.png)

*Figure 15. VPN SETTINGS*

![](Imatges markdown/Imagen16.png)

*Figure 16. CONNECTING TO VPN*

#### [**HOL02-VoliaServer:**]{.underline}

</p align="center">

The instance will be associated with subnet HOL02-Production, with the Amazon Linux 2023  AMI and will associated with its security group, to be able to connect through internet and through the VPN. First of all, we must connect to the instance through the VPN to install the Nginx. It must be connected with the SSH group using its private IP address (10.0.2.198).

</p>

![](Imatges markdown/Imagen17.png)

*Figure 17. CONNECTING TO VOLIA SERVER INSTANCE*

</p align="center">

Once, we are in we execute the piece of code provided to configure the Nginx. After, is completed we can enter to the volia server through internet with its public IP address.

</p>

![](Imatges markdown/Imagen18.png)

*Figure 18. CONNECTING TO VOLIA SERVER*

#### [**HOL02-Jupyter:**]{.underline}

</p align="center">

The instance will be associated with subnet HOL02-Research, with the Amazon Linux 2023  AMI (by default python is installed) and will associated with its security group, to be able to be connected through the VPN. First of all, we must connect to the instance through the VPN to install the Jupyter notebook. It must be connected with the SSH group using its private IP address (10.0.3.140).

</p>

![](Imatges markdown/Imagen19.png)

*Figure 19. CONNECTING TO JUPYTER INSTANCE*

</p align="center">

After successfully connecting to the instance we update the instance with the command: *sudo yum update -y* and creating the virtual environment to be set as default when we connect to the instance using this command: *echo "source jupyter-env/bin/activate" \>\> \~/.bashrc.* Then, we install the Jupyter Notebook in the instance, *pip install jupyter.* Also, we use the following command to run Jupyter Notebook in the background: *nohup jupyter notebook \--ip=0.0.0.0 \--notebook-dir=/home/ec2-user/notebooks \--no-browser \> /home/ec2/user/notebooks/jupyter.log  2\>&1 &*

</p>

Finally, we are able to deploy the Jupyter Notebook.

![](Imatges markdown/Imagen20.png)

*Figure 20. DEPLOYING JUPYTER*

To connect to the jupyter notebook, we must connect through the private Ip address (10.0.3.140).

![](Imatges markdown/Imagen21.png)

*Figure 21. CONNECTING WITH JUPYTER*

</p align="center">

However, for the final porpouse of deploying this structure we must be sure the HOL02-Jupyter instance can\'t be acces via internet with its public IP.

</p>

![](Imatges markdown/Imagen22.png)

*Figure 22. UNABLE TO CONNECT WITH JUPYTER VIA INTERNET*

</p align="center">

Fortunately, we have deployed correctly the infrastructure in particular our security groups and it is impossible to connect to the jupyter instance of the research team via internet.

</p>

[**S3 BUCKET**]{.underline}

</p align="center">

Finally, we have deployed our last structure a S3 bucket, where all the notebooks used by the research team will be stored. Where we have created a folder named hol02.

</p>

![](Imatges markdown/Imagen23.png)

*Figure 23. BUCKET CREATION*

![](Imatges markdown/Imagen24.png)

To prove everything works properly, we upload a file named *hol02-app.ipynb* into the bucket.

*Figure 24. FILE UPLOADING*

</p align="center">

Once, we have upload the file let\'s synchronized our bucket with the Volia Server instance and the Jupyter instance, in that manner as son as the research team saves their work will in the jupyter instance, it will be updated into the S3 bucket and accessible through internet via the Volia Server. We are going to execute the following command *aws s3 sync . s3://notebook-hol02/hol02* in order to syncronized the bucket with the jupyter instance.

</p>

![](Imatges markdown/Imagen25.png)

*Figure 25. JUPYTER SYNCRONIZATION WITH THE S3 BUCKET*

</p align="center">

In order to synchronize, the bucket with the volia server we will execute the following command command *aws s3 sync s3://notebook-hol02/hol02 .* . Also we check if the synchronization worked properly, as we can confirm we can see the hol02-app.ipynb uploaded from the bucket and the notebooks from the jupyter instance.

</p>

![](Imatges markdown/Imagen26.png)

*Figure 26. VOLIA SERVER SYNCRONIZATION WITH THE S3 BUCKET*

</p align="center">

To make sure the syncronitzation work properly at the jupyter instance with the bucket, we take a look at the jupyter notebook to check what files have saved the research team.

</p>

![](Imatges markdown/Imagen27.png)

*Figure 27. JUPYTER WORK*

Now, let\'s look at the bucket to check if the syncronitzation has worked properly.

![](Imatges markdown/Imagen28.png)

*Figure 28. S3 BUCKET JUPYTER FILES*

</p align="center">

It worked properly, we can see all the files in the notebooks folder of the jupyter now are available on the S3 bucket. Finally, we are going to automate the synchronitzation with cron. Cron jobs are a powerful tool for scheduling tasks automatically at specific times, in our case we are going to Update our operating System every week on Sunday at 0:00, with this *command echo "0 0 \* \* 0  sudo yum update -y" \| crontab -*. First of all, we have installed cronie in order to execute the instruction.

</p>

![](Imatges markdown/Imagen29.png)

*Figure 29.  INSTALLING CRONIE*

![](Imatges markdown/Imagen30.png)

*Figure 30. SUBMITTING THE SYNCHRONIZATION COMMAND*

![](Imatges markdown/Imagen31.png)

*Figure 31. CHECKING IF THE COOMAND WAS SUBMITTED PROPERLY*

</p align="center">

Finally, we take a look if the command has been properly executed it with command *crontab -e* and we can check the command has been properly executed and saved it.

</p>

## [**Troubleshooting**]{.underline}

### [**OpenVPN Configuration**]{.underline}

</p align="center">

The first of the main problems, I encounter during the development of the infrastructure was that I wasn\'t able to connect through the OpenVPN. Checking the instructions provided, I realized while I was configuring the network settings I had a misunderstanding and instead of type the public elastic IP I was typing the private IP, this way it was impossible to work the vpn.

</p>

### [**Security Groups Configuration**]{.underline}

</p align="center">

The second of the issues, was related that I couldn\'t connect to the Jupyter and Volia services through the VPN. The reason was because during the definition both of the security groups for the instances they weren\'t defined as they were supposed due I didn\'t select the traffic must be coming from the VPN instance through the DMZ security group.

</p>

![](Imatges markdown/Imagen32.png)

*Figure 32. WRONG DEFINITION OF THE SECURITY GROUP*

### [**Jupyter Notebook Installation**]{.underline}

</p align="center">

After the launch of the jupyter instance, once I was connected to the instance trying to install the jupyter notebook and updating the instance I had several issues because I couldn\'t download the updates and the jupyter notebook because I had no access to the internet. Due this reason, I decided to connect the route table of the jupyter instance to the internet gateway.

</p>

### [**S3 Bucket Sync**]{.underline}

Finally, I had two main issues with the  synchronization of the S3 bucket with the instances:

</p align="center">

As, the screenshot provided below I couldn\'t synchronized the bucket due my machine wasn\'t able to locate the credentials.

</p>

![](Imatges markdown/Imagen33.png)

*Figure 33. PROBLEMS WITH SYNCHRONIZING THE S3 BUCKET*

</p align="center">

However, checking the work done in the previous lectures I realized with commands *aws configure* and *aws configure session_token,* providing the *aws_access_key_id, aws_secret_access_key* and the *aws_session_token.* I would be able to synchronized with the S3 bucket.

</p>

![](Imatges markdown/Imagen34.png)

*Figure 34. IDENTIFYING TO THE MACHINE*

</p align="center">

Finally, when I tried to synchronized the S3 bucket I was providing to the system the following command *aws s3 sync . s3://notebook-hol02/hol02* which is a command that synchronizes from the local to S3 bucket. However, modifying slightly the command *aws s3 sync  s3://notebook-hol02/hol02 .* I\'ve been able to synchronized from the S3 bucket to the local.

</p>
