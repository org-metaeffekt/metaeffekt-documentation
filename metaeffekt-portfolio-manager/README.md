# {metæffekt} Portfolio Manager

## Introduction

The {metæffekt} Portfolio Manager allows to provide a simple and easy way to manage and organize software related
documents including Software Bill of Materials (SBOMs) and reports.

The {metæffekt} Portfolio Manager is a building block in a primary process and api-driven setup and allows integration
into diverse processing landscapes applying a service-oriented approach.

Currently, the {metæffekt} Portfolio Manager is provided in three parts:

* Service Endpoint based on Spring-Boot
* Command-Line Client & Shell based on Spring-Boot and Spring-Shell
* A Maven plugin for ease of integration in Maven-based build environments

To be able to associate documents with a given software an AssetGroup / Asset concept is used to harmonize different
levels of granularity and versioning. See [Asset Identification](#a-nameasset-indentificationasset-identification) for 
details.

## Security

The {metæffekt} Portfolio Manager utilizes X.509 certificate-based mutual authentication. To access the service you or
the machine you are using requires a set of credentials to establish a connection to the remote service. These 
credentials are usually supplied by a contact within your organization.

## Tokens

Apart from the X.509 Certificates based access control the {metæffekt} Portfolio Manager uses access tokens to authorize 
the different users in different roles on the managed documents.

The portfolio manager distinguishes three different roles:

### Admin

Admins administrate projects. The creator of a project is automatically the project admin. Apart from client certificate
any user can create projects and become an admin.

Admins are primarily authorized to manage the project. An admin can:

- view the project structure
- upload, download and remove files
- list, add and remove users
- delete an administrated project

### User

A user of a project is authorized to:

- view the project structure
- upload and download files

### Auditor

While the authorized actions of the two previous roles are only allowed to work with a selected project, the auditor
is able to view all projects in the portfolio. An auditor is authorized to:

- view the project structure
- download files

As such, the auditor is able to see all data, but is not able to modify any data.

Auditors are managed by the portfolio manager support.


## <a name="asset-indentification"/>Asset Identification

### Asset Group Identifier

Every document, must be categorized under the following scheme. The scheme lays out that assets belong to an asset
group. An asset group consists of an ID and a version. When specifying an asset group they are written like this:

```
{assetGroupId}:{assetGroupVersion}
```

### Asset Identifier

Assets themselves are defined with only an ID. When software components require further segmentation, inorder to be
correctly defined, it is also possible to define an asset version, a part ID and a partVersion. These are optional:

```
 {assetId} [:{assetVersion}:{partId}:{partVersion}]
```

Since the asset identification construct is hierarchical, fields must be defined from left to right. (demo:2.1::
5.2-alpha is not a valid asset.)

## Command-Line Interface / Shell

The Command-Line Interface (CLI) may be started with the following command:

    java -jar {pathToJar}/ae-portfolio-manager-cli-HEAD-SNAPSHOT-exec.jar

The command requires an `application.properties` in the working directory configuring the service endpoint and SSL
setup.

The Portfolio Manager uses exclusively mutual authenticated SSL connections to protect the communication between the
CLI and the service endpoint.

## Non-Interactive Mode

The Portfolio Manager may also be run from a non-interactive mode:

    java -jar {pathToJar}/ae-portfolio-manager-cli-<VERSION>-exec.jar [COMMAND]

Just like the interactive shell the non-interactive mode requires an `application.properties` in the working directory.

The Portfolio Manager will execute the command without starting a blocking shell session.

## Script

Additionally, it is also possible to initiate a shell session with the usage of an external script file.

    java -jar {pathToJar}/ae-portfolio-manager-cli-<VERSION>-exec.jar @[scriptfile]

The file may contain different commands, written in the same way it is expected as in the shell, seperated by a new
line. This way the specified commands will be executed during startup.

## Maven Plugin

The ```ae-portfolio-manager-maven-plugin``` can be used for automatically uploading/downloading files to an already
previously created project. The plugin requires the same structure for defining assets as the cli.

In your `<pluginManagement/>` section you may add the following:

    <plugin>
        <groupId>com.metaeffekt.portfolio.manager</groupId>
        <artifactId>ae-portfolio-manager-maven-plugin</artifactId>
        <version>${ae.portfolio.manager.version}</version>
    
        <executions>
        ...
        </executions>
    
        <configuration>
            <serviceUrl>${serviceUrl}</serviceUrl>
            <keyStoreFile>${pathToKeystoreFile}</keyStoreFile>
            <keyStorePassword>${keyStorePassword}</keyStorePassword>
            
            <trustStoreFile>${pathToTruststoreFile}</trustStoreFile>
            <trustStorePassword>${trustStorePassword}</trustStorePassword>
        </configuration>
    </plugin>    

While the variables `serviceUrl`, `pathToKeystoreFile` and `pathToTruststoreFile` may be static in your setup, you
should manage the secrets `keyStorePassword` and `trustStorePassword` as protected credentials.

The keystores and truststores as well as the required credentials protecting these can be obtained by the portfolio
manager support in your organization.

### Goal `push`

When using the plugin you may use the following fragment to push (upload) a document to the service endpoint in your
`<build/>` configuration.

    <plugin>
        <groupId>com.metaeffekt.portfolio.manager</groupId>
        <artifactId>ae-portfolio-manager-maven-plugin</artifactId>
        <executions>
            <execution>
                <id>push</id>
                <goals>
                    <goal>push</goal>
                </goals>
                <configuration>
                    <fileToPush>${pathToFile}</fileToPush>
            
                    <projectName>${projectName}</projectName>
                    <token>${token}</token>
                    
                    <assetGroup>${assetGroupRef}</assetGroup>
                    <asset>${assetRef}</asset>
                    <modifier>${modifier}</modifier>
                </configuration>
            </execution>
        </executions>
    </plugin>

The variables for `projectName`, `assetGroupRef`, `assetRef` and `modifier`. Have to be defined. The following may match
your expectation:

    <properties>
        <assetGroupRef>${project.groupId}:${project.version}</assetGroupRef>
        <assetRef>${project.artifactId}:${project.version}</assetRef>
        <modifier>initial</modifier>
    </properties>

The modifier reflects a status of the document in the asset lifecycle. For an initial upload of a not further processed
SBOM for example, the modifier `initial` is sufficient.

### Goal `pull`

The following complements an example configuration for pull (download):

    <plugin>
        <groupId>com.metaeffekt.portfolio.manager</groupId>
        <artifactId>ae-portfolio-manager-maven-plugin</artifactId>
        <executions>
            <execution>
                <id>pull</id>
                <goals>
                    <goal>pull</goal>
                </goals>
                <configuration>
                    <targetDir>${pathToDownloadDir}</targetDir>
                    
                    <projectName>A</projectName>
                    <token>${token}</token>
                        
                    <assetGroupQuery>*:1.0.0</assetGroupQuery>
                    <assetQuery>C:*</assetQuery>
                    <modifier>*</modifier>
                </configuration>
            </execution>
        </executions>
    </plugin>

# OpenAPI Spec

Please find the current OpenAPI Spec [here](docs/portfolio-manager-api-0.5.0.yaml). The spec currently only contains
a subset of the service endpoints.
