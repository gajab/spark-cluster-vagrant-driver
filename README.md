# spark-cluster-vagrant-driver
My running notes to Setup spark cluster using vagrant and get driver program running in eclipse/Java on my mac laptop.

* Follow steps from https://github.com/is-land/vagrant-spark-cluster. Thanks @vitojeng
FYI : I tried few others like "https://github.com/wwken/Vagrant-Puppet" but got stuck at multiple points. 
@vitojeng worked best for me.

* Upgrade it to latest version of Spark in ansible/spark-cluster.yml before doing `vagrant up`
```
 - hosts: masters, slaves
  remote_user: root
  roles:
    - common
    - williamyeh.oracle-java
    - apache-spark
  vars:
    os_user: vagrant
    os_locale: en_US.UTF-8
    ethernet_interface: ansible_eth1
    java_version: 8
    java_set_javahome: true
    spark_version: 2.1.1
    spark_hadoop_version: 2.7
 ```

Check compitability of java, spark and spark_hadoop version.
It downloads the binaries from http://d3kbcqa49mib13.cloudfront.net/ (check `/ansible/roles/apache-spark/tasks/preparation.yml`)
ensure that source contains the required spark version.

 
