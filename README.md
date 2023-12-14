# aws-panw-vmseries-cft-deployment
Various CFT-based solutions are collected in this repository for puposes of deploying Software Firewall labs in the context of AWS events platform. Most recently used as AWS Re:invent 2023.

## Outline

### [vmseries-gwlb-2023](https://github.com/seanyoungberg/panw-vmseries-aws-jam/tree/main/vmseries-gwlb-2023)


![diagram](https://github.com/AfrahAyub/aws-panw-vmseries-cft-deployment/assets/93593501/abc4170d-dc38-4519-927b-a56bc5224527)


This is the version being used for Re:invent Jam 2023. It is structured in a specific way to meet the layout of the Jam Event platform.

- aws-jam-panw-gwlb-cfn-root.yaml. This is the root stack that will be deployed. The other template files are nested from this one.

To deploy in normal AWS account:

1. Prep S3 bucket

we will use bucket names `aws-jam-challenge-resources`

To deploy in a separate account, prepare a bucket with same format but apply a unique prefix to the bucket. For example, we used `panw-aws-jam-challenge-resources`


2. Upload assets to S3

Create a folder in the bucket named `panw-vmseries-gwlb` In that folder, upload the following assets:

- The nested templates (combined, security, vmseries)
- authcodes
- bootstrap.xml
- init-cfg.txt

3. Deploy stack

Use aws-jam-panw-gwlb-cfn-root.yaml to create the stack. There is only one parameter that will need to be modified, which is the name of the bucket. Modify the bucket to include the prefix used in your account

## please go through the following tasks in order to run the use cases
## Overview
Welcome to the idyllic town of Riverdale, where the legendary 'Hop & Code' Brewery, has been crafting iconic brews for generations. We recently modernized by migrating to AWS and acquiring a third-party web store, 'Pixel Pints.' to sell our beer all over the world!

But here's the kicker: we suspect our competitor, 'Sneaky Suds,' has been siphoning off our top-secret IPA recipe.

You, a brilliant Network Security Engineer, have joined us to secure our digital kingdom. Your tools? Palo Alto's VM-Series with deep packet inspection and AWS Gateway Load Balancer.

Why wasn't this set up already? Our previous security engineer got too 'hoppy' with our beer supplies and left the job half-brewed. Time to sober up our security!

The original architecture was a simple two-tier web app. One VPC with the Web Store Frontend, and another VPC hosting the database. This DB was also used to store all of the 'Hop & Code' brewing data. The DB required outbound Internet connection for a SaaS health monitoring solution, which was provided by a NAT Gateway.

Original Traffic Flows

<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/hld-original.png" /></p>
<br />

Goal Architecture
Our previous engineer started to implement a solution to inspect our AWS traffic with Palo Alto Networks VM-Series to catch the thief. They created a Security VPC with AWS Gateway Load Balancer and attached it to the Transit Gateway. However, they never actually made the AWS routing changes to forward the traffic to the GWLB / VM-Series.

Your goal is to make sure all traffic (outbound, east-west, inbound) will be inspected by the VM-Series, find the intruder, and identify how we were compromised.
<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/hld-goal.png" /></p>
<br />

## Task1
The "Hop & Code" owners are convinced that our competitor “Sneaky Suds” is exfiltrating our secret recipes.

Your Application development team found some very strange behaviour on the Beer Database Server and asked you to have a deeper look into it to figure out what's going on.  After some investigations, you realized that no outbound traffic gets analyzed by the Palo Alto Networks Firewall. That's something that we have to fix.

You started the journey by conducting a comprehensive audit of the existing AWS infrastructure. With a discerning eye, you created a detailed diagram of the AWS environment. You mapped out the route tables of every VPC and the Transit Gateway.

<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/detailed-start.png" alt="Full Starting Diagram with Route Tables" width="1500" /></p>
<br />


## Task

**Redirect all outbound traffic from the Beer Store Data Database Server to the Palo Alto Networks Firewall**

1. First, login to the Firewall. (**Helpful Info Section**)

2. Check the Firewall Monitor traffic logs to verify if you can see any traffic from the Beer Store Data Database Server. ((**Helpful Info Section**)

3. Update AWS routing to redirect the Beer Store Data Database Server outbound traffic for inspection by VM-Series through the Transit Gateway. <br />
<br />

## Task Validation

- Once you made the appropriate changes in the AWS routing you can log into the **Beer Store Data Database Server** via the SSM service and test with the **curl** command if the EC2 instance has internet access.
  - example curl command **sudo curl www.google.de** 


- If the curl command isn't working in the **Beer Store Data Database Server**, check the Palo Alto Networks Firewall Monitor Logs to see which Application is now blocked from the Firewall. 

- You should see the following example log in the firewall monitoring. By adding the following filter **( zone.src eq internal ) and ( zone.dst eq external )** into the Monitor Logs filter bar.<br />
   Some fields in the example log were removed.
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task1-deny2.png" alt="VPC Logs" width="1500" /></p>
<br />

- Input the Name of the blocked Application in the answer field to complete the task. <br />
<br />

## Helpful Info
**To Login into the VM Series Firewall Web UI**
- Identify the Elastic IP (Security VM-Series Management) of the EC2 Instance named "Security VM-Series"
- Open a browser window and navigate to https://("Security VM-Series-EIP")
- Login with the following credentials:
  - Username: admin
  - Password: Pal0Alt0@123

**How to see the Traffic Logs inside the Firewall**
- Login into the firewall
- Inside the firewall navigate to Monitor -> Traffic
- See the following picture as an example <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/example.png" alt="Monitor Logs" width="1500" /></p>
- In the Monitor Traffic window change the refresh timer from **Manual** to **10 seconds** by clicking on the dropdown field on the top right as the picture below shows
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task1-10.png" alt="Monitor Logs" width="1500" /></p>


**Login into the Beer Store Data Database Server**
- Use the Session Manager to log into the Server
- The name of the VM is "Beer Store Data Database"

**How to find the server's private IP?**
- On the AWS Console go to EC2
- On the EC2 Dashboard click on Instances
- The following EC2 instances are used by the lab:
  - Beer Store Data Database
  - Beer Store Frontend Webserver
  - Security VM-Series (Palo Alto Networks Firewall)<br />
<br />

## Inventory
- Palo Alto Networks NGFW VM-Series
- Amazon EC2
- Amazon VPC
- AWS Systems Manager (SSM)
- AWS Lambda
- AWS AWS Tranist Gateway
- AWS Gateway Load Balancer <br />
<br />

## Services You Should Use
- Palo Alto Networks NGFW VM-Series
- Amazon EC2
- Amazon VPC (Route tables) <br />

## clues
Clue 1:Did you check the VPC Route Table?
You should check if the Route Tables of the Beer Store Data VPC is pointing to the correct Ressource

Clue 2:Traffic still not showing in the Firewall?
If you still can't see the traffic in the Firewall monitoring. Please do the following:

1. Login into the AWS console
2. Go to VPC
3. Select in Filter by VPC field the "Beer Store Data VPC"
4. As next go to Route Tables and select the Beer Store Data Private route table
5. In the route table click on Routes (see below)
<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task1-routes.png" /></p>
<br />
6. Click Edit routes and do the following changes:

  1. Remove the route 10.0.0.0/8 -> Target TGW
  2.  Change the route 0.0.0.0/0 -> TGW
  3. click Save

7. Once you made the changes your routle should looks like the example below
<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task1-clue2-new.png" /></p>
<br />
Answer:- Look for "web-browsing"
