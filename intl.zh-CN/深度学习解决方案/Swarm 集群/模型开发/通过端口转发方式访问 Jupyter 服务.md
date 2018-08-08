# 通过端口转发方式访问 Jupyter 服务 {#concept_und_p5g_vdb .concept}

通过端口转发方式访问 Jupyter 服务具有以下优点，但是设置较复杂。

-   您不需要购买负载均衡实例，可以节省您的费用。
-   您不需要在官网上开放端口即可从外部访问 Jupyter 服务。

下面的示例假设通过本地的 12345 端口进行转发 SSH 访问您的 Jupyter 服务。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15336968682130_zh-CN.png)

## 步骤 1 打通 SSH Tunnel {#section_kqj_dvg_vdb .section}

**Mac OS X 和 Linux 环境下**

运行以下命令打通本地端口和 ECS 实例的连接。

```
ssh -ND 12345 root@39.***.**.236
```

`12345` 是您要使用的本地端口，您可以自定义。`39.***.**.236` 是您的 ECS 实例绑定的弹性公网 IP。

**说明：** 您可以登录 [ECS 管理控制台](https://ecs.console.aliyun.com/#/home) 查看 ECS 实例绑定的弹性公网 IP。如果您的实例未绑定弹性公网 IP，您需要先为其绑定弹性公网 IP，参见 [管理弹性公网 IP](https://www.alibabacloud.com/help/zh/doc-detail/51995.htm)。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15336968682131_zh-CN.png)

**Windows 环境下**

1.  配置本地端口跳转。

    运行 PuTTY 并配置 SSH **Tunnels**。

    1.  填写 **Source Port**。本示例中为 12345。
    2.  选择 **Dynamic**。
    3.  单击 **Add**。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15336968682132_zh-CN.png)

2.  登录 ECS 实例。
    1.  运行 PuTTY，配置 **Session** 并单击 **Open**。

        填写 IP 地址，即 ECS 实例的弹性公网 IP，本示例中为 39.\*\*\*.\*\*.236。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15336968682133_zh-CN.png)

    2.  在弹出的会话框里输入您的登录账号和 ECS 实例的登录密码。

        您成功登录到 ECS 实例上。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15336968682134_zh-CN.png)


## 步骤 2 配置浏览器的网络连接 {#section_dcn_1wg_vdb .section}

**Firefox 浏览器**

如果您使用的是 Firefox 浏览器，打开浏览器，单击 **工具** \> **选项** \> **高级** \> **网络** 选项卡 \> 单击 **连接** 对应的 **设置** 按钮 \> 设置 **SOCKS 主机**。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15336968682135_zh-CN.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15336968682136_zh-CN.png)

**Chrome 浏览器**

运行以下命令。

```
Chrome --proxy-server="socks5://localhost:12345" --host-resolver-rules="MAP * 0.0.0.0 , EXCLUDE localhost" --user-data-dir=/tmppath/
```

**其中：**

-   `12345` 是您所使用的本地端口。
-   若是 Windows 系统，这里的 `/tmppath/` 可以写成类似 `d:/tmppath`；若是 Linux 或者 Mac OS X，可以直接写成 `/tmp/`。

在不同的操作系统中，Chrome 的位置不同，请参见下表：

|操作系统|Chrome 的位置|
|:---|:---------|
|Mac OS X|/Applications/Google\\ Chromapp/Contents/MacOS/Google\\ Chrome|
|Linux|/usr/bin/google-chrome|
|Windows|C:\\Program Files \(x86\)\\Google\\Chrome\\Application\\chrome.exe|

以 Windows 系统为例：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15336968682137_zh-CN.png)

## 步骤 3 访问 Jupyter 服务 {#section_z4c_x5g_vdb .section}

在您的浏览器中输入 Jupyter 服务的访问地址。本示例中为 192.\*\*\*.\*.83:32774。即可成功地从外部 SSH 访问到 Jupyter 服务。

**Firefox：**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15336968692138_zh-CN.png)

**Chrome：**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7429/15336968692139_zh-CN.png)

