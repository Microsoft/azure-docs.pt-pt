---
title: Copy data from Amazon Marketplace Web Service
description: Learn how to copy data from Amazon Marketplace Web Service to supported sink data stores by using a copy activity in an Azure Data Factory pipeline.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.date: 08/01/2018
ms.openlocfilehash: 24e0de3efec4e2d442f746e19f4c30363f0b4bfa
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218762"
---
# <a name="copy-data-from-amazon-marketplace-web-service-using-azure-data-factory"></a>Copy data from Amazon Marketplace Web Service using Azure Data Factory

This article outlines how to use the Copy Activity in Azure Data Factory to copy data from Amazon Marketplace Web Service. It builds on the [copy activity overview](copy-activity-overview.md) article that presents a general overview of copy activity.

## <a name="supported-capabilities"></a>Supported capabilities

This Amazon Marketplace Web Service connector is supported for the following activities:

- [Copy activity](copy-activity-overview.md) with [supported source/sink matrix](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

You can copy data from Amazon Marketplace Web Service to any supported sink data store. For a list of data stores that are supported as sources/sinks by the copy activity, see the [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) table.

Azure Data Factory provides a built-in driver to enable connectivity, therefore you don't need to manually install any driver using this connector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

The following sections provide details about properties that are used to define Data Factory entities specific to Amazon Marketplace Web Service connector.

## <a name="linked-service-properties"></a>Linked service properties

The following properties are supported for Amazon Marketplace Web Service linked service:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | The type property must be set to: **AmazonMWS** | Sim |
| endpoint | The endpoint of the Amazon MWS server, (that is, mws.amazonservices.com)  | Sim |
| marketplaceID | The Amazon Marketplace ID you want to retrieve data from. To retrieve data from multiple Marketplace IDs, separate them with a comma (`,`). (that is, A2EUQ1WTGCTBG2)  | Sim |
| sellerID | The Amazon seller ID.  | Sim |
| mwsAuthToken | The Amazon MWS authentication token. Mark this field as a SecureString to store it securely in Data Factory, or [reference a secret stored in Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| accessKeyId | The access key ID used to access data.  | Sim |
| secretKey | The secret key used to access data. Mark this field as a SecureString to store it securely in Data Factory, or [reference a secret stored in Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| useEncryptedEndpoints | Specifies whether the data source endpoints are encrypted using HTTPS. The default value is true.  | Não |
| useHostVerification | Specifies whether to require the host name in the server's certificate to match the host name of the server when connecting over SSL. The default value is true.  | Não |
| usePeerVerification | Specifies whether to verify the identity of the server when connecting over SSL. The default value is true.  | Não |

**Exemplo:**

```json
{
    "name": "AmazonMWSLinkedService",
    "properties": {
        "type": "AmazonMWS",
        "typeProperties": {
            "endpoint" : "mws.amazonservices.com",
            "marketplaceID" : "A2EUQ1WTGCTBG2",
            "sellerID" : "<sellerID>",
            "mwsAuthToken": {
                 "type": "SecureString",
                 "value": "<mwsAuthToken>"
            },
            "accessKeyId" : "<accessKeyId>",
            "secretKey": {
                 "type": "SecureString",
                 "value": "<secretKey>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties

For a full list of sections and properties available for defining datasets, see the [datasets](concepts-datasets-linked-services.md) article. This section provides a list of properties supported by Amazon Marketplace Web Service dataset.

To copy data from Amazon Marketplace Web Service, set the type property of the dataset to **AmazonMWSObject**. The following properties are supported:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | The type property of the dataset must be set to: **AmazonMWSObject** | Sim |
| tableName | Name of the table. | No (if "query" in activity source is specified) |

**Exemplo**

```json
{
    "name": "AmazonMWSDataset",
    "properties": {
        "type": "AmazonMWSObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<AmazonMWS linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

For a full list of sections and properties available for defining activities, see the [Pipelines](concepts-pipelines-activities.md) article. This section provides a list of properties supported by Amazon Marketplace Web Service source.

### <a name="amazon-mws-as-source"></a>Amazon MWS as source

To copy data from Amazon Marketplace Web Service, set the source type in the copy activity to **AmazonMWSSource**. The following properties are supported in the copy activity **source** section:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | The type property of the copy activity source must be set to: **AmazonMWSSource** | Sim |
| consulta | Use the custom SQL query to read data. Por exemplo: `"SELECT * FROM Orders where  Amazon_Order_Id = 'xx'"`. | No (if "tableName" in dataset is specified) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAmazonMWS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AmazonMWS input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonMWSSource",
                "query": "SELECT * FROM Orders where Amazon_Order_Id = 'xx'"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Lookup activity properties

To learn details about the properties, check [Lookup activity](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
For a list of data stores supported as sources and sinks by the copy activity in Azure Data Factory, see [supported data stores](copy-activity-overview.md#supported-data-stores-and-formats).
