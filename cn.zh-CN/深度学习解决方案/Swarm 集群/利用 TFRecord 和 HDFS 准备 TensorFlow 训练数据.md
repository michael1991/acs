# 利用 TFRecord 和 HDFS 准备 TensorFlow 训练数据 {#concept_nmg_rll_vdb .concept}

数据准备和预处理在一个深度学习训练过程中扮演着非常重要的角色，它影响着模型训练的速度和质量。

而 TensorFlow 对于 HDFS 的支持，将大数据与深度学习相集成，完善了从数据准备到模型训练的完整链条。在阿里云容器服务深度学习解决方案中，为 TensoFlow 提供了 OSS、NAS 和 HDFS 三种分布式存储后端的支持。

本文介绍如何将数据转化为 TFRecord 格式，并且将生成的 TFRecord 文件保存到 HDFS 中，本示例使用阿里云 Elastic MapReduce（E-MapReduce）的 HDFS 服务。

## 为什么要使用 TFRecord {#section_c4q_5ll_vdb .section}

TFRecord 是 TensorFlow 内定的统一标准数据格式，可以支持多线程数据读取，并且可以通过 batch size 和 epoch 参数来控制训练时单次 batch 的大小和样本文件迭次数，同时能更好的利用内存和方便数据的复制和移动，所以是利用 TensorFlow 进行大规模深度学习训练的首选。

## 步骤 1 创建 E-MapReduce 集群 {#section_d4q_5ll_vdb .section}

E-MapReduce 是运行在阿里云平台上的一种大数据处理的系统解决方案。更多详细信息参见 [E-MapReduce 概述](https://www.alibabacloud.com/help/zh/doc-detail/28068.htm?spm)。

登录 [E-MapReduce 管理控制台](https://emr.console.aliyun.com/?spm=5176.2020520101.1001.134.pBUl5n#/cluster/overview/cn-shenzhen) 创建一个 E-MapReduce 集群。有关创建过程，参见 [创建 E-MapReduce 集群](https://www.alibabacloud.com/help/zh/doc-detail/28088.htm)。

本示例创建了一个位于华南 1 地域的集群，且 **网络类型** 为 **VPC**。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7441/15336969992172_zh-CN.png)

## 步骤 2 创建容器集群，并且打通两个集群间的网络 {#section_vmt_1ml_vdb .section}

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/?spm=5176.2020520111.1001.77.rQ4gDS#/overview/all)，在同一个 VPC 下创建 GPU 容器集群。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7441/15336969992175_zh-CN.png)

2.  登录 [ECS 管理控制台](https://ecs.console.aliyun.com/?spm=5176.2020520143.1001.102.fiFGza#/home) 将容器服务集群的节点加入到 E-MapReduce 集群对应的安全组。
    1.  选择安全组所在的地域（本示例为华南 1），选择安全组并单击右侧的 **管理实例**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7441/15336969992176_zh-CN.png)

    2.  单击 **添加实例**，选择容器集群中的节点并单击 **确定**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7441/15336969992177_zh-CN.png)


## 步骤 3 生成 TFRecord 数据 {#section_k4q_5ll_vdb .section}

本示例利用模型训练服务提供运行环境执行 convert\_to\_records.py，生成 TFRecord 数据，保存到 HDFS 中。

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/?spm=5176.2020520143.1001.77.wB6AMG#/overview/all)。
2.  单击左侧导航栏中的 **镜像与方案** \> **解决方案**。
3.  在 **模型训练** 中单击 **创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7441/15336969992178_zh-CN.png)

4.  填写模型训练的配置信息并单击 **确定**。

    本示例的具体配置如下所示：

    -   **训练框架：****tensorflow:1.0.0**。
    -   **单Worker使用GPU数量：** 0。
    -   **数据卷名：**不使用数据卷。
    -   **Git地址：**https://code.aliyun.com/deeplearning/mnist-examples.git。
    -   **执行命令：** 

        ```
        python neural_style.py --iterations 50000 --content
                      /neural-style/examples/1-content.jpg --styles /neural-style/examples/1-style.jpg
                      --output /neural-style/output.jpg
        ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7441/15336969992179_zh-CN.png)

    运行成功后，可以查看执行的日志，显示 TFRecord 文件已经保存到了 HDFS。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7441/15336969992180_zh-CN.png)

    登录到 E-MapReduce 机器上查看产生的 TFRecord 文件。

    ```
    # hdfs dfs -ls /mnist-tfrecord
    SLF4J: Class path contains multiple SLF4J bindings.
    SLF4J: Found binding in [jar:file:/opt/apps/hadoop-2.7.2/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
    SLF4J: Found binding in [jar:file:/opt/apps/tez-0.8.4/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
    SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
    SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
    Found 3 items
    -rw-r--r--   3 root hadoop    8910000 2017-05-23 19:34 /mnist-tfrecord/test.tfrecords
    -rw-r--r--   3 root hadoop   49005000 2017-05-23 19:33 /mnist-tfrecord/train.tfrecords
    -rw-r--r--   3 root hadoop    4455000 2017-05-23 19:33 /mnist-tfrecord/validation.tfrecords
    ```


