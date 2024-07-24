# {metæffekt} Portfolio Manager

## Introduction

## Command-line Interface / Shell
The cli may be started with the following command:```java  -Dspring.config.additional-location={pathToApplicationProperties} -jar {pathToJar}/ae-portfolio-manager-cli-HEAD-SNAPSHOT-exec.jar```
The properties file can be used to configure the service endpoint and ssl.

The portfolio manager lays out three different rolls:
### User
A user is authorized to upload/download files and to view the current project structure

### Admin
Admins are authorized to do the same actions as users and additionally have the ability to remove files, add and remove users and also to delete entire projects 
### Auditor
While the authorized actions of the two previous roles are only allowed to impact one specific project, the auditor is able to view all projects. Although an auditor is only permitted to view and download files.   


### Asset Identification
#### Asset Group Identifier
Every document, must be categorized under the following scheme. The scheme lays out that assets belong to an asset group. An asset group consists of an ID and a version. When specifying an asset group they are written like this:
```
{assetGroupId}:{assetGroupVersion}
```

#### Asset Identifier
Assets themselves are defined with only an ID. When software components require further segmentation, inorder to be correctly defined, it is also possible to define an asset version, a part ID and a partVersion. These are optional:
```
 {assetId} [:{assetVersion}:{partId}:{partVersion}]
```
Since the asset identification construct is hierarchical, fields must be defined from left to right. (demo:2.1::5.2-alpha is not a valid asset.)

## Maven Plugin
The ```ae-portfolio-manager-maven-plugin``` can be used for automatically uploading/downloading files to an already previously created project. The plugin requires the same structure for defining assets as the cli. 

```
<groupId>com.metaeffekt.portfolio.manager</groupId>
<artifactId>ae-portfolio-manager-maven-plugin</artifactId>

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
```
   
### Goal `push`
```
<execution>
    <id>push</id>
    <goals>
        <goal>push</goal>
    </goals>
    <configuration>
        <fileToPush>${pathToFile}</fileToPush>

        <projectName>${projectName}</projectName>
        <token>${token}</token>
        
        <assetGroup>${assetGroupIdentifier}</assetGroup>
        <asset>${assetIdentifier}</asset>
        <modifier>{modifier}</modifier>
    </configuration>
</execution>
```
TODO: example

### Goal `pull`
```
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
```


TODO: example
