# Jenkins-Table of Contents
- [Jenkins-Table of Contents](#jenkins-table-of-contents)
    - [Jenkins CICD-Steps](#jenkins-cicd-steps)
    - [Setup JDK for Jenkins](#setup-jdk-for-jenkins)
    - [Installing the Git plugin](#installing-the-git-plugin)
    - [Setup maven](#setup-maven)
    - [To install mvn locally:](#to-install-mvn-locally)
### Jenkins CICD-Steps
- Jenkins Installation
- Launch an EC2 instance with Amazon Linux 2
- Jenkins requires Java. To install Java, execute:

```
sudo yum install java
```
#Install Jenkins using YUM repository. Create a .repo file for jenkins
```
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
OR
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhatstable/jenkins.repo
```
- view the file and import the following key
```
cat /etc/yum.repos.d/jenkins.repo
sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
```
- Install Jenkins and start Jenkins Service , other commands for stop and restart are:
```
sudo yum install jenkins
sudo service jenkins start

netstat -nltp
sudo service jenkins stop
sudo service jenkins restart
```
- Below file is contains port information for jenkins service
`sudo cat /etc/sysconfig/jenkins | grep -i port`


- Lets look at the jenkins log file
```
sudo cat /var/log/jenkins/jenkins.log
```

- Access the Jenkins UI
```
http://public-ip:8080
```

- Admin Password is written to a file
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
- As of now select Install Suggested Plugins only

- It will ask for creating for first admin user, enter details as required.

- To add jenkins user in linux to sudoers group

```
sudo usermod -a -G wheel jenkins
id jenkins
```
- Current home directory for jenkins is (/var/lib/jenkins)
### Setup JDK for Jenkins
- Install OpenJDK 8 JDK
- To install OpenJDK 8 JDK using yum, run this command:
```
sudo yum install java-1.8.0-openjdk-devel
```

Provide the path under provide the path under :
Go to Jenkins Dashboard -> Manage Jenkins -> Global Tool Configuration > JDK > Give a Name > Give appropriate path to JDK e.g `/usr/lib/jvm/java-1.8.0-openjdk`

### Installing the Git plugin
- We need to install the Git client on to our Jenkins server
```
sudo yum install git -y
git --version
```
- This will return the version of the Git client that you installed.
- Go to `Manage Jenkins > Manage Plugins > Available Tab > Filter "Git Plugin" `
This will prompt Jenkins to download the plugin, install it, and restart Jenkins to make it available for use.
### Setup maven
- Go to `Jenkins Dashboard` -> `Manage Jenkins` -> `Global Tool Configuration` > `Maven` > Give a Name `Maven_Local` > Check `Install Automatically` > Install from Apache (specify a version) > `Save`
- You can give a logical name to identify the correct version while configuring a build job:

------Jenkins Maven Project------
- Here create a Local Build Environment for Maven, 
### To install mvn locally:
```
cd /opt/
wget http://mirrors.estointernet.in/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
sudo tar -xvzf /opt/apache-maven-3.6.3-bin.tar.gz
Edit the /etc/environment file and add the following environment variable:
M2_HOME="/opt/apache-maven-3.6.3"
source /etc/environment
Append the bin directory to the PATH variable:
export PATH="/opt/apache-maven-3.6.3/bin:$PATH"
```
### Install Apache Tomcat on Amazon Linux:
```
cd /opt/
sudo wget http://mirrors.estointernet.in/apache/tomcat/tomcat-9/v9.0.30/bin/apache-tomcat-9.0.30.tar.gz
sudo tar -zvxf apache-tomcat-9.0.30.tar.gz
ls -ltr /opt/apache-tomcat-9.0.30/bin
cd /opt/apache-tomcat-9.0.30/bin
```
- To Start Apache Tomcat : Run the `./startup.sh` file in `/opt/apache-tomcat-9.0.30/bin`
>If you want to run Apache Tomcat on same Machine where Jenkins is Installed, then change the port of Apache Tomcat in : `/opt/apache-tomcat-9.0.30/conf/server.xml` file to `8090` as below and restart Apache Tomcat using `./shutdown.sh && ./startup.sh`
 ```
 <Connector port="8090" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```
-------------------------------
- Create an empty repo and clone it, add project files into the local git folder and commit -> push the local repo to remote github repo using Git Bash
- verify the files are available in your github repository
### Create a Jenkins Freestyle Project
- Click on **New Item** then enter an item name, select **Freestyle project**.
- Select the GitHub project checkbox and set the Project URL to point to your GitHub Repository. `https://github.com/YourUserName/`

- Under Source Code Management Section : Provide the Github Repository URL of the Maven Project, keep the branch as master.

- Go to Jenkins Project -> Configure -> Under Build Environment Build Step > Select "Invoke top-level Maven targets" from dropdown > select the Maven Version that we just created.

- Click on Build and select Invoke top-level Maven targets > select the Maven Version that you just created 
#Enter `clean install` > Save
- Click on Build Now to Build this Project

- We will configure Build Triggers after manual build

- Click on **Build Now** to Build this Project


### Jenkins Github Webhook
- Integrate jenkins with github so automatically CICD works when any commit is made to the repo
Go to Jenkins > Manage Jenkins > Add a Github Server
Enter URL : http://public-ip:8080/github-webhook/

- Lets add a webhook in Github to point to Jenkins URL
- In `Github Repository > Go to Repository > Settings > Go to webhook and addnew webhook > Specify http://35.153.194.83/github-webhook/`
- Go to Jenkins Project, Select the “Build when a change is pushed to GitHub” checkbox under Build Triggers tab > `Save`

- For Webhook to work, open port 8080 in security group.

- Now if we make some changes to some file in Github, this Jenkins Project should be triggered.

### Jenkins Maven Build Project
- To Create a new task for Jenkins, click on “New Item” then enter an item name that is suitable for your project and select Freestyle project. Now click Ok.


### Tomcat War file deployment

During the deployment phase, we'll have some options, one of which is to use Tomcat's management dashboard. To access this dashboard, we must have an admin user configured with the appropriate roles.

To have access to the dashboard the admin user needs the manager-gui role. Later, we will need to deploy a WAR file using Maven, for this, we need the manager-script role too.

Let's make these changes in $CATALINA_HOME\conf\tomcat-users:
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<user username="admin" password="password" roles="manager-gui, manager-script"/>

------Jenkins Maven Project------

Step 1 To Create a new task for Jenkins, click on “New Item” then enter an item name that is suitable for your project and select Freestyle project. Now click Ok.

Step 2 Select the GitHub project checkbox and set the Project URL to point to your GitHub Repository.
https://github.com/YourUserName/

Step 3 Under Source Code Management tab, select Git and then set the Repository URL to point to your GitHub Repository.

https://github.com/YourUserName/repo-name.git

Step 4 Now Under Build Triggers tab, select the “Build when a change is pushed to GitHub” checkbox.

Step 5 At the end, execute Shell script to take a clone from dev. When the configuration is done, click on save button.

Step 6 Click OK and Build a Job and you will see that a war file is created and as soon as build is successfully triggered , same war file is copied by other project (Other project to build)

#Create a new job in Jenkins
#Provide a Name for Project
#under Source Code Management , select "git" and enter your github repo url
#If you get an error "Failed to connect to repository"


1. Go to Manage Jenkins > Go to Global Tool Configuration to configure the tools, their locations, and automatic installers.
2. Go to the Git section > Give the Name and click on Install Automatically, or provide a path to Git.

When you create a build job in Jenkins and configure it, you need to specify the Git version that will be used by the build execution. You can use existing Git installation available on the system as well if you don't want to install automatically.
In the Source Code Management section, select the appropriate Git that is configured in Global
Tool Configuration from the list.
Go to the Git executable configuration to change the Git installable

Download the required Git version based on the operating system, or install automatically.

https://github.com/octocat/Hello-World.git

#github url


#Lets configure some users in Jenkins, create a read only user
Select Manage Jenkins > Manage Users > Create a user


#To Install Build Pipeline Plugin in Jenkins
#Manage Jenkins > Manage Plugins > search for build Pipeline under Available/Installed tab
#If it is under available tab, install it and it will restart jenkins after installation

#
Select the Enable security flag. The easiest way is to use Jenkins own user database. Create at least the user "Anonymous" with read access. Also create entries for the users you want to add in the next step.


### Managing access control and authorization
Managing access control and authorization
- Go to Manage Jenkins > Configure Global Security > Enable security.

- On the Jenkins dashboard, click on Manage Jenkins. Click on Manage Users.
 We can edit user details on the same page. This is a subset of users, which
also contains auto-created users.

### Maintaining roles and project-based security

For authorization, we can define Matrix-based security on the Configure Global
Security page.
1. Add group or user and configure security based on different sections such as
Credentials, Slave, Job, and so on.
2. Click on Save.

- Lets configure some users in Jenkins, create a read only user
Select Manage Jenkins > Manage Users > Create a user

Try to access the Jenkins dashboard with a newly added user who has no rights, and we will find the authorization error.

### Role-Based-Authorization Strategy
- Add plugin from available tab in Plugins Manager
- Select the Role Based Strategy
- Manage Jenkins > Manage and Assign Roles > Assign read only roles to a user in jenkins
- create a role with "manage Role" , select Read Permissions
- Assign a role to another user
### Audit Trail Plugin – an overview and usage
- Manage Jenkins > Manage Plugins > Install the `Audit Trail` Plugin.
- Go to Manage Jenkins > Configure Systems > Audit Trail > Add Logger > Select Log 
- Provide the Log Location as `/var/log/jenkins/audit-%g.log`, provide Log File Size as `50` and Log File Count `10`.
- After executing some build job for some Jenkins Project, check the content of the audit file as below.
```
ls -ltr /var/log/jenkins/
```

### Maven build phases

> **Validate** : Validate Project is correct & all necessary information is available.
**Compile** : Compile the Source Code
**Test** : Test the Compiled Source Code using suitable unit Testing Framework (like JUnit)
**package** : Take the compiled code and package it.
**Install** : Install package in Local Repo, for use as a dependency in other project locally.
**Deploy** : Copy the final package to the remote repository for sharing with other developers.

- The above are always are sequential, if you specify "install", all the phases before "install" are checked. 

---
### Jenkins Build with Jenkinsfile
- Add below content as Jenkinsfile and push in Github.

- Provide a name for your new item and select Pipeline

- Click the Add Source button, select git choose the type of repository you want to use and fill in the details.

- Click the Save button and watch your first Pipeline run!
```
pipeline {
    agent { docker { image 'python:3.5.1' } }
    stages {
        stage('build') {
            steps {
                sh 'python --version'
                sh 'echo Hello Jenkins'
            }
        }
    }
}
```
- Since above `Jenkinsfile` contains a stage with docker agent, we need to install docker on the Jenkins Node. 
```
#install docker

sudo yum install docker -y
sudo systemctl start docker

#add jenkins user to docker and wheel group

sudo usermod -aG wheel jenkins
sudo usermod -aG docker jenkins

#Restart jenkins
sudo systemctl restart jenkins

```
- Click on **Build Now** to build the jenkinsfile project
-----------------------
> Audit Trail Plugin keeps a log of users who performed particular Jenkins operations, such as configuring jobs.
This plugin adds an Audit Trail section in the main Jenkins configuration page. 
Here you can configure log location and settings (file size and number of rotating log files), and a URI pattern for requests to be logged. The default options select most actions with significant effect such as creating/configuring/deleting jobs and views or delete/save-forever/start a build. The log is written to disk as configured and recent entries can also be viewed in the Manage / System Log section. 

## Continuous Delivery
- Archiving artifacts
- Copying an artifact from another build job
- Integrating Jenkins with Artifactory
- Deploying a WAR file from Jenkins to Tomcat
- Deploying a WAR file from Jenkins to AWS Beanstalk
- Deploying a WAR file from Jenkins to Azure App Services
- Promoting builds

> Continuous Delivery (CD) is a DevOps practice that is used to deploy an application quickly while maintaining a high quality with an automated approach. It is about the way application package is deployed in the Web Server or in the Application Server in environment such as dev, test or staging. Deployment of an application can be done using shell script, batch file, or plugins available in Jenkins. Approach of automated deployment in case of Continuous Delivery and Continuous Deployment will be always same most of the time. In the case of Continuous Delivery, the application package is always production ready

## Jenkins-D2.md
#### Pre-requisites
- Below steps assume that, you have a Jenkins Server Up and Running on one of the EC2 instance.

- If above changes are made, execute the command `tomcatup` and `tomcatdown`

- Create an empty repo and clone it, add project files into the local git folder and commit -> push the local repo to remote github repo using Git Bash.
- Verify the files are available in your github repository



    - The Tomcat URL is the base URL through which your Tomcat instance can be reached (e.g http://172.31.67.85:8090)
    >Make Sure network is open on specific port
    - `Save` the changes and `Build Now`.


- If you check the directory structure, there will be archive directory present under the subsequent build number for which the job is executed with Post build action as `Archive the artifacts`

- Once build is successfull , lets add webhook to the github project.




- Now if we make some changes to some file in Github, this Jenkins Project should be triggered.
- Lets configure some users in Jenkins, create a read only user :
`Select Manage Jenkins` > `Manage Users` > `Create a user`

### Managing access control and authorization
Managing access control and authorization
- Go to `Manage Jenkins` > `Configure Global Security` > `Enable security`.

- On the Jenkins dashboard, click on Manage Jenkins. Click on Manage Users.
- We can edit user details on the same page. This is a subset of users, which also contains auto-created users.

### Maintaining roles and project-based security

For authorization, we can define Matrix-based security on the Configure Global Security page.
1. Add group or user and configure security based on different sections such as Credentials, Slave, Job, and so on.
2. Click on Save.

- Lets configure some users in Jenkins, create a read only user
Select Manage Jenkins > Manage Users > Create a user

Try to access the Jenkins dashboard with a newly added user who has no rights, and we will find the authorization error.

### Role-Based-Authorization Strategy
- Add plugin from available tab in Plugins Manager
- Select the Role Based Strategy
- Manage Jenkins > Manage and Assign Roles > Assign read only roles to a user in jenkins
- create a role with "manage Role" , select Read Permissions
- Assign a role to another user

### Audit Trail Plugin – an overview and usage
- Manage Jenkins > Manage Plugins > Install the `Audit Trail` Plugin.
- Go to Manage Jenkins > Configure Systems > Audit Trail > Add Logger > Select Log
- Provide the Log Location as `/var/log/jenkins/audit-%g.log`, provide Log File Size as `50` and Log File Count `10`.
- After executing some build job for some Jenkins Project, check the content of the audit file as below.
```
ls -ltr /var/log/jenkins/
```

### Archiving artifacts
1. Go to Jenkins dashboard -> Jenkins project or build job -> Post-build Actions -> Add post-build action -> Archive the artifacts:

You can use wildcards, such as module/dist/**/*.zip. The artifact archiver uses Ant
org.apache.tools.ant.DirectoryScanner, which excludes the following patterns by default: **/%*%,**/.git/**,**/SCCS,**/.bzr,**/.hg/**,**/.bzrignore,**/.git,**/SCCS/**,**/.hg,**/.#s

### Copying an artifact from another build job
1. Go to Jenkins dashboard | Project | Configure | Build | Add build step | Copy artifacts from
another project.
2. Give the Project name from which you want to copy the artifact.
3. For the Which build field, select appropriate options from the available list:
added this content to checkkwebhook testing
added this content to checkkwebhook testing

