# CarSharing Webservice

The intention of this project is to develop a carsharing web service based on JEE.

The project is written to run under glassfish 4.1.1. It should be able to run it
using other JEE implementations but it may be nessasary to change some configs
or provide others.

## Project setup

Please follow the [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html).
You can get a config xml for use with eclipse or the netbeans eclipse formatter:
[eclipse-java-google-style.xml](https://raw.githubusercontent.com/google/styleguide/gh-pages/eclipse-java-google-style.xml)

The project is published under AGPLv3. You can find netbeans compatible
[licenseheader file](licenseheader.txt) to ensure the stuff gets added to
new files automaticaly.

### create and add database

Create new javadb database in netbeans and add the following tables.

```sql
create table users ( username varchar(255) not null primary key, password varchar(255) not null );
create table roles ( rolename varchar(255) not null primary key, description varchar(255) );
create table users_roles ( username varchar(255) not null, rolename varchar(255) not null );
```

JDBC connection Pool adding is broken in glassfish 4.1.1 so we have to modify
the domain.xml manualy.
Add the following to `${glassfish_home}/glassfish/domains/domain1/config/domain.xml`
into the `<resources>`-tag.

```xml
<jdbc-connection-pool datasource-classname="org.apache.derby.jdbc.ClientDataSource" name="CarSharing" res-type="javax.sql.DataSource">
    <property name="URL" value="jdbc:derby://localhost:1527/carsharing"></property>
    <property name="PortNumber" value="1527"></property>
    <property name="Password" value="admin"></property>
    <property name="DatabaseName" value="carsharing"></property>
    <property name="serverName" value="localhost"></property>
    <property name="User" value="carsharing_admin"></property>
</jdbc-connection-pool>
<jdbc-resource pool-name="CarSharing" jndi-name="jdbc/carsharing"></jdbc-resource>
```

### setup JAAS

The application should be jaas secured. JAAS is a Java Technology that makes
authentication processes simple and maintainable.
We will setup a jaas Security Provider in glassfish. For this setup we need the
database and tables created earlier.
