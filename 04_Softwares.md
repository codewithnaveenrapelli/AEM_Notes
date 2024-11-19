### Softwares

### Prerequisites
- Java Development Kit (JDK): AEM requires JDK 8 or 11. Download and install the appropriate version from Oracle or AdoptOpenJDK.
- AEM Quickstart Jar from Adobe.
- Integrated Development Environment (IDE): Use an IDE like IntelliJ IDEA or Eclipse.
- Apache Maven: AEM projects often use Maven for build and dependency management. Download and install Maven from Apache.
- Git: For version control. Download and install from Git.

### Install Java Development Kit (JDK)

Experience Manager is a Java application, and thus requires the Java SDK to support the development and the AEM as a Cloud Service SDK.

1. **[Download and install the latest release Java 11 SDK](https://www.oracle.com/in/java/technologies/javase/jdk11-archive-downloads.html)**.
2. Verify Oracle Java 11 SDK is installed by running the command:
```js
Go to CMD Type Below Command

$ java -version
```
Set the JAVA_HOME environment variable to point to your JDK installation directory.

### Download AEM Quickstart Jar

1. Download the cloud SDK latest Jar from [Download](https://experience.adobe.com/#/downloads) using your organization's Adobe ID

2. Unzip downloaded aem-sdk-<version>.zip file

3. Copy the Jar file to a separate working folder of yours.

Place the AEM Quickstart Jar (aem-quickstart-6.x.x.jar).

follow this link to creating instance **[AEM Local Instance Setup](./05_AEM_Instances_Setup.md)**

### Install Maven

Apache Maven is the open-source Java command-line tool used to build AEM Projects generated from the AEM Project Maven Archetype. All major IDE’s (IntelliJ IDEA, Visual Studio Code, Eclipse, etc.) have integrated Maven support.

1. **[Download Maven](https://maven.apache.org/download.cgi)**

2. **[Install Maven](https://maven.apache.org/install.html)**

3. Open your Terminal/Command Prompt

4. Verify Maven is installed, using the command: **$ mvn -v**

### Install Git

Git is the source control management system used by Adobe Cloud Manager, and thus is required for development.

1. **[Download and install Git](https://git-scm.com/downloads)**

2. Open your Terminal/Command Prompt

3. Verify Git is installed, using the command: **$ git --version**

### Install Node.js (and npm)

Node.js is a JavaScript runtime environment used to work with the front-end assets of an AEM project’s ui.frontend sub-project. Node.js is distributed with npm, is the defacto Node.js package manager, used to manage JavaScript dependencies.

1. **[Download and install Node.js](https://nodejs.org/en/download/)**

2. Open your Terminal/Command Prompt

3. Verify Node.js is installed, using the command: **$ node -v**

4. Verify npm is installed, using the command: **$ npm -v**

### Set up the development IDE

IntelliJ IDEA is a powerful IDE for Java development. IntelliJ IDEA comes in two flavors, a free Community edition and a commercial (paid) Ultimate version. The free Community version is sufficient for AEM development, however the Ultimate expands its capability set.

1. **[Download IntelliJ IDEA](https://www.jetbrains.com/idea/download/?section=windows)**
2. **[Download the Repo tool](https://github.com/Adobe-Marketing-Cloud/tools/tree/master/repo#installation)**  Optional

Open or create a new Maven project.

### Configure AEM Server in IDE

In IntelliJ IDEA:

- Go to File > Settings.
Navigate to Plugins and ensure AEM-related plugins are installed.
- Go to Run > Edit Configurations.
Add a new configuration for your AEM server.

### Start AEM
To start AEM in author mode:

```java
java -jar aem-quickstart-6.x.x.jar -r author
```
To start AEM in publish mode:
```java
java -jar aem-quickstart-6.x.x.jar -r publish
```
This will start AEM and create a crx-quickstart directory where the AEM repository and other configurations are stored.


#### Build and Deploy the Project
Use Maven to build and deploy your AEM project to the local AEM instance.

```java
mvn clean install -PautoInstallPackage
```

### Debugging
To debug your AEM instance:

- Start AEM with remote debugging enabled.
```java
java -Xdebug -Xrunjdwp:transport=dt_socket,address=30303,server=y,suspend=n -jar aem-quickstart-6.x.x.jar -r author
```
In your IDE, set up a remote debugging configuration to connect to localhost:30303.

