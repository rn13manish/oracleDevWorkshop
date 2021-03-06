![](../common/images/customer.logo.png)
---
# ORACLE Cloud-Native DevOps workshop #
-----
## Deploy TechCo (Java EE) Demo Application to Java Cloud Service ##

### Introduction ###
This section describes deploying and undeploying applications to an Oracle Java Cloud Service instance by using the WebLogic Server Administration Console. You cannot deploy and undeploy applications directly through the Oracle Java Cloud Service Console.

You can use the WebLogic Server Administration Control graphical user interface to deploy and undeploy an application to an Oracle Java Cloud Service instance, just as you would deploy and undeploy the application to an on-premises service instance.

### About this tutorial ###
This tutorial demonstrates how to:
  
+ create resource (Data Source) for Java Cloud Service using WebLogic console,
+ deploy JEE application to Java Cloud Service using WebLogic console.

### Prerequisites ###


#### Prerequisites specific to students working on the shared environment provided by us ####

If you are operating in a shared environment provided by us, you will need to modify the application itself to make the deployment unique. Otherwise you might affect other students. If you are using your own/trial environment you can ignore these prerequisites.

Open a terminal and change to `GIT_REPO_LOCAL_CLONE/techco-app` folder.

You need to modify 4 files in the Techco app. 

(1)	FILE NAME: techco-app\pom.xml  
   
   Change the "artifactId" and "name" attributes as shown below to suffix "StudentXX", where XX is your ID. Eg "Student01" below

    <groupId>com.oracle.samples</groupId>
    <artifactId>TechCo-ECommerce-Student01</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <name>TechCo-ECommerce-Student01</name>


(2)	FILE NAME: techco-app\src\main\resources\META-INF\persitence.xml
   
   Change the "jta-data-source" attribute value to include your student ID, example "jdbc/student01-OE" below
   
    <persistence-unit name="em" transaction-type="JTA">
    <jta-data-source>jdbc/student01-OE</jta-data-source>
     <class>com.oracle.cloud.demo.oe.entities.ProductInformation</class>
     <class>com.oracle.cloud.demo.oe.entities.Customer</class>
     <class>com.oracle.cloud.demo.oe.entities.OrderItem</class>
     <class>com.oracle.cloud.demo.oe.entities.Order</class>
    </persistence-unit>


(3)	FILE NAME: techco-app\src\main\webapp\WEB-INF\weblogic.xml
   
   Suffix the "context-root" element with your studentID, example "TechCo-ECommerce-student01" below
   
    <context-root>TechCo-ECommerce-student01</context-root>


(4)	Remove the entire "test" directory
    techco-app\src\test    to avoid EJB JNDI name conflicts.
    
    
(5) Remove the "test" directory    
    techco-app\src\main\java\com\oracle\cloud\demo\oe\test to avoid EJB JNDI name conflicts

#### Prerequisites specific to students working on your own/trial environments ####
   ** If you have obtained an environment from us, please  DO NOT do this section since both the database and the java cloud service instances have already been created for you**. This section is only for those students using their own/trial environments.

- [Prepared Database Cloud Service](../dbcs-prepare/README.md) instance which holds the TechCo Demo application's data.
- Running [Java Cloud Service instance](../jcs-create/README.md) configured to access to the prepared Database Cloud Service




### Lab Steps ###

#### Build sample application ####
If you have not already done so, open a terminal and change to `GIT_REPO_LOCAL_CLONE/techco-app` folder and build the sample application.

    $ [oracle@localhost Desktop]$ cd /u01/content/cloud-native-devops-workshop/techco-app
    $ [oracle@localhost techo-app]$ mvn install
Running the build you should see output similar to:

    [oracle@localhost techo-app]$ mvn install
    [INFO] Scanning for projects...
    [INFO]                                                                         
    [INFO] ------------------------------------------------------------------------
    [INFO] Building TechCo-ECommerce 1.0-SNAPSHOT
    [INFO] ------------------------------------------------------------------------
    Downloading: https://repo.maven.apache.org/maven2/org/apache/maven/plugins/maven-war-plugin/2.4/maven-war-plugin-2.4.pom
    Downloaded: https://repo.maven.apache.org/maven2/org/apache/maven/plugins/maven-war-plugin/2.4/maven-war-plugin-2.4.pom (10 KB at 3.7 KB/sec)
    ...
    ...
    ...
    [INFO] Installing /u01/content/cloud-native-devops-workshop/techo-app/pom.xml to /home/oracle/.m2/repository/com/oracle/samples/TechCo-ECommerce/1.0-SNAPSHOT/TechCo-ECommerce-1.0-SNAPSHOT.pom
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 01:06 min
    [INFO] Finished at: 2016-08-20T05:41:55-07:00
    [INFO] Final Memory: 24M/491M
    [INFO] ------------------------------------------------------------------------
    [oracle@localhost techco-app]$

Now the web archive (war) is ready to deploy in the `target` folder.

#### Create Data Source for sample application ####
[Sign in](../common/sign.in.to.oracle.cloud.md) to [https://cloud.oracle.com/sign-in](https://cloud.oracle.com/sign-in). Using the dashboard open the Java Cloud Service Console.

![](images/00.png)

Click on the hamburger icon located at the right top corner of the service summary. From the 
menu select Open WebLogic Server Console

![](images/01.png)

A new browser opens and you are redirected to the selected console’s log-in page. If the server is protected with a self-signed certificate, you will be warned that this certificate is not trusted. This is the default configuration and you can configure your certification. Select I Understand the Risk, and Add Exception (accept certificate). 

![](images/02.png)

When dialog appears select Confirm Security Exception.

![](images/03.png)

When the console log-in page appears, enter the log-in credentials you entered for WebLogic Administrator when you created the service instance.

![](images/04.png)

After a successful login the WebLogic Server Administration Console is displayed. Click Lock & Edit and Service -> Data Sources. Create New **Generic Data Source**

![](images/05.png)

Configure the data source with the following parameters. Note that if you are using our shared environment, you must add your StudentID as shown below:

+ **Name**: jdbc-OE-Student01
+ **Scope**: Global (default)
+ **JNDI Name**: jdbc/student01-OE
+ **Database type**: Oracle (default)

Click Next.

![](images/06.png)

Leave the default Database Driver and click Next.

![](images/07.png) 

Leave the default Transactions Options and click Next.

![](images/08.png)

Configure the database service connection descriptor.

+ **Database Name**: `PDB1.<identitydomain>.oraclecloud.internal` . An example for identity domain a480049 would be PDB1.a480049.oraclecloud.internal
+ **Host Name**: IP adress of the techcoDB Database Cloud. If you dont know this, go to the Database Cloud Service page and click on the techcoDB instance. The public IP address will be shown on the page.
+ **Port**: leave the default 1521
+ **Database User Name**: oe
+ **Password**: Welcome1#
+ **oracle.jdbc.DRCPConnectionClass**: leave empty

Click Next.

![](images/09.png)

Test the data source by click **Test Configuration**

![](images/10.png)

Select the servers or clusters to which you want to deploy the application. For this tutorial, choose to deploy the application to all the servers in the cluster, and then click Finish.

![](images/11.png)

Activate Changes and check the acknowledgement: `All changes have been activated. No restarts are necessary`

![](images/12.png)

#### Deploy sample application ####
Click Lock & Edit and select Deployments in the Domain Structure tree panel. On the Deployments page, click Install.

![](images/13.png)

On the Install page, click upload your file(s) to upload file.

![](images/14.png)

In the Install Application Assistant, click Browse to find `GIT_REPO_LOCAL_CLONE/techco-app/target/TechCo-ECommerce-1.0-SNAPSHOT.war` (In case of virtualbox image environment it is: `/u01/content/cloud-native-devops-workshop/techco-app/target/TechCo-ECommerce-1.0-SNAPSHOT.war`) to deploy and upload it.

![](images/15.png)

After the file is uploaded, its name appears next to the Browse button. Click Next.

![](images/16.png)

Select the file, and then click Next.

![](images/17.png)

Make sure the installation type is Install this deployment as an application. Click Next.

![](images/18.png)

Choose to deploy the application to all the servers in the cluster, and then click Next.

![](images/19.png)

The default settings for Optional Settings are typically adequate so leave defaults. Click Next

![](images/20.png)

In the Change Center, click Activate Changes.

![](images/21.png)

Now the application is in Prepared state and you need to make it ready to accept requests. To start a deployed application select Deployments in Domain Structure and click on Control Tab. The previously deployed application can be found in the list.

![](images/22.png)

Select the application, click Start and then select Servicing all requests.

![](images/23.png)

Click Yes to confirm the deployment.

![](images/24.png)

The application is now in the Active state and is ready to accept requests.

![](images/25.png)

#### Launch sample application ####

To test an application that you have deployed and started on an Oracle Java Cloud Service instance that does not include a load balancer requires public IP address of the compute node.

Change to the browser where the Oracle Cloud My Services dashboard is already opened. If it was closed or the session time expired then [sign in](../common/sign.in.to.oracle.cloud.md) to [https://cloud.oracle.com/sign-in](https://cloud.oracle.com/sign-in). On the dashboard open the Java Cloud Service Console.

![](images/00.png)

Click the name of the service instance to which the application is deployed.

![](images/26.png)

On the service instance details pages, find the list of nodes, and take note of the public IP address for the virtual machine that contains the Administration Server and Managed Server. In case if your Java Cloud Service have Load Balancer configured then use its public IP address.

![](images/27.png)

Open a browser and write the following URL: `https://<public-ip-address>/TechCo-ECommerce`
You should now see the home page of the sample application.

![](images/28.png)
