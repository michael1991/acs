# 一键部署 Docker Datacenter {#concept_ljs_hv1_ydb .concept}

## DDC 简介 {#section_rkr_vv1_ydb .section}

Docker Datacenter（DDC）是 Docker 发布的企业级容器管理和服务部署的整体解决方案平台。DDC 由以下三个组件构成：

-   Docker Universal Control Plane（Docker UCP）：一套图形化管理界面。
-   Docker Trusted Registry（DTR）：授信的 Docker 镜像仓库。
-   Docker Engine 商业版：提供技术支持的 Docker 引擎。

DDC 在 Docker 官网的地址为 [https://www.docker.com/products/docker-datacenter](https://www.docker.com/products/docker-datacenter)。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7266/15344062396145_zh-CN.png)

DDC 与 Docker 公司的另外一个在线产品 Docker Cloud 对应。不过 DDC 主要针对企业用户在内部部署。用户注册自己的 Dokcer 镜像到 DTR，UCP 管理整个 Docker 集群。并且这两个组件都提供了 Web 界面。

使用 DDC 需要购买 Licence， 但是 Docker 公司提供了一个月的试用 Licence，可以在 Docker 官网注册后直接下载。

## DDC 部署架构 {#section_ukr_vv1_ydb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7266/15344062396146_zh-CN.png)

在上面的基础架构图里，Controller 主要运行 UCP 组件，DTR 运行 DTR 组件， Worker 主要运行客户自己的 Docker 服务。整个 DDC 环境都部署在 VPC 网络之下，所有的 ECS 加入同一个安全组。每个组件都提供了一个负载均衡，供外网访问。而运维操作则是通过跳板机实现。另一方面为了提升可用性，整个 DDC 环境都是高可用部署，也就是说 Controller 至少有两台，同理 DTR 也至少有两台。

## DDC 一键部署 {#section_wkr_vv1_ydb .section}

您可以使用阿里云资源编排 ROS 通过下面的链接一键部署 DDC。

[一键部署 DDC](https://ros.console.aliyun.com/?hideTopbar=false&skipProtocal=true&/#/stack/cn-beijing/create?url=http:%2F%2Fros-template.cn-hangzhou.oss.aliyun-inc.com%2Fdocker_ddc.json&regionEnable=true&step=2)

上边的编排模板默认会在华北 2 地域部署 DDC。如果您需要调整地域，请单击页面右下角的**上一步**，然后重新选择地域，然后单击**下一步**。

填写如下图中必填的信息或者根据您的需求调整信息后，单击**创建**就可以部署一套 DDC。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7266/15344062396147_zh-CN.PNG)

## DDC 访问 {#section_ykr_vv1_ydb .section}

使用 ROS 创建 DDC 成功后，您可以进入 ROS 的资源栈管理页面，找到所创建的资源栈，并单击资源栈名称或单击右侧的**管理**进入资源栈的概要信息页面。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7266/15344062396148_zh-CN.png)

您可以输出查看登录 UCP 和 DTR 的地址。

在浏览器中输入 UCP 的地址就会显示 UCP 的访问页面，输入在安装 UCP 时创建的管理账号和密码，系统会提示导入 Licence 文件，把准备好的 Licence 导入，即可进入 UCP 的控制界面。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7266/15344062396149_zh-CN.png)

