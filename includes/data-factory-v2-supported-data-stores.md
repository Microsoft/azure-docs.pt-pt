---
title: incluir ficheiro
description: incluir ficheiro
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 02/26/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 223d38044b5e31c9c8e61fff74e1dd823970debd
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825335"
---
| Categoria | Arquivo de dados | Suportado como origem | Suportado como sink | Suportado pelo [IR do Azure](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) | Suportado pelo [IR autoalojado](../articles/data-factory/concepts-integration-runtime.md#self-hosted-integration-runtime) |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **Azure** |[Armazenamento de Blobs do Azure](../articles/data-factory/connector-azure-blob-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Cosmos DB (SQL API)](../articles/data-factory/connector-azure-cosmos-db.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[API do Azure Cosmos DB para o MongoDB](../articles/data-factory/connector-azure-cosmos-db-mongodb-api.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Explorador de dados do Azure](../articles/data-factory/connector-azure-data-explorer.md) |✓ |✓ |✓ |✓ |
| &nbsp; |[Armazenamento do Azure Data Lake Ger1](../articles/data-factory/connector-azure-data-lake-store.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Geração 2 Lake armazenamento de dados do Azure](../articles/data-factory/connector-azure-data-lake-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Base de dados do Azure para MariaDB](../articles/data-factory/connector-azure-database-for-mariadb.md) |✓ | |✓ |✓  |
| &nbsp; |[Base de Dados do Azure para MySQL](../articles/data-factory/connector-azure-database-for-mysql.md) |✓ | |✓ |✓  |
| &nbsp; |[Base de Dados do Azure para PostgreSQL](../articles/data-factory/connector-azure-database-for-postgresql.md) |✓ | |✓ |✓  |
| &nbsp; |[File Storage do Azure](../articles/data-factory/connector-azure-file-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Base de Dados SQL do Azure](../articles/data-factory/connector-azure-sql-database.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Instância gerida da base de dados SQL do Azure](../articles/data-factory/connector-azure-sql-database-managed-insance.md) | |✓ |✓ |✓  |
| &nbsp; |[Azure SQL Data Warehouse](../articles/data-factory/connector-azure-sql-data-warehouse.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Índice do Azure Search](../articles/data-factory/connector-azure-search.md) | |✓ |✓ |✓  |
| &nbsp; |[Table Storage do Azure](../articles/data-factory/connector-azure-table-storage.md) |✓ |✓ |✓ |✓  |
| **Base de Dados** |[Amazon Redshift](../articles/data-factory/connector-amazon-redshift.md) |✓ | |✓ |✓  |
| &nbsp; |[DB2](../articles/data-factory/connector-db2.md) |✓ | |✓ |✓  |
| &nbsp; |[Drill (Pré-visualização)](../articles/data-factory/connector-drill.md) |✓ | |✓ |✓  |
| &nbsp; |[Google BigQuery](../articles/data-factory/connector-google-bigquery.md) |✓ | |✓ |✓  |
| &nbsp; |[Greenplum](../articles/data-factory/connector-greenplum.md) |✓ | |✓ |✓  |
| &nbsp; |[HBase](../articles/data-factory/connector-hbase.md) |✓ | |✓ |✓  |
| &nbsp; |[Hive](../articles/data-factory/connector-hive.md) |✓ | |✓ |✓  |
| &nbsp; |[Apache Impala (Pré-visualização)](../articles/data-factory/connector-impala.md) |✓ | |✓ |✓  |
| &nbsp; |[Informix](../articles/data-factory/connector-odbc.md#ibm-informix-source) |✓ | | |✓  |
| &nbsp; |[MariaDB](../articles/data-factory/connector-mariadb.md) |✓ | |✓ |✓  |
| &nbsp; |[Microsoft Access](../articles/data-factory/connector-odbc.md#microsoft-access-source) |✓ | | |✓  |
| &nbsp; |[MySQL](../articles/data-factory/connector-mysql.md) |✓ | |✓ |✓  |
| &nbsp; |[Netezza](../articles/data-factory/connector-netezza.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle](../articles/data-factory/connector-oracle.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Phoenix](../articles/data-factory/connector-phoenix.md) |✓ | |✓ |✓  |
| &nbsp; |[PostgreSQL](../articles/data-factory/connector-postgresql.md) |✓ | |✓ |✓  |
| &nbsp; |[Presto (Pré-visualização)](../articles/data-factory/connector-presto.md) |✓ | |✓ |✓  |
| &nbsp; |[SAP Business Warehouse aberto Hub](../articles/data-factory/connector-sap-business-warehouse-open-hub.md) |✓ | | |✓  |
| &nbsp; |[SAP Business Warehouse por meio do MDX](../articles/data-factory/connector-sap-business-warehouse.md) |✓ | | |✓  |
| &nbsp; |[SAP HANA](../articles/data-factory/connector-sap-hana.md) |✓ |✓ | |✓  |
| &nbsp; |[Spark](../articles/data-factory/connector-spark.md) |✓ | |✓ |✓  |
| &nbsp; |[SQL Server](../articles/data-factory/connector-sql-server.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Sybase](../articles/data-factory/connector-sybase.md) |✓ | | |✓  |
| &nbsp; |[Teradata](../articles/data-factory/connector-teradata.md) |✓ | | |✓  |
| &nbsp; |[Vertica](../articles/data-factory/connector-vertica.md) |✓ | |✓ |✓  |
| **NoSQL** |[Cassandra](../articles/data-factory/connector-cassandra.md) |✓ | |✓ |✓  |
| &nbsp; |[Couchbase (Pré-visualização)](../articles/data-factory/connector-couchbase.md) |✓ | |✓ |✓  |
| &nbsp; |[MongoDB](../articles/data-factory/connector-mongodb.md) |✓ | |✓ |✓  |
| **Ficheiro** |[Amazon S3](../articles/data-factory/connector-amazon-simple-storage-service.md) |✓ | |✓ |✓  |
| &nbsp; |[Sistema de Ficheiros](../articles/data-factory/connector-file-system.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[FTP](../articles/data-factory/connector-ftp.md) |✓ | |✓ |✓  |
| &nbsp; |[Armazenamento na Cloud do Google](../articles/data-factory/connector-google-cloud-storage.md) |✓ | |✓ |✓  |
| &nbsp; |[HDFS](../articles/data-factory/connector-hdfs.md) |✓ | |✓ |✓  |
| &nbsp; |[SFTP](../articles/data-factory/connector-sftp.md) |✓ | |✓ |✓  |
| **Protocolo genérico** |[HTTP genérico](../articles/data-factory/connector-http.md) |✓ | |✓ |✓  |
| &nbsp; |[OData genérico](../articles/data-factory/connector-odata.md) |✓ | |✓ |✓  |
| &nbsp; |[ODBC genérico](../articles/data-factory/connector-odbc.md) |✓ |✓ | |✓  |
| &nbsp; |[REST genérico](../articles/data-factory/connector-rest.md) |✓ | |✓ |✓  |
| **Serviços e aplicações** |[Serviço Web do Amazon Marketplace (Pré-visualização)](../articles/data-factory/connector-amazon-marketplace-web-service.md) |✓ | |✓ |✓  |
| &nbsp; |[Common Data Service for Apps](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Concur (Pré-visualização)](../articles/data-factory/connector-concur.md) |✓ | |✓ |✓  |
| &nbsp; |[Dynamics 365](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Dynamics AX (pré-visualização)](../articles/data-factory/connector-dynamics-ax.md) |✓ | |✓ |✓  |
| &nbsp; |[Dynamics CRM](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Google AdWords (pré-visualização)](../articles/data-factory/connector-google-adwords.md) |✓ | |✓ |✓  |
| &nbsp; |[HubSpot (Pré-visualização)](../articles/data-factory/connector-hubspot.md) |✓ | |✓ |✓  |
| &nbsp; |[Jira (Pré-visualização)](../articles/data-factory/connector-jira.md) |✓ | |✓ |✓  |
| &nbsp; |[Magento (Pré-visualização)](../articles/data-factory/connector-magento.md) |✓ | |✓ |✓  |
| &nbsp; |[Marketo (Pré-visualização)](../articles/data-factory/connector-marketo.md) |✓ | |✓ |✓  |
| &nbsp; |[Office 365 (pré-visualização)](../articles/data-factory/connector-office-365.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Eloqua (Pré-visualização)](../articles/data-factory/connector-oracle-eloqua.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Responsys (Pré-visualização)](../articles/data-factory/connector-oracle-responsys.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle serviço Cloud (pré-visualização)](../articles/data-factory/connector-oracle-service-cloud.md) |✓ | |✓ |✓  |
| &nbsp; |[Paypal (Pré-visualização)](../articles/data-factory/connector-paypal.md) |✓ | |✓ |✓  |
| &nbsp; |[QuickBooks (Pré-visualização)](../articles/data-factory/connector-quickbooks.md) |✓ | |✓ |✓  |
| &nbsp; |[Salesforce](../articles/data-factory/connector-salesforce.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Salesforce Service Cloud](../articles/data-factory/connector-salesforce.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Salesforce Marketing Cloud (Pré-visualização)](../articles/data-factory/connector-salesforce-marketing-cloud.md) |✓ | |✓ |✓  |
| &nbsp; |[SAP Cloud for Customer (C4C)](../articles/data-factory/connector-sap-cloud-for-customer.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[SAP ECC](../articles/data-factory/connector-sap-ecc.md) |✓ | |✓ |✓ |
| &nbsp; |[ServiceNow](../articles/data-factory/connector-servicenow.md) |✓ | |✓ |✓  |
| &nbsp; |[Shopify (Pré-visualização)](../articles/data-factory/connector-shopify.md) |✓ | |✓ |✓  |
| &nbsp; |[Square (Pré-visualização)](../articles/data-factory/connector-square.md) |✓ | |✓ |✓  |
| &nbsp; |[Tabela Web (tabela HTML)](../articles/data-factory/connector-web-table.md) |✓ | | |✓  |
| &nbsp; |[Xero (Pré-visualização)](../articles/data-factory/connector-xero.md) |✓ | |✓ |✓  |
| &nbsp; |[Zoho (Pré-visualização)](../articles/data-factory/connector-zoho.md) |✓ | |✓ |✓  |

> [!NOTE]
> Qualquer conector marcado como *Pré-visualização* significa que o pode experimentar e enviar-nos comentários.  Se quiser realizar uma dependência em conectores de pré-visualização na sua solução, contacte o [Suporte do Azure](https://azure.microsoft.com/support/).