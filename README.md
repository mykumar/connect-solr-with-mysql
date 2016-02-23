
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
mysql> create table TEST_TABLE(
    -> first_name varchar(30),
    -> last_name varchar(30),
    -> age varchar(6),
    -> sex varchar(1),
    -> income varchar(10)
    -> );
mysql> insert into TEST_TABLE(first_name, last_name, age, sex, income)
    -> values
    -> ('smileof', 'ninja', '25', 'M', '12345');
mysql> exit;
```

Start and Configure Solr
-------------------------------------

```
cd <Solr Extracted Folder Name> ; 
bin/solr start     # By default solr will start on 8983 port, 
```
Remember solr can start on port 8983, but to access the Solr Admin UI http://localhost:8983/solr




