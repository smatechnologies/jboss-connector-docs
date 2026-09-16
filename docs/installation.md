---
title: Installation
description: "Install the JBoss Connector on Windows or UNIX/Linux, configure the Connector.config file, and set up OpCon global properties for job sub-type access."
tags:
  - Procedural
  - System Administrator
  - Installation
sidebar_label: 'Installation'
---

# Installation

## What is it?

The JBoss Connector installation extracts connector files to a directory on a server where an OpCon agent is installed. The connector is available for both Windows and UNIX/Linux platforms.

- Use this guide when installing the JBoss Connector for the first time on a Windows or UNIX/Linux server
- Use this guide when configuring the `Connector.config` file to connect the connector to a JBoss Enterprise Application Platform environment

## Prerequisites

Before installing, confirm the following:

- An OpCon agent is installed on the target Windows or Linux server
- The JBoss Connector is compatible with JBoss EAP versions
- Java 11 is available (supplied with the Windows installation package; must be installed separately on UNIX/Linux)
- OpCon LTS 23.0 or greater is running

## Installing the connector on Windows

To install the connector on Windows, complete the following steps:

1. Copy `SMAJbossConnector-win.zip` to the target directory on the server.
2. Extract the zip file contents to that directory.

The extracted root directory contains:

| Item | Description |
| --- | --- |
| Connector executable | The main JBoss Connector program |
| `encrypt.exe` | Utility for encoding passwords used in `Connector.config` |
| `dfiles.exe` | Supporting utility |
| `Connector.config` | Configuration file for the connector |
| `java/` | Java 11 runtime environment required by the connector |
| `emplugins/` | Enterprise Manager job sub-type plug-in |

## Installing the connector on Linux

To install the connector on Linux, complete the following steps:

1. Copy `SMAJbossConnector-linux.zip` to the target directory on the server.
2. Extract the zip file contents to that directory.
   The extracted root directory contains the connector jar file, `cjboss.sh`, `encrypt.sh`, `dfiles.sh`, `Connector.config`, and the `emplugins/` directory.
3. Download and install a UNIX/Linux Java 11 runtime. The UNIX/Linux package does not include one.
4. Update `cjboss.sh`, `encrypt.sh`, and `dfiles.sh` to point to the Java installation path.
5. Run `chmod` to add execute permissions on `cjboss.sh`, `dfiles.sh`, and `encrypt.sh`.

## Job sub-type installation

The Enterprise Manager job sub-type enables the **JBoss** job type in your job definitions.

To install the job sub-type, complete the following steps:

1. Copy the plug-in file from the `emplugins/` directory in the connector installation to the `dropins/` directory of your Enterprise Manager installation.
   If `dropins/` does not exist, create it in the Enterprise Manager root directory.
2. Restart Enterprise Manager. The **JBoss** job sub-type appears under both Windows and UNIX job types.

**NOTE:** If the **JBoss** sub-type is not visible after restarting, close Enterprise Manager and reopen it using **Run as Administrator**. After this, Enterprise Manager can be used normally.

## Create global properties

The JBoss job sub-type uses a global property to locate the connector installation directory. Create one property per platform you have installed:

| Platform | Property name | Value |
| --- | --- | --- |
| Windows | `JBOSSPathWindows` | Full path to the connector root installation directory |
| Linux | `JBOSSPathUnix` | Full path to the connector root installation directory |

Create these properties in OpCon before creating any JBoss jobs.

## Configuration

The `Connector.config` file is located in the connector root installation directory. Edit it to define your JBoss environment connection settings and OpCon API credentials.

### Encoding passwords

Passwords stored in `Connector.config` must be encoded before use. The Encrypt utility (`encrypt.exe` on Windows, `encrypt.sh` on UNIX/Linux) is included in the installation.

:::caution

The Encrypt utility **encodes** passwords; it does not encrypt them. It applies no cipher and uses no key, so anyone who can read `Connector.config` can recover the original password from the encoded value. Encoding stops a password being read at a glance, and that is all it does. Protect the file with operating system permissions and treat every value in it as recoverable.

:::

To encode a value on Windows:

```
encrypt.exe -v "your-password-here"
```

The utility displays the encoded value. Copy the encoded output into the relevant password field in `Connector.config`.

### [CONNECTOR] settings

General connector settings.

| Property | Description | Default |
| --- | --- | --- |
| `NAME` | Display name for the connector | — |
| `CONFIGURATION` | JBoss server configuration type | `STANDALONE` |
| `MSGIN_DIR` | Path to the MSGIN directory of the associated Windows or UNIX agent | — |
| `OPCON_EVENT_USER` | OpCon user account used to submit events | — |
| `OPCON_EVENT_USER_PASSWORD` | Password for the OpCon event user. Encode before use | — |
| `DEBUG` | Logging verbosity (`ON` or `OFF`). Required — the connector fails to load its configuration if this setting is absent, so set it to `OFF` rather than removing it | — |

### [OPCONAPI] settings

Connection settings for the OpCon REST API.

| Property | Description | Default |
| --- | --- | --- |
| `ADDRESS` | Address and port of the OpCon REST API (`server:port`) | — |
| `TLS` | TLS version for API communication | `TLSv1.2` |
| `TOKEN` | OpCon REST API application token for authentication | — |

### [LOCAL] settings

Connection settings for a specific JBoss server. The section header (for example, `[LOCAL]`) must match the **Server Name** value entered in the job definition.

| Property | Description | Default |
| --- | --- | --- |
| `URL` | Address of the server hosting the target JBoss instance | — |
| `ADM_PORT` | JBoss management port for JMX requests | `9999` |
| `APP_PORT` | JBoss application port for JMS requests | `4447` |
| `APP_LIB_DIRECTORY` | Directory containing additional jar files to add to the connector classpath for EJB execution | — |
| `SECURITY` | Whether security is enabled on the JBoss environment (`Y` or `N`). Always required when using remote management or JMS access | — |
| `MGT_USER` | JMX management user for submitting JMX requests | — |
| `MGT_USER_PASSWORD` | Password for the JMX management user. Encode before use | — |
| `APP_USER` | Application user for submitting JMS requests | — |
| `APP_USER_PASSWORD` | Password for the application user. Encode before use | — |

### Example Connector.config

Replace every value in angle brackets with your own. Use a **separate password for each of the three accounts** and encode each one with the Encrypt utility before entering it.

```
[CONNECTOR]
NAME=JBoss Connector
CONFIGURATION=STANDALONE
DEBUG=OFF
MSGIN_DIR=C:\\Agent\\MSGIN
OPCON_EVENT_USER=<opcon-event-user>
OPCON_EVENT_USER_PASSWORD=<encoded-opcon-event-user-password>

[OPCONAPI]
ADDRESS=<opcon-server>:443
TLS=TLSv1.2
TOKEN=<opcon-api-application-token>

[LOCAL]
URL=<jboss-server-address>
ADM_PORT=9999
APP_PORT=4447
APP_LIB_DIRECTORY=C:\\JBossConnector\\applibs
SECURITY=Y
MGT_USER=<jboss-management-user>
MGT_USER_PASSWORD=<encoded-jboss-management-user-password>
APP_USER=<jboss-application-user>
APP_USER_PASSWORD=<encoded-jboss-application-user-password>
```

## Log files

The connector writes its log files into a `log` directory under the connector root installation directory. The active log file is named for the connector. Rolled files are written into a subdirectory named for the month and carry the date and an index in the file name, for example `log/2026-09/jboss.connector_2026-09-16.0.log`.

Set `DEBUG=ON` in the `[CONNECTOR]` section of `Connector.config` to increase the detail written to these files. These files are separate from the OpCon job log, which records the connector's output for an individual job.

:::caution

Rolled log files are retained indefinitely. The `log` directory grows until you remove old files, so include it in whatever disk monitoring you apply to the connector host.

:::

## Security considerations

**Authentication**: The connector authenticates with JBoss EAP using JMX management user credentials (`MGT_USER` / `MGT_USER_PASSWORD`) and application user credentials (`APP_USER` / `APP_USER_PASSWORD`). It authenticates with the OpCon REST API using the application token (`TOKEN`).

**Password encoding**: All passwords in `Connector.config` must be encoded using the Encrypt utility before being entered. Do not store plain-text passwords in the configuration file. Encoding is not encryption: the values are recoverable by anyone who can read the file, so file permissions are the control that protects them.

**TLS**: Connector-to-OpCon-API communication uses TLS. The version is set by the `TLS` property in `[OPCONAPI]`. The default is `TLSv1.2`.

**Access control**: Restrict access to `Connector.config` to System Administrators only. The file contains recoverable credentials and an API token. Because the encoding can be reversed without a key, operating system permissions on this file are the only protection the accounts it references have.

## FAQs

**What Java version does the connector require?**
Java 11, on both platforms. The Windows installation package supplies it. The UNIX/Linux package does not, so install a Java 11 runtime separately and point `cjboss.sh`, `encrypt.sh`, and `dfiles.sh` at it.

**What OpCon version is required?**
The JBoss Connector requires OpCon LTS 23.0 or greater.

**Where is the `Connector.config` file located?**
The `Connector.config` file is in the root directory where you extracted the connector files.

**How do I encode passwords for `Connector.config`?**
Run `encrypt.exe -v "value"` on Windows or `encrypt.sh -v "value"` on UNIX/Linux. Copy the encoded output into the relevant password field in `Connector.config`. The output is encoded, not encrypted, and can be reversed without a key.

**What is the `[LOCAL]` section header in `Connector.config`?**
The section header (for example, `[LOCAL]`) must match the **Server Name** value entered in the JBoss job definition in Enterprise Manager. This tells the connector which server configuration to use for a given job.

## Glossary

**Global Property** — A system-wide variable in OpCon referenced using `[[property_name]]` syntax in job definitions. The JBoss Connector uses `JBOSSPathWindows` or `JBOSSPathUnix` to locate the connector installation.

**Connector.config** — The configuration file in the connector root installation directory. Defines the connector name, JBoss server connection settings, OpCon API credentials, and event submission settings.

**Encrypt utility** — A command-line tool included with the JBoss Connector that produces an encoded form of a password. Use it on every password before placing it in `Connector.config`. Despite its name it applies no cipher and no key, so the output is reversible and the file must be protected by operating system permissions.

**MSGIN directory** — The directory monitored by an OpCon agent for incoming event files. The JBoss Connector places retrieved JMS messages here as OpCon events.

**Job sub-type** — A connector-specific extension to the Enterprise Manager job definition interface. The JBoss job sub-type exposes the fields needed to configure JBoss operations (operation type, mbean name, server name, and so on).
