# Bonus Lab : DTC

## Students

Julien Amacher<br />
Pierre-Alain Curty

## Source files

Source files are directly available as two NetBeans projects.<br />
The first one is named **DTCImplementation** and is the implementation of the server.<br />
The other one is named **DTCClient** and is the implementation of the client.

For convenience, JAR files are readily available in the dist folders (in both the server and client folders)

To produce JAR files, open the projects using NetBeans and click the **Clean and Build project**.

## Protocol specification

Details of the protocol are available in the file **AmacherCurty_RES_LaboBonus.pdf**.

##Demo

A Youtube demonstration is available here : [http://youtu.be/VbXEMpu85PI](http://youtu.be/VbXEMpu85PI "DTC demonstration")

##Client

The client is run using the following command :<br />**java -jar DTCClient.jar**

##Server

The server usage is the following :<br />
**&lt;serverName&gt; (-auth-dir=&lt;authDir&gt;) &lt;serviceName1&gt; &lt;serviceName2&gt; ... &lt;serviceNameN&gt;**

**&lt;ServerName&gt;** is the advertised server name which will be periodically sent by broadcasts.

The -auth-dir=<value> parameter is the (relative or absolute) path to the directory which will store
user accounts. Every user account has the username as file name and its salted, sha1'd password as file contents.
In case this parameter is not supplied, the server will NOT require authentication.

Following is a list of (case-sensitive) services which will be active on the server.
Available services are : ADD, MIN, MULT, SIN, SUM and TOUPPERCASE.

Usage example :

Server will be named Maguro, its authentication folder will be relative and named "accounts" and
the available services are SUM and SIN :

**java -jar DTCImplementation.jar Maguro -auth-dir=accounts SUM MIN**