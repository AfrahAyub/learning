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
