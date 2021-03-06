Deploying IBM i2 Intelligence Analysis Platform Developer Essentials
====================================================================

IBM i2 Intelligence Analysis Platform Developer Essentials is a set of files and example projects that build on the standard Deployment Toolkit. You can deploy Developer Essentials on a single workstation.

Before you begin
----------------

IBM i2 Intelligence Analysis Platform Developer Essentials has a longer and more specific set of requirements than the platform itself. Before you begin, you must have access to all of the following software:

-   IBM Installation Manager 1.6
-   IBM i2 Intelligence Analysis Platform 3.0.5 Fix Pack 1
-   IBM DB2 9.7 Express Edition
-   IBM WebSphere MQ 7.5 with SupportPac ME01
-   IBM WebSphere Application Server 8.5.5
-   IBM WebSphere Application Server 8.5.5 Liberty Profile
-   Web Server Plug-ins for IBM WebSphere Application Server 8.5.5
-   IBM HTTP Server 8.5
-   Apache Derby
-   Python 2.7
-   Eclipse Kepler

Note: If you obtain WebSphere Application Server Liberty Profile from the project website at https://www.ibmdw.net/wasdev/downloads/websphere-application-server-liberty-profile, then you must download both the runtime and the extended content.

About this task
---------------

The deployment process for Intelligence Analysis Platform Developer Essentials follows the same sequence as the deployment process for the Intelligence Analysis Platform. As a result, many of the steps in these instructions make reference to the Intelligence Analysis Platform Deployment Guide. It is helpful to keep that guide open as you read the steps in this document.

Procedure
---------

In the first part of the procedure, you use Developer Essentials to customize the Deployment Toolkit. The libraries and settings that you add enable development for the Intelligence Analysis Platform on a single computer.

1.  Install IBM i2 Intelligence Analysis Platform 3.0.5 Fix Pack 1 according to the instructions in the product readme file. If you accept all of the default settings, the Deployment Toolkit with the fix pack applied is installed to `C:\IBM\iap-3.0.5.1`.
2.  Navigate to the `C:\IBM\iap-3.0.5.1\IAP-Deployment-Toolkit` directory, make a copy of the `configuration-example` directory, and name it `configuration`.
3.  Install all the other prerequisite software and accept all of the default settings, apart from the target directories. As suggested by Chapter 5 of the Deployment Guide, change the following installation paths:

    |Product|Install path|
    |:------|:-----------|
    |IBM WebSphere Application Server|`C:\IBM\WebSphere\AppServer`|
    |IBM WebSphere Application Server Liberty Profile|`C:\IBM\WebSphere\Liberty`|
    |Web Server Plug-ins for IBM WebSphere Application Server|`C:\IBM\WebSphere\Plugins`|
    |IBM WebSphere MQ|`C:\IBM\WebSphereMQ`|
    |IBM HTTP Server|`C:\IBM\HTTPServer`|

    Note: Developer Essentials uses IBM DB2 Express Edition, which does not create Windows groups when you install it. Record the details of the `db2admin` user that DB2 creates; you need them later in this procedure.

4.  Extract Intelligence Analysis Platform Developer Essentials, and copy the resulting structure into the `iap-3.0.5.1\IAP-Deployment-Toolkit` directory. Confirm any requests to replace existing files with new files from Developer Essentials.

    The new files contain presets that enable the Deployment Toolkit to deploy the platform onto a single computer, and to configure the platform to use WebSphere Application Server Liberty Profile.

5.  Copy the JDBC driver for DB2 from `C:\Program Files\IBM\SQLLIB\java\db2jcc4.jar` to the `IAP-Deployment-Toolkit\configuration\environment\common\jdbc-drivers` directory.
6.  Copy the JDBC driver for Apache Derby from `lib\derby.jar` in the Derby distribution to the same location where you copied the DB2 driver.
7.  Set the write-side environment properties by completing the `environment.properties` file in `IAP-Deployment-Toolkit\configuration\environment\write`.

    If you use the comments in the file as a guide, and you followed the instructions in step 3, the values look like this example:

    ``` {.pre .codeblock}
    was.home.dir=C:/IBM/WebSphere/AppServer

    db.installation.dir.db2=C:/Program Files/IBM/SQLLIB
    db.database.location.dir.db2=C:

    mq.home.dir=C:/IBM/WebSphereMQ
    mq.var.dir=C:/IBM/WebSphereMQ

    http.server.home.dir=C:/IBM/HTTPServer
    http.was.module.dir=C:/IBM/WebSphere/Plugins/bin/32bits
    http.server.ssl.enabled=false
    http.server.host.name=
    http.server.keystore.dir=

    help.port.number=9999
    help.content.dir=C:/IBM/WebSphere/AppServer/WebHelp

    apollo.data=C:/iap-data

    wlp.home.dir=C:/IBM/WebSphere/Liberty
    ```

    Note: The values that you provide for the "help" properties must be valid, but are not used by the development version of the Intelligence Analysis Platform.

8.  Set the read-side environment properties by completing the `environment.properties` file in `IAP-Deployment-Toolkit\configuration\environment\read`.

    If you use the comments in the file as a guide, and you followed the instructions in step 3, the values look like this example:

    ``` {.pre .codeblock}
    was.home.dir=C:/IBM/WebSphere/AppServer

    db.installation.dir.db2=C:/Program Files/IBM/SQLLIB
    db.database.location.dir.db2=C:

    db.upgrade.security.schema.file.name=

    apollo.data=C:/iap-data

    wlp.home.dir=C:/IBM/WebSphere/Liberty
    ```

9.  Do not modify the file at `IAP-Deployment-Toolkit\configuration\environment\topology.xml`. The version of `topology.xml` in Developer Essentials contains all the information necessary to run the development version of the Intelligence Analysis Platform on a single computer.
10. Open the file at `IAP-Deployment-Toolkit\configuration\environment\credentials.properties` in a text editor, and add user names and passwords for WebSphere Application Server and DB2.

    The "`websphere`" user names and passwords must be present, but their values are not important for Developer Essentials. You must set all of the "`db`" properties to the name and password of the user that was created when you installed DB2.

11. Copy the example security schema file (`example-security-schema.xml`) from `IAP-Deployment-Toolkit\configuration\examples\security-schema` to `IAP-Deployment-Toolkit\configuration\fragments\common\WEB-INF\classes`.

    The example security schema contains security dimensions, values, and tags that match the default permissions that are configured in `IAP-Deployment-Toolkit\configuration\fragments\write\ApolloClientSettings.xml`.

12. Copy all the schema and charting scheme files from `IAP-Deployment-Toolkit\configuration\examples\schemas\en_US` to `IAP-Deployment-Toolkit\configuration\fragments\common\WEB-INF\classes`.

    The configuration file at `IAP-Deployment-Toolkit\configuration\fragments\common\WEB-INF\classes\ApolloServerSettingsMandatory.properties` specifies the law enforcement schema and a handful of other standard settings.

At this stage, configuration of the development version of the Intelligence Analysis Platform is complete. You can now deploy the platform into WebSphere Application Server Liberty Profile.

1.  Open a command prompt as administrator, navigate to `C:\IBM\iap-3.0.5.1\IAP-Deployment-Toolkit\scripts`, and then run the following commands in sequence:

    ``` {.pre .codeblock}
    deploy.py -s write -t delete-queue-manager
    deploy.py -s write -t deploy-liberty
    deploy.py -s read -t deploy-liberty
    deploy.py -s write -t start-queue-manager
    ```

    Note: The first command in the list is not strictly necessary when you deploy the platform for the first time, but it is necessary on future occasions.

2.  Use the Services application in Windows (`services.msc`) to restart IBM HTTP Server. Do not attempt to start the application server or the web applications yet.

The development version of the Intelligence Analysis Platform uses a file-based registry of users and groups. Developer Essentials includes definitions of users and groups that correspond to the security schema file that you specified earlier. To complete this aspect of the installation process, you must add the users and groups to the application server.

1.  In an XML editor, open `C:\IBM\WebSphere\Liberty\usr\shared\config\server.security.xml` and `IAP-Deployment-Toolkit\configuration\examples\example-user-repository\liberty-example-users.xml`.
2.  Copy the contents of `liberty-example-users.xml` into `server.security.xml`. You must insert the `<basicRegistry>` element immediately after the `<featureManager>` element.

Now, the development version of the Intelligence Analysis Platform is ready for use. The remaining steps configure the Eclipse development environment to interact with the platform.

1.  Start Eclipse Kepler, and then open a web browser and navigate to https://www.ibmdw.net/wasdev. This website contains tools and information for developing web applications for WebSphere Application Server Liberty Profile.
2.  Click **Downloads**. Then, from the **WebSphere Developer Tools for Eclipse** section, click **Download (for Juno & Kepler)** to open the tools download page. Follow the instructions on the page to install the tools for **WAS V8.5.5 Liberty Profile**.
3.  In Eclipse, click **Window** \> **Preferences** to open the Preferences window.
4.  Optional: If IBM Java is not the default JRE on your computer, then you must configure it as the default JRE in Eclipse.
    1.  In the Preferences window, click **Java** \> **Installed JREs** \> **Add** to open the Add JRE window.
    2.  Use the Add JRE window to add a **Standard VM** named **IBM Java**. The home directory for this JRE is `C:\IBM\WebSphere\AppServer\Java`.
    3.  Select the check box that makes IBM Java the default JRE in Eclipse.

5.  Create a server runtime environment in which the Intelligence Analysis Platform write-side and read-side applications will both run.
    1.  In the Preferences window, click **Server** \> **Runtime Environments** \> **Add** to open the New Server Runtime Environment window.
    2.  In the New Server Runtime Environment window, click **IBM** \> **WebSphere Application Server V8.5 Liberty Profile**, and then click **Next**.
    3.  Set the **Path** of the new server runtime environment to `C:\IBM\WebSphere\Liberty`, instruct it to use the default JRE, and then click **Finish**.

6.  Click **OK** to close the Preferences window.
7.  Create a server in Eclipse that represents the write side of an Intelligence Analysis Platform deployment.
    1.  In the Eclipse application window, click the **Servers** tab.
        Note: If the **Servers** tab is not visible, click **Window** \> **Show View** \> **Servers** to open it.
    2.  Right-click inside the tab, and then click **New** \> **Server** to open the New Server window.
    3.  In the New Server window, click **IBM** \> **WebSphere Application Server V8.5 Liberty Profile** to select the server type.
    4.  Change the **Server name** to write, and then click **Next**.
    5.  From the **Liberty profile server** list, select **write**, and then click **Finish**.

8.  Repeat the previous step, but replace "write" with "read" wherever it appears.
9.  Start the development version of the Intelligence Analysis Platform.
    1.  Inside the **Servers** tab, double-click the **read** and **write** servers in turn. New **read** and **write** tabs open in the top part of the Eclipse application window.
    2.  In both new tabs, clear the **Run applications directly from the workspace** check box, and then press Ctrl+s to save the settings.
    3.  Back inside the **Servers** tab, start the write server, followed by the read server. When the startup process completes, you have a running instance of the Intelligence Analysis Platform that you can control through Eclipse.

10. To test the platform, open a web browser and navigate to http://localhost/apollo. You can log in to the Intelligence Portal with any of the user names and passwords that were specified in the `liberty-example-users.xml` file.

* * * * *

© Copyright IBM Corporation 2014.


