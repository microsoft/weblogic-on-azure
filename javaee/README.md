# Basic Java EE CRUD Application
This is a basic Java EE 7 application used throughout the WebLogic on Azure demos. It is a simple CRUD application. It uses Maven and Java EE 7 (JAX-RS, EJB, CDI, JPA, JSF, Bean Validation).

We use Eclipse but you can use any Maven and WebLogic capable IDE such as NetBeans. We use Postgres but you can use any relational database such as MySQL or SQL Server.

## Setup

* Install the latest version of Oracle JDK 8 (we used [8u241](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)).
* Install the Eclipse IDE for Enterprise Java Developers from [here](https://www.eclipse.org/downloads/packages/).
* Install WebLogic 12.2.1.3 (note - not the latest version) using the Quick Installer by downloading it from [here](https://www.oracle.com/middleware/technologies/weblogic-server-downloads.html).
   * **preview** use `60a78f02.microsoft.com@amer.teams.ms` and `hEc!ucesW3Th` for the credentials
* Download this repository somewhere in your file system (easiest way might be to download as a zip and extract).
* You will need an Azure subscription. If you don't have one, you can get one for free for one year [here](https://azure.microsoft.com/en-us/free).

## Start Managed PostgreSQL on Azure
We will be using the fully managed PostgreSQL offering in Azure for this demo. Below is how we set it up. 

* Go to the [Azure portal](http://portal.azure.com).
* The steps in this section use `<your suffix>`.  The suffix could be your first name such as "reza".  It should be short and reasonably unique.
* Select Create a resource -> Databases -> Azure Database for PostgreSQL.  In "How do you plan to use the service?" select "single server".
* In "Resource group" select "Create new" and enter weblogic-cafe-db-group-`<your suffix>`
* Specify the Server name to be weblogic-cafe-db-`<your suffix>`.
* Specify the location to be a location close to you.
* Leave the Version at its default.
* In Compute + Storage click "Configure Server" then choose Basic.
   * Set vCore to the minimum.
   * Set Storage to the minimum.
   * Click 'OK'
* Specify the Admin username to be postgres. 
* Specify the Password to be Secret123! (do not forget the exclamation point). 
* Hit 'Review+create' then 'Create'. It will take a moment for the database to deploy and be ready for use.
* In the portal, go to 'All resources'. Enter `<your suffix>` into the filter box and press enter.
* Find and click on weblogic-cafe-db-`<your suffix>`. 
* Under Settings, open the connection security panel.
   * Toggle "Allow access to Azure services" to "on"
   * Toggle "Enforce SSL connection" to "DISABLED". 
   * Hit Add client IP. This allows connection to the database from the IP you are currently using to access Azure.  As a precaution, verify the IP entered is actually your IP.  You can do this by googling "what is my ip".  Hit Save.

## Setting Up WebLogic
The next step is to get the application up and running. Follow the steps below to do so.
* Start Eclipse.
* Go to the 'Servers' panel, secondary click. Select New -> Server
* Select Oracle -> Oracle WebLogic Server Tools. Click next. Accept the license agreement, click 'Finish'.  Eclipse may ask to be restarted.  If so, comply with the request.
* After the Eclipse WebLogic adapters are done installing, go to the 'Servers' panel again, secondary click. 
   * Select New -> Server -> Oracle -> Oracle WebLogic Server. 
   * Choose the defaults and hit 'Next'. 
   * Enter where you have WebLogic installed.  Even though the dialog says "WebLogic home" you may have to enter the full path to the `wlserver` directory within the `Oracle_Home` directory.
   * Enter where the Oracle JDK is installed.  Click next. 
   * For the domain directory, hit Create -> Create Domain. 
   * For the domain name, specify 'domain1'. Hit 'Finish' to add the new server to Eclipse.  If Eclipse asks to create a master password hint, do so.  Consider using `<your suffix>` for the questions and answers.
   * Copy the Postgres driver to where you have WebLogic installed under server/lib. This file is located in the javaee/server directory where you downloaded the application code.
   * Depending on your platform, find the commExtEnv.sh or commExtEnv.cmd file in the oracle_common/common/bin directory where you have WebLogic installed. You will need to add the Postgres driver to the WebLogic classpath. The end result will look something like the following.  There are several assignments to `WEBLOGIC_CLASSPATH`.  You will likely need to edit the first one.

#### commExtEnv.sh
```
WEBLOGIC_CLASSPATH="${JAVA_HOME}/lib/tools.jar${CLASSPATHSEP}${PROFILE_CLASSPATH}${CLASSPATHSEP}${ANT_CONTRIB}/ant-contrib-1.0b3.jar${CLASSPATHSEP}${CAM_NODEMANAGER_JAR_PATH}${CLASSPATHSEP}${WL_HOME}/server/lib/postgresql-42.2.4.jar"
```

#### commExtEnv.cmd
```
set WEBLOGIC_CLASSPATH=%JAVA_HOME%\lib\tools.jar;%PROFILE_CLASSPATH%;%ANT_CONTRIB%\ant-contrib-1.0b3.jar;%CAM_NODEMANAGER_JAR_PATH%;%WL_HOME%\server\lib\postgresql-42.2.4.jar
```

* Go to the 'Servers' panel, secondary click on the registered WebLogic instance and select Start.  If the server does not start, put aside this workshop and troubleshoot why the server did not start.  Once the server is successfully started from Eclipse, you may continue.

## Connect WebLogic to the PostgreSQL Server

* Once WebLogic starts up, go to http://localhost:7001/console/ and log onto the console. Unless you changed them, the userid is `weblogic` and the password is `welcome1`.  
   * Click on Services -> Data Sources. Select New -> Generic Data Source. 
   * Enter the name as 'WebLogicCafeDB', JNDI name as 'jdbc/WebLogicCafeDB' and select the database type to be PostgreSQL. Click next. 
   * Accept the defaults and click next.  Do not click Finish, even though you could do so.
   * On the next screen select 'Logging Last Resource' and click next. 
   * Enter the database name to be 'postgres'. 
   * Enter the host name as 'weblogic-cafe-db-`<your suffix>`.postgres.database.azure.com'.
   * Leave the port unchanged.
   * Enter the user name as 'postgres@weblogic-cafe-db-`<your suffix>`'. 
   * Enter the password as 'Secret123!'. Click next. 
   * On the next screen, accept the defaults and click next.
   * On the "Select Targets" screen, select AdminServer and click Finish.
   * Test the connection.   
      * In the "Data Sources" pane, click "WebLogicCafeDB".
      * Click Monitoring -> Testing
      * Select AdminServer and click "Test Data Source".  You must see "Test of WebLogicCafeDB on server AdminServer was successful." at the top of this pane after clicking the button.  If you do not, put this workshop aside, troubleshoot and resolve the issue.  Once the connection successfully tests, you may continue.

## Open weblogic-cafe in the IDE
* Get the weblogic-cafe application into the IDE. In order to do that, go to File -> Import -> Maven -> Existing Maven Projects.  Click Next
* Then browse to where you have this repository code in your file system and select javaee/weblogic-cafe and click "Open".  
* Accept the rest of the defaults and click "finish".
* Once the application loads, you should do a full Maven build by going to the application and secondary clicking -> Run As -> Maven install.
   * You must see `BUILD SUCCESS` in the Eclipse console in order to proceed.  If you do not, troubleshoot the build problem and resolve it.  Once the application has successfully built, you may continue.

## Deploying the Application

* It is now time to run the application. Secondary click the application -> Run As -> Run on Server.
   * Select the local WebLogic instance.
   * Make sure to select "Always use this server when running this project" and click "Finish". Just accept the defaults and wait for the application to finish deploying.
* Once the application runs, Eclise will open it up in a browser. The application is available at [http://localhost:7001/weblogic-cafe](http://localhost:8080/weblogic-cafe).

## Exploring the Application

The application is composed of:

- **A RESTFul service*:** protocol://hostname:port/weblogic-cafe/rest/coffees

	- **_GET by Id_**: protocol://hostname:port/weblogic-cafe/rest/coffees/{id} 
	- **_GET all_**: protocol://hostname:port/weblogic-cafe/rest/coffees
	- **_POST_** to add a new element at: protocol://hostname:port/weblogic-cafe/rest/coffees
	- **_DELETE_** to delete an element at: protocol://hostname:port/weblogic-cafe/rest/coffees/{id}
	
- **A JSF Client:** protocol://hostname:port/weblogic-cafe/index.xhtml

Feel free to take a minute to explore the application.
    
Some sample interactions:

```
curl --verbose http://localhost:7001/weblogic-cafe/rest/coffees
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 7001 (#0)
> GET /weblogic-cafe/rest/coffees HTTP/1.1
> Host: localhost:7001
> User-Agent: curl/7.64.1
> Accept: */*
>
< HTTP/1.1 200 OK
< Date: Fri, 10 Jan 2020 22:12:26 GMT
< Content-Length: 201
< Content-Type: application/xml
< X-ORACLE-DMS-ECID: 89fd1d3f-df20-4336-9f81-f1edc0ccab49-00000027
< X-ORACLE-DMS-RID: 0
<
* Connection #0 to host localhost left intact
<?xml version="1.0" encoding="UTF-8" standalone="yes"?><coffees><coffee><id>1</id><name>Strong</name><price>5.0</price></coffee><coffee><id>2</id><name>Weak</name><price>0.25</price></coffee></coffees>* Closing connection 0

curl --verbose http://localhost:7001/weblogic-cafe/rest/coffees/1
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 7001 (#0)
> GET /weblogic-cafe/rest/coffees/1 HTTP/1.1
> Host: localhost:7001
> User-Agent: curl/7.64.1
> Accept: */*
>
< HTTP/1.1 200 OK
< Date: Fri, 10 Jan 2020 22:13:51 GMT
< Content-Length: 102
< Content-Type: application/xml
< X-ORACLE-DMS-ECID: 89fd1d3f-df20-4336-9f81-f1edc0ccab49-00000028
< X-ORACLE-DMS-RID: 0
<
* Connection #0 to host localhost left intact
<?xml version="1.0" encoding="UTF-8"?><coffee><id>1</id><name>Strong</name><price>5.0</price></coffee>* Closing connection 0

cat medium.xml
<?xml version="1.0" encoding="UTF-8"?>
<coffee><id>200</id><name>Medium</name><price>5.0</price></coffee>

curl --verbose -X POST -d @medium.xml http://localhost:7001/weblogic-cafe/rest/coffees --header "Content-Type: application/xml"
Note: Unnecessary use of -X or --request, POST is already inferred.
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 7001 (#0)
> POST /weblogic-cafe/rest/coffees HTTP/1.1
> Host: localhost:7001
> User-Agent: curl/7.64.1
> Accept: */*
> Content-Type: application/xml
> Content-Length: 104
>
* upload completely sent off: 104 out of 104 bytes
< HTTP/1.1 201 Created
< Date: Fri, 10 Jan 2020 22:17:55 GMT
< Location: http://localhost:7001/weblogic-cafe/rest/coffees/200
< Content-Length: 0
< X-ORACLE-DMS-ECID: 89fd1d3f-df20-4336-9f81-f1edc0ccab49-00000029
< X-ORACLE-DMS-RID: 0
<
* Connection #0 to host localhost left intact
* Closing connection 0
```

## Cleaning Up

Once you are done exploring all aspects of the demo, you should delete the weblogic-cafe-group-`<your suffix>` resource group. You can do this by going to the portal, going to resource groups, finding and clicking on weblogic-cafe-group-`<your suffix>` and hitting delete. This is especially important if you are not using a free subscription! If you do keep these resources around (for example to begin your own prototype), you should in the least use your own passwords and make the corresponding changes in the demo code.
