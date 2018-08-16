# Triggers {#concept_yx3_y3k_xdb .concept}

## Introduction {#section_hfd_mjk_xdb .section}

A trigger is an API provided by Container Service for simple and fast redeployment and resource scaling.

The strict authentication is needed because standard APIs must guarantee the security. However, in scenarios where an API is integrated with a third-party system \(for example, Jenkins or other continuous integration CI/CD  system\), the required permissions are limited, for example, messaging only.  Therefore, to guarantee the security and convenience, APIs that have partial authentication policies and can be flexibly called  are widely applied in scenarios requiring continuous integration and delivery.

Currently, Container Service provides redeployment trigger and resource scaling trigger.

-   Redeployment trigger

    You can integrate your APIs with your monitoring system and redeploy your applications when the system has exceptions. You can also integrate your APIs with container Hub,  and then the application can be automatically redeployed by using the latest image after the new image is constructed.

-   Resource scaling trigger

    You can call the resource scaling trigger to realize container scaling.


## Create a trigger {#section_dhk_pjk_xdb .section}

**Procedure**

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/#/overview/all).
2.  Click **Applications** in the left-side navigation pane.
3.  Select the cluster in which the application resides from the Cluster list.
4.  Click the application name. name.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/13784/15344170153652_en-US.png)

5.  Click **Create Trigger** in the upper  right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/13784/15344170153653_en-US.png)

6.  The **Create Trigger** dialog box appears. Select **Redeploy** or  **Resource Scaling** from the Action list and then click **Confirm**.

    -   **Redeploy**

        If you have Write permission to the image used by the application, you can select the Associated with Image Update check box. Then, the application can be automatically redeployed by using the latest image after the new image is constructed. **Associated with Image Update** check box.  Then, the application can be automatically redeployed by using the latest image after the new image is constructed.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/13784/15344170163655_en-US.png)

    -   **Resource scaling**

        Select the service that needs to set the resource scaling trigger from the **Service** list.

        **Note:** To use the resource scaling trigger, upgrade the cluster Agent to the latest version.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/13784/15344170163658_en-US.png)

    The generated trigger address is the API address.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/13784/15344170163659_en-US.png)


## Subsequent operations {#section_qfd_mjk_xdb .section}

You can call the trigger by using a third-party integration system or a GET or POST request. For example, you can run the CURL command to call the trigger.

**Call the redeployment trigger:**

```
curl 'https://cs.console.aliyun.com/hook/trigger?triggerUrl=YzI4YTk5NzFkZWZkYzQ2MTJiOWZkNTM1MzY2ZDU1M2NifGNvbGxlY3RkLWJlbmNobWFya3xyZWRlcGxveXwxOGlxbjc1Z25uMmVzfA==&secret=44586c6b466352395143584c3970654ff5323d2509d546fdc1b33054b0928da8'
```

**Call the resource scaling trigger:**

**Note:** When calling the resource scaling trigger, manually add the following parameters to the trigger URL.

|Parameter name|Required |Meaning|Optional value|
|:-------------|:--------|:------|:-------------|
|type |Yes |The scaling type.|Contract: scale\_in. Expand: scale\_out.|
|step|Yes|The scaling number. |Positive integer: 1-100.|

For example, calling the following trigger will add five containers to the service.

```
curl 'https://cs.console.aliyun.com/hook/trigger?triggerUrl=Y2IxZjI5YzhhYjIwMzRlMjBiYjc2OGUzYTlmZDgyNDAyfHdvcmRwcmVzcy10ZXN0fHNjYWxpbmd8MTkzZmEyMXFwZXVwMXw=&secret=53374142724e4e4a626f664a313131556e62c6716cd0d97d096900b3ad42a9ad&type=scale_out&step=5'
```

