---
title: Copiar dados da nuvem de marketing do Salesforce usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do Salesforce marketing Cloud para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: 93d875fa67e9954268cec26f7413b6a4a4131f9c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931035"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Copiar dados da nuvem de marketing do Salesforce usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados do Salesforce marketing Cloud. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Recursos com suporte

O conector de nuvem do Salesforce marketing tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Salesforce marketing Cloud para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

O conector de nuvem de marketing do Salesforce dá suporte à autenticação OAuth 2. Ele é criado sobre a [API REST de nuvem de marketing do Salesforce](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm).

>[!NOTE]
>Esse conector não dá suporte à recuperação de objetos personalizados ou extensões de dados personalizados.

## <a name="getting-started"></a>Introdução

Você pode criar um pipeline com a atividade de cópia usando o SDK do .NET, o SDK do Python, o Azure PowerShell, a API REST ou o modelo de Azure Resource Manager. Confira o [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções detalhadas para criar um pipeline com uma atividade de cópia.

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas para o conector de nuvem do Salesforce marketing.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado de nuvem do Salesforce marketing:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **SalesforceMarketingCloud** | Sim |
| clientId | A ID do cliente associada ao aplicativo de nuvem de marketing do Salesforce.  | Sim |
| clientSecret | O segredo do cliente associado ao aplicativo de nuvem de marketing do Salesforce. Você pode optar por marcar esse campo como uma SecureString para armazená-lo com segurança no ADF ou armazenar a senha em Azure Key Vault e deixar a atividade de cópia do ADF efetuar pull a partir daí ao executar a cópia de dados-Saiba mais em [armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Sim |
| useEncryptedEndpoints | Especifica se os pontos de extremidade da fonte de dados são criptografados usando HTTPS. O valor padrão é true.  | Não |
| useHostVerification | Especifica se deve ser necessário o nome do host no certificado do servidor para corresponder ao nome do host do servidor ao se conectar via SSL. O valor padrão é true.  | Não |
| usePeerVerification | Especifica se a identidade do servidor deve ser verificada ao se conectar via SSL. O valor padrão é true.  | Não |

**Exemplo:**

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}

```

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de vendas do Salesforce marketing Cloud.

Para copiar dados do Salesforce marketing Cloud, defina a propriedade Type do conjunto de dado como **SalesforceMarketingCloudObject**. As propriedades a seguir têm suporte:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **SalesforceMarketingCloudObject** | Sim |
| tableName | Nome da tabela. | Não (se "Query" na origem da atividade for especificada) |

**Exemplo**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela fonte de Cloud marketing do Salesforce.

### <a name="salesforce-marketing-cloud-as-source"></a>Equipe de marketing do Salesforce como fonte

Para copiar dados do Salesforce marketing Cloud, defina o tipo de fonte na atividade de cópia como **SalesforceMarketingCloudSource**. As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **SalesforceMarketingCloudSource** | Sim |
| consulta | Use a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "TableName" no DataSet for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
