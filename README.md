# Jenkins Remoting Project

CodeAlpha DevOps Internship — Task 2
Created by Kayalvili Balendran

## About
A Jenkins controller assigns a Java build to a separate Docker agent.
The agent downloads code from GitHub, builds it with Gradle,
runs three tests, and creates a ZIP package.

Both containers run on the same laptop.

## Tools Used
- Docker Desktop
- Jenkins
- Java 21
- Gradle
- GitHub

## Controller Setup
Run these commands in PowerShell:

```powershell
docker network create codealpha-jenkins
docker volume create codealpha-jenkins-data
docker run -d --name codealpha-jenkins-controller --network codealpha-jenkins -p 127.0.0.1:8081:8080 -v codealpha-jenkins-data:/var/jenkins_home jenkins/jenkins:lts-jdk21
```

Open http://localhost:8081 and complete Jenkins setup.

Set the Built-In Node's number of executors to 0.

## Agent Settings
In Manage Jenkins → Nodes, create a Permanent Agent:

- Name: codealpha-agent
- Executors: 1
- Remote root directory: /home/jenkins/agent
- Label: docker-agent
- Usage: Only build jobs with matching labels
- Launch method: Launch agent by connecting it to the controller

## Start the Agent
Copy the actual agent secret from its Jenkins connection instructions.
Keep the secret private.

In PowerShell:

```powershell
$env:JENKINS_SECRET = Read-Host "Paste the actual agent secret"
docker run -d --init --name codealpha-jenkins-agent --network codealpha-jenkins -e JENKINS_SECRET jenkins/inbound-agent:jdk21 -url http://codealpha-jenkins-controller:8080/ -name codealpha-agent -webSocket -workDir /home/jenkins/agent
Remove-Item Env:\JENKINS_SECRET
```

Confirm that Jenkins shows "Agent is connected."

## Run the Pipeline
Create a Pipeline job named CodeAlpha-Agent-Build.
Paste the Jenkinsfile contents into its Pipeline script.
Save and select Build Now.

## Verified Results
- Build runs on codealpha-agent
- Three calculator tests pass
- Application ZIP is archived
- Pipeline finishes with SUCCESS

## Limitations
This setup demonstrates separate controller and agent containers
on one laptop. It has not been tested across separate machines
or different CPU architectures.