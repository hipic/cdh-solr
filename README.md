cdh-solr: Solr on CDH4
========

## This is to illustrate how to lauch Solr on CDH using an AWS instance


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

### Activate Solr service

After launching Cloudera Manager and its instances as shown at whirr_cm above, go to 'Host' > 'Parcels' tab of the Cloudera Manager's Web UI.
Then, you can download the latest available CDH, Solr, Impala.

Download "CDH 4.3.0-1.cdh4.3.0.p0.22" > Distribute > Activate > Restart the current Cluster
Download "SOLR 0.9.1-1.cdh4.3.0.p0.275" > Distribute > Activate > Restart the current Cluster

Note: Restarting the cluster will take several minutes




## References
#### [1]. http://github.com/hipic/whirr_cm
#### [2].
