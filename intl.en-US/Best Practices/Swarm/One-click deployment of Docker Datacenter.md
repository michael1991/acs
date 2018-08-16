# One-click deployment of Docker Datacenter {#concept_ljs_hv1_ydb .concept}

## About DDC {#section_rkr_vv1_ydb .section}

Docker Datacenter \(DDC\) is an enterprise-level container management and service deployment package solution platform released by Docker. DDC is composed of the following three components:

-   Docker Universal Control Plane \(Docker UCP\): A set of graphical management interfaces.
-   Docker Trusted Registry \(DTR\): A trusted Docker image repository.
-   Docker Engine Enterprise Edition: The Docker Engine providing technical support.

DDC is available on the Docker official website [https://www.docker.com/products/docker-datacenter](https://www.docker.com/products/docker-datacenter).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7266/15344062416145_en-US.png)

DDC is a counterpart of Docker Cloud, another online product of the Docker company. However, DDC primarily targets enterprise users for internal deployment. You can register your own Docker image to DTR and use UCP to manage the entire Docker cluster. Both components provide web interfaces.

You must purchase a license to use DDC, but the Docker company provides a free license for a one-month trial. You can download the trial license from the Docker official website after signing up.

## DDC deployment architecture {#section_ukr_vv1_ydb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7266/15344062416146_en-US.png)

In the preceding basic architecture figure, Controller primarily runs the UCP component, DTR runs the DTR component, and Worker primarily runs your own Docker service. The entire DDC environment is deployed on the Virtual Private Cloud \(VPC\) and all Elastic Compute Service \(ECS\) instances are in the same security group. Every component provides a Server Load Balancer instance for extranet access. Operations and maintenance are implemented by using the jump server. To enhance the availability, the entire DDC environment is deployed for high availability, meaning at least two Controllers and two DTRs exist.

## One-click deployment of DDC {#section_wkr_vv1_ydb .section}

You can use Alibaba Cloud Resource Orchestration Service \(ROS\) to deploy DDC in one click at the following link.

[One-click deployment of DDC](https://ros.console.aliyun.com/?hideTopbar=false&skipProtocal=true&/#/stack/cn-beijing/create?url=http:%2F%2Fros-template.cn-hangzhou.oss.aliyun-inc.com%2Fdocker_ddc.json&regionEnable=true&step=2)

In the preceding orchestration template, DDC is deployed in the region China North 2 \(Beijing\)  by default. To change the region for deployment, click **Back** in the lower-right corner of the page. Select your region and then click **Next**.

Complete the configurations. Click **Create** to deploy a set of DDC.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7266/15344062416147_en-US.PNG)

## DDC access {#section_ykr_vv1_ydb .section}

After creating DDC successfully by using ROS, you can enter the ROS stack management page by clicking Stack Management in the left-side navigation pane. Find the created stack, and then click the stack name or **Manage** at the right of the stack. The Stack Overview page appears.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7266/15344062416148_en-US.png)

You can view the addresses used to log on to UCP and DTR in the Output section.

Enter the UCP address in the browser and the UCP access page appears. Enter the administrator account and password created when installing UCP and the system prompts you to import the license file. Import the license file and then enter the UCP control interface.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7266/15344062416149_en-US.png)

