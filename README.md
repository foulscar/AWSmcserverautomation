# AWSmcserverautomation
A tutorial on how to set up a private minecraft server with EC2 that automatically turns off after 3 (or however many hours you want). I also include a way to setup a private static website that allows you to turn on the server with a seperate IAM user's access key.

I could have set this up using ECS and EFS, but for a small server with very little usage, aswell as the cost and ability for others to replicate, EC2 better adheres to what is needed

## Prerequisies
- I set all of this up in US-EAST-1, in order to change this, you might have to change it in some of the code but I'll tell you when later; Or just use Global Accelerator :)
- Setup a public VPC & Subnet

## STEP 1: Create the EC2, Security Group, & Elastic IP
![Image Alt Text](images/image1.png)

I used Amazon 2023 as the AMI

![Image Alt Text](images/image2.png)



![Image Alt Text](images/image3.png)



![Image Alt Text](images/image4.png)




![Image Alt Text](images/image5.png)




![Image Alt Text](images/image6.png)




![Image Alt Text](images/image7.png)




![Image Alt Text](images/image8.png)

