# Installation Guide

## Prerequisites

To use the application and its services, first the following prerequirements have to be fulfilled:

| Requirements | Description                                                                                                                          |
|:-------------|:-------------------------------------------------------------------------------------------------------------------------------------|
| **Postgres** | For importing and storing the data the inventory index by default uses a postres database (currently used version is 18.1).          |
| **Java**     | The application is developed with java 17 and therefor it must be installed to execute the resulting .jar file.                      |
| **Maven**    | The application is build with Maven 3.9.11 to simplify the process of setting up the initial database schema, roles and permissions. |

The instructions for setup and use of the deployed application are listed
here: [Deployment](https://github.com/org-metaeffekt/metaeffekt-inventory-index/blob/main/deployment/README.md)

### Postgres

The {metæffekt} Inventory Index requires a postgres database to run by default. The database has to be started by the user manually as a prerequisite.
The database schema is then set up by the application using SQL scripts that also generate the necessary user and permissions for accessing the
database.

The following steps are executed by the initial setup scripts.

#### Creating a schema

If the schema already exists it first will be dropped. With the creation of the schema those will be created:

* Sequences
* Tables
* Indexes
* Constraints for foreign keys

#### Creating views

All materialized and not materialized views will be created. If a views already exists with the same name, it will be replaced by the new one.

#### Creating the user

Creating the user includes the following steps:

* Creating a database user with a name (if it already exists, it will be dropped first with all it's privileges)
* Granting access to the database
* Granting privileges on the schema
* Granting privileges on all tables in the schema
* Granting privileges on all sequences in the schema
* Granting privileges on all materialized views in the schema

### Certificates

The endpoints use `HTTPS` by default to ensure an encrypted and secure way of client server interaction. As a consequence the browser (here it
represents the client) first has to trust the servers certificate to be able to send requests.

#### Trusting the certificate

To enable the certificate the user initially once has to visit the corresponding url in the browser and trust the certificate. After that the browser
will trust the servers certificate and the user will be able to build send requests.
The interaction between browser and backend runs over encrypted data using public/private keys.

## Configuration

### Environment properties

The env.rc file contains all properties that are necessary for the setup of the application and allows the user to manually configure them.

Those properties are accessed by the importer service and query service as well as by the pom when initializing the database.

The properties are:

| Property                                | Description                                                                                                       |
|:----------------------------------------|:------------------------------------------------------------------------------------------------------------------|
| **II_VERSION**                          | Defines the version of the executive jar file and application.                                                    |
| **II_DATABASE_URL**                     | Defines the database url to connect with.                                                                         |
| **II_DATABASE_USER_NAME**               | Defines the database user name. This user has limited permissions.                                                |
| **II_DATABASE_USER_PASSWORD**           | Defines the database user password.                                                                               |
| **II_DATABASE_ADMIN_NAME**              | Defines the database admin user name. This user has all required permissions.                                     |
| **II_DATABASE_ADMIN_PASSWORD**          | Defines the database admin user password.                                                                         |
| **II_TOKEN_ISSUER_TRUSTSTORE_PASSWORD** | Defines the trustore password bcause the token required to authenticate on the Inventory Query Service is signed. |
| **II_SSL_KEYSTORE_PASSWORD**            | Defines the keystore password bcause the Inventory Query Service is configured to use SSL.                        |
| **II_SCHEDULER_BATCH_SIZE**             | Defines the inventory batch size for persisting inventories.                                                      |
| **II_SCHEDULER_INPUT_DIR**              | Defines the directory where the inventories to be persisted are located.                                          |
| **II_SCHEDULER_TENANT_ID**              | Defines the tenant of the inventories.                                                                            |
| **II_SCHEDULER_CRON**                   | Defines on which time periods the scheduler runs.                                                                 |

### Importer Service

The importer service is responsible for importing the initial inventories after the database and its schema are set up.

#### Application Properties

The importer service uses these properties which can be configured before running the service:

* II_DATABASE_URL
* II_DATABASE_USER_NAME
* II_DATABASE_USER_PASSWORD
* II_SCHEDULER_BATCH_SIZE
* II_SCHEDULER_INPUT_DIR
* II_SCHEDULER_TENANT_ID
* II_SCHEDULER_CRON

### Query Service

The query service is responsible to run the server in the backend and export the endpoints allowing the client to send requests.

#### Application Properties

The query service uses these environmental properties:

* II_DATABASE_URL
* II_DATABASE_USER_NAME
* II_DATABASE_USER_PASSWORD
* II_TOKEN_ISSUER_TRUSTSTORE_PASSWORD
* II_SSL_KEYSTORE_PASSWORD

## Administration Tasks

Some administration tasks need to be completed before the installation can be done. Those include f.e. creating the executable .jar file and
preparing the repository and installation instructions.

Additionally, the user has to start a postgres database manually before configuring the env.rc file and setting up the application.

## Database backup

No database backup is necessary. Setting up the database and filling it happens via SQL scripts and inventory import.

