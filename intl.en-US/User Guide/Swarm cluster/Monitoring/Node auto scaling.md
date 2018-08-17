# Node auto scaling {#concept_jyy_dd5_xdb .concept}

To meet the demands of applications under different loads, Container Service provides the auto scaling both for containers and nodes. The node auto scaling is to automatically adjust the number of nodes by monitoring the node resource usage.

**Node scaling policies:**

-   When the monitoring metric value exceeds the configured expansion condition, Container Service increases the number of nodes at your configured expansion step.
-   When the monitoring metric value is lower than the configured contraction condition, Container Service reduces the number of nodes at the system default step 1.

**Auto scaling monitoring metrics:**

-   Cluster CPU average usage
-   Cluster memory average usage

## Prerequisite {#section_a45_4d5_xdb .section}

-   Upgrade the cluster Agent to the latest version.  For more information, see [Upgrade Agent](intl.en-US/User Guide/Swarm cluster/Clusters/Upgrade Agent.md#).
-   Upgrade the cluster monitoring service \(acsmonitoring\) to the latest version.  For more information, see [Upgrade system services](intl.en-US/User Guide/Swarm cluster/Clusters/Upgrade system services.md#).
-   Activate the RAM service and update the RAM authorization information in the cluster \(Complete the following steps: Log on to the Container Service console. Click **Clusters** in the left-side navigation pane. Click **More** at the right of the cluster. Select **Update RAM Authorization Information** from the list.

## Instructions {#section_jql_pd5_xdb .section}

-   When determining whether the monitoring metric value exceeds the configured upper limit or lower limit, Container Service uses the average value of the monitoring metrics \(namely, the average CPU usage and the average memory usage\) within a sample period \(one minute\). Container Service triggers scaling only when the average monitoring metrics of three consecutive sample periods all exceed the configured upper limit or lower limit so as to avoid frequent scaling caused by monitoring data jitter.
-   Node contraction only contracts nodes that are created by means of node expansion. Your manually added or created nodes are not affected.  To perform auto contraction on those manually added nodes, add the following label for those nodes:

    `aliyun.reschedule==true`

-   During node contraction, the system deletes the Elastic Compute Service \(ECS\) instances in the cluster. Therefore, back up data in advance.
-   Do not schedule services with statuses to nodes that can be contracted.
-   The ECS instances added by expansion do not affect the deployed containers. The newly deployed containers are deployed according to the container deployment rules.
-   During node contraction, Container Service migrates containers on the deleted ECS instances to other ECS instances.

## Create node scaling rules {#section_mjy_qd5_xdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Click **Clusters** in the left-side navigation pane.
3.  On the Cluster List page, click **Manage** at the right of the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7092/15344947605355_en-US.png)

4.  Click **Node Scaling** in the left-side navigation pane and click **Create Scaling Rule**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7092/15344947605357_en-US.png)

5.  Configure the scaling rule and click **Next**.

    Constraint rules:

    -   The range of the **Expansion Condition** is 50%–100%. The range of the **Contraction Condition** is 0%–50%.
    -   The Expansion Condition must be at least 30% higher than the Contraction Condition.
    -   The range of the expansion step is 1–5. Currently, the default **contraction step** is 1 and cannot be configured.
    -   Set the **Min Number of Cluster Nodes** and **Max Number of Cluster Nodes**.  For contraction, if the number of nodes is less than or equal to the **Min Number of Cluster Nodes**, contraction is not performed. For expansion, if the number of nodes is greater than or equal to the  **Max Number of Cluster Nodes**, expansion is not performed.
    **Note:** Set the scaling policies with due care.  If the cluster already meets the configured scaling conditions when you set the scaling rule and the cluster still meets the scaling conditions after the scaling, the monitoring will continuously trigger the scaling.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7092/15344947605366_en-US.png)

6.  Configure the instance specifications and click **Submit**.

    Configure the specifications for the nodes added by expansion after setting the expansion condition.  For more information about how to configure the instance specifications, see [Create a cluster](intl.en-US/User Guide/Swarm cluster/Clusters/Create a cluster.md#).

    You can also configure whether or not to add the IP addresses of the nodes added by expansion to the RDS instance whitelist, which facilitates the ECS instances to access the RDS  instances.

    **Note:** The ECS instance must be in the same region as the RDS instance so that the IP address of the ECS instance can be added to the RDS instance whitelist.

    Click **Select RDS Instances** in the Advanced section at the bottom of the page.  The Add to RDS instance whitelist dialog box appears. Select the RDS instances and then click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7092/15344947605367_en-US.png)


## View created node scaling rules {#section_qkx_nd5_xdb .section}

You can view the crated node scaling rules.

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Click **Clusters** in the left-side navigation pane.
3.  On the Cluster List page, click **Manage** at the right of the cluster.
4.  Click **Node Scaling** in the left-side navigation pane. You can view the created node scaling rules.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7092/15344947605371_en-US.png)

    You can click **Modify** to modify the node scaling rule or click **Delete** to delete this rule.


## View monitoring metrics {#section_rk2_sd5_xdb .section}

1.  Click **Clusters** in the left-side navigation pane.
2.  On the Cluster List page, click **Monitor** at the right of the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7092/15344947605374_en-US.png)

    You are redirected to the CloudMonitor console and can view the cluster monitoring information.

    **Note:** If the monitoring data does not exist, check whether or not the monitoring service \(acsmonitoring\) is correctly installed. If not, redeploy the monitoring service \(acsmonitoring\). Check whether or not the cluster  Agent is in the latest version. If not, upgrade the cluster Agent. Check whether or not the monitoring service \(acsmonitoring\) is in the latest version. If not, upgrade the monitoring service \(acsmonitoring\).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7092/15344947605376_en-US.png)

3.  Click **Container Service** in the left-side navigation pane. Click **View All Rules**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7092/15344947605377_en-US.png)

4.  On the Alarm Rules page, you can view the automatically configured alarm rules of auto scaling.

     

    If no monitoring alarm rule exists, update the RAM authorization information in the cluster \(On the Cluster List page, click**More** \> **Update RAM Authorization Information**at the right of the cluster\). Activate the RAM service before updating the RAM authorization information. Otherwise, the system reports an error.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7092/15344947605381_en-US.png)

5.  Select an alarm rule to modify the alarm conditions and notification contacts who can be notified in SMSs, e-mails, and other ways. You can also disable the alarm rule.

     


## Troubleshoot {#section_alx_nd5_xdb .section}

If your configured node auto scaling rule does not take effect, see[What if the auto scaling rule does not take effect](intl.en-US/User Guide/Swarm cluster/Monitoring/What if the auto scaling rule does not take effect.md#) for troubleshooting.

