# Serverless Kubernetes 集群 {#concept_f3m_dgc_5db .concept}

## 产品简介 {#section_r2q_2gc_5db .section}

阿里云 Serverless Kubernetes 让您无需管理和维护集群与服务器，即可快速创建 Kubernetes 容器应用，并且根据应用实际使用的 CPU 和内存资源量进行按需付费。使用Serverless Kubernetes，您可以专注于设计和构建应用程序，而不是管理运行应用程序的基础设施。它基于阿里云弹性计算基础架构，并且完全兼容 Kubernetes API 的解决方案，充分结合了虚拟化资源带来的安全性、弹性和 Kubernetes 生态。

阿里云 Kubernetes 服务具有两种模式，一种是经典的集群方式，一种是 Serverless 模式。

-   在经典的 Kubernetes 集群模式中：您可以对集群基础设施和容器应用进行更细粒度的控制，比如选择宿主机实例规格（ECS/EGS/EBM）和操作系统，指定 Kubernetes 版本、自定义 Kubernetes 特性开关设置，使用不同的网络模型等。阿里云 Kubernetes 服务负责为集群创建底层云资源，升级等自动化运维操作。监控集群中所有 CPU、内存及其他资源，并根据您指定的资源要求查找最适合运行容器的服务器。而您需要规划、维护、升级服务器集群，也可以通过监控应用程序容器的资源利用率，手动或自动在集群中添加或删除服务器。
-   在 Serverless 模式下：您无需创建底层虚拟化资源，可以利用 Kubernetes 命令指明应用容器镜像、CPU 和内存要求以及对外服务方式，直接启动应用程序。

## 产品优势 {#section_py2_ggc_5db .section}

-   **简单易用**：秒级部署应用。无需管理 Kubernetes 集群基础设施，内建高可用和安全性。
-   **兼容并包**：您可使用熟悉的 Kubernetes 命令行和 API 来部署容器应用，无需修改就支持 Kubernetes 典型应用场景。也可轻松的迁移您的应用到支持 Kubernetes 的运行平台，如阿里云 Kubernetes 集群。
-   **安全隔离**：基于阿里云弹性计算基础架构，应用容器之间相互隔离防止互相干扰。
-   **按需扩容**：无需再担心集群节点的的水平扩展，根据应用负载，轻松灵活扩容应用所需资源。
-   **互联互通**：支持容器应用与阿里云基础服务无缝整合。支持与用户 VPC 中现有应用、数据库直接交互；支持容器与虚拟机应用的互联互通。

## 产品架构 {#section_u2q_2gc_5db .section}

Serverless Kubernetes 与 kubernetes 容器服务的对比架构图如下所示。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6864/15341354971393_zh-CN.png)

## 应用场景 {#section_w2q_2gc_5db .section}

serverless kubernetes 应用场景非常广泛，基于容器的高移植性高灵活性，和 IaaS 的高弹性高隔离性，为 serverless 类应用提供强大的基础运行设施。适合的 serverless 应用包括多媒体处理、捕获数据修改、物联网传感器数据处理、流式计算、聊天机器人、批量计算、web 应用、移动应用后端服务、商业逻辑处理、持续集成。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6864/15341354971394_zh-CN.png)

