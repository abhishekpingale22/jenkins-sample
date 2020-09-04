- [Distributed Jenkins Builds](#distributed-jenkins-builds)
  - [Master](#master)
  - [Distributed Builds Architecture](#distributed-builds-architecture)
  - [Advantages of Distributed Builds](#advantages-of-distributed-builds)
- [Distributed Jenkins Builds-Practical](#distributed-jenkins-builds-practical)
### Distributed Jenkins Builds
- Distributed Builds run on nodes other than the master node.
  - Additional nodes are configured and the master node manages these nodes to do the actual build executions.

#### Master
- Serves HTTP requests
- Stores all important information

#### Distributed Builds Architecture


#### Advantages of Distributed Builds
- ${JENKINS_HOME} is protected from malicious builds
- Makes the Jenkins instance more scalable
- If you do not have enough resources to run your builds, just add more agents
- Distributes the build load for better resource utilization
- Run different operating systems, use different CPU architectures, or different JDK versions

### Distributed Jenkins Builds-Practical
- Here we can create nodes and agents to create a distributed Jenkins instance that is scalable and secure.
- The target is to have:
  - 2 ssh agents that handle builds
  - Each agent has 1 executor
    - We have 2 CPUs available; rule of thumb says ~2 executors
  - Consider that the master does not have any executors, so no builds run on master
- Login to Jenkins Home Console
Start by logging into http://localhost:5000/jenkins using the following credentials:
    Username: butler
    Password: butler
- To managed nodes using Jenkins, navigate to:
  - Go to Manage Jenkins → Managed Nodes
Please note that the lab instance is already configured with two agents. We will first go ahead and delete those.

    You will see two agents, jdk7-node and jdk-8-node