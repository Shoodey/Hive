# Derby Setup

Apache Derby (previously distributed as IBM Cloudscape) is a relational database management system (RDBMS) developed by the Apache Software Foundation that can be embedded in Java programs and used for online transaction processing. 

It has some drawbacks when it comes to using with Hive, for instance, it only allow access to a single user at a time, but for our project this didn't seem to be an issue since it offered the advantage of having a really small disk space footprint of less than 4MB.

## Derby Installation

First of all, we need to download Derby from and unzip it.

```
wget http://archive.apache.org/dist/db/derby/db-derby-10.13.1.1/db-derby-10.13.1.1-bin.tar.gz

sudo tar xvzf db-derby-10.13.1.1-bin.tar.gz -C /usr/local
```

As we did previously, we need to configure our environment variables by editing the **~/.bashrc** file and sourcing it.

```
export DERBY_HOME=/usr/local/db-derby-10.13.1.1-bin
export PATH=$PATH:$DERBY_HOME/bin
export CLASSPATH=$CLASSPATH:$DERBY_HOME/lib/derby.jar:$DERBY_HOME/lib/derbytools.jar
```

We then create the datastore folder within the DERBY_HOME directory.

```
sudo mkdir $DERBY_HOME/data
```
