# Deploying a Java EE Application on Azure Using a Single WebLogic Virtual Machine Instance
This demo shows how you can deploy a Java EE application to Azure using a simple instance of WebLogic Server deployed from the Azure Marketplace.

## Setup

* Install the latest version of Oracle JDK 8 (we used [8u241](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)).
* Install the Eclipse IDE for Enterprise Java Developers from [here](https://www.eclipse.org/downloads/packages/).
* Install WebLogic 12.2.1.3 (note - not the latest version) using the Quick Installer by downloading it from [here](https://www.oracle.com/middleware/technologies/weblogic-server-downloads.html). You need this locally even if you are not running WebLogic locally because the Eclipse WebLogic deployment support needs it.
* Download this repository somewhere in your file system (easiest way might be to download as a zip and extract).
* You will need an Azure subscription. If you don't have one, you can get one for free for one year [here](https://azure.microsoft.com/en-us/free).

## Start Managed PostgreSQL on Azure

We will be using the fully managed PostgreSQL offering in Azure for this demo. Below is how we set it up. 

* Go to the [Azure portal](http://portal.azure.com).
* Select Create a resource -> Databases -> Azure Database for PostgreSQL.  In "How do you plan to use the service?" select "single server".
* The steps in this section use `<your suffix>`. The suffix could be your first name such as "reza".  It should be short and reasonably unique, and less than 10 charracters in length.
* Create and specify a new resource group named weblogic-cafe-db-group-`<your suffix>` . 
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
   * Toggle "Allow access to Azure services" to "Yes"
   * Toggle "Enforce SSL connection" to "DISABLED". 
   * Hit Save.

## Create the WebLogic Instance on Azure
The next step is to get a WebLogic instance up and running. Follow the steps below to do so.

* **preview** Go to the [preview link in the Azure portal](https://portal.azure.com/#create/microsoft_javaeeonazure_test.20200429-edburns-04-preview20200429-edburns-04).
* Click 'Create'. 
* **preview** Ensure "Oracle Enterprise Java" is selected in "Subscription".
* In the basics blade, for "Project details"
   * Create and specify a new resource group named weblogic-cafe-group-`<your suffix>` . 
   * Select Region '(US) East US'. 
* For "Credentails for Virtual Machines and WebLogic"
   * For the "Password for admin account of VMs", enter 'Secret123456'. 
   * Enter your OTN/Oracle.com username and password (you can create an account for free).
      * **preview** use `60a78f02.microsoft.com@amer.teams.ms` and `hEc!ucesW3Th` for the credentials.
   * For the "Password for WebLogic Administrator", enter 'Secret123456'.
* For "Optional Basic Configuration", ensure  `Yes` is selected to accept default for optional configuration.
* Click Next.
* In "Database" use these values
   * Toggle "Connect to DataBase" to `Yes`.
   * For "Choose database type", from the dropdown menu, select the option for PostgreSQL.
   * Specify JNDI Name to be 'jdbc/WebLogicCafeDB'. 
   * Specify DataSource Connection String to be 'jdbc:postgresql://weblogic-cafe-db-`<your suffix>`.postgres.database.azure.com:5432/postgres?&sslmode=require'
   * Specify the Database Username to be 'postgres@weblogic-cafe-db-`<your suffix>`'
   * Enter the Database Password as 'Secret123!'.  Make sure to get the exclamation point. 
* Click Next.
* In "Azure Active Directory", leave "Connect to Active Directory" with `No` selected.
* Click Next:Review + create
   * On the Summary blade you must see "Validation passed".  If you don't see this, you must troubleshoot and resolve the reason.  After you have done so, you can continue.
   * On the final screen, click Create.
* It will take some time for the WebLogic configuration to properly deploy (could be up to 15 minutes). Once the deployment completes, in the portal go to 'All resources'.
* **preview** You must modify the security rules to open port 7001.  Locate your resource group weblogic-cafe-group-`<your suffix>` and, within the group, select `wls-nsg`.
* **preview** Within the Network Security Group, select "NRMS-Rule-105".  In the blade that appears, delete `7001` from the list of "Destination port ranges" and hit Save.  Wait for the rule to update.  View the list of resources in your resource group.
* Find and click on adminVM. Copy the DNS name for the admin server. You should be able to log onto http://`<admin server DNS name>`:7001/console successfully using the credentials above.  If you are not able to log in, you must troubleshoot and resolve the reason why before continuing.

## Verify the database connection is successful

* After logging in to the WebLogic Server Administration Console, in the "Domain Structure" pane, expand "Services".
* Click on "Data Sources".
* In the middle of the page, you should see a link with text "jdbc/WebLogicCafeDB".  Click the link.
* In the upper tab bar, select "Monitoring", then, in the lower tab bar select "Testing".
* Select the radio button next to the server, then click the "Test Data Source" button.  You must see the text "Test of jdbc/WebLogicCafeDB on server admin was successful." in the Messages section of the page after clicking the button.  If you do not see this, you must troubleshoot and resolve the reason why before continuing.

## Setting Up WebLogic in Eclipse
The next step is to get the application up and running. 

* Start Eclipse.
* If you have not yet installed the Oracle WebLogic Server Tools, do so now.
   * Go to the 'Servers' panel, secondary click. Select New -> Server
   * Select Oracle -> Oracle WebLogic Server Tools. Click next. Accept the license agreement, click 'Finish'.  Eclipse may ask to be restarted.  If so, comply with the request.
* You will now connect Eclipse to your remote WebLogic server.
* Go to the 'Servers' panel, secondary click. Select New -> Server -> Oracle -> Oracle WebLogic Server. Hit Next.
* Choose remote, for the remote host enter `<admin server DNS name>`
* Enter the WebLogic admin username/password from above.
* Click "Test connection".  If "Test connection succeeded!" appears, click Ok and you may continue.  Otherwise, troubleshoot and resolve the reason for the connection failure.
* 'Finish'.

### Open weblogic-cafe in the IDE
* Get the weblogic-cafe application into the IDE. In order to do that, go to File -> Import -> Maven -> Existing Maven Projects.  Click Next
* Then browse to where you have this repository code in your file system and select javaee/weblogic-cafe and click "Open".  
* Accept the rest of the defaults and click "finish".
* Once the application loads, you should do a full Maven build by going to the application and secondary clicking -> Run As -> Maven install.
   * You must see `BUILD SUCCESS` in the Eclipse console in order to proceed.  If you do not, troubleshoot the build problem and resolve it.  Once the application has successfully built, you may continue.

### Deploying the Application
Ensure that the deployment action from Eclipse will target the WebLogic Server running on Azure.
* Secondary click on the weblogic-cafe in the Project Explorer and choose Properties.
* Click on Server in the left navigation pane.
* You should see your local and remote WebLogic servers.  Select the remote one and choose Apply and Close.
* Secondary click on weblogic-cafe in the Prjoect Explorer and choose Run As -> Run on Server.
* Once the application runs, Eclise will open it up in a browser. The application is available at http://`<admin server DNS name>`:7001/weblogic-cafe.

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
curl --verbose http://wls-3eb5405ea2-admindomain.eastus.cloudapp.azure.com:7001/weblogic-cafe/rest/coffees
*   Trying 40.121.58.67...
* TCP_NODELAY set
* Connected to wls-3eb5405ea2-admindomain.eastus.cloudapp.azure.com (40.121.58.67) port 7001 (#0)
> GET /weblogic-cafe/rest/coffees HTTP/1.1
> Host: wls-3eb5405ea2-admindomain.eastus.cloudapp.azure.com:7001
> User-Agent: curl/7.64.1
> Accept: */*
>
< HTTP/1.1 200 OK
< Date: Sat, 11 Jan 2020 01:10:00 GMT
< Content-Length: 335
< Content-Type: application/xml
< X-ORACLE-DMS-ECID: 4f2101e9-b8c9-4e76-a6eb-22bd86e0edfc-00000022
< X-ORACLE-DMS-RID: 0
<
* Connection #0 to host wls-3eb5405ea2-admindomain.eastus.cloudapp.azure.com left intact
<?xml version="1.0" encoding="UTF-8" standalone="yes"?><coffees><coffee><id>1</id><name>Strong</name><price>5.0</price></coffee><coffee><id>2</id><name>Weak</name><price>0.25</price></coffee><coffee><id>200</id><name>Medium</name><price>5.0</price></coffee><coffee><id>202</id><name>Medium 2</name><price>5.0</price></coffee></coffees>* Closing connection 0

curl --verbose http://wls-3eb5405ea2-admindomain.eastus.cloudapp.azure.com:7001/weblogic-cafe/rest/coffees/1
*   Trying 40.121.58.67...
* TCP_NODELAY set
* Connected to wls-3eb5405ea2-admindomain.eastus.cloudapp.azure.com (40.121.58.67) port 7001 (#0)
> GET /weblogic-cafe/rest/coffees/1 HTTP/1.1
> Host: wls-3eb5405ea2-admindomain.eastus.cloudapp.azure.com:7001
> User-Agent: curl/7.64.1
> Accept: */*
>
< HTTP/1.1 200 OK
< Date: Sat, 11 Jan 2020 01:10:44 GMT
< Content-Length: 102
< Content-Type: application/xml
< X-ORACLE-DMS-ECID: 4f2101e9-b8c9-4e76-a6eb-22bd86e0edfc-00000023
< X-ORACLE-DMS-RID: 0
<
* Connection #0 to host wls-3eb5405ea2-admindomain.eastus.cloudapp.azure.com left intact
<?xml version="1.0" encoding="UTF-8"?><coffee><id>1</id><name>Strong</name><price>5.0</price></coffee>* Closing connection 0

cat medium.xml
<?xml version="1.0" encoding="UTF-8"?>
<coffee>
  <id>207</id>
  <name>Medium 7</name>
  <price>7.0</price>
</coffee>

curl --verbose -X POST -d @medium.xml http://wls-3eb5405ea2-admindomain.eastus.cloudapp.azure.com:7001/weblogic-cafe/rest/coffees --header "Content-Type: application/xml"
Note: Unnecessary use of -X or --request, POST is already inferred.
*   Trying ::1...
* TCP_NODELAY set
* Connected to wls-3eb5405ea2-admindomain.eastus.cloudapp.azure.com (::1) port 7001 (#0)
> POST /weblogic-cafe/rest/coffees HTTP/1.1
> Host: wls-3eb5405ea2-admindomain.eastus.cloudapp.azure.com:7001
> User-Agent: curl/7.64.1
> Accept: */*
> Content-Type: application/xml
> Content-Length: 112
>
* upload completely sent off: 112 out of 112 bytes
< HTTP/1.1 201 Created
< Date: Mon, 13 Jan 2020 19:59:14 GMT
< Location: http://wls-3eb5405ea2-admindomain.eastus.cloudapp.azure.com:7001/weblogic-cafe/rest/coffees/207
< Content-Length: 0
< X-ORACLE-DMS-ECID: 6b7bc556-c060-46b0-bdd6-f3b5c95614bd-00000041
< X-ORACLE-DMS-RID: 0
<
* Connection #0 to host wls-3eb5405ea2-admindomain.eastus.cloudapp.azure.com left intact
* Closing connection 0
```

## Cleaning Up

Once you are done exploring all aspects of the demo, you should delete the weblogic-cafe-group-`<your suffix>` and weblogic-cafe-db-group-`<your suffix>` resource groups. You can do this by going to the portal, going to resource groups, finding and clicking the groups and hitting delete. This is especially important if you are not using a free subscription! If you do keep these resources around (for example to begin your own prototype), you should in the least use your own passwords and make the corresponding changes in the demo code.
