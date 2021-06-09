# Deploying a Java EE Application with Coherence on Azure Using a WebLogic Virtual Machine Cluster
This demo shows how you can deploy a Java EE application with Coherence to Azure into a WebLogic cluster using a marketplace solution.
## Setup

* Install the Window Subsystem for Linux (or other Linux, we used [WSL 2 with Ubuntu 18.04 LST](https://docs.microsoft.com/en-us/windows/wsl/install-win10)).
* Install Maven in your WSL
* Download this repository somewhere in your WSL file system (easiest way might be to download as a zip and extract).
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
* Specify the Password to be Secret123!. 
* Hit 'Review+create' then 'Create'. It will take a moment for the database to deploy and be ready for use.
* In the portal, go to 'All resources'. Enter `<your suffix>` into the filter box and press enter.
* Find and click on weblogic-cafe-db-`<your suffix>`. 
* Under Settings, open the connection security panel.
   * Toggle "Allow access to Azure services" to "Yes"
   * Toggle "Enforce SSL connection" to "DISABLED". 
   * Hit Save.

## Create User-Assigned Managed Identity

* Go to the [Azure portal](http://portal.azure.com).
* Select Create a resource -> User Assigned Managed Identity.
* The steps in this section use `<your suffix>`. The suffix could be your first name such as "reza".  It should be short and reasonably unique, and less than 10 charracters in length.
* Create and specify a new resource group named weblogic-cafe-managed-identity-`<your suffix>`.
* Specify the Instance name to be weblogic-cafe-managed-identity-`<your suffix>`. 
* Specify the location to be a location close to you.
* Click Review + create -> Create

## Create the WebLogic Cluster on Azure
The next step is to get a WebLogic cluster up and running. Follow the steps below to do so.

* Go to the [Azure portal](https://ms.portal.azure.com/).
* Use the search bar on the top to navigate to the Marketplace.
* In the Marketplace, type in 'Oracle WebLogic Server Cluster' in the search bar and click Enter.
* Locate the offer named 'Oracle WebLogic Server Cluster' and click 'Create'. 
* Ensure "Oracle Enterprise Java" is selected in "Subscription", or select your own subscription.
* In the basics blade, for "Project details"
   * Create and specify a new resource group named weblogic-cafe-group-`<your suffix>` . 
   * Select Region '(US) East US'. 
* For "Credentails for Virtual Machines and WebLogic"
   * For the "Password for admin account of VMs", enter 'Secret123456'. 
   * For the "Password for WebLogic Administrator", enter 'Secret123456'.
* For Number of VMs, change the value to 3.
* For "Optional Basic Configuration", ensure  `Yes` is selected to accept default for optional configuration.
* Click Next.
* In "TLS/SSL Configuration", leave "Configure WebLogic Administration Console on HTTPS (Secure) port, with your own TLS/SSL Certificate?" with `No` selected.
* In the "Azure Application Gateway" use these values
   * Toggle "Connect to Azure Application Gateway" to `Yes`.
   * Choose "Generate a self-signed certificate" for the "Select desired TLS/SSL certificate option".
   * To auto-generate a self-signed certificate, you will need to add user-assigned managed identities(at least one), click "Add".
   * In the window pops from right, choose weblogic-cafe-managed-identity-`<your suffix>`, then click "Add"
* Click Next.
* In "Database" use these values
   * Toggle "Connect to DataBase" to `Yes`. 
   * For "Choose database type", from the dropdown menu, select the option for "Azure Database for PostgreSQL".
   * Specify JNDI Name to be 'jdbc/WebLogicCafeDB'. 
   * Specify DataSource Connection String to be 'jdbc:postgresql://weblogic-cafe-db-`<your suffix>`.postgres.database.azure.com:5432/postgres'
   * Specify the Database Username to be 'postgres@weblogic-cafe-db-`<your suffix>`'
   * Enter the Database Password as 'Secret123!' 
* Click Next.
* In "DNS Configuration", leave "Configure Custom DNS Alias?" with `No` selected.
* Click Next.
* In "Azure Active Directory", leave "Connect to Active Directory" with `No` selected.
* Click Next.
* In "Elasticsearch and Kibana", leave "Export logs to Elasticsearch?" with `No` selected.
* Click Next.
* In "Coherence", toggle "Use Coherence cache?" to `Yes`. Leave other values to default.
* Click Next:Review + create
   * On the Summary blade you must see "Validation passed".  If you don't see this, you must troubleshoot and resolve the reason.  After you have done so, you can continue.
   * On the final screen, click Create.
* It will take some time for the WebLogic cluster to properly deploy (could be up to an hour). Once the deployment completes, in the portal go to 'All resources'.
* Find and click on adminVM. Copy the DNS name for the admin server. You should be able to log onto http://`<admin server DNS name>`:7001/console successfully using the credentials above.  If you are not able to log in, you must troubleshoot and resolve the reason why before continuing.
* Find and click on myAppGateway. Copy the DNS name for the Application Gateway as `<app gateway DNS>`.
* Find and click on wls-nsg. In the Overview page, find `WebLogicManagedChannelPortsDenied` under the Inbound Security Rules, click on it. In the pop window, select `Allow` for Action and click `Save`, wait for the update to be completed.

## Prepare WebLogic-Cafe App
* Open a command line window of WSL and cd to `<your path to the repo>/weblogic-on-azure/clusterwithcoherence/weblogic-cafe`.
* Run "mvn clean install", a `weblogic-cafe.war` file will be generated in /target directory.

## Deploy above apps from WebLogic Admin Console
* Go to `<admin server DNS name>:7001/console`
* Signin with Username=`weblogic`, Password=`Secret123456`
* Click `Deployment` in "Domain Structure" window.
* Click `Lock & Edit` in "Change Center" window.
* Click `Install` in "Summary of Deployments" window, "Configuration" tab.
* Click `Upload your file(s)` and select weblogic-cafe.jar file built in the previous step.
* Accept default deployment settings by accepting default values and clicking `Next` button, when it comes to the "Select deployment targets' window, select `cluster1`
* Accept default deployment settings by accepting default values and clicking `Next` until you click `Finish`
* Click `Activate Changes` in the "Change Center" window
* Click `Deployment` in "Domain Structure" window, and select `Control` tab on the right, verify that all apps are in `Prepared` state.
* Click the checkbox on the left of `Name` to select all apps, click `Start` -> `Servicing all requests`

## Link to the Coherence Mbean using JConsole
The Coherence Mbean server will be started in the oldest member of the cluster, so we need to find it. [JConsole](https://en.wikipedia.org/wiki/JConsole) is a GUI tool that can monitor remote JVM, and it comes for free with JDK.
* Go to `<admin server DNS name>:7001/console`
* Signin with Username=`weblogic`, Password=`Secret123456`.
* Click `Diagonostics` in "Domain Structure" window, and go to `Log Files`.
* Look for `ServerLog` of Server `msp1`, select and click `view` button.
* Click `Customize this table` and change `Time Interval` to `All` and `Number of rows displayed per page` to `5000`, click `Apply`
* Use browser's search function to look for `oldestMember`, write down the machine name `<oldestMember>`.
* In Azure Portal, go to resource group weblogic-cafe-group-`<your suffix>`.
* Search for the virtual machine named `<oldestMember>`, click on it to enter the Overview page.
* Copy the Public IP address `<oldestMemberIP>`.
* Open JConsole using the following command:
```bash
"<Path to JDK>\bin\jconsole" -J-Djava.class.path="<Path to JDK>\lib\jconsole.jar;<Path to JDK>\lib\tools.jar;<Path to Oracle Home>\wlserver\server\lib\weblogic.jar" -J-Djmx.remote.protocol.provider.pkgs=weblogic.management.remote
```
* In the JConsole:New Connection window, select `Remote Process` and fill the address as following:
```bash
service:jmx:t3://`<oldestMemberIP>`:8501/jndi/weblogic.management.mbeanservers.runtime
```
* Fill in Username=`weblogic`, Password=`Secret123456`, click Connect. If a window pop up and says 'Secure connection failed. Retry insecurely?', click `Insecure connection`
* After the connection is established, verify it by checking there is a green connection icon on the top right.

## Test the cache
* In JConsole switch to the `Mbeans` tab.
* Step into the Coherence cache attribute window by clicking `Coherence->Cache->"oracle.coherence.web:DistributedSessions"->session-storage->myCoherence->mspStorage1->3->back->Attributes`.
* You can see the size now is `0`. That is because you haven't access the WebLogic-Cafe app.
* Now open Microsoft Edge and go to `<app gateway DNS>`/weblogic-cafe/index.xhtml, right now you just created a SessionScoped bean, wait for a few second and you will see the cache size increment by 1. Accessing this page in another tab won't affect this number because you will hit the cache.
* Open a new InPrivate Window and access the page, the cache size will be increased because the private window does not share sessions with previous ones.