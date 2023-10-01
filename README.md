# AWSmcserverautomation
A tutorial on how to set up a private minecraft server with EC2 that automatically turns off after 3 (or however many hours you want). I also include a way to setup a private static website that allows you to turn on the server with a seperate IAM user's access key.

I could have set this up using ECS and EFS, but for a small server with very little usage, aswell as the cost and ability for others to replicate, EC2 better adheres to what is needed

## Prerequisies
- I set all of this up in US-EAST-1, in order to change this, you might have to change it in some of the code but I'll tell you when later; Or just use Global Accelerator :)
- Setup a public VPC & Subnet

## STEP 1: Create the EC2 & Security Group
### I used Amazon 2023 as the AMI

![Image Alt Text](images/image1.png)

---


### I set the type as t2.medium
t2.medium is NOT under the free tier btw, but I think 4gb of ram is the minimum for a minecraft server personally



![Image Alt Text](images/image2.png)


---

### Select your VPC, Subnet, and add your SG Rules
- Allow SSH for YOUR IP
- Allow TCP Port 25565 (minecraft port) for anywhere



![Image Alt Text](images/image3.png)


---

### I gave it 8gb of gp3, which is under the free tier tier, you can go lower or higher
Once done, go ahead and launch your EC2



![Image 4 Alt Text](images/image4.png)


---


## Step 2: Create the Elastic IP and associate it with your EC2 Instance


### Click Allocate Elastic IP address



![Image 5 Alt Text](images/image6.png)


---

### Click Allocate



![Image 6 Alt Text](images/image5.png)


---

### Now associate the Elastic IP with your ec2 instance



![Image 7 Alt Text](images/image7.png)


---

### Once done copy the allocate ipv4 address, you will need this later



![Image 8 Alt Text](images/image8.png)

---


## Step 3: Setup Minecraft


### SSH into your instance



![Image 9 Alt Text](images/image9.png)
![Image 10 Alt Text](images/image10.png)

---

### We now need to install Java on our instance

Go ahead and run these commands:


These will import our repo

```console
sudo rpm --import https://yum.corretto.aws/corretto.key 
```

```
sudo curl -L -o /etc/yum.repos.d/corretto.repo https://yum.corretto.aws/corretto.repo
```


This will install Java 17

```
sudo yum install -y java-17-amazon-corretto-devel
```

This will test what Java version you have; Verify with the screenshot below

```console
java --version
```

![Image 11 Alt Text](images/image11.png)

---

### We now need to add a user and give it a directory

Run these commands

```console
sudo adduser minecraft
```

```console
sudo su
```

```console
mkdir /opt/minecraft/
```

```console
mkdir /opt/minecraft/server/
```

```console
cd /opt/minecraft/server/
```

![Image 12 Alt Text](images/image12.png)

---

### We now need to download the Server JAR and initialize the files

```console
wget https://piston-data.mojang.com/v1/objects/5b868151bd02b41319f54c8d4061b8cae84e665c/server.jar
```

```console
java -Xmx1024m -Xms1024m -jar server.jar nogui
```

You should now receive an error "Failed to load eula.txt"

---

![Image 13 Alt Text](images/image13.png)
![Image 14 Alt Text](images/image14.png)

---

### We will now edit the "eula.txt" file to agree to the minecraft eula 

Open the file

```console
vim eula.txt
```

![Image 15 Alt Text](images/image15.png)


Press the I key to edit

Change "eula=false" to "eula=true"

![Image 16 Alt Text](images/image16.png)


Type 
```
:wq
```
 to save and quit

![Image 17 Alt Text](images/image17.png)

---

### Now we will give the user permissions

```console
sudo chown -R minecraft:minecraft /opt/minecraft/
```

![Image 18 Alt Text](images/image18.png)

---

## Now we will make a service file

```console
sudo vim /etc/systemd/system/minecraft.service
```

![Image 19 Alt Text](images/image19.png)


Paste the following config

```console
[Unit]
Description=Mineraft Server
After=network.target

[Service]
User=minecraft
Nice=5
KillMode=none
SuccessExitStatus=0 1
NoNewPrivileges=true
WorkingDirectory=/opt/minecraft/server
ReadWriteDirectories=/opt/minecraft/server
ExecStart=/usr/bin/java -Xmx3900M -Xms1024M -jar server.jar nogui
ExecStop=/opt/minecraft/tools/mcrcon/mcrcon -H 127.0.0.1 -P 25575 -p strong-password stop

[Install]
WantedBy=multi-user.target
```

Now make sure to change the "-Xmx3900M" part to whatever max RAM you want to allocate

 Once done type 
 ```console
:wq
```
 to save and quit

![Image 20 Alt Text](images/image20.png)

---

### Now we will make a service from our service file

Type the following commands:

```console
chmod 664 /etc/systemd/system/minecraft.service
```

```console
systemctl daemon-reload
```

```console
systemctl enable minecraft.service
```

This will make the server start when the EC2 Instance gets started

---

## Step 4: Create our Lambda functions


### Create our mc_start function
- Name it "mc_start"
- Use the "Node.js 14.x" runtime

![Image 21 Alt Text](images/image21.png)

---

### We will now upload our zip file
[mc_start.zip](/mc_start.zip)

![Image 22 Alt Text](images/image22.png)
![Image 23 Alt Text](images/image23.png)

---

![Image 24 Alt Text](images/image24.png)
![Image 25 Alt Text](images/image25.png)
![Image 26 Alt Text](images/image26.png)
![Image 27 Alt Text](images/image27.png)
![Image 28 Alt Text](images/image28.png)
![Image 29 Alt Text](images/image29.png)
![Image 30 Alt Text](images/image30.png)
![Image 31 Alt Text](images/image31.png)
![Image 32 Alt Text](images/image32.png)
![Image 33 Alt Text](images/image33.png)
![Image 34 Alt Text](images/image34.png)
![Image 35 Alt Text](images/image35.png)
![Image 36 Alt Text](images/image36.png)
![Image 37 Alt Text](images/image37.png)
![Image 38 Alt Text](images/image38.png)
![Image 39 Alt Text](images/image39.png)
![Image 40 Alt Text](images/image40.png)
![Image 41 Alt Text](images/image41.png)
![Image 42 Alt Text](images/image42.png)
![Image 43 Alt Text](images/image43.png)
![Image 44 Alt Text](images/image44.png)
![Image 45 Alt Text](images/image45.png)
![Image 46 Alt Text](images/image46.png)
![Image 47 Alt Text](images/image47.png)
![Image 48 Alt Text](images/image48.png)
![Image 49 Alt Text](images/image49.png)
![Image 50 Alt Text](images/image50.png)
![Image 51 Alt Text](images/image51.png)
![Image 52 Alt Text](images/image52.png)
![Image 53 Alt Text](images/image53.png)
![Image 54 Alt Text](images/image54.png)
![Image 55 Alt Text](images/image55.png)
![Image 56 Alt Text](images/image56.png)
![Image 57 Alt Text](images/image57.png)
![Image 58 Alt Text](images/image58.png)
