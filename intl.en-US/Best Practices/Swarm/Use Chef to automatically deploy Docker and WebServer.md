# Use Chef to automatically deploy Docker and WebServer {#concept_czt_23z_g2b .concept}

Chef is an automated deployment framework. Combined with Alibaba Cloud Container Service, Chef can help you achieve customization and automation in your deployment. Log on to the [Chef](https://www.chef.io/) official website first to learn about basic terms for quick start, such as cookbook, recipe, chef workstation, chef server, and chef nodes.

## Prerequisites {#section_w15_33z_g2b .section}

-   You have created a swarm cluster that retains the EIP.
-   Prepare a local Linux environment. This example uses Ubuntu 16.04. According to your local environment, download a ChefDK at [https://downloads.chef.io/chefdk/](https://downloads.chef.io/chefdk/).
-   Log on to the Chef official website to register an account and create an organization. In this example, the created organization is called example.

## Install the chef workstation on Linux {#section_d3b_3lz_g2b .section}

You need to go to the Chef official website to download a ChefDK which is compatible with your local Linux environment. This example uses a ChefDK corresponding to Ubuntu 16.04.

First create a chef-repo directory in the /home directory.

```
mkdir /home/chef-repo
```

Enter the chef-repo directory and use the curl command to download a ChefDK package to install.

```
cd /home/chef-repo
curl -O https://packages.chef.io/files/stable/chefdk/3.0.36/ubuntu/16.04/chefdk_3.0.36-1_amd64.deb
dpkg -i chefdk_3.0.36-1_amd64.deb
```

Then you need to perform a large number of Chef installation configurations. If you encounter problems during installation, see Chef official documents to troubleshoot the problems.

**Verify Chef**

```
chef verify #Verify if the ChefDK components are normal
chef --version #View the Chef version.

```

**Set Chef environment variables**

Set environment variables related to Chef, such as GEM\_ROOT, GEM\_HOME, and GEM\_PATH.

```
export GEM_ROOT="/opt/chefdk/embedded/lib/ruby/gems/2.1.0"
export GEM_HOME="/root/.chefdk/gem/ruby/2.1.0"
export GEM_PATH="/root/.chefdk/gem/ruby/2.1.0:/opt/chefdk/embedded/lib/ruby/gems/2.1.0"

```

In addition, if Ruby is already installed on your system, update the PATH variable related to Ruby.

```
export PATH="/opt/chefdk/bin:/root/.chefdk/gem/ruby/2.1.0/bin:/opt/chefdk/embedded/bin:/opt/chefdk/bin:/root/.chefdk/gem/ruby/2.1.0/bin:/opt/chefdk/embedded/bin:/opt/chefdk/bin:/root/.chefdk/gem/ruby/2.1.0/bin:/opt/chefdk/embedded/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin"
```

**Configure firewalld rules for accessing Chef**

To access the Chef Manage GUI on the Chef server, add the following firewalld rules and open corresponding ports on the Chef server.

```
firewall-cmd --direct --add-rule ipv4 \
filter INPUT_direct 0 -i eth0 -p tcp \
 --dport 443 -j ACCEPT

firewall-cmd --direct --add-rule ipv4 \
filter INPUT_direct 0 -i eth0 -p tcp \
 --dport 80 -j ACCEPT

firewall-cmd --direct --add-rule ipv4 \
filter INPUT_direct 0 -i eth0 -p tcp \
 --dport 9683 -j ACCEPT

firewall-cmd --reload
```

**Download Starter Kit from the Chef Manage Gui**

Log on to [Chef Manage GUI](https://manage.chef.io/organizations), click **Administration**, and select the organization in the drop-down list. In this example, the organization is example. After the organization is selected, click the **Starter Kit** in the left-side navigation pane to download the chef-starter.zip file to your local host.

Transfer the chef-starter.zip file to the Chef workstation in your local Linux , and extract it to the home/chef-repo directory.

```
# cd /home/chef-repo
unzip chef-starter.zip 
```

**Download the SSL Certificate for the Chef server**

The certificate is downloaded to the chef-repo/.chef/trusted\_certs directory.

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

**Verify if the Chef workstation is installed successfully**

After completing configuration, execute the following commands. If the created organization is displayed, you have successfully connected to the workstation.

```
# cd ~/chef-repo

# knife client list
example-validator
```

## Create a cookbook that implements Docker automatic initialization {#section_ssd_rdg_h2b .section}

1.  Create a cookbook on the Chef workstation.
    -   In the chef-repo/cookbooks directory, execute the following command to create a cookebook named docker\_init.

        ```
        chef generate cookbook docker_init
        ```

    -   Go to the chef-repo/cookbooks/docker\_init/recipe/ directory to find the default.rb file and configure the file. This example is used to start the latest version of Docker in Ubuntu.

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

2.  Verify if the cookbook named docker\_init works locally.

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

    Execute the following command to check if the locally installed docker is upgraded to the latest version.

    ```
    # docker --version
    Docker version 17.06.2-ce, build 2e0fd6f
    
    
    ```

3.  Upload the cookbook to the Chef server.
    -   On the Chef workstation, upload the cookbook named docker\_init to the Chef server by executing the following command.

        ```
        knife cookbook upload docker_init
        ```

    -   Execute the following command to verify that the cookbook is uploaded successfully.

        ```
        # knife cookbook list
        docker_init 0.1.0
        
        
        ```

4.  Import the cookbook into the node of the Alibaba Cloud swarm cluster.
    -   On the Chef workstation, execute the following command to import docker\_init into the node of the swarm cluster that act as a Chef node.

        **Note:** Replace ADDRESS with the EIP of the ECS node of the swarm cluster. USER is the logon user of the ECS node, typically root. PASSWORD is the ECS node logon password. If the swarm cluster has multiple nodes, execute this command for each ECS node.

        ```
        # knife bootstrap ADDRESS --ssh-user USER --ssh-password 'PASSWORD' --sudo --use-sudo-password --node-name node1-ubuntu --run-list 'recipe[docker_init]'
        
        Creating new client for node1-ubuntu
        Creating new node for node1-ubuntu
        Connecting to 121.196.219.18
        ...
        https://download.docker.com/linux/ubuntu/dists/xenial/stable/" ----
        121.196.219.18 Ran add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu/dists/xenial/stable/" returned 1
        
        
        ```

    -   Log on to each ECS node to check if the docker installed on each node has been updated to the latest version. Execute the `docker -- version` command to verify.

Now you have updated the version of Alibaba Cloud container cluster Docker through the Chef automated deployment system.

## Create a cookbook that automates the deployment of Web Server {#section_s3z_nqg_h2b .section}

1.  Create a new cookbook on the Chef workstation.
    -   In the chef-repo/cookbooks directory, execute the following command to create a cookbook named web\_init.

        ```
        chef generate cookbook web_init
        ```

    -   Go to the chef-repo/cookbooks/web\_init/recipe/directory to find the default.rb file and configure the file.

        ```
        
        execute 'apt-repo' do
        command 'apt-get -y install apache2 --allow-unauthenticated'
        end
        
        
        service 'apache2' do
        action [:start, :enable]
        end
        
        
        file '/var/www/html/index.html' do
        content '
        hello,world
        '
        end
        
        
        service 'iptables' do
        action :stop
        end
        
        
        ```

2.  Verify that the cookbook works locally.
    -   Execute the `curl http://localhost:80` command to check if the web\_init works on the local host.
    -   On the Chef workstation, upload the cookbook named web\_init to the Chef server.

        ```
        knife cookbook upload web_init
        ```

3.  Import the cookbook into the node of the Alibaba Cloud swarm cluster.

    On the Chef workstation, execute the following command to import web\_init into the node of the swarm cluster that acts as a chef node.

    **Note:** Replace ADDRESS with the EIP of the ECS node of the swarm cluster. USER is the logon user of the ECS node, typically root. PASSWORD is the ECS node logon password. If the swarm cluster has multiple nodes, execute this command for each ECS node.

    ```
    knife bootstrap ADDRESS --ssh-user USER --ssh-password 'PASSWORD' --sudo --use-sudo-password --node-name node1-ubuntu --run-list 'recipe[web_init]'
    ```

4.  Check if the Web Server starts successfully in the Alibaba Cloud swarm cluster. Log on to the node of the Alibaba Cloud swarm cluster.
    -   Execute the `systemctl status apache2.service` command to check if apache2 operates normally.
    -   Visit `http://ADDRESS:80` in the browser to see if `hello world` is displayed.

        **Note:** ADDRESS is the EIP of the node.


