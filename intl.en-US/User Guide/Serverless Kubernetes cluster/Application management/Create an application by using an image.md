# Create an application by using an image {#task_fnw_vd1_ydb .task}

Only one Serverless Kubernetes cluster can be created. For more information, see [Create a Serverless Kubernetes cluster](intl.en-US/User Guide/Serverless Kubernetes cluster/Cluster management/Create a Serverless Kubernetes cluster.md#).

1.  Log on to the [Container Service console](https://cs.console.aliyun.com). 
2.  Under Kubernetes, click **Application** \> **Deployment** in the left-side navigation pane to enter the Deployment List page. 
3.  Click **Create by image** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6965/15341327955653_en-US.png)

4.  Set **Application name**, **Cluster deployment**, and **Namespace**. Then click **Next** to enter the application configuration page. 

    If the **namespace** is not set, the default namespace is used.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6965/15341327955654_en-US.png)

5.  Select the image you want to use and the version of the image. 
    -   **Image name**: In the displayed dialog box click **Select Image**, and then click **OK**. You can also enter the private registry. In the format of `domainname/namespace/imagename:tag`. In this example, the image name is nginx. 
    -   **Image version**: Click **Select image version** to select the version. If not specified, the latest version is used by default.
6.  Configure the number of containers \(**Scale**\). 

    This example is a single container pod, and if multiple containers are specified, the same number of pods is started. 

7.  Configure resource limits and required resources for the container. 

    Serverless Kubernetes is currently in beta stage and only supports the 2C4G specification.

    -   Resource limits: You can specify the maximum amount of resources that the application can use, including CPU and memory to prevent excessive use of resources. 
    -   Required resources: The amount of resources reserved for the application, including CPU and memory. That is, container monopolizes the resource, so as to prevent other services or processes from competing for the resource due to insufficiency, resulting the application to be unavailable. 
    CPU is measured in millicores \(one thousandth of one core\). Memory is measured in bytes, which can be GB, MB, or KB.

8.  Configure the environment variable. 

    You can configure the environment variable for the pod in the format of key-value pairs. For adding environment label or passing configuration to pod, see [Pod variable](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=a2c4g.11186623.2.6.p4Ezey).

9.  Configure the container. 

    You can configure the Command and Arguments for the container running in the pod.

    Command and Args: If not configured, the default settings for the image is used. If configured, the default settings are overwritten. If only arguments are configured, when the container starts, the default command executes the new arguments.

    Command and arguments cannot be modified after pod is created.

10. After the application configuration is complete, click **Next** to enter the Access Settings page to set up a service that binds the backend pod. 

    You can select not to create a service, or select a service type. Currently, only load balancing type is supported.

    -   **Load Balancing**: Load Balancer is a load balancing service provided by Alibaba Cloud, public network access or intranet access can be used.
    -   **Name**: By default, a service name with the application name suffix `svc` is generated, in this example serverless-app-svc. Name of the service can be modified.
    -   **Port mapping**: Specify the port mapping between service and container, and select TCP or UDP as the protocol. 
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6965/15341327955655_en-US.png)

11. After the access configuration is complete, click **Create**. 

After the creation is successful, go to the creation completion page. The objects contained in the application are displayed. You can click View to view the deployment list. 

You can view the new serverless-app-svc under the deployment list.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6965/15341327955656_en-US.png)

Click **Application** \> **Service** in the left-side navigation pane to see the new service serverless-app-svc under the list of services.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6965/15341327955657_en-US.png)

Access the external endpoint in the browser to access the Nginx welcome page.

