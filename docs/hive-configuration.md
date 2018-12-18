# Hive Configuration

Now that we have installed our pre-requisites which are Hive and Derby we need to configure the different Hive components.

## Hive Metastore

Configuring Metastore means specifying to Hive where the database is stored. We want to do this by editing the hive-site.xml file, which is in the $HIVE_HOME/conf directory.

A template is offered for us to implement a basic configuration that is done by running the following:

```
cd $HIVE_HOME/conf

sudo cp hive-default.xml.template hive-site.xml
```

We then modify the file's content to include the following configuration:

```
<property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:derby:;databaseName=metastore_db;create=true</value>
    <description>
      JDBC connect string for a JDBC metastore.
      To use SSL to encrypt/authenticate the connection, provide database-specific SSL flag in the connection URL.
      For example, jdbc:postgresql://myhost/db?ssl=true for postgres database.
    </description>
  </property>
```

Finally we need to create a **jpox.properties** file that will hold data store related configuration.

```
javax.jdo.PersistenceManagerFactoryClass =
org.jpox.PersistenceManagerFactoryImpl
org.jpox.autoCreateSchema = false
org.jpox.validateTables = false
org.jpox.validateColumns = false
org.jpox.validateConstraints = false
org.jpox.storeManagerType = rdbms
org.jpox.autoCreateSchema = true
org.jpox.autoStartMechanismMode = checked
org.jpox.transactionIsolation = read_committed
javax.jdo.option.DetachAllOnCommit = true
javax.jdo.option.NontransactionalRead = true
javax.jdo.option.ConnectionDriverName = org.apache.derby.jdbc.ClientDriver
javax.jdo.option.ConnectionURL = jdbc:derby://hadoop1:1527/metastore_db;create = true
javax.jdo.option.ConnectionUserName = APP
javax.jdo.option.ConnectionPassword = mine
```


## Schema Initialization

Our final step is to set Derby as the default database type by running the schematool command:

``` 
schematool -dbType derby -initSchema
```

Which when successfull outputs the following:

```
Starting metastore schema initialization to 3.1.1
Initialization script hive-schema-3.1.1.derby.sql
Initialization script completed
schemaTool completed
```
