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

### Activate Solr service

After launching Cloudera Manager and its instances as shown at whirr_cm above, go to 'Host' > 'Parcels' tab of the Cloudera Manager's Web UI.
Then, you can download the latest available CDH, Solr, Impala.

Download "SOLR 0.9.1-1.cdh4.3.0.p0.275" > Distribute > Activate > Restart the current Cluster

#### not sure yet to download CDH 4.3.0-1
Download "CDH 4.3.0-1.cdh4.3.0.p0.22" > Distribute > Activate > Restart the current Cluster

Note: Restarting the cluster will take several minutes

### Add Solr service
Actions (of Cluster 1 - CDH4) > Add a Service > Solr > Zookeeper as a dependency

### Update Solr conf at a zookeeper node
You can see a solr configuration file as '/etc/default/solr' and update it with as follows:
```bash
sudo vi /etc/default/solr 
```


## References
#### [1]. http://github.com/hipic/whirr_cm
#### [2]. http://blog.cloudera.com/blog/2013/03/how-to-create-a-cdh-cluster-on-amazon-ec2-via-cloudera-manager/
#### [3]. Managing Clusters with Cloudera Manager, http://www.cloudera.com/content/cloudera-content/cloudera-docs/CM4Ent/latest/PDF/Managing-Clusters-with-Cloudera-Manager.pdf
#### [4]. Cloudera Search Installation Guide, http://www.cloudera.com/content/cloudera-content/cloudera-docs/Search/latest/PDF/Cloudera-Search-Installation-Guide.pdf


