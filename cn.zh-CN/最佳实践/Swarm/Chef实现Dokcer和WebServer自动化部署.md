# Chef实现Dokcer和WebServer自动化部署 {#concept_czt_23z_g2b .concept}

Chef 是一个自动化部署框架，结合阿里云容器服务，可实现定制化的自动部署工作。首先参考[Chef官网](https://www.chef.io/)了解一些基本概念，诸如 Cookbook、 Recipe 、Chef Workstation、 Chef Server、 Chef Nodes，以便快速入门。

## 前提条件 {#section_w15_33z_g2b .section}

-   您已成功创建一个Swarm集群，该集群保留EIP。
-   准备一个本地Linux环境，本示例是Ubuntu 16.04，您可根据本地环境，参考[https://downloads.chef.io/chefdk/](https://downloads.chef.io/chefdk/)获取相应的ChefDK。
-   您需要登录Chef官网，注册一个账号，创建一个Organization，本例中为example。

## 在Linux上安装chef工作站 {#section_d3b_3lz_g2b .section}

您需要前往chef官网下载符合本地Linux环境的ChefDK，本例中使用Ubuntu 16.04对应的ChefDK。

首先在/home目录下创建一个chef-repo文件夹。

```
mkdir /home/chef-repo
```

进入chef-repo目录，使用curl命令下载ChefDK程序包，并进行安装。

```
cd  /home/chef-repo
curl  -O https://packages.chef.io/files/stable/chefdk/3.0.36/ubuntu/16.04/chefdk_3.0.36-1_amd64.deb
dpkg  -i  chefdk_3.0.36-1_amd64.deb
```

然后您需要进行大量的Chef安装配置，您在安装过程中如果遇到问题，可参见Chef官方文档进行排查。

**验证Chef**

```
chef verify                                                  #验证ChefDK的组件是否正常
chef --version                                                #查看chef版本

```

**设置Chef 环境变量**

设置Chef相关的环境变量，如：GEM\_ROOT、GEM\_HOME、GEM\_PATH。

```
export GEM_ROOT="/opt/chefdk/embedded/lib/ruby/gems/2.1.0"
export GEM_HOME="/root/.chefdk/gem/ruby/2.1.0"
export GEM_PATH="/root/.chefdk/gem/ruby/2.1.0:/opt/chefdk/embedded/lib/ruby/gems/2.1.0"

```

此外，如果你的系统上已经安装了ruby，你需要更新与ruby相关的PATH变量。

```
export PATH="/opt/chefdk/bin:/root/.chefdk/gem/ruby/2.1.0/bin:/opt/chefdk/embedded/bin:/opt/chefdk/bin:/root/.chefdk/gem/ruby/2.1.0/bin:/opt/chefdk/embedded/bin:/opt/chefdk/bin:/root/.chefdk/gem/ruby/2.1.0/bin:/opt/chefdk/embedded/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin"
```

**设置访问Chef的Firewalld规则**

为了访问Chef服务器上的Chef Manage GUI，添加以下firewalld规则，开放Chef服务器上的相应端口。

```
firewall-cmd --direct  --add-rule ipv4 \
filter INPUT_direct 0 -i eth0 -p tcp \
 --dport 443 -j ACCEPT

firewall-cmd --direct  --add-rule ipv4 \
filter INPUT_direct 0 -i eth0 -p tcp \
 --dport 80 -j ACCEPT

firewall-cmd --direct  --add-rule ipv4 \
filter INPUT_direct 0 -i eth0 -p tcp \
 --dport 9683 -j ACCEPT

firewall-cmd --reload
```

**从Chef Manage GUI下载Starter Kit**

登录[Chef Manage GUI](https://manage.chef.io/organizations)，单击**Administration**选项，从列表中选择organization。此例中，organization为example，选中organization之后，点击左侧菜单中的**Starter Kit**，将chef-starter.zip文件下载到本地机器。

将chef-starter.zip文件传输到本地Linux下的Chef工作站，并解压到home/chef-repo目录下。

```
# cd /home/chef-repo
unzip chef-starter.zip 
```

**下载Chef服务器的SSL证书**

证书会下载到chef-repo/.chef/trusted\_certs目录中。

```
# cd ~/chef-repo 
# knife ssl fetch

WARNING: Certificates from api.chef.io will be fetched and placed in your trusted_cert               
directory (/root/chef-repo/.chef/trusted_certs).

Knife has no means to verify these are the correct certificates. You should
verify the authenticity of these certificates after downloading.

Adding certificate for wildcard_opscode_com in /root/chef-repo/.chef/trusted_certs/wildcard_opscode_com.crt   
Adding certificate for DigiCert_SHA2_Secure_Server_CA in /root/chef-repo/.chef/trusted_certs/DigiCert_SHA2_Secure_Server_CA.crt


```

**验证Chef工作站是否安装成功**

配置成功后，执行以下命令，可看到我们预先创建的Organization，说明我们已成功连接到工作站。

```
# cd ~/chef-repo

# knife client list
example-validator
```

## 创建实现Docker自动初始化的CookBook {#section_ssd_rdg_h2b .section}

1.  在Chef工作站上创建一个CookBook。
    -   在chef-repo/cookbooks目录下，执行以下命令，创建一个名为docker\_init的CookBook。

        ```
        chef generate cookbook docker_init
        ```

    -   进入chef-repo/cookbooks/docker\_init/recipe/目录，找到default.rb文件，进行配置。该示例用于在Ubuntu中启动最新的Docker版本。

        ```
        
        apt_update
        
        
        package 'apt-transport-https'
        package 'ca-certificates'
        package 'curl'
        package 'software-properties-common'
        
        
        execute 'apt-key' do
        command 'apt-key fingerprint 0EBFCD88'
        end
        
        
        execute 'apt-repo' do
        command 'add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu/dists/xenial/stable/"'
        
        
        end
        
        
        execute 'apt-repo' do
        command 'apt-get update'
        end
        
        
        execute 'apt-repo' do
        command 'apt-get install docker-ce -y --allow-unauthenticated'
        end
        
        
        service 'docker' do
        action [:start, :enable]
        end
        
        ```

2.  校验docker\_init这个CookBook是否在本地工作。

    ```
    # chef-client --local-mode --runlist 'recipe[docker_init]'
    
    [2018-06-27T15:54:30+08:00] INFO: Started chef-zero at chefzero://localhost:1 with repository at /root/chef-repo
    One version per cookbook
    
    
    Starting Chef Client, version 14.1.12
    [2018-06-27T15:54:30+08:00] INFO: *** Chef 14.1.12 ***
    [2018-06-27T15:54:30+08:00] INFO: Platform: x86_64-linux
    [2018-06-27T15:54:30+08:00] INFO: Chef-client pid: 2010
    [2018-06-27T15:54:30+08:00] INFO: The plugin path /etc/chef/ohai/plugins does not exist. Skipping...
    [2018-06-27T15:54:31+08:00] INFO: Setting the run_list to [#] from CLI options
    [2018-06-27T15:54:32+08:00] INFO: Run List is [recipe[docker_init]]
    [2018-06-27T15:54:32+08:00] INFO: Run List expands to [docker_init]
    [2018-06-27T15:54:32+08:00] INFO: Starting Chef Run for yxm
    [2018-06-27T15:54:32+08:00] INFO: Running start handlers
    [2018-06-27T15:54:32+08:00] INFO: Start handlers complete.
    resolving cookbooks for run list: ["docker_init"]
    [2018-06-27T15:54:32+08:00] INFO: Loading cookbooks [docker_init@0.1.0]
    Synchronizing Cookbooks:
    - docker_init (0.1.0)
    Installing Cookbook Gems:
    Compiling Cookbooks...
    Converging 10 resources
    Recipe: docker_init::default
    * apt_update[] action periodic[2018-06-27T15:54:32+08:00] INFO: Processing apt_update[] action periodic (docker_init::default line 9)
    ....
    ---- End output of add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu/dists/xenial/stable/" ----
    Ran add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu/dists/xenial/stable/" returned 1
    
    
    ```

    执行以下命令，检查本地安装的Docker是否升级到最新版本。

    ```
    # docker --version
    Docker version 17.06.2-ce, build 2e0fd6f
    
    
    ```

3.  将该CookBook上传到Chef Server。
    -   在Chef工作站，执行以下命令，将名为docker\_init的CookBook上传到Chef Server。

        ```
        knife cookbook upload docker_init
        ```

    -   执行以下命令，检验该CookBook是否被成功上传。

        ```
        # knife cookbook list
        docker_init 0.1.0
        
        
        ```

4.  将该cookbook导入到阿里云Swarm集群的节点。
    -   在Chef工作站执行以下命令，将docker\_init导入充当Chef Node的Swarm集群的节点。

        **说明：** 将ADDRESS替换为Swarm集群的ECS节点的EIP， USER是ECS节点登录用户，一般为root，PASSWORD是ECS节点登录密码。若Swarm集群中有多个节点，需要对每个ECS节点执行该命令。

        ```
        # knife bootstrap ADDRESS --ssh-user USER --ssh-password 'PASSWORD' --sudo --use-sudo-password --node-name node1-ubuntu --run-list 'recipe[docker_init]'
        
        Creating new client for node1-ubuntu
        Creating new node for node1-ubuntu
        Connecting to 121.196.219.18
        ...
        https://download.docker.com/linux/ubuntu/dists/xenial/stable/" ----
        121.196.219.18 Ran add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu/dists/xenial/stable/" returned 1
        
        
        ```

    -   登录每个ECS节点，检查每个节点上安装的Docker是否已更新到最新版本。执行`docker -- version`命令进行检验。

至此，通过Chef自动部署系统，实现对阿里云容器集群Docker的版本更新。

## 创建自动化部署Web Server的CookBook {#section_s3z_nqg_h2b .section}

1.  在Chef工作站创建一个新的CookBook。
    -   在chef-repo/cookbooks目录下，执行以下命令，创建名为web\_init的CookBook。

        ```
        chef generate cookbook web_init
        ```

    -   进入chef-repo/cookbooks/web\_init/recipe/目录，找到default.rb文件，进行配置。

        ```
        
        execute 'apt-repo' do
        command 'apt-get -y install apache2 --allow-unauthenticated'
        end
        
        
        service 'apache2' do
        action [:start, :enable]
        end
        
        
        file '/var/www/html/index.html' do
        content '
        hello world
        '
        end
        
        
        service 'iptables' do
        action :stop
        end
        
        
        ```

2.  检验该CookBook是否在本地工作。
    -   执行`curl http://localhost:80`命令，检查web\_init是否在本机工作。
    -   在Chef 工作站，将web\_init这个CookBook上传到Chef Server。

        ```
        knife cookbook upload web_init
        ```

3.  将该cookbook导入到阿里云Swarm集群的节点。

    在Chef工作站执行以下命令，将web\_init导入充当Chef Node的Swarm集群的节点。

    **说明：** 将ADDRESS替换为Swarm集群的ECS节点的EIP， USER是ECS节点登录用户，一般为root，PASSWORD是ECS节点登录密码。若Swarm集群中有多个节点，需要对每个ECS节点执行该命令。

    ```
    knife bootstrap ADDRESS --ssh-user USER --ssh-password 'PASSWORD' --sudo --use-sudo-password --node-name node1-ubuntu --run-list 'recipe[web_init]'
    ```

4.  在阿里云Swarm集群中，检查Web Server是否成功启动。登录阿里云Swarm集群的节点。
    -   执行`systemctl status apache2.service`，检查apache2 是否正常运行。
    -   在浏览器中访问`http://ADDRESS:80`，查看浏览器屏幕是否输出`hello world`。

        **说明：** ADDRESS是指节点的EIP。


