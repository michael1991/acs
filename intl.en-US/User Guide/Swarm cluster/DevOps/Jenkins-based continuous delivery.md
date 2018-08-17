# Jenkins-based continuous delivery {#concept_m15_5m5_xdb .concept}

As an important step in agile development, continuous integration aims to maintain high quality while accelerating product iteration.  Every time codes are updated, an automated test is performed to test the codes and function validity. The codes can only be delivered and deployed after they pass the automated test.  This document mainly introduces how to integrate Jenkins,  one of the most popular continuous integration tools, with Alibaba Cloud Container Service to realize automated test and image building push.

The following example demonstrates how to perform automated test and build a Docker image by using Alibaba Cloud Container Service Jenkins, which realizes high-quality continuous integration.

## Background information {#section_z3s_vm5_xdb .section}

Every time codes are submitted to nodejs project in GitHub, Alibaba Cloud Container Service Jenkins  will automatically trigger a unit test. If the test is successful, Jenkins continues to build images and then pushes them to a target image repository. Finally, Jenkins notifies you of the results by email.

A general process is as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125455_en-US.png)

Slave-nodejs is a slave node used for unit test and building and pushing the image.

## Jenkins introduction {#section_bjs_vm5_xdb .section}

Jenkins is an open-sourced continuous integration tool developed on Java. It monitors and triggers continuously repeated work and supports expansion of multiple platforms and plug-ins. Jenkins is an open-sourced tool featuring easy installation and interface-based management. It uses  job to describe every work step,  and node is a project execution environment.  The master node is a default execution environment of a Jenkins job and also the installation environment for Jenkins applications.

## Master/slave {#section_cjs_vm5_xdb .section}

Master/slave is equivalent to the server/agent concept.  A master provides Web interface with which you manage the job and slave.  The job can run on the master or be assigned to the slave. One master can be associated with several slaves to serve different jobs or different configurations of the same job.

Several slaves can be configured to prepare a separate test and building environment for different projects.

**Note:** The Jenkins job and project mentioned in this document all refer to a build unit of Jenkins, namely, an execution unit.

## Step 1 Deploy Jenkins applications and slave nodes {#section_djs_vm5_xdb .section}

The building and testing of different applications need different dependencies. The best practice is to use different slave containers with corresponding runtime dependencies and tools to perform the test and building.  By using the slave images and sample templates provided by Alibaba Cloud Container Service for different environments  such as Python, Node.js, and Go, you can quickly and easily generate Jenkins applications and various slave nodes, configure node information  in Jenkins applications, and specify the execution nodes in the build projects so as to implement the entire continuous integration process.

**Note:** For images provided by Alibaba Cloud Container Service for developing slave nodes, see [https://github.com/AliyunContainerService/jenkins-slaves](https://github.com/AliyunContainerService/jenkins-slaves).

**1.1 Create a Jenkins orchestration template**

Create a template and create the orchestration based on the following contents.

The labels supported by Alibaba Cloud Container Service Jenkins master are: 1.651.3, 2.19.2, and 2.32.2.

**Note:** For how to create an orchestration template, see [EN-US\_TP\_7019.md\#](intl.en-US/User Guide/Swarm cluster/Images and templates/Create an orchestration template.md#).

```
jenkins:
    image: 'registry.aliyuncs.com/acs-sample/jenkins:1.651.3'
    volumes:
        - /var/lib/docker/jenkins:/var/jenkins_home
    restart: always 
    labels:
        aliyun.scale: '1'
        aliyun.probe.url: 'tcp://container:8080'
        aliyun.probe.initial_delay_seconds: '10'
        aliyun.routing.port_8080: jenkins
    links:
        - slave-nodejs
slave-nodejs:
    image: 'registry.aliyuncs.com/acs-sample/jenkins-slave-dind-nodejs'
    volumes:
        - /var/run/docker.sock:/var/run/docker.sock
    restart: always 
    labels:
        aliyun.scale: '1'
```

**1.2 Use the template to create Jenkins application and slave node**

Use the orchestration template created in the preceding section or the Jenkins sample template provided by Alibaba Cloud Container Service to create the Jenkins application and slave node.

**Note:** For how to create an application by using an orchestration template, see [EN-US\_TP\_7045.md\#](intl.en-US/User Guide/Swarm cluster/Applications/Create an application .md#).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125456_en-US.jpg)

After a successful creation, the Jenkins application and slave node are displayed in the service list.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125457_en-US.jpg)

Open the access endpoint provided by Container Service to use the deployed Jenkins application.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125459_en-US.jpg)

## Step 2 Realize automated test and automated build and push of image {#section_jnt_kn5_xdb .section}

**2.1 Configure the slave container as the slave node of the Jenkins application**

Open the Jenkins application. Click Manage Jenkins in the left-side navigation pane. Click Manage Nodes on the right pane. Click New Node in the left-side navigation pane. Enter the node name and then click OK. Then, complete the parameters as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125461_en-US.jpg)

**Note:** 

-   Label is the unique identifier of the slave.
-   The slave container and Jenkins container run on the Alibaba Cloud platform at the same time. Therefore, enter a container node IP address that is inaccessible to the Internet to isolate the test environment.
-   When adding the credentials, use the jenkins account and password \(the initial password is jenkins\)  in Dockerfile for the creation of the slave-nodejs image. The image Dockerfile address is [jenkins-slave-dind-nodejs](jenkins-slave-dind-nodejs?spm=a2c4g.11186623.2.6.zKuGQc). 

**2.2 Create a project to implement automated test**

1.  Go back to the Jenkins home page. Click New Item in the left-side navigation pane. Enter the item name, select Freestyle project, and then click OK.
2.  Enter the project name and select a node for running the project. In this example, enter the slave-nodejs-ut node prepared in the preceding section.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125462_en-US.png)

3.  Configure the source code management and code branch. In this example, use GitHub to manage source codes.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125463_en-US.png)

4.  Configure the build trigger. In this example, automatically trigger project execution by combining GitHub Webhooks & services.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125464_en-US.png)

5.  Add the Jenkins service hook to GitHub to implement automatic triggering.

    On the GitHub project home page, click the **Settings**. Click **Webhooks & services**, click **Add Service**, and then select **Jenkins\(Git plugin\)** from the drop list.  In the dialog box of Jenkins hook url, enter `${Jenkins IP}/github-webhook/`. For example:

    ```
    http://jenkins.cd****************.cn-beijing.alicontainer.com/github-webhook/
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125465_en-US.png)

6.  Add a build step of Execute shell type and write shell scripts to perform the test.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125466_en-US.png)

    The commands in this example are as follows:

    ```
    pwd
     ls
     cd chapter2
     npm test
    ```


**SVN source code example:**

Select **Subversion**  in Source Code Management  and enter the SVN repository address in the Repository URL field \(if the Jenkins master and SVN server are in different time zones, add `@HEAD` at the end of the repository address\). Add the username and password of the SVN server in Credentials.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125467_en-US.png)

Configure the build trigger. In this example, Post-commit hook is used to automatically trigger the project execution.  Enter your configured token in Token Name .

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125468_en-US.png)

Log on to the SVN server. Create a post-commit file in the hooks  directory of the code repository \(svn-java-demo\).

```
cd /home/svn/svn-java-demo/hooks
cp post-commit.tmpl post-commit
chmod 755 post-commit
```

Add the curl -u $\{Jenkins\_account\}:$\{password\} 

```
${Jenkins_url}/job/svn/build?
        token=${token}  command 
```

in the <g id="1"\>post-commit</g\> file.  For example:

```
curl -u test:test
      http://127.0.0.1:8080/jenkins/job/svn/build?token=qinyujia
```

.

**2.3 Create a project to automatically build and push images**

1.  Go back to the Jenkins home page. Click New Item in the left-side navigation pane. Enter the item name, select Freestyle project, and then click OK.
2.  Enter the project name and select a node for running the project.   In this example, enter the slave-nodejs-ut node prepared in the preceding section.
3.  Configure the source code management and code branch.  In this example, use GitHub to manage source codes.
4.  Add the following trigger and set to automatically build the image only after the unit test is successful.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125469_en-US.png)

5.  Write the shell script for building and pushing images.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125470_en-US.png)

    The commands in this example are as follows:

    ```
    cd chapter2
     sudo docker build -t registry.aliyuncs.com/qinyujia-test/nodejs-demo .
     sudo docker login -u ${yourAccount} -p ${yourPassword} registry.aliyuncs.com
     sudo docker push registry.aliyuncs.com/qinyujia-test/nodejs-demo
    ```


## Step 3 Automatically redeploy the application {#section_syf_l45_xdb .section}

**3.1 Deploy the application for the first time**

Use the orchestration template to deploy the image created in step 2.3 to Container Service and create the nodejs-demo application.

Example:

```
express:
image: 'registry.aliyuncs.com/qinyujia-test/nodejs-demo'
expose:
    - '22'
    - '3000'
restart: always
labels:
    aliyun.routing.port_3000: express
```

**3.2 Automatic redeployment**

1.  Select the created application **nodejs-demo** and create the trigger.

    **Note:** For how to create a trigger, see [../../../../dita-oss-bucket/SP\_8/DNCS011835708/EN-US\_TP\_13784.md\#](../../../../intl.en-US/Developer Guide/Swarm API reference/Triggers/Triggers.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951125471_en-US.png)

2.  Add a line to the shell script in 2.3.  The address is the trigger link of the created trigger.

    ```
    curl ‘https://cs.console.aliyun.com/hook/trigger?triggerUrl=***==&secret=***’
    ```

3.  Change the command  in the example of 2.3 as follows:

    ```
     cd chapter2
     sudo docker build -t registry.aliyuncs.com/qinyujia-test/nodejs-demo .
     sudo docker login -u ${yourAccount} -p ${yourPassword} registry.aliyuncs.com
     sudo docker push registry.aliyuncs.com/qinyujia-test/nodejs-demo
     curl ‘https://cs.console.aliyun.com/hook/trigger?triggerUrl=***==&secret=***’
    ```


After pushing the image, Jenkins automatically triggers the redeployment of the nodejs-demo application.

## Step 4 Configure email notification of the results {#section_qks_vm5_xdb .section}

To send the unit test or image building results to relevant developers or project execution initiators by email,  perform the following configurations:

1.  On the Jenkins homepage, click Manage Jenkins \> Configure System, and configure the Jenkins system administrator email.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951136211_en-US.png)

2.  Install the Extended Email Notification plug-in, configure the SMTP server and other relevant information,  and then set the default email recipient list, as shown in the following figure:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951135472_en-US.png)

    The preceding example shows the parameter settings of the Jenkins application system. The following example shows the relevant configurations for Jenkins projects whose results are to be pushed by email.

3.  Add post-building steps in the Jenkins project,  select Editable Email Notification and enter the email recipient list.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951135474_en-US.png)

4.  Add a trigger to send emails.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951135475_en-US.png)


