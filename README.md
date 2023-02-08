# Project-14: EXPERIENCE CONTINUOUS INTEGRATION WITH JENKINS ANSIBLE ARTIFACTORY SONARQUBE PHP


## Table of Contents.
- [Introduction](#introduction)
    - [What is Continuous Integration?](#what-is-continuous-integration)
    - [CI Workflow](#ci-workflow)
    - [Continuous Integration in the Real World](#continuous-integration-in-the-real-world)
    - [Common Best Practices of CI/CD](#common-best-practices-of-cicd)
    - [Devops Success Metrics](#devops-success-metrics)
- [Prerequisites](#prerequisites)
- [Simulating a typical CI/CD Pipeline for a PHP based application](#simulating-a-typical-ci-cd-pipeline-for-a-php-based-application)
    - [Setting up the environment](#setting-up-the-environment)
    - [CI Environment](#ci-environment)
    - [Other Environments from Lower to Higher](#other-environments-from-lower-to-higher)
    - [DNS Requirements](#dns-requirements)
- [Ansible Inventory](#ansible-inventory)
    - [Inventory Files](#inventory-files)
        - [CI Environment Inventory File](#ci-environment-inventory-file)
- [Ansible Roles for CI Environment](#ansible-roles-for-ci-environment)
    - [Creating our Jenkinsfile](#creating-our-jenkinsfile)


## Introduction
In this project, we would understand and get hands-on experience with the entire concept of CI/CD from an applications perspective. This would help us to gain real expertise around this idea, it is best to see it in action across different programming languages and from the platform perspective. In this project, we would focus on the application perspective focusing on the language PHP

### What is Continuous Integration?
Continuous integration is a software development practice where developers regularly merge their code changes into a shared repository, such as a Git repository. This is done several times per day to avoid conflicts in the code and to ensure that the codebase remains stable and working. By running automated tests on the code after each merge, teams can catch and fix issues early, which helps to prevent "merge hell" and improve the overall quality of the code. Continuous integration helps teams to collaborate more effectively and deliver higher-quality software faster.

Note: <b>The CI concept is not only about committing our code. There is a general workflow.</b>

### CI Workflow
The CI workflow is as follows:

- <b>Run tests locally</b>: When you are working on a feature, you should run the tests locally to make sure that your code is working as expected. This is a good practice to follow even if you are not using CI. If you are using CI, you should run the tests locally before you commit your code to the repository. Developers write tests for their code called unit tests, and before they commit their work, they run their tests locally. This practice helps a team to avoid having one developer's work-in-progress code from breaking other developers’ copy of the codebase.

- <b>Compile code in CI</b>: After testing the code locally, you should commit your code to the repository. This is the second step in the CI workflow. The CI server picks up the code and runs the build there. In this project, we would use Jenkins as our CI server. The build happens either periodically or after every commit. The benefit of having a CI server is that it ensures that the code runs in a stable state and is visible to everyone. If the build fails, the CI server notifies the team members. This helps the team to fix the issue quickly.

- <b>Run further tests in CI</b>: Even though tests have been run locally by developers, it is important to run the unit tests on the CI server as well. But, rather than focusing solely on unit tests, other kinds of tests and code analysis can be run using a CI server. These are extremely critical to determining the overall quality of code being developed, how it interacts with other developers’ work, and how vulnerable it is to attacks. A CI server can use different tools for Static Code Analysis, Code Coverage Analysis, Code smells Analysis, and Compliance Analysis. In addition, it can run other types of tests such as Integration and Penetration tests. Other tasks performed by a CI server include the production of code documentation from the source code and facilitating manual quality assurance (QA) testing processes.

- <b>Deploy an artifact from CI</b>: Once the code has been tested and is ready to be deployed, the CI server can deploy the code to the production environment. This is the final step in the CI workflow. The CI server can deploy the code to the production environment automatically or manually. If the code is deployed automatically, the CI server can also perform a rollback if the deployment fails. This is a very important feature of a CI server. If the deployment fails, the CI server can roll back to the previous version of the code. This helps to avoid downtime and ensures that the production environment is always stable. 


On the other hand, is Continuous Delivery which ensures that software checked into the mainline is always ready to be deployed to users. The deployment here is manually triggered after certain QA tasks are passed successfully. There is another CD known as Continuous Deployment which is also about deploying the software to the users, but rather than manual, it makes the entire process fully automated. Thus, Continuous Deployment is just one step ahead in automation than Continuous Delivery.

### Continuous Integration in the Real World
To emphasize a typical CI pipeline further, let us explore the diagram below:

![CI Pipeline](https://www.darey.io/wp-content/uploads/2021/07/CI-Pipeline-Regular.png)

- <b>Version Control</b>: This is the stage where developers commit their code to the version control system after they have tested their code locally.

- <b>Build</b>: Depending on the type of language or technology used, we might need to build the code into binary executables for compiled languages or just package the code together with all necessary dependencies for interpreted languages.

- <b>Unit Test</b>: Unit tests that have been developed by the developers are run on the code. This is done to ensure that the code is working as expected. Depending on how the CI job is configured, the entire pipeline may fail if part of the tests fails, and developers will have to fix this failure before starting the pipeline again. A job is a phase in the pipeline and since Unit Test is a phase in the pipeline, it is called a job.

- <b>Deploy</b>: Once the tests are passed, the next phase is to deploy the compiled or packaged code into an artifact repository. This is where all the various versions of code are stored. This is also where the CI tool will have to pick up the code from this location to proceed with the remaining parts of the pipeline.

- <b>Auto Test</b>: Apart from Unit testing, there are many other kinds of tests that are required to analyze the quality of code and determine how vulnerable the software will be to external or internal attacks. These tests must be automated, and there can be multiple environments created to fulfill different test requirements. For example, a server dedicated to Integration Testing will have the code deployed there to conduct integration tests. Once that passes, there can be other sub-layers in the testing phase to which the code will be deployed, to conduct further tests. Such is User Acceptance Testing (UAT), and another can be Penetration Testing. These servers will be named according to what they have been designed to do in those environments. A UAT server is generally used for UAT, SIT server is for Systems Integration Testing, PEN Server is for Penetration Testing and they can be named whatever the naming style or convention in which the team is used. An environment does not necessarily have to reside on one single server. In most cases, it might be a stack as you have defined in your Ansible Inventory. All the servers in the inventory/dev are considered as Dev Environment. The same goes for inventory/stage (Staging Environment) inventory/preprod (Pre-production environment), inventory/prod (Production environment), etc. So, it is all down to naming convention as agreed and used company or team-wide.

- <b>Deploy to production</b>: Once all the tests have been passed, and the release manager has approved the release, the code is deployed to the production environment. This is an ideal Continuous Delivery Pipeline. If the entire process is automated, it is called a Continuous Deployment Pipeline. This is because the cycle will be repeated, and every time there is a code commit and push, it causes the pipeline to trigger, and the loop continues over and over again.

- <b>Measure and Validate</b>: This are where real users are interacting with the application and feedback is collected for further improvements and bug fixes. 


### Common Best Practices of CI/CD
There are principles that define a reliable and robust CI/CD pipeline. These principles are:
- Maintain a code repository.
- Automate the build process.
- Make the build self-testing.
- Everyone commits to the baseline every day.
- Every commit (to baseline) should be built.
- Keep the build fast.
- Test in a clone of the production environment.
- Make it easy to get the latest deliverables.
- Everyone can see what’s happening and the results of the latest build.
- Automate deployment. This should be done if you're confident enough in your CI/CD pipeline and willing to go for a fully automated continuous deployment.


### Devops Success Metrics
Many metrics must be determined and observed here. We will quickly go through 13 metrics that must be determined and observed in a CI/CD pipeline. 

After all, DevOps is all about continuous delivery or deployment, and being able to ship out quality code as fast as possible. This is a very ambitious thing to desire; therefore, we must be careful not to break things as we are moving very fast. By tracking these metrics, we can determine our delivery speed and bottlenecks before breaking things. Ultimately, the goals of DevOps are enhanced Velocity, Quality, and Performance. But how do we track these parameters? Let us have a look at the 13 metrics to watch out for.

1 Deployment frequency: Tracking how often you do deployments is a good DevOps metric. Ultimately, the goal is to do more smaller deployments as often as possible. Reducing the size of deployments makes it easier to test and release. I would suggest counting both production and non-production deployments separately. How often you deploy to QA or pre-production environments is also important. You need to deploy early and often in QA to ensure enough time for testing.

2 Lead time: If the goal is to ship code quickly, this is a key DevOps metric. I would define lead time as the amount of time that occurs between starting on a work item until it is deployed. This helps you know that if you started on a new work item today, how long would it take on average until it gets to production.

3 Customer tickets: The best and worst indicator of application problems is customer support tickets and feedback. The last thing you want is your users reporting bugs or having problems with your software. Because of this, customer tickets also serve as a good indicator of application quality and performance problems.

4 Percentage of passed automated tests: To increase velocity, it is highly recommended that the development team makes extensive usage of unit and functional testing. Since DevOps relies heavily on automation, tracking how well automated tests work is a good DevOps metrics. It is good to know how often code changes break tests.

5 Defect escape rate: Do you know how many software defects are being found in production versus QA? If you want to ship code fast, you need to have confidence that you can find software defects before they get to production. Defect escape rate is a great DevOps metric to track how often those defects make it to production.

6 Availability: The last thing we ever want is for our application to be down. Depending on the type of application and how we deploy it, we may have a little downtime as part of scheduled maintenance. It is highly recommended to track this metric and all unplanned outages. Most software companies build status pages to track this. Such as this Google Products Status Page

7 Service level agreements: Most companies have some service level agreement (SLA) that they promise to the customers. It is also important to track compliance with SLAs. Even if there are no formally stated SLAs, there probably are application non-functional requirements or expectations to be met.

8 Failed deployments: We all hope this never happens, but how often do our deployments cause an outage or major issues for the users? Reversing a failed deployment is something we never want to do, but it is something you should always plan for. If you have issues with failed deployments, be sure to track this metric over time. This could also be seen as tracking *Mean Time To Failure (MTTF).

9 Error rates: Tracking error rates within the application is super important. Not only they serve as an indicator of quality problems, but also ongoing performance and uptime related issues. In software development, errors are also known as exceptions, and proper exception handling is critical. If they are not handled nicely, we can figure it out while monitoring the rate of errors.

Bugs – Identify new exceptions being thrown in the code after a deployment
Production issues – Capture issues with database connections, query timeouts, and other related issues

Presenting error rate metrics like this simply gives greater insights into where to focus attention.


10 Application usage & traffic: After a deployment, we want to see if the number of transactions or users accessing our system looks normal. If we suddenly have no traffic or a giant spike in traffic, something could be wrong. An attacker may be routing traffic elsewhere, or initiating a DDOS attack

11 Application performance: Before we even perform a deployment, we should configure monitoring tools like Retrace, DataDog, New Relic, or AppDynamics to look for performance problems, hidden errors, and other issues. During and after the deployment, we should also look for any changes in overall application performance and establish some benchmarks to know when things deviate from the norm.

It might be common after a deployment to see major changes in the usage of specific SQL queries, web service or HTTP calls, and other application dependencies. These monitoring tools can provide valuable visualizations like this one below that helps make it easy to spot problems.



12 Mean time to detection (MTTD): When problems happen, it is important that we identify them quickly. The last thing we want is to have a major partial or complete system outage and not know about it. Having robust application monitoring and good observability tools in place will help us detect issues quickly. Once they are detected, we also must fix them quickly!

13 Mean time to recovery (MTTR): This metric helps us track how long it takes to recover from failures. A key metric for the business is keeping failures to a minimum and being able to recover from them quickly. It is typically measured in hours and may refer to business hours, not calendar hours.

These are the major metrics that any DevOps team should track and monitor to understand how well CI/CD process is established and how it helps to deliver quality application to the users.


## Prerequisites

## Simulating a typical CI/CD Pipeline for a PHP based application

As part of an ongoing infrastructure development with Ansible from our previous projects. Here we will be creating a pipeline that simulates continuous integration and delivery. The target end-to-end CI/CD pipeline is shown below:

![CI/CD Pipeline](https://www.darey.io/wp-content/uploads/2021/07/CI_CD-Pipeline-For-PHP-ToDo-Application.png)

<b>Note</b>: It is important to know that both Tooling and TODO web applications are based on an interpreted (scripting) language which is PHP. This means that it can be deployed directly onto a server and will work without compiling the code to a machine language.

The challenge behind this approach is, it would be difficult to package and version the software for different releases. And so, in this project, we will be using a different approach for releases, rather than downloading directly from git, we will be using Ansible's 'uri module' to download the latest release from GitHub.

### Setting up the Environment
This project is partly a continuation of your Ansible work, so simply add and subtract based on the new setup in this project. It will require a lot of servers to simulate all the different environments from dev/ci all the way to production. This will be quite a lot of servers altogether (But you don’t have to create them all at once. Only create servers required for an environment you are working with at the moment. For example, when doing deployments for development, do not create servers for integration, pentest, or production yet).

To get started we would focus on the following environments:

- CI
- Development (Dev)
- Pentest

Both SIT – For System Integration Testing and UAT – User Acceptance Testing do not require a lot of extra installation or configuration. They are basically the webservers holding our applications. But Pentest – For Penetration testing is where we will conduct security related tests, so some other tools and specific configurations will be needed. In some cases, it will also be used for Performance and Load testing. Otherwise, that can also be a separate environment on its own. It all depends on decisions made by the company and the team running the show.

What we want to achieve, is having Nginx to serve as a reverse proxy for our sites and tools. Each environment setup is represented in the below table and diagrams.

![CI](https://www.darey.io/wp-content/uploads/2021/07/Environment-setup.png)


#### CI Environment
![CI environment](https://www.darey.io/wp-content/uploads/2021/07/Project-14-CI-Environment.png)


#### Other Environments from Lower to Higher
![Other Environments](https://www.darey.io/wp-content/uploads/2021/07/Project-14-Pentest-Environment.png)


#### DNS Requirements
- Make DNS entries to create a subdomain for each environment. Assuming our main domain is "test.io"

We should have a subdomains list like this:


| Server | Domain |
| ------ | ------ |
| Jenkins | https://ci.infradev.test.io|
| Sonarqube | https://sonar.infradev.test.io|
| Artifactory | https://artifactory.infradev.test.io|
| Production tooling | https://tooling.test.io|
| Pre-production tooling | https://tooling.preprod.test.io|
| Pentest tooling | https://tooling.pentest.test.io|
| UAT tooling | https://tooling.uat.test.io|
| SIT tooling | https://tooling.sit.test.io|
| Development tooling | https://tooling.dev.test.io|
| Production TODO-webapp | https://todo.test.io|
| Pre-production TODO-webapp | https://todo.preprod.test.io|
| Pentest TODO-webapp | https://todo.pentest.test.io|
| UAT TODO-webapp | https://todo.uat.test.io|
| SIT TODO-webapp | https://todo.sit.test.io|
| Development TODO-webapp | https://todo.dev.test.io|


### Ansible Inventory
Our Ansible inventory folder should look like this:
```
├── ci
├── dev
├── pentest
├── pre-prod
├── prod
├── sit
└── uat
```

Results:
![Ansible Inventory](img/ansible-inventory-structure.png)

#### Inventory files
Our ansible inventory files for each environment should look like this:

#### CI Environment Inventory File
Create an inventory file for the CI environment. This will be used to access Jenkins, Nginx, Artifact Repository and Sonarqube. The inventory file should look like this:
```yaml
[jenkins]
<Jenkins-Private-IP-Address>

[nginx]
<Nginx-Private-IP-Address>

[sonarqube]
<SonarQube-Private-IP-Address>

[artifact_repository]
<Artifact_repository-Private-IP-Address>
```

Results:
![CI Inventory](img/ci-inventory.png)

##### Development Environment Inventory File
Create an inventory file for the Development environment. This will be used to access Nginx, TODO-webapp and Tooling. The inventory file should look like this:
```yaml
[tooling]
<Tooling-Web-Server-Private-IP-Address>

[todo]
<Todo-Web-Server-Private-IP-Address>

[nginx]
<Nginx-Private-IP-Address>

[db:vars]
ansible_user=ec2-user
ansible_python_interpreter=/usr/bin/python

[db]
<DB-Server-Private-IP-Address>
```

Results:
![Dev Inventory](img/dev-inventory.png)

##### Pentest Environment Inventory File
Create an inventory file for the Pentest environment. This will be used to access Nginx, TODO-webapp and Tooling. The inventory file should look like this:
```yaml
[pentest:children]
pentest-todo
pentest-tooling

[pentest-todo]
<Pentest-for-Todo-Private-IP-Address>

[pentest-tooling]
<Pentest-for-Tooling-Private-IP-Address>
```

Results:
![Pentest Inventory](img/pentest-inventory.png)


<b><h4>Note</h4></b>
You will notice that in the pentest inventory file, we have introduced a new concept pentest:children This is because, we want to have a group called pentest which covers Ansible execution against both pentest-todo and pentest-tooling simultaneously. But at the same time, we want the flexibility to run specific Ansible tasks against an individual group.
The db group has a slightly different configuration. It uses a RedHat/Centos Linux distro. Others are based on Ubuntu (in this case user is ubuntu). Therefore, the user required for connectivity and path to python interpreter are different. If all your environment is based on Ubuntu, you may not need this kind of set up. Totally up to you how you want to do this. Whatever works for you is absolutely fine in this scenario.
This makes us to introduce another Ansible concept called group_vars. With group vars, we can declare and set variables for each group of servers created in the inventory file.

For example, If there are variables we need to be common between both pentest-todo and pentest-tooling, rather than setting these variables in many places, we can simply use the group_vars for pentest. Since in the inventory file it has been created as pentest:children Ansible recognizes this and simply applies that variable to both children.


### Ansible Roles for CI Environment
Now go ahead and Add two more roles to ansible:

1. SonarQube 
2. Artifactory

<b>Why do we need SonarQube?</b>

SonarQube is an open-source platform developed by SonarSource for continuous inspection of code quality, it is used to perform automatic reviews with static analysis of code to detect bugs, code smells, and security vulnerabilities. Watch a short description here. There is a lot more hands on work ahead with SonarQube and Jenkins. So, the purpose of SonarQube will be clearer to you very soon.

<b>Why do we need Artifactory?</b>

Artifactory is a product by JFrog that serves as a binary repository manager. The binary repository is a natural extension to the source code repository, in that the outcome of your build process is stored. It can be used for certain other automation, but we will it strictly to manage our build artifacts.

#### Configuring Ansible for Jenkins Deployment
Prior to this all the previous projects have involved launching ansible commands manually from a CLI. Now with jenkins, we will start running Ansible from Jenkins UI.

To get started on this:

- Go to our Jenkins URL
    ```
    https://<public-ip-address>:8080
    ```

    - Login with the credentials we created earlier

    Results:
    ![Jenkins Login](img/jenkins-login.png)

- Install & Open Blue Ocean Jenkins Plugin
    - Click on Manage Jenkins
    - Click on Manage Plugins
    - Search for Blue Ocean
    - Click on Install without restart
    
    Results:
    ![Jenkins Blue Ocean](img/jenkins-blue-ocean.png)

- Create a new pipeline

    - Click on create a new pipeline

    - select Github 

    Results:
    ![Jenkins Create Pipeline](img/jenkins-create-pipeline.png)

- Login to Github and Generate an Access token and copy it

    Results:
    ![Github Access Token](img/github-access-token.png)

- Paste the token and connect in the jenkins connect to github input box

    Results:
    ![Jenkins Connect to Github](img/jenkins-connect-to-github.png)

- select the organization and repository

    Results:
    ![Jenkins Select Repo](img/jenkins-select-repo.png)

<b>Note:</b> At this point we do not have a  Jenkinsfile in the Ansible repository, so Blue Ocean will attempt to give us some guidance to create one. But we do not need that. We will rather create one ourselves. So, click on Administration to exit the Blue Ocean console.

- Here is our newly created pipeline. It takes the name of your GitHub repository.

    Results:
    ![Jenkins Pipeline](img/jenkins-pipeline.png)


#### Creating our Jenkinsfile

- Inside our Ansible project, create a new directory called deplit and start a new file Jenkinsfile inside the directory.

    ```bash
    mkdir deploy
    cd deploy
    touch Jenkinsfile
    ```

    Results:
    ![Jenkinsfile](img/create-jenkinsfile.png)

- Add the code snippet below to start building our Jenkinsfile gradually. This pipeline currently has just one stage called 'Build' and the only thing we are doing is using the shell script module to echo 'Building stage'

    ```groovy
    pipeline {
        agent any
        stages {
            stage('Build') {
                steps {
                    sh 'echo "Building stage"'
                }
            }
        }
    }
    ```
    - commit and push the changes to Github

    Results:
    ![Jenkinsfile Build Stage](img/jenkinsfile-build-stage.png)

- Now go back into the ansible pipeline in jenkins, and select configure
    - Scroll down to 'Build Configuration' section and specify the location of the Jenkinsfile at 'deploy/Jenkinsfile'

    - Save the changes
        Results:
        ![Jenkins Configure Pipeline](img/jenkins-configure-pipeline.png)

- Back to the pipeline again, and click "Build now"

    Results:
    ![Jenkins Build Now](img/jenkins-build-now.png)

<b>Note:</b> This will trigger a build and you will be able to see the effect of our basic Jenkinsfile configuration by going through the console output of the build.

To really appreciate and feel the difference of Cloud Blue UI, it is recommended to try triggering the build again from Blue Ocean interface.

- Open blue ocean
    - select the project
    - click on the play button against the branch

    Results:
    ![Jenkins Blue Ocean Build](img/jenkins-blue-ocean-build.png)

<b>Note:</b> that this pipeline is a multibranch one. This means, if there were more than one branch in GitHub, Jenkins would have scanned the repository to discover them all and we would have been able to trigger a build for each branch.


- Now lets create a new git branch and name it 'feature/jenkinspipelins-stages'
    ```
    git checkout -b feature/jenkinspipelins-stages
    ```

    - Add the code snippet below to our Jenkinsfile to create a new stage called 'Test' and echo 'Testing stage'

    ```groovy
    pipeline {
        agent any

        stages {
                stage('Build') {
                steps {
                    script {
                    sh 'echo "Building Stage"'
                    }
                }
                }

                stage('Test') {
                steps {
                    script {
                    sh 'echo "Testing Stage"'
                    }
                }
                }
            }
    }

    ```

    Results:
    ![Jenkinsfile Test Stage](img/jenkinsfile-test-stage.png)

- To make your new branch show up in Jenkins, we need to tell Jenkins to scan the repository
    - Click on the administrator button in blue ocean

    - Navigate to the Ansible project and click on "Scan repository now"

    - Refresh the page and both branches should start building automatically. You can go into Blue ocean and see both branches there too.

    Results:
    ![Jenkins Scan Repo](img/jenkins-scan-repo.png)
    ![Jenkins Scan Repo blue ocean](img/jenkins-scan-repo-blue-ocean.png)

- In Blue Ocean, you can now see how the Jenkinsfile has caused a new step in the pipeline launch build for the new branch.

    Results:
    ![Jenkins Blue Ocean Build](img/jenkins-blue-ocean-build-info.png)

- Let's create a pull request to merge the latest code into the main branch

- After the pull request is merged, go back into your terminal and switch to the main branch

    ```
    git checkout main
    ```
    and pull the latest changes

    ```
    git pull
    ```

    Results:
    ![Jenkins Blue Ocean Build](img/git-checkout-main.png)

- Let's head back to jenkins and scan for changes and head to Blue ocean to see the build on the main branch

    Results:
    ![Jenkins Blue Ocean Build](img/jenkins-blue-ocean-build-main.png)

- Now let's Create a new branch, add more stages into the Jenkins file to simulate below phases. (We just need to add an echo command like we have in build and test stages)
    
    - Package
    - Deploy
    - Clean up

    Results:
    ![Jenkinsfile Stages](img/jenkinsfile-stages.png)

- Verify in Blue Ocean that all the stages are working, then merge your feature branch to the main branch

    Results:
    ![Jenkins Blue Ocean Build](img/jenkins-blue-ocean-build-stages.png)

- Eventually, your main branch should have a successful pipeline like this in blue ocean

    Results:
    ![Jenkins Blue Ocean Build](img/jenkins-blue-ocean-build-success.png)