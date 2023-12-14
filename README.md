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


##please go through the following tasks in order to run the use cases
