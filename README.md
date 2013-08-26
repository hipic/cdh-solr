cdh-solr: Solr on CDH4 using Cloudera Manager
========

## This is to illustrate how to lauch Solr on CDH using Cloudera Manager and an AWS instance


### Install Cloudera Manager from an AWS instance
You can launch an AWS instance at CentOS 6.3 and ssh to the instance. Then, download the Cloudera Manager 4.5 installer and execute it on the remote instance:
```bash
$ wget http://archive.cloudera.com/cm4/installer/latest/cloudera-manager-installer.bin
$ chmod +x cloudera-manager-installer.bin
$ sudo ./cloudera-manager-installer.bin
```
#### (1) Follow the command based installation accepting licenses.
#### (2) Open and go to Cloudera Manager's Web UI, which might be local URLbut you may use the global URL, to launch the services.
#### (3) All Services > Add Cluster > Continue on Coudera Manager Express Wizard > CentOS 6.3; m1.xlarge; ...
#### Note: >= m1.large recommended

### Stop unneccessay services
Stop HBase, Hive, Oozie, Sqoop

#### All systems are located at 'usr/lib/'
For example, /usr/lib/solr, /usr/lib/zookeeper, ...

Sometimes, it is not at '/usr/lib', then find the location as follows:
```bash
$ ls -la /usr/bin/sqoop
lrwxrwxrwx 1 root root 23 Aug 19 16:03 /usr/bin/sqoop -> /etc/alternatives/sqoop
$ ls -al /etc/alternatives/sqoop 
lrwxrwxrwx 1 root root 58 Aug 19 16:03 /etc/alternatives/sqoop -> /opt/cloudera/parcels/CDH-4.3.0-1.cdh4.3.0.p0.22/bin/sqoop
$ ls -al /opt/cloudera/parcels/CDH-4.3.0-1.cdh4.3.0.p0.22/lib
```

#### Optional: Activate Solr service

After launching Cloudera Manager and its instances as shown at whirr_cm above, go to 'Host' > 'Parcels' tab of the Cloudera Manager's Web UI.
Then, you can download the latest available CDH, Solr, Impala.

Download "SOLR 0.9.1-1.cdh4.3.0.p0.275" > Distribute > Activate > Restart the current Cluster

#### Optional: download CDH 4.3.0-1
Download "CDH 4.3.0-1.cdh4.3.0.p0.22" > Distribute > Activate > Restart the current Cluster

Note: Restarting the cluster will take several minutes

### Add Solr service
Actions (of Cluster 1 - CDH4) > Add a Service > Solr > Zookeeper as a dependency

### Open a Web UI of Hue
Default login/pwd is admin
You can see Solr. Select it

### Update Solr conf at a zookeeper node
At a node that has a Solr (zookeeper) installed, You can see a solr configuration file as '/etc/default/solr' and update it with as follows:
```bash
sudo vi /etc/default/solr 
```

or $SOLR_HOME needs to be set, of which the location is found as in the begining:
```bash
export $SOLR_HOME=/opt/cloudera/parcels/SOLR-0.9.3-1.cdh4.3.0.p0.366
...
sudo vi $SOLR_HOME/etc/default/solr 
```

Note: it may not recognize 'localhost' so that use '127.0.0.1' alternatively

### Create the /solr directory in HDFS:
```bash
$ sudo -u hdfs hadoop fs -mkdir /solr
$ sudo -u hdfs hadoop fs -chown solr /solr
```

### Create a collection

You change to root account and need to add solr to zookeeper. From now on, I run shell commands as root user. 
```bash
$ sudo su
$ solrctl init
```
or
```bash
$ solrctl init --force
```

If you see the following error/warning msgs from the above as well:
```bash
$ solrctl instancedir --list
Warning: Non-SolrCloud mode has been completely deprecated
Please configure SolrCloud via SOLR_ZK_ENSEMBLE setting in 
/opt/cloudera/parcels/SOLR-0.9.3-1.cdh4.3.0.p0.366/bin/../etc/default/solr
If you running remotely, please use --zk zk_ensemble.
```

If there are more than one zoopkeepers, update the zookeeper option, for example, solr runs on host2, host3, host4:
```bash
SOLR_ZK_ENSEMBLE=host2:2181,host3:2181,host4:2181/solr
```
Then, at Cloudera Manager's Web UI, restart solr service.

Before you create 'solr_confis' directory, you need to locate 'coreconfig-template' directory of $SOLR_HOME, 
that is '/opt/cloudera/parcels/SOLR-0.9.3-1.cdh4.3.0.p0.366/lib/solr
':
```bash
[root@prism2 solr]# cd /opt/cloudera/parcels/SOLR-0.9.3-1.cdh4.3.0.p0.366/
[root@prism2 SOLR-0.9.3-1.cdh4.3.0.p0.366]# ln -s ./lib/solr/coreconfig-template coreconfig-template
```
Run the following commands to create a collection at a zookeeper node
```bash
$ solrctl instancedir --generate $HOME/solr_configs
$ solrctl instancedir --create collection $HOME/solr_configs
$ solrctl collection --create collection -s 1
```
While running 'solrctl collection ...', you may go to /var/log/solr and check out if the solr runs well without any error:
```bash
$ tail -f solr-cmf-solr1-SOLR_SERVER-ip-10-138-xx-xx.ec2.internal.log.out 
```
Upload an example data to solr
```bash
$ cd /usr/share/doc/solr-doc-4.3.0+52/example/exampledocs/
$ java -Durl=http://127.0.0.1:8983/solr/collection/update -jar post.jar *.xml
SimplePostTool version 1.5
Posting files to base url http://127.0.0.1:8983/solr/collection/update using content-type application/xml..
POSTing file gb18030-example.xml
POSTing file hd.xml
POSTing file ipod_other.xml
...
POSTing file utf8-example.xml
POSTing file vidcard.xml
14 files indexed.
COMMITting Solr index changes to http://127.0.0.1:8983/solr/collection/update..
Time spent: 0:00:00.818
```
#### Query using Hue Web UI
Open Hue Web UI at Cloudera Manager's Hue service and select solr tab.
##### 1. Make sure to import collections - core may not be needed.
##### 2. select "Search page" link at the top right of the solr web UI page.
##### 3. As default, the page shows 1-15 of 32 results.
##### 4. Type in 'photo' at a search box ans will show 1 -2 of 2 results.

#### Customize the view of Solr Web UI
Select 'Customize this collection' that will present Visual Editor for view.

### Trouble Shooting
if you cannot open a Web UI of 'http://public_dns:8983/solr', you need to open AWS console management web and look at the name of security group of the solr instance.
That is, NETWORK & SECURITYGROUP > Security Groups > [Your Solr Security Group] > Inboud > Port Range > [Add 8983] > Add Rule > Apply Change
Then, test the port
```bash
$ wget http://public_dns:8983/solr
```
And, index.html of the solr server will be downloaded.
```bash
$ more index.html 
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>

<!--
Licensed to the Apache Software Foundation (ASF) under one or more
contributor license agreements.  See the NOTICE file distributed with
this work for additional informa...
```


## References
#### [1]. http://github.com/hipic/whirr_cm
#### [2]. http://blog.cloudera.com/blog/2013/03/how-to-create-a-cdh-cluster-on-amazon-ec2-via-cloudera-manager/
#### [3]. Managing Clusters with Cloudera Manager, http://www.cloudera.com/content/cloudera-content/cloudera-docs/CM4Ent/latest/PDF/Managing-Clusters-with-Cloudera-Manager.pdf
#### [4]. Cloudera Search Installation Guide, http://www.cloudera.com/content/cloudera-content/cloudera-docs/Search/latest/Cloudera-Search-Installation-Guide
#### [5]. Cloudera Search User Guide, http://www.cloudera.com/content/cloudera-content/cloudera-docs/Search/latest/Cloudera-Search-User-Guide
#### [6]. Cloudera Search Installation Guide, http://www.cloudera.com/content/cloudera-content/cloudera-docs/Search/latest/PDF/Cloudera-Search-Installation-Guide.pdf


