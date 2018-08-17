# 基于 Jenkins 的持续交付 {#concept_m15_5m5_xdb .concept}

持续集成作为敏捷开发重要的一步，其目的在于让产品快速迭代的同时，尽可能保持高质量。每一次代码更新，都要通过自动化测试来检测代码和功能的正确性，只有通过自动测试的代码才能进行后续的交付和部署。本文主要介绍如何将时下最流行的持续集成工具之一的 Jenkins 结合阿里云容器服务，实现自动测试和镜像构建推送。

以下内容演示如何通过阿里云容器服务 Jenkins 实现自动测试和 Docker 镜像构建，实现高质量的持续集成。

## 背景信息 {#section_z3s_vm5_xdb .section}

每次代码提交到 GitHub 上的 nodejs 的项目后，阿里云容器服务 Jenkins 都会自动触发单元测试，测试通过则继续镜像构建及推送到目标镜像仓库中，最后邮件通知结果。

大致流程如下图所示：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951055455_zh-CN.png)

slave-nodejs 用于进行单元测试，构建镜像和推送镜像的 slave 节点。

## Jenkins 相关介绍 {#section_bjs_vm5_xdb .section}

Jenkins 是基于 Java 开发的一种开源持续集成工具，监控并触发持续重复的工作，具有开源，支持多平台和插件扩展，安装简单，界面化管理等特点。Jenkins 使用 job 来描述每一步工作。节点是用来执行项目的环境。Master 节点是 Jenkins job 的默认执行环境，也是 Jenkins 应用本身的安装环境。

## master/slave {#section_cjs_vm5_xdb .section}

master/slave 相当于 server 和 agent 的概念。master 提供 web 接口让用户来管理 job 和 slave，job 可以运行在 master 本机或者被分配到 slave 上运行。一个 master 可以关联多个 slave 用来为不同的 job 或相同的 job 的不同配置来服务。

可以通过配置多个 slave 为不同项目准备单独的测试和构建环境。

**Note:** 本文中提到的 Jenkins job 和项目均指的是 Jenkins 一个构建单元，执行单元。

## 步骤 1 使用容器服务部署 Jenkins 应用和 slave 节点 {#section_djs_vm5_xdb .section}

不同应用构建、测试所需要的依赖不同，最佳实践是使用包含相应的运行时依赖和工具的不同 Slave 容器执行测试和构建。通过阿里云容器服务提供的针对 Python、Node.js、Go 等不同环境的 Slave 镜像以及示例模板，用户可以简单快速地生成 Jenkins 应用以及各种 slave 节点，在 Jenkins 应用中配置节点信息，然后在构建项目中指定执行节点，从而实现整个持续集成流程。

**Note:** 有关阿里云容器服务提供的开发 slave 节点镜像，参见 [https://github.com/AliyunContainerService/jenkins-slaves](https://github.com/AliyunContainerService/jenkins-slaves)。

**1.1 创建 Jenkins 编排模版**

新建模版，以如下内容为基础，创建编排。

阿里云容器服务 Jenkins master 支持标签 1.651.3、2.19.2、2.32.2。

**Note:** 有关如何创建编排模板，参见[创建编排模板](intl.zh-CN/用户指南/Swarm 集群/镜像与模板管理/创建编排模板.md#)。

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

**1.2 使用模板创建 Jenkins 应用和 slave 节点**

使用上边新建的编排模板或者直接使用阿里云容器服务提供的 Jenkins 示例模版创建 Jenkins 应用和 slave 节点。

**Note:** 有关如何使用编排模板创建应用，参见[创建应用](intl.zh-CN/用户指南/Swarm 集群/应用管理/创建应用.md#)。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951065456_zh-CN.jpg)

创建成功后，Jenkins 应用和 slave 节点显示在服务列表中。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951065457_zh-CN.jpg)

打开容器服务提供的访问端点，就可以使用刚刚部署的 Jenkins 应用。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951065459_zh-CN.jpg)

## 步骤 2 实现自动测试及自动构建推送镜像 {#section_jnt_kn5_xdb .section}

**2.1 将 slave 容器配置成 Jenkins 应用的 slave 节点。**

打开 Jenkins 应用，进入系统设置界面，选择管理节点，新建节点，配置相应参数。如下图所示。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951065461_zh-CN.jpg)

**Note:** 

-   标签是 slave 的唯一标识。
-   slave 容器和 Jenkins 容器同时运行在阿里云平台上，因此需要填写外网访问不到的容器节点 IP，隔离测试环境。
-   添加 Credential 的时候，使用创建 slave-nodejs 镜像的 Dockerfile 里的 jenkins 用户（初始密码为 jenkins）。镜像 Dockerfile 的地址为[jenkins-slave-dind-nodejs](jenkins-slave-dind-nodejs?spm=a2c4g.11186623.2.6.zKuGQc)。

**2.2 创建项目实现自动化测试**

1.  新建 Item，选择构建一个自由风格的软件项目。
2.  填写项目名称，并选择项目运行节点。此示例中填写上述准备的 slave-nodejs-ut 节点。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951065462_zh-CN.png)

3.  配置源码管理和代码分支。此示例中源代码使用 GitHub 管理。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951065463_zh-CN.png)

4.  配置构建触发器，此示例采用结合 GitHub Webhooks & services 实现自动触发项目执行。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951065464_zh-CN.png)

5.  在 GitHub 中添加 Jenkins 的 service hook，完成自动触发实现。

    在 GitHub 项目主页单击**Settings**，单击左侧菜单栏中的**Webhooks & services**，单击**Add Service**，在下拉框中选择**Jenkins\(Git plugin\)**。在Jenkins hook url 对话框中填写`${Jenkins IP}/github-webhook/`，例如：

    ```
    http://jenkins.cd****************.cn-beijing.alicontainer.com/github-webhook/
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951065465_zh-CN.png)

6.  增加 Execute shell 类型的构建步骤，编写 shell 脚本执行测试。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951065466_zh-CN.png)

    本示例中的命令如下所示：

    ```
    pwd
     ls
     cd chapter2
     npm test
    ```


**SVN 代码源示例:**

在源码管理模块选择**Subversion**。Repository URL中填入 SVN repository 的地址。（如果 Jenkins master 时区和 SVN 服务器时区不一致，请在 repository 地址末尾添加 `@HEAD`）Credentials中添加 SVN 服务器的用户名和密码。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951065467_zh-CN.png)

配置构建触发器，此示例采用 Post-commit hook 实现自动触发项目执行。在身份验证令牌中填写您设置的 token。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951065468_zh-CN.png)

登录 SVN 服务器，在代码 repository（svn-java-demo）的hooks 文件目录下创建 post-commit文件。

```
cd /home/svn/svn-java-demo/hooks
cp post-commit.tmpl post-commit
chmod 755 post-commit
```

在 post-commit文件中添加

```
curl -u ${Jenkins_account}:${password}
        ${Jenkins_url}/job/svn/build?token=${token}
```

命令。例如：

```
curl -u test:test
      http://127.0.0.1:8080/jenkins/job/svn/build?token=qinyujia
```

。

**2.3 创建项目实现自动构建，推送镜像。**

1.  新建 Item，选择构建一个自由风格的软件项目。
2.  填写项目名称，并选择项目运行节点。此示例中填写上述准备的 slave-nodejs-ut 节点。
3.  配置源码管理和代码分支。此示例中源代码使用 GitHub 管理。
4.  添加如下触发器，设置只有在单元测试成功之后才执行自动构建镜像。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951065469_zh-CN.png)

5.  编写构建镜像和推送镜像的 shell 脚本。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951065470_zh-CN.png)

    本示例的命令如下所示：

    ```
    cd chapter2
     sudo docker build -t registry.aliyuncs.com/qinyujia-test/nodejs-demo .
     sudo docker login -u ${yourAccount} -p ${yourPassword} registry.aliyuncs.com
     sudo docker push registry.aliyuncs.com/qinyujia-test/nodejs-demo
    ```


## 步骤 3 自动重新部署应用 {#section_syf_l45_xdb .section}

**3.1 首次部署应用**

使用编排模板，将 2.3 中创建的镜像部署到容器服务中，创建 nodejs-demo应用。

示例如下所示：

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

**3.2 自动重新部署**

1.  选择刚刚创建的应用 **nodejs-demo**，创建触发器。

    **Note:** 有关如何创建触发器，参见[触发器](../../../../intl.zh-CN/开发指南/Swarm API参考/触发器/触发器.md#)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951065471_zh-CN.png)

2.  在2.3 中的 shell 脚本中添加一行，地址即为上文创建的触发器给出的触发器链接。

    ```
    curl ‘https://cs.console.aliyun.com/hook/trigger?triggerUrl=***==&secret=***’
    ```

3.  把 2.3 示例中的命令改为：

    ```
     cd chapter2
     sudo docker build -t registry.aliyuncs.com/qinyujia-test/nodejs-demo .
     sudo docker login -u ${yourAccount} -p ${yourPassword} registry.aliyuncs.com
     sudo docker push registry.aliyuncs.com/qinyujia-test/nodejs-demo
     curl ‘https://cs.console.aliyun.com/hook/trigger?triggerUrl=***==&secret=***’
    ```


到此，镜像推送之后，Jenkins 会自动触发重新部署 nodejs-demo 应用。

## 步骤 4 配置邮件推送结果 {#section_qks_vm5_xdb .section}

如果希望单元测试或者镜像构建的结果能够通过邮件推送给相关开发人员或者项目执行发起者。可以通过如下配置来实现。

1.  在 Jenkins 主页，选择系统管理，系统设置，配置 Jenkins 系统管理员邮箱。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951066211_zh-CN.png)

2.  安装 Extended Email Notification plugin，配置 SMTP server 等相关信息，并设置默认邮件接收人列表。如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951075472_zh-CN.png)

    以上是 Jenkins 应用系统参数设置，下面是为需要用邮件推送结果的 Jenkins 项目进行相关配置。

3.  在 Jenkins 项目中添加构建后操作步骤。选择 Editable Email Notification 类型，填写邮件接收人列表。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951075474_zh-CN.png)

4.  添加邮件发送触发器。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7101/15344951075475_zh-CN.png)


