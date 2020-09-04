# Jenkins - Table of Contents
- [Jenkins - Table of Contents](#jenkins---table-of-contents)
    - [Jenkins Installation](#jenkins-installation)
    - [Setup JDK for Jenkins](#setup-jdk-for-jenkins)
    - [Installing the Git plugin](#installing-the-git-plugin)
    - [Setup maven](#setup-maven)
    - [Create a Jenkins Freestyle Project](#create-a-jenkins-freestyle-project)
      - [Build with Parameters](#build-with-parameters)
    - [Jenkins Github Webhook](#jenkins-github-webhook)
    - [Jenkins Maven Build Project](#jenkins-maven-build-project)
    - [Maven build phases](#maven-build-phases)
    - [Managing access control and authorization](#managing-access-control-and-authorization)
    - [Maintaining roles and project-based security](#maintaining-roles-and-project-based-security)
    - [Role-Based-Authorization Strategy](#role-based-authorization-strategy)
    - [Audit Trail Plugin – an overview and usage](#audit-trail-plugin--an-overview-and-usage)
    - [Jenkins Build with Jenkinsfile](#jenkins-build-with-jenkinsfile)
  - [Continuous Delivery](#continuous-delivery)


### Jenkins Installation
- Launch an EC2 instance with Amazon Linux 2 with below userdata
```
#!/bin/bash
sudo yum update -y
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io.key
sudo yum install java-1.8.0 -y
sudo yum install jenkins -y
sudo service jenkins start
```
This package installation will:
- Setup Jenkins as a daemon launched on start. See `/etc/init.d/jenkins` for more details.
- Create a `jenkins` user to run this service.
- Direct console log output to the file `/var/log/jenkins/jenkins.log`.
- Check this file if you are troubleshooting Jenkins.
- Populate /etc/default/jenkins with configuration parameters for the launch, e.g JENKINS_HOME
- Set Jenkins to listen on port 8080. Access this port with your browser to start configuration.

- Login to EC2 Jenkins Server using ssh.
```
netstat -nltp
sudo service jenkins status
sudo service jenkins stop
sudo service jenkins restart
```

- Check Jenkins Port Information
```
sudo cat /etc/sysconfig/jenkins | grep -i port
ps -elf | grep 8080
```
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
- Select `Install Suggested Plugins` only

- It will ask for creating for first admin user, enter details as required.

- A linux user with name `jenkins` is created while Jenkins Installation, add `jenkins` user in linux to `sudoers` group
```
cat /etc/passwd | grep -i 'jenkins'
sudo usermod -a -G wheel jenkins
id jenkins
```
- Current home directory for jenkins is **/var/lib/jenkins**
- To view the Jenkins Systems Information, navigate to `Manage Jenkins` > `System Information`
### Setup JDK for Jenkins
- Install OpenJDK 8 JDK
- To install OpenJDK 8 JDK using yum, run this command:
```
sudo yum install java-1.8.0-openjdk-devel
```
- Use below find command to search for files with name "jdk"
```
sudo find / -name "*jdk1*"
```
Provide the path under provide the path under :
Go to `Jenkins Dashboard -> Manage Jenkins -> Global Tool Configuration > JDK` > Give a Name > Give appropriate path to JDK e.g `/usr/lib/jvm/java-1.8.0-openjdk`

### Installing the Git plugin
- We need to install the Git client on to our Jenkins server
```
sudo yum install git -y
git --version
```
- Check if Git Plugin is installed under:
`Manage Jenkins > Manage Plugins > Installed Tab > Filter "Git Plugin" `, if not install it from `available` tab.
- This will prompt Jenkins to download the plugin, install it, and restart Jenkins to make it available for use.

### Setup maven
- Go to `Jenkins Dashboard` -> `Manage Jenkins` -> `Global Tool Configuration` > `Maven` > Give a Name `Maven_Local` > Check `Install Automatically` > Install from Apache (specify a version) > `Save`
- You can give a logical name to identify the correct version while configuring a build job

### Create a Jenkins Freestyle Project
- Click on **New Item** then enter an item name, select **Freestyle project**.
- Enter some build commands i.e bash commands `printenv` to be executed in the freestyle project.

#### Build with Parameters
- Sometimes, it is useful/necessary to have your builds take several "parameters."
- This can to run a Pipeline Job as per SDLC Environment or any other value to be passed on Job Runtime.
- Under a specific jenkins project, select `Configure` option, select the checkbox `This project is parameterized` and `Add Parameter`
- For testing the value of the runtime parameter, keep `Source Code Management` as `None`
- The parameters are available as environment variables. So a shell $PARAM_VAR,can be used to access these values.

- Select the GitHub project checkbox and set the Project URL to point to your GitHub Repository. `https://github.com/YourUserName/`

- Under Source Code Management Section: Provide the Github Repository URL of the Maven Project, keep the branch as master.

- Go to Jenkins Project -> Configure -> Under Build Environment Build Step > Select "Invoke top-level Maven targets" from dropdown > select the Maven Version that we just created.

- Click on Build and select Invoke top-level Maven targets > select the Maven Version that you just created
#Enter `clean install` > Save

- Click on Build Now to Build this Project

- We will configure Build Triggers after manual build

- Click on **Build Now** to Build this Project

- Once build is successful, lets add webhook to the github project

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

- Select the GitHub project checkbox and set the Project URL to point to your GitHub Repository.
https://github.com/YourUserName/

- Under Source Code Management tab, select Git and then set the Repository URL to point to your GitHub Repository.

https://github.com/YourUserName/repo-name.git

- Now Under Build Triggers tab, select the “Build when a change is pushed to GitHub” checkbox.

- At the end, execute Shell script to take a clone from dev. When the configuration is done, click on save button.

- Click OK and Build a Job and you will see that a war file is created and as soon as build is successfully triggered , same war file is copied by other project (Other project to build)

Download the required Git version based on the operating system, or install automatically.

### Maven build phases

> **Validate** : Validate Project is correct & all necessary information is available.
**Compile** : Compile the Source Code
**Test** : Test the Compiled Source Code using suitable unit Testing Framework (like JUnit)
**package** : Take the compiled code and package it.
**Install** : Install package in Local Repo, for use as a dependency in other project locally.
**Deploy** : Copy the final package to the remote repository for sharing with other developers.

- The above are always are sequential, if you specify "install", all the phases before "install" are checked.
### Managing access control and authorization
Managing access control and authorization
- Go to Manage Jenkins > Configure Global Security > Enable security.

- On the Jenkins dashboard, click on Manage Jenkins. Click on Manage Users.
- We can edit user details on the same page. This is a subset of users, which also contains auto-created users.

### Maintaining roles and project-based security

For authorization, we can define Matrix-based security on the Configure Global
Security page.
1. Add group or user and configure security based on different sections such as
Credentials, Slave, Job, and so on.
2. Click on Save.

- Lets configure some users in Jenkins, create a read only user
`Select Manage Jenkins > Manage Users > Create a user`

- Try to access the Jenkins dashboard with a newly added user who has no rights, and we will find the authorization error.

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

> Audit Trail Plugin keeps a log of users who performed particular Jenkins operations, such as configuring jobs.
This plugin adds an Audit Trail section in the main Jenkins configuration page.
Here you can configure log location and settings (file size and number of rotating log files), and a URI pattern for requests to be logged. The default options select most actions with significant effect such as creating/configuring/deleting jobs and views or delete/save-forever/start a build. The log is written to disk as configured and recent entries can also be viewed in the Manage / System Log section.

## Continuous Delivery
> Continuous Delivery (CD) is a DevOps practice that is used to deploy an application quickly while maintaining a high quality with an automated approach. It is about the way application package is deployed in the Web Server or in the Application Server in environment such as dev, test or staging. Deployment of an application can be done using shell script, batch file, or plugins available in Jenkins. Approach of automated deployment in case of Continuous Delivery and Continuous Deployment will be always same most of the time. In the case of Continuous Delivery, the application package is always production ready