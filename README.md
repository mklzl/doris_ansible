# Doris_Ansible

## One-click deployment, upgrade, degrade, start, stop, and expand the capacity of the highly available Doris cluster
# 玩转Doris_Ansible

## step 1 : 下载安装ansible

    yum install ansible

## step 2 : 下载Doris_Ansible

    git clone https://github.com/mklzl/doris_ansible.git

## step 3 : 编辑配置文件

### 编辑参与主机groups,请根据自身集群规划，编辑对应主机组

* vi /etc/ansible/hosts

        ## 集群参与的机器ip
        [doris_hosts]
        192.168.1.239
        192.168.1.241
        192.168.1.243

        ##fe所在机器的ip
        [frontends]
        192.168.1.239
        192.168.1.241
        192.168.1.243

        ##master节点所在的ip
        [master]
        192.168.1.241

        ##follower所在节点的ip
        [follower]
        192.168.1.239
        192.168.1.243

        ##be所在节点的ip
        [backends]
        192.168.1.239
        192.168.1.241
        192.168.1.243
        
        ##broker节点所在的ip
        [brokers]
        192.168.1.239
        192.168.1.241
        192.168.1.243

### 编辑初始化安装机器配置变量文件

* vi setup_vars.yml

      ---
      # 生产环境的fe.conf所在路径。
      # 如果没有特殊配置，建议使用安装包内的fe.conf，请根据机器情况酌情配置priority_networks
      fe_conf_path: /home/doris_ansible/fe.conf
      #生产环境的be.conf所在路径。
      # 如果没有特殊配置，建议使用安装包内的be.conf，请根据机器情况酌情配置priority_networks
      be_conf_path: /home/doris_ansible/be.conf
      # heartbeat_service_port，请和be.conf中的heartbeat_service_port配置保持一致
      heartbeat_service_port: 9050
      # edit_log_port，请和fe.conf中的edit_log_port配置保持一致
      edit_log_port: 9010
      # query_port，请和fe.conf中的query_port配置保持一致
      query_port: 9030
      # broker_ipc_port，请和apache_hdfs_broker.conf中的broker_ipc_port保持一致
      broker_ipc_port: 8000
      # 待安装的Doris压缩包所在路径，请写绝对路径
      sr_path: /home/doris_ansible/PALO-0.15.1-rc09-binary.tar.gz
      # Doris压缩包要解压安装的位置
      dest_path: /home/doris_ansible
      # 安装后，Doris中fe所在的位置，如无特殊情况，一般在dest_path下当前安装版本目录下的fe
      fe_path: /home/doris_ansible/PALO-0.15.1-rc09-binary/fe
      # 安装后，Doris中be所在的位置，如无特殊情况，一般在dest_path下当前安装版本目录下的be
      be_path: /home/doris_ansible/PALO-0.15.1-rc09-binary/be
      # 安装后，Doris中broker所在的位置，如无特殊情况，一般在dest_path下当前安装版本目录下的apache_hdfs_broker
      broker_path: /home/doris_ansible/PALO-0.15.1-rc09-binary/apache_hdfs_broker
      # 机器java_home所在路径，请确保所有机器保持一致
      java_home: /usr/java/jdk1.8.0_131
      # master所在的机器ip
      master: 192.168.1.241

## step 4 : 启动初始化集群

      ansible-playbook setup.yml

## step 5 : 添加集群角色

    ansible-playbook add_roles.yml

## step 6 : 查看集群状态

    可以根据自己配置的具体情况，登录集群，通过show frontends;show backends;show broker;查看集群的搭建情况

## step 7 : 启停集群

    #stop all
    ansible-playbook stop_all.yml
    #start all
    ansible-playbook start_all.yml

## step 8: 升级或者回滚集群

### 编辑升级回滚所需配置文件

* vi upgrade_vars.yml

      ---
      #原集群be所在路径
      be_path: /home/doris_ansible/PALO-0.15.1-rc09-binary/be
      #原集群fe所在路径
      fe_path: /home/doris_ansible/PALO-0.15.1-rc09-binary/fe
      #原集群broker所在路径
      broker_path:  /home/doris_ansible/PALO-0.15.1-rc09-binary/apache_hdfs_broker
      #需要回滚或者升级的压缩包所在路径
      new_pkg_path: /home/doris_ansible/PALO-0.15.3-release-binary.tar.gz
      #压缩包解压路径
      new_filepath: /home/doris_ansible
      #解压缩后，新be所在路径，如无特殊情况，一般填写为解压路径下的对应版本的be所在路径
      new_be_path: /home/doris_ansible/PALO-0.15.3-release-binary/be
      #解压缩后，新fe所在路径，如无特殊情况，一般填写为解压路径下的对应版本的be所在路径
      new_fe_path: /home/doris_ansible/PALO-0.15.3-release-binary/fe
      #解压缩后，新broker所在路径，如无特殊情况，一般填写为解压路径下的对应版本的apache_hdfs_broker所在路径
      new_broker_path: /home/doris_ansible/PALO-0.15.3-release-binary/apache_hdfs_broker
      #java_home所在路径
      java_home: /usr/java/jdk1.8.0_131

### 执行升级或者回滚

    ansible-playbook upgrade.yml

### 使用示例
* 使用的为POLO的安装包，需要注意的是本机的jdk和待更新的包的jdk版本
#### 环境参数

* 节点规划

      3fe + 3be + 3broker
* frontends

      192.168.1.239
      192.168.1.241
      192.168.1.243
* backends

      192.168.1.239
      192.168.1.241
      192.168.1.243
* brokers

      192.168.1.239
      192.168.1.241
      192.168.1.243
* master

      192.168.1.241
* follower

      192.168.1.239
      192.168.1.243
* 安装所需压缩包所在路径

      /home/doris_ansible/PALO-0.15.1-rc09-binary.tar.gz
* 生产环境fe和be的配置环境所在位置

      /home/doris_ansible/fe.conf
      /home/doris_ansible/be.conf
* java_home

      /usr/java/jdk1.8.0_131

#### 编辑配置文件

* 编辑主机组

      vi /etc/ansible/hosts 添加以下内容

      [doris_hosts]
      192.168.1.239
      192.168.1.241
      192.168.1.243

      [frontends]
      192.168.1.239
      192.168.1.241
      192.168.1.243
      
      [master]
      192.168.1.241
      
      [follower]
      192.168.1.239
      192.168.1.243
      
      [backends]
      192.168.1.239
      192.168.1.241
      192.168.1.243

      [brokers]
      192.168.1.239
      192.168.1.241
      192.168.1.243

* 编辑初始化配置

      vi setup_vars.yml

      ---
      priority_networks: 192.168.1.0/24
      heartbeat_service_port: 9050
      edit_log_port: 9010
      query_port: 9030
      broker_ipc_port: 8000
      sr_filepath: /home/doris_ansible/PALO-0.15.1-rc09-binary.tar.gz
      dest_filepath: /home/doris_ansible
      fe_conf_path: /home/doris_ansible/fe.conf
      be_conf_path: /home/doris_ansible/be.conf
      fe_path: /home/doris_ansible/PALO-0.15.1-rc09-binary/fe
      be_path: /home/doris_ansible/PALO-0.15.1-rc09-binary/be
      broker_path: /home/doris_ansible/PALO-0.15.1-rc09-binary/apache_hdfs_broker
      java_home: /usr/java/jdk1.8.0_131
      master: 192.168.1.241

* 启动初始化操作

      ansible-playbook setup.yml
* 添加角色

      ansible-playbook add_roles.yml

#### 集群升降级
* 前置条件

      集群从0.15.1升级为0.15.3
      新安装包所在目录为/home/doris_ansible/PALO-0.15.3-release-binary.tar.gz

* 编辑升降级配置文件

      vi upgrade_vars.yml

      ---
      be_path: /home/doris_ansible/PALO-0.15.1-rc09-binary/be
      fe_path: /home/doris_ansible/PALO-0.15.1-rc09-binary/fe
      broker_path: /home/doris_ansible/PALO-0.15.1-rc09-binary/apache_hdfs_broker
      new_pkg_path: /home/doris_ansible/PALO-0.15.3-release-binary.tar.gz
      new_filepath: /home/doris_ansible
      new_be_path: /home/doris_ansible/PALO-0.15.3-release-binary/be
      new_fe_path: /home/doris_ansible/PALO-0.15.3-release-binary/fe
      new_broker_path: /home/doris_ansible/PALO-0.15.3-release-binary/apache_hdfs_broker
      java_home: /usr/java/jdk1.8.0_131


* 执行升降级操作

      ansible-playbook upgrade.yml

* 查看集群状态

  通过show frontends;show backends;show broker;查看版本信息
