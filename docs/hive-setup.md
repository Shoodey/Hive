# Hive Setup

This section will delineate the initial setup required to start working on the project.

Since our hadoop cluster has already been setup during the previous project which is comprised of a single namenode and five datanodes, we only need to install Hive to start working.

## Hive Installation

First of all, we need to download the latest version of Hive from [the official website](https://hive.apache.org/downloads.html). We chosen version 3.1.1 that was released in November 2018 which is compatible with our Hadoop version 3.1.1

```
wget https://www-eu.apache.org/dist/hive/hive-3.1.1/apache-hive-3.1.1-bin.tar.gz

sudo tar xvzf apache-hive-3.1.1-bin.tar.gz -C /usr/local/hive-folder
```

We then configure our environment variables by editing the **~/.bashrc** file and sourcing it.

```
export HIVE_HOME=/usr/local/hive-folder
export HIVE_CONF_DIR=/usr/local/hive-folder/conf
export PATH=$HIVE_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:/usr/local/hadoop/lib/*:.
export CLASSPATH=$CLASSPATH:/usr/local/hive-folder/lib/*:.
```

We then move to modifying hive's configuration files.

In **hive-env.sh** we add tje following:

```
export HADOOP_HOME=/usr/local/hadoop
```
