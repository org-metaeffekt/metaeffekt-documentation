# {metæffekt} Portfolio Manager

## Introduction

## Command-line Interface / Shell


### Asset Identification
Every asset belongs to a project which is further specified into different asset groups. An assetGroup consists of an ID and a version. When specifying an asset group () the are written like this:
```
{assetGroupId}:{assetGroupVersion}
```
Assets are defined with only an ID. When further segmentation is required it is also possible to define an asset version, a part ID and a partVersion. These are purely optional:
```
 {assetId}(:{assetVersion}:{partId}:{partVersion})
```
Since the asset identification construct is purely hierarchical, fields must be defined from left to right. (demo:2.1::5.2-alpha is not a valid asset.)


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

        <projectName>A</projectName>
        <token>${token}</token>
        <assetGroupRef>B:1.0.0</assetGroupRef>
        <assetRef>C:1.0.0</assetRef>
        <modifier>initial</modifier>
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
