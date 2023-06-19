# CI-CD-on-DevOps-Website-Solution
## Introduction
Jenkins is an open-source Continuous Integration server written in Java for orchestrating a chain of actions to achieve the Continuous Integration process in an automated fashion. Jenkins supports the complete development life cycle of software from building, testing, documenting the software, deploying, and other stages of the software development life cycle.

![jenkins_architecture](./Img/jenkins-continuous-integration-min.png)
#

Here I introduce Jenkins to automate code delivery to the NFS server


## Jenkins Web Architecture For CI Builds
#
 
![my_jenkins_architecture](./Img/2022-10-05_050932.png)

## Installing Jenkins Server

Spun up a web server on AWS cloud and SSH into it.

Installing JDK which is an important Java based package required for Jenkins to run.
```
sudo apt update
sudo apt install default-jdk-headless
```
![java_installation](./Img/1.java_installation.jpg)

```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins

sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```
![jenkins_installation](./Img/2.jenkins_installation_status.jpg)

Since Jenkins runs on default port 8080, open this port on the Security Group inbound rule of the jenkins server on AWS 
![jenkins_rule](./Img/3.jenkins_sg_rule.jpg)

Jenkins is up and running, copy and paste jenkins server public ip address appended with port 8080 on a web server to gain access to the interactive console. `<jenkins_server_public_ip_address>:8080`
![jenkins_install_success](./Img/4.jenkins_install_success.jpg)

The admin password can be found in the `'/var/lib/jenkins/secrets/initialAdminPassword'` path on the server.

![suggested_installation](./Img/5.suggested_install.jpg)
![plugin_installation](./Img/6.plugin_installation.jpg)
![login_success](./Img/7.login_success.jpg)

## Attaching WebHook to Jenkins Server

On the github repository that contains application code, create a webhook to connect to the jenkins job. To create webhook, go to the settings tab on the github repo and click on webhooks.
Webhook should look like this `<public_ip_of_jenkins_server>:8080/github-webhook/`
![webhook_creation](./Img/8.webhook_creation.jpg)

## Creating Job and Configuring GIT Based Push Trigger

On the jenkins server, create a new freestyle job
![creating_job](./Img/9.creating_job.jpg)

In configuration of the Jenkins freestyle job choose Git repository, provide there the link to the GitHub repository and credentials (user/password) so Jenkins could access files in the repository. Also specify the branch containing code

![git_url_input](./Img/10.git_url_input.jpg)
![specify_branch](./Img/11.specify_branch.jpg)
![specify_credentials](./Img/12.specify-credentials.jpg)

## Configuring Build Triggers

Specify the particular trigger to use for triggering the job. Click "Configure" on the jenkins job and add these two configurations

### 1. Configure triggering the job from GitHub webhook:

![adding_build_triggers](./Img/13.adding_build_triggers.jpg)

### 2. Configure "Post-build Actions" to archive all the files – files resulted from a build are called "artifacts".

![post_build_step](./Img/14.post_build_step.jpg)

At this point, our architecture has pretty much been built, lets taste it by making a change on any file on the Github repository and then push it to see the triggered job
![git_push](./Img/15.github_push.jpg)

The console output shows the created job and the successful build.
In this case the code on Github was built into an artifact on our Jenkins server workspace. Find the artificat by checking the status tab of the completed job .
![new_build](./Img/16.new_build.jpg)
![persisted_archive](./Img/17.persisted_archive.jpg)

Our created artifact can be found on our local terminal too at this path 
`/var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/`
![artifact_terminal](./Img/18.artifact_terminal.jpg)


