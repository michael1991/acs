# Distributed model training {#concept_plc_yjl_vdb .concept}

You can run your model training codes by using the elastic computing resources and storage service provided by Alibaba Cloud to quickly perform distributed training. During the training, you can control how to distribute the computing resources \(CPU and GPU\), check logs and monitor the training status at any time, and back up the training results to the storage service.

By using the model training service described in this document, you can not only train a model from scratch, but also use new data to continue the training \(for example, fine tuning\) based on an existing model \(checkpoint\). You can adjust the hyper-parameters and perform iterative training by continuously updating the configurations of an existing application.

## Prerequisites {#section_grb_srm_vdb .section}

Before running a model training task, make sure you have performed the following operations:

-   Create a container cluster that contains a certain number of elastic computing resources \(Elastic Compute Service \(ECS\) or EGS\). For more information, see [Create a container cluster](intl.en-US/Deep learning/Swarm cluster/Environment preparations/Create a container cluster.md#).
-   To use Object Storage Service \(OSS\) to store data for model training, use the same account to create an OSS bucket. Then, create data volumes in the preceding container cluster to mount the OSS bucket as a local directory to the container in which you want to run the training task. For more information, see [Create a data volume](intl.en-US/Deep learning/Swarm cluster/Environment preparations/Create a data volume.md#).
-   Synchronize the model training codes to an available GitHub code repository.

## Conventions {#section_irb_srm_vdb .section}

To facilitate your training codes to read training data, output training logs, and store training iteration status data \(checkpoints\), note the following conventions:

-   The training data is automatically stored in the /input, which is consistent with the path in OSS. Your codes read the training data from this directory.
-   All the data output by codes, including logs and checkpoint files, is stored in the /output directory. All files stored in the /output directory are automatically synchronized to your OSS bucket with the same directory structure. If a special Python dependent repository is required for the training codes, write all dependencies to the requirements.txt configuration file and store the file to the code root directory in the GitHub repository.

## Procedure {#section_jp5_xrm_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/#/overview/all).
2.  Under Swarm, click **Images and Templates** \> **Solutions**in the left-side navigation pane.
3.  Click **Launch** in **Training**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7432/15344952002254_en-US.png)

4.  Configure the basic information for the distributed training task.

    Complete the following configurations.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7432/15344952002255_en-US.png)

    -   **Cluster**: Select the container cluster in which the distributed model training task will be run.
    -   **Application Name**: Name of the application created for running the training task, which can be 1–64 characters long and contain numbers, English letters, and hyphens \(-\). For example, tf-train-distributed.
    -   **Framework**: Select the framework used for model training. Currently, the supported frameworks include TensorFlow, Keras, Python, and customized image. If Customized Image is selected, enter the valid image address in the Image field.
    -   **Distributed Training**: Select whether or not to perform distributed training tasks. Here select this check box to perform distributed training. Configure the Number of Parameter Servers and Number of Workers to specify the number of parameter server nodes and the number of worker nodes used for computing during the distributed training. In this example, one parameter server and two workers are configured.
    -   **GPUs Per Worker**: Specify the number of GPUs in use for each worker when the training task is performed. Setting this parameter to 0 indicates CPU, instead of GPU, is used for training. In this example, each worker will use one GPU.
    -   **Data Source**: Select the data source used to store training data. Select the data volume created in the cluster by OSS or select Local Directory and then enter the absolute path. You can also select **No Data Source**. In this example, the tfoss data volume is selected.
    -   **Git URL**: Specify the address of the GitHub code repository in which the training codes are stored.

        **Note:** Currently, HTTP and HTTPS are supported, while SSH is not supported.

    -   **Private Git Information**: Select this check box if you use the private code repository. Then, configure the **Git Username** \(your GitHub account\) and **Git Password**.
    -   **Command**: Specify the command used to run the preceding codes to perform model training.

        **Note:** If you select a framework supporting Python3, directly call `python3`, instead of `python`, in the command line.

    -   **Enable Monitor**: Select whether or not to use TensorBoard to monitor the training status. With this check box selected, enter a valid path where the training logs will be stored in the Log Directory field and make sure that the path is the same as the log output path in the training codes. For example, if you set **Log Directory** as /output/training\_logs, your codes will output the logs to the same path. As shown in the preceding figure, the path consistency is guaranteed by specifying the code command line parameter --log\_dir.

        **Note:** Here the training logs indicate the event files output by the TensorFlow API for TensorBoard, and the checkpoint files that store the model status \(when performing distributed training by using TensorFlow, the worker playing the Chief role is generally responsible for storing checkpoint\).

5.  After the configurations, click **OK** to create the application used to perform model training tasks.
6.  Click **Applications** in the left-side navigation pane. Select the cluster from the Cluster list and then click the name of the created application \(**tf-train-distributed** in this example\).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7432/15344952002256_en-US.png)

7.  Click the **Routes** tab. Two links starting with tensorboard are displayed, each of which is used to open the TensorBoard monitoring page corresponding to a worker node. The link starting with tensorboard0 indicates worker 0, and the link starting with tensorboard1 indicates worker 1.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7432/15344952002258_en-US.png)

    Click the link to view the training monitoring data.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7432/15344952002259_en-US.png)

8.  Click the **Logs** tab to view the logs that are output to stdout/stderr by your codes when the training tasks are being performed.

    **Note:** Generally, to run the training task application, multiple service containers are automatically created to run different program branches respectively. For example, generally, the ps container is used to run the parameter server codes, the worker container is used to run model computing codes, and the tensorboard container is used to run TensorBoard training monitoring. You can click the **Services** or **Containers** tab to view the created services/containers.

    You can filter the logs by service container name, for example, worker0, and view detailed logs output to stdout/stderr by the container operating program. You can also filter the logs by time and display quantity, and select to download the log file to a local directory. In this example, after checking the logs of worker0 and worker1, you can find that they calculate the same model in parallel mode based on some training data, and perform gradient update simultaneously by using ps0.

    **Note:** The stdout/stderr logs mentioned here are different from the preceding TensorBoard event files and checkpoint files.

    **worker0 logs**

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7432/15344952002260_en-US.png)

    **worker1 logs**

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7432/15344952002261_en-US.png)

9.  Click the **Services** tab. If the status of all the worker services is Stopped, the training task is completed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7432/15344952002262_en-US.png)

10. In the OSS client, you can check that the training results are automatically stored in the OSS bucket.

    After the training is completed, the system automatically copies all files stored in the local /output directory of the worker container to the OSS bucket corresponding to your specified data volume. In the OSS client, you can check that the event files and checkpoint files written to the /output directory have been backed up to your OSS bucket.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7432/15344952002263_en-US.png)

11. Manage the training task.

    To stop, restart, or delete a training task \(for example, to release computing resources for a new training task\), return to the **Applications** \> Application List page, find the corresponding application, and perform operations on the right.


