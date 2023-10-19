# Jenkins-deploy-to-AWSEB
Small guide for the creation of the highly-available webapp deployment after tests to AWS Elastic Beanstalk.

Zero downtime and rollback automatation.

![image](https://github.com/MrEkoebve/Jenkins-deploy-to-AWSEB/assets/74951744/be9f79fe-08fb-4457-9496-3c2e2c45603d)

Resouces:
- Azure VM environment - Linux Ubuntu Server
- Installed Jenkins webserver ( +Java)
- Github repository with the working webapplication
- Amazon Work Station Elastic Beanstalk environment


1. **SSH CONNECTION OF YOUR SERVER AND GIT REPO** 
Create public and private keys in your server. If you want to have a connection between your server and git, add SSH-keys to you Github.

Profile > Settings > SSH and GPG Keys > New SSH key > add. 

![image](https://github.com/MrEkoebve/Jenkins-deploy-to-AWSEB/assets/74951744/6aef437d-b12c-4fce-9327-5b75ca81e0f1)

Clone your repository to the server (remember: SSH is more secured way to do all operations). 

2. **AMAZON WEB SERVICES ELASTIC BEANSTALK**

Go to your AWS account. Choose availability zone of your project in the right corner. Find Elastic Beanstalk service. Create environment. Name it and choose code language, for example PHP 8.2. Choose sample application if you don’t have your working environment. In presets choose high/availability in case of requirements.

![image](https://github.com/MrEkoebve/Jenkins-deploy-to-AWSEB/assets/74951744/26094dfb-c578-4ec0-b082-383168f40b03)
 
Example of scalling
 
![image](https://github.com/MrEkoebve/Jenkins-deploy-to-AWSEB/assets/74951744/df752db4-c4dc-4704-824b-2d0215ed01e1)

Example of configuration updates

![image](https://github.com/MrEkoebve/Jenkins-deploy-to-AWSEB/assets/74951744/8c07796e-8a56-4404-ad18-8abf3fbdb6bb)

When minimum requirements are done you need to create a new “Jenkins user” to pass inside of AWS from Jenkins. Go to IAM. Create user, add rights to using Elastic Beanstalk. After that, make a new access key with the password. Save it somewhere in safe.

![image](https://github.com/MrEkoebve/Jenkins-deploy-to-AWSEB/assets/74951744/3824ab1a-9bdd-4aa2-a9ae-ca95f0fdad64)


3. **GIT WEBHOOK**

Setting > Webhooks
You need to add Payload URL (http://jenkins-ip/github-webhhok/). It will send trigger signal to Jenkins. If you need trigger only for push event, choose push event. 

![image](https://github.com/MrEkoebve/Jenkins-deploy-to-AWSEB/assets/74951744/eaa85f96-22e5-460c-bef5-2e9330ed241f)

4. **JENKINS**

Github plugin have to be installed. In not, so go to Dashboard > Manage Jenkins > Plugins > Available > Git Plugin. Go to credentials and add it to github connection. Dashboard > Manage Jenkins > Credentials > Add credentials > SSH Username with private key

![image](https://github.com/MrEkoebve/Jenkins-deploy-to-AWSEB/assets/74951744/1c70687e-4ddb-47e7-8488-441aa0f57454)

In addition, add AWSEB Plugin and Credentials. Go Dashboard > Manage Jenkins > Plugins > Available > AWS Elastic Beanstalk Deployment. When plugin is installed go to Dashboard > Manage Jenkins > Credentials > Add credentials > AWS Credentials. Add you “Jenkins user” from AWS and put Access Key and required password (Secret Access Key)

![image](https://github.com/MrEkoebve/Jenkins-deploy-to-AWSEB/assets/74951744/953487f5-34bf-4497-b8dc-01c878fc8476)

5. **JENKINS CONTINIOUS DEPLOYMENT**

On the Dashboard click ”New Item”. Name it and you can choose a freestyle project. In settings you can change how many last versions of builds you need to keep and how many days. After that, go straight to Source Code Management and find GIT. Put repository of your project in Github ( SSH connection is more secured) and add previosly added credentials of Github. Choose your right branch.

![image](https://github.com/MrEkoebve/Jenkins-deploy-to-AWSEB/assets/74951744/942d6017-4f4d-49fd-81d9-25c5e1fa5d92)

If you need to do any test in shell you can Add build step > Execute shell. 

Now it is possible to add AWSEB to successful deployment. Press “Add build step” and choose “AWS Elastic Beanstalk”. Add your credentials to aws (previously added). Click right availability zone of your webapp in AWSEB and how many times Jenkins will try to connect to the environment of webapp. Fill the name of your project and its environment. In packaging choose directory/fie to clone. (if it full repository, you can add a dot (.)) You can add some extra steps to your project. For example, name of the build ${BUILD_ID}. After that you can apply and save this configuration. All versions of your webapp you can find in AWSEB Applications > applications versions. 
