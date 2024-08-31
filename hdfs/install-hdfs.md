# This document demonstrates how to install and configure hdfs


## Environment
```
ubuntu 22.04
```

## Servers

| no | hostname     | ip            | role       |
|----|--------------|---------------|------------|
| 1  | hdfs-master1 | 192.168.23.11 | namenode   |
| 2  | hdfs1        | 192.168.23.21 | datanode 1 |
| 3  | hdfs2        | 192.168.23.22 | datanode 2 |

## Map ip addresses to hostnames

Run the command: ``` sudo nano /etc/hosts ``` and add mapping configuration:

```
192.168.23.11   hdfs-master1
192.168.23.21   hdfs1 
192.168.23.22   hdfs2  
```

## Installation

### install hdfs
| no. | thao tác                                                                                               | user | server      | note                      |
|-----|--------------------------------------------------------------------------------------------------------|------|-------------|---------------------------|
| 1   | # wget https://dlcdn.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz                         | root | all servers |                           |   
| 2   | # tar -xzvf hadoop-3.3.6.tar.gz </br># sudo mv hadoop-3.3.6 /opt/hadoop && chown hdfs:hdfs /opt/hadoop | root | all servers | change owner to user hdfs |   
| 3   | # mkdir /data && chown hdfs:hdfs /data                                                                 | root | all servers | make folder to save data  |   
| 4   | # sudo apt-get install -y openjdk-8-jdk                                                                | root | all servers | install java 8            |   

### configure hdfs
| no. |                                           | user | server      | note                                                |
|-----|-------------------------------------------|------|-------------|-----------------------------------------------------|
| 1   | nano /opt/hadoop/etc/hadoop/core-site.xml | hdfs | all servers | add the configuration in /config/hdfs/core-site.xml |
| 2   | nano /opt/hadoop/etc/hadoop/hdfs-site.xml | hdfs | all servers | add the configuration in /config/hdfs/hdfs-site.xml |
| 3   | nano /opt/hadoop/etc/hadoop/hadoop-env.sh | hdfs | all servers | add the configuration in /config/hdfs/hadoop-env.sh |

## Run hdfs

| no. | thao tác                                       | user | server       | note                                                   |
|-----|------------------------------------------------|------|--------------|--------------------------------------------------------|
| 1   | # /opt/hadoop/bin/ hdfs namenode -format       | hdfs | hdfs-master1 | the first time you bring up HDFS, it must be formatted |
| 2   | # /opt/hadoop/bin/hdfs --daemon start namenode | hdfs | hdfs-master1 | run namenode                                           |
| 3   | # /opt/hadoop/bin/hdfs --daemon start datanode | hdfs | hdfs1, hdfs2 | run datanode                                           |

you can go check the UI in browser:

namenode: http://<ip>:9870

datanode: http://<ip>:9864

And it's done, we have configured and run a basic hdfs cluster

Next we will integrate this cluster with kerberos, and set up high availability for this cluster