---
layout: post
title: intro to aws ec2
cover: cover.jpg
date: 2016-05-30 12:00:00
categories: posts
---

Hi there! Are you excited about cloud computing? Do you understand that, through services like Amazon Web Services (AWS), and Google Cloud Platform, anyone now has access to a virtual supercomputer? Well, if you weren't aware, this is big news. Cloud computing is behind a lot of the internet services that we use everyday, and it has also allowed for a proliferation of private sector activity around 'big-data' applications across industries. One of these industries is the earth-observation based 'imagery-insights' ecosystem, which I've briefly detailed in [my previous post](/posts/2016/05/30/imagery-insights.html).

Anyways, if you're like me and have wanted to bring AWS into your repertoire of tech-skills, then you're in the right place. In the steps that follow, I'll be detailing the steps that I took to:

- Set up an AWS account
- Create an IAM user and understand security credentials
- Install the AWS CLI utility
- Set up AWS CLI tab completion
- Give your IAM user account access to relevant services, through IAM groups
- Create an EC2 security group, and provide 'ingress' permissions relative to IP address or an IAM group
- Set up billing alarms just in case usage and billing exceed expectations
- Create an EC2 login key
- Initiating your first EC2 instance (Ubuntu)
- 'SSH'ing into your EC2 instance

Also. In this guide I'm assuming that you are on Ubuntu or OS X, and that you are comfortable with the command line.

# 1. Signing up for AWS and installing the AWS CLI
------------------

Go to [http://aws.amazon.com/](http://aws.amazon.com/) and click 'Create a Free Account'

This option allows for a number of 'freebies' across many AWS services including free access to low instance tiers (t2.micro) of EC2 images and 5GB of storage on S3 repositories.

Follow the account setup dialogues. along with some personal information, you'll be asked to provide a payment method gasp, as there is nothing stopping you from exceeding your free account limitations through non-covered usages. While this concept initially frightened me, I found that these services were actually very cheap. Also, since the documentation to sort through the implementation of services is rather dense, one inevitably knows what they are doing before they can get anything to work in the first place.

Before we continue, I'll point out that we're using both AWS GUI and AWS CLI to access the services throughout this tutorial. If you have not formerly installed the AWS CLI (this stands for Command Line Interface), then please do that before we move to the next section. The AWS CLI can be installed through 'pip' (the Python Package manager) with:

```
pip install awscli
```

If you're having trouble installing the AWS CLI, then check out AWS's [installation instructions page.](http://docs.aws.amazon.com/cli/latest/userguide/installing.html)

Additionally, I recommend that you set up AWS CLI tab completion for ease of use. This can be done by adding the following line to your '~/.bash_profile' file on OSX, or the '~/.bashrc' file in Ubuntu:

```
complete -C '/usr/local/bin/aws_completer' aws
```

# 2. Understanding and Setting up IAM Services
------------------

Are you logged into your AWS account? Great! Now let's talk about AWS's IAM Management Console.

IAM stands for 'Identity and Access Management.'

The service functions as a permissions management tool and security/access configurations are set through definitions applied to ['Identities,'](http://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) namely: Users, Roles, and Groups.

A User defines an individual person. You would apply a User to each person that will be working within the given AWS account. Users are also provided security credentials in the form of a Public Access Key and a Private Access Key.

[AWS highly recommends access to AWS services through IAM Users rather than through the 'root' AWS account and its credentials](http://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#create-iam-users). This is to prevent exposing your root-account's secret, all-powerful, access credentials. This is also a way to ensure that you only access the services you plan to access, through manually allowing their accessibility through Groups (which are covered below).

[Roles](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) are similar to a User, except that the Role is defined by a security Policy and can be assumed by any person that needs access to a service. Roles are not given static login credentials and instead have dynamically generated login credentials any time a User is assigned to that Role.

[Groups](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups.html) are designations, to which Users can be assigned, that specify a set of 'access to services' permissions. To provide an individual User with permissions, one must assign that User to a group.

Now that you understand the basics of IAM 'Identities', Create a new IAM User and download the given credentials file (credentials.csv). Since these credentials are only available this one time, make sure to store them in a safe place. If you lose these credentials, you will need to create a new User.

Once you have these security credentials in a safe place, use the AWS CLI to configure your credentials. This is done with the command:

```
aws configure
```

Which will prompt you to enter your security credential keys, your region, and a desired output format.

```
$ aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-east-1
Default output format [None]: json
```

Upon successful completion, 'config' and 'credentials' files will be automatically generated in the hidden directory: '~/.aws' and if you are having trouble with this step, [see the documentation](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html) for more thorough instructions.

To test for successful configuration, try to access AWS through the command:

```
aws ec2 describe-regions
```

What you should find is that your terminal returns the following error message:

```
A client error (UnauthorizedOperation) occurred when calling the DescribeRegions operation:
You are not authorized to perform this operation.
```

Why does this happen? Think back to IAM Groups. To authorize the User's access to AWS services we need to assign this User to a group with the appropriate permissions. This is a result of AWS's conservative permissions philosophy that it is safer to grant permissions case by case, rather than to provide permissions upfront w/ subsequent revocation.

To remedy this blocker, return to your AWS account's IAM interface, and select 'Groups' under 'IAM Resources.' Next, select 'Create New Group,' give that group a name (I went with 'Admin'), and select/toggle relevant permissions, designated by 'Policy Name,' to add them to the Group's functional capacity (I went with 'AdministratorAccess'). Lastly, click through the remaining dialogues and create the group.

After creating the Group, add Users to the Group by, on the IAM Groups page, clicking on the Group you've just created. This will bring you to this new Group's 'Summary' page. Select the 'Users' tab, and then select the 'Add Users to Group' button. Lastly, select the User(s) that you wish to add to the group and select 'Add Users'.

The User added to the Group should now have that Groups permissions, and the command that we tested above should return a valid response.

# 3. Setting up CloudTrail and Billing Alerts
------------------

Since we have set up our appropriate IAM User Permissions, we are ready to create our first EC2 instance. However, before we do that, I want to detail two services that AWS provides to aid in account management:

The first of these services is AWS CloudTrail. CloudTrail provides a record of all the activity on your account and can be quickly activated through the steps on [the CloudTrail landing page](https://aws.amazon.com/cloudtrail/).

The second of these services is AWS Billing Alarms, which can prove useful in monitoring expenditures related to your accounts activities. These Alerts can help detect unwanted, unplanned, or left-running services on your account, before they get to expensive.

As a default, I've set up one Billing Alarm to email me if I exceed $.01 (which will let me know when I exceed the limits of my free trial), and I've set up one Billing Alarm to email me if I exceed $10.00 (which I deem a reasonable 'substantial charge' in the context of AWS experimentation).

# 4. Initiating Our First EC2 Instance
------------------

Now that we've established our IAM settings, configured out local machine for AWS access, and added some responsible account management features, we're ready to get to this post's exciting finale of initiating an AWS EC2 Ubuntu instance.

First, we need to set up an [EC2 Security Group](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html). EC2 Security Groups act as "a virtual firewall that controls the traffic for one or more instances". We also neet to create an EC2 Key Pair. To complete these two steps, I followed the 'Step 2' instructions in [this linked documentation.](https://docs.aws.amazon.com/cli/latest/userguide/tutorial-ec2-ubuntu.html#configure-cli-launch-ec2)

As you will see in this documentation, first we create a security group with the following code. It will return a security group ID, which we will need as input to our EC2 initialization command:

```
aws ec2 create-security-group --group-name full-access \
--description "security group for full access to EC2"
```

Next, we authorize input 'ingress' authorizations by allowing access to specific IP ranges (through CIDR notation), or to an IAM User. If, like me, you need to read up on CIDR notation and IP subnet masking, check [these youtube videos](https://gist.github.com/alexkapps/7e63072dad146cf93a7b9101e725aab9) Either of the following commands worked for me:

```
aws ec2 authorize-security-group-ingress \
--group-name full-access --protocol tcp \
--port 22 \
--group-owner <IAM USER> \
--source-group full-access

aws ec2 authorize-security-group-ingress \
--group-name full-access \
--protocol tcp \
--port 22 \
--cidr <YOUR CIDR IP SUBNET>
```

Next, open the command line and create an EC2 login key with the following command. Then, use chmod to change the file permissions so that only you have access to the key file:

```
aws ec2 create-key-pair \
--key-name alex-key \
--query 'KeyMaterial' \
--output text > alex-key.pem 

chmod 400 alex-key.pem
```

Finally, we'll enter in the command to initiate our EC2 Ubuntu instance. Enter in the following command with your relevant image-id (I used the default Ubuntu image), security-group-ids (which we created in the previous steps), instance-type (I used the t2.small which, unlike the t2.micro, IS NOT INCLUDED IN THE FREE TRIAL), and key-name (which we created in the previous step):

```
aws ec2 run-instances --image-id ami-fce3c696 \
--security-group-ids sg-ae07b1d5 \
--count 1 \
--instance-type t2.small \
--key-name alex-key \
--query 'Instances[0].InstanceId'
```

Congratulations! You've created your first AWS EC2 instance! Next, we'll retrieve the IP adress of this instance and then use it to SSH in via the command line. These two steps are accomplished with the following commands:

```
aws ec2 describe-instances \
--instance-ids i-03bbea23e0f752024 \
--query 'Reservations[0].Instances[0].PublicIpAddress' 

ssh -i alex-key.pem ubuntu@52.91.88.28 <-- Your relevant IP Address
```

And you're in!

# 5. Closing
------------------

This is just an empty, fresh, Ubuntu install. To create anything interesting we will have to install some software and bring in some data. Since you probably want to get started right away with your experimenting, [Jacques Tardie has a good blog post (which inspired this post) on setting up EC2 with Docker and landsat-util](https://jqtr.de/technical/2015/05/26/compute-remotely.html). As for additional next steps, I plan to write my next post about bringing imagery processing tools and data into EC2 based workflows. Stay tuned!

Thanks for reading!

-Alex


