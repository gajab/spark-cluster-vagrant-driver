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

 Ensure compatiblity between java, spark and spark_hadoop version.
 It downloads the binaries from http://d3kbcqa49mib13.cloudfront.net/ (check `/ansible/roles/apache-spark/tasks/preparation.yml`) ensure that source contains the required spark version.

* Create your spark project in eclipse, following millions of link on the web. Ensure you have compitable client java libraries. Exception `local class incompatible: stream classdesc serialVersionUID = 7674242335164700840, local class serialVersionUID = -7685200927816255400` may be due to incompatiblity between your spark cluster version and spark driver/client libraries. Below version of depenencies worked for me for spark 2.1.1 cluster

```
        <spark.version>2.1.1</spark.version>
           
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-core_2.11</artifactId>
            <version>${spark.version}</version>
            
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-sql_2.11</artifactId>
            <version>${spark.version}</version>
            
           
            <groupId>com.databricks</groupId>
            <artifactId>spark-redshift_2.11</artifactId>
            <version>2.0.0</version>
     

```

* On exception like `WARN Utils: Service 'sparkDriver' could not bind on port 0. Attempting port 1.` or `Can't assign requested address: Service 'sparkDriver' failed after 16 retries (starting from 0)! Consider explicitly setting the appropriate port for the service 'sparkDriver' (for example spark.ui.port for SparkUI) to an available port or increasing spark.port.maxRetries.` Ensure to add IP address of the machine (in my case mac laptop) where Driver program is running. The workers (running as virtual box with IP 172.16.1.20 and 172.16.1.30) need to contact driver program to send results back, hence require IP address of driver to connect back.
```
  this.sBuilder = SparkSession.builder().appName(this.SPARK_APP_NAME)
                    
                   .config("spark.driver.host", "X.Y.44.79");
```
The IP address of your machine (laptop) may change at work / home / starbucks so ensure to use correct IP address.

* On `java.lang.ClassNotFoundException:` ensure to add your spark project jar in spark context. This will ensure that all workers receive all the classes require to execute spark tasks.
```
 sc.sparkContext().addJar("/path/to/project.jar");
 
```
