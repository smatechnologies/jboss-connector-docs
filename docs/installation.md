---
sidebar_label: 'Installation'
hide_title: 'true'
---

# Installation

## Requirements
- The JBoss Connector can be installed on any Linux or Windows Server where there is an OpCon Agent installed.

## Restrictions
- The JBoss Connector is compatible with JBoss 7.1.1 and matching JBoss EAP versions.
- JBoss connector requires Java 1.8 (supplied with Windows installation).
- Requires OpCon version LTS 20.0 or greater.
- 
## Installing the Connector Windows
Copy the supplied zip file SMAJbossConnector-win.zip to a directory on the server.
Unzip the file and extract the contents to a directory on the server. 

After the extraction is complete, the installation root directory contains the connector executable, the encryption executable, the dfiles executable, the Connector.config file, a java directory containing the required Java environment and an emplugins directory containing the Job Sub type.

## Installing the Connector Linux
Copy the supplied zip file SMAJbossConnector-linux.zip to a directory on the server.
Unzip the file and extract the contents to a directory on the server. 

After the extraction is complete, the installation root directory contains the connector jar file, connector shell file, the encryption shell file, the dfiles shell file, the Connector.config fileand an emplugins directory containing the Job Sub type.

Download the appropriate Unix / Linux Java 1.8 version and if installed as a separate Java, update the cjboss.sh, encrypt.sh and dfiles.sh shells to point to the Java version.

Set execute options on cjboss.sh, dfiles.sh, encrypt.sh and /java/bin/java using chmod.

## Job Subtype Installation
Copy the Enterprise Manager plug-in from the installation /emplugins directory to the dropins directory of the Enterprise Manager installation. 
If the dropins directory does not exist, create the dropins directory off the root directory. 

Restart Enterprise Manager and a new Windows or UNIX job subtype called **JBoss** will be visible.

If not restart Enterprise Manager using 'Run as Administrator'. After this Enterprise Manager can be used normally.

## Create Global Properties for JBoss Connector job sub-type
Within OpCon create a global property that points to the root installation directory of the connector. 
- Windows installation 
  - name is JBOSSPathWindows
  - value is ***root installation directory***
- Linux installation
  - name is JBOSSPathUnix
  - value is ***root installation directory***


## Configuration
The configuration of the JBoss Connector requires setting information about the JBoss environment and defining the connection to the OpCon-API of the host OpCon system.

### Encrypt Utility
The Encrypt utility uses standard 64 bit encryption.

Supports a -v argument and displays the encrypted value

On Windows, example on how to encrypt the value "abcdefg":
```
encrypt.exe -v "abcdefg"
```
### Connector.config File
The Connector.config file is in the root installation directory.

Property Name | Value
--------- | -----------
**[CONNECTOR]**                  | header
**NAME**                         | The connector name.
**CONFIGURATION**                | The JBoss configuration (default STANDALONE).
**MSGIN_DIR**                    | The directory of the msgin directory associated with the Windows or UNIX Agent.
**OPCON_EVENT_USER**             | The OpCon user used to submit events to opCon.
**OPCON_EVENT_USER_PASSWORD**    | The password of the OpCon user. Must be encrypted using the **encrypt** utility.
**DEBUG**                        | Values are ON or OFF, default is OFF.
**[OPCON_API]**                  | header 
**ADDRESS**                      | The address of the OpCon Rest-API (i.e. ***server***:***port***).
**TLS**                          | The TLS level supported by the OpCon REST-api Default TLSv1.2.
**TOKEN**                        | OpCon Rest-API application token used to authenticate Rest-API requests to OpCon.
**[LOCAL]**                      | header  (matches the name defined in the Server Name field of the job definition).
**URL**                          | The address of the server where the target JBoss server is running. 
**ADM_PORT**                     | The JBoss management port, used to submit JMX requests (default 9999). 
**APP_PORT**                     | The JBoss ap'plication port used to submit JMS requests (default 4447).
**APP_LIB_DIRECTORY**            | Directory that contains jar files that may be loaded into the classpath for EJB execution.
**SECURITY**                     | Defines if security is enabled on the target JBoss environment. Values are either Y or N. When working with remote access to management or with JMS access, security is always required.
**MGT_USER**                     | The name of a JMX management user that will be used to submit JMX requests.
**MGT_USER_PASSWORD**            | The password of the JMX user. Must be encrypted using the **encrypt** utility.          
**APP_USER**                     | The name of an APP user that will be used to submit JMS requests.
**APP_USER_PASSWORD**            | The password of the APP user. Must be encrypted using the **encrypt** utility.          

Example configuration file. 

```
[CONNECTOR]
NAME=JBoss Connector
CONFIGURATION=STANDALONE
DEBUG=ON
MSGIN_DIR=c:\\test
OPCON_EVENT_USER=ocadm
OPCON_EVENT_USER_PASSWORD=6233426a6232353463484d3d

[OPCONAPI]
ADDRESS=DESKTOP-QMQS7D3:443
TLS=TLSv1.2
TOKEN=d62db7db-6098-4a08-8abd-2923a3418ebd

[LOCAL]
URL=192.168.1.164
ADM_PORT=9999
APP_PORT=4447
APP_LIB_DIRECTORY=C:\\test\\java
SECURITY=Y
MGT_USER=jbossadm
MGT_USER_PASSWORD=6233426a6232353463484d3d
APP_USER=jbossapp
APP_USER_PASSWORD=6233426a6232353463484d3d

```

