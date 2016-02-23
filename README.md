
Prerequisites
-------------------------------------
 * Install Apache Solr
 
Download Solr from apache [SOLR5.0.0](http://archive.apache.org/dist/lucene/solr/5.0.0/solr-5.0.0.zip)
Extracts solr5.0.0.zip folder into Downloads directory 

```
unzip solr-5.0.0.zip
```

* Install Mysql-connector

1) Place [mysql-connector-jar](http://cdn.mysql.com/archives/mysql-connector-java-5.1/mysql-connector-java-5.1.32.tar.gz) file into "<solr extracted folder>/solr-5.0.0/contrib/dataimporthandler/lib". 

2) Create subdirectory "lib" if it is not present.

* Install Mysql

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install mysql-server
```

* Create Test Database

Now, First we will create database named test and inside this DB a table EMPLOYEE and in this table we will put first_name, last_name, age, sex, income all are in varchar for now.

```
su - #swith to root user
mysqladmin -u root -p create test #provide your root passwd when it requires.
mysql -u root -p test #provide root passwd if required.
mysql> create table EMPLOYEE(
    -> first_name varchar(30),
    -> last_name varchar(30),
    -> age varchar(6),
    -> sex varchar(1),
    -> income varchar(10)
    -> );
mysql> insert into EMPLOYEE(first_name, last_name, age, sex, income)
    -> values
    -> ('smileof', 'ninja', '25', 'M', '12345');
mysql> exit;
```

Start and Configure Solr
-------------------------------------
* Start the Solr 

```
cd <Solr Extracted Folder Name> ; 
bin/solr start     # By default solr will start on 8983 port, 
```
Remember solr can start on port 8983, but to access the Solr Admin UI http://localhost:8983/solr

* Create Core

```
bin/solr create_core -c employees -d basic_configs
```

* solrconfig.xml file

Open the file 

```
vi <Solr Extracted Folder Name>/server/solr/employees/conf/solrconfig.xml
```

In the <config> tag, add the following lines

``` 
<lib dir="<Solr Extracted Folder Name>/contrib/dataimporthandler/lib/" regex=".*\.jar" />
<lib dir="<Solr Extracted Folder Name>/dist/" regex="solr-dataimporthandler-\d.*\.jar" /> 
    <requestHandler name="/dataimport" class="org.apache.solr.handler.dataimport.DataImportHandler">
     <lst name="defaults"> 
       <str name="config">db-data-config.xml</str> 
      </lst> 
    </requestHandler>
```
* schema.xml file (Here we will tell Solr to index the mysql table )

Open schema.xml in the same directory i.e <Solr Extracted Folder Name>/server/solr/employeed/schema.xml 

```
vi <Solr Extracted Folder Name>/server/solr/employeed/schema.xml
```
Add following lines which gudies solr what to index from table of mysql.

```
<dynamicField name="*_name" type="text_general" multiValued="false" indexed="true" stored="true" />
   <dynamicField name="*age" type="int" multiValued="false" indexed="true" stored="true" />
   <dynamicField name="*sex" type="text_general" multiValued="false" indexed="true" stored="true" />
   <dynamicField name="*income" type="int" multiValued="false" indexed="true" stored="true" />
```
* create db-data-config.xml file 

```
vi db-data-config.xml
```
Add the following mysql DB configuration information. This file also guides the Solr what to show on the search:


```
<dataConfig>
<dataSource type="JdbcDataSource"
driver="com.mysql.jdbc.Driver"
url="jdbc:mysql://localhost:3306/test"   # Connecting Database test
user="root"
password="secret" />
<document>
<entity name="id" query="select first_name as 'id' , first_name, last_name, age, sex, income from EMPLOYEE ;" />
</document>
</dataConfig>
```

* Restart the Solr
```
cd <Solr Extracted Folder Name>/
bin/solr restart
```

* Data Import

To index all the table data :

```
http://localhost:8983/solr/employees/dataimport?command=full-import
```

Now if you want only the changes which happended in DB to index, use the URL as follows: 

```
http://localhost:8983/solr/employees/dataimport?command=delta-import
```

* Ready to Search MYSQL Table using the Solr

Now we have indexed all the EMPLOYEE table data. Open the browser and go to  

```
http://localhost:8983/solr/employees/select?q=*&wt=json&qf=first_name%20last_name&defType=edismax&indent=true
```



