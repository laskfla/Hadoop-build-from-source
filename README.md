# Hadoop-build-from-source on Mac

## build hadoop 3.0 but failed , see below for the issues met in build 
1. install packges first :
brew install gcc autoconf automake libtool cmake snappy gzip bzip2  zlib openssl

2. build& install protobuf from source , not sure why brew instal protobuf250 not working well when build hadoop . 
It always throw some errors related to protobuf .
checkou this for install protobuf manually . 
https://github.com/shawfdong/hyades/wiki/Building-Hadoop-from-source

3. run below build command instead, otherwise cmake may throw error and not able to find openssl.  
The "export" openssl varaibles does not work in this hadoop3.0.  
cmake actually does not respect those variables , instead , need to pass as -D parameters, check this for 
detail .  
https://stackoverflow.com/questions/16248775/cmake-not-able-to-find-openssl-library . 
https://gist.github.com/rajkrrsingh/77f3c4d20c109c96a3e9b36c224c6b3d . 

 Below -D option is to pass the value to the maven variables in pom.xml like openssl.lib , etc  .
 
   mvn clean package -Dmaven.javadoc.skip=true  \
 -Drequire.openssl=true \
 -Dopenssl.prefix=/usr/local/opt/openssl \
 -Dopenssl.lib=/usr/local/opt/openssl/lib \
 -Dopenssl.include=/usr/local/opt/openssl/include \
 -Pdist,native -DskipTests -Dtar 

4.  But still failed to build and this is reported in JIRA .  
    https://issues.apache.org/jira/browse/YARN-8622 . 
    will try that later.

## build hadoop 2.7.1 . 
This time it is easy and run :  
 mvn package -Dmaven.javadoc.skip=true -Pdist,native -DskipTests -Dtar . 
 To start a single-node cluster , check this for detail ,basically is to setup the config files and then start.  
 https://github.com/laskfla/Hadoop-On-Window
 
 The difference between a "single-node cluster" and "single-process hadoop" :  
 https://stackoverflow.com/questions/23435333/what-is-the-difference-between-single-node-pseudo-distributed-mode-in-hadoop
 
``` By default, Hadoop is configured to run in a non-distributed or standalone mode, as a single Java process. There are no daemons running and everything runs in a single JVM instance. HDFS is not used.

You don't have to do anything as far as configuration is concerned, except the JAVA_HOME. Just download the tarball, unzip it, and you are good to go.

Pseudo-distributed mode
The Hadoop daemons run on a local machine, thus simulating a cluster on a small scale. Different Hadoop daemons run in different JVM instances, but on a single machine. HDFS is used instead of local FS.

As far as pseudo-distributed setup is concerned, you need to set at least following 2 properties along with JAVA_HOME:

fs.default.name in core-site.xml.

mapred.job.tracker in mapred-site.xml.

You could have multiple datanodes and tasktrackers, but that doesn't make much sense on a single machine.

HTH 
```
 




### Reference
https://medium.com/@faizanahemad/hadoop-native-libraries-installation-on-mac-osx-d8338a6923db  
https://github.com/shawfdong/hyades/wiki/Building-Hadoop-from-source . 
https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html#Fully-Distributed_Operation




