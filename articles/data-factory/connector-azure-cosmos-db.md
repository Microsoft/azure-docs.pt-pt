---
title: Copiar dados de ou para Azure Cosmos DB (API do SQL) usando Data Factory
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte para ou de Azure Cosmos DB (API do SQL) para repositórios de coletor com suporte usando Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 74bec61cbb69b17afddeb5abc1df16d8c1140234
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681307"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Copiar dados de ou para Azure Cosmos DB (API do SQL) usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-documentdb-connector.md)
> * [Versão atual](connector-azure-cosmos-db.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para Azure Cosmos DB (API do SQL). O artigo se baseia na [atividade de cópia no Azure data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

>[!NOTE]
>Esse conector só dá suporte à cópia de dados de/para Cosmos DB API do SQL. Para a API do MongoDB, consulte o [conector para a API de Azure Cosmos DB para MongoDB](connector-azure-cosmos-db-mongodb-api.md). Não há suporte para outros tipos de API agora.

## <a name="supported-capabilities"></a>Recursos com suporte

Esse conector de Azure Cosmos DB (API do SQL) tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de Azure Cosmos DB (API do SQL) para qualquer armazenamento de dados de coletor com suporte ou copiar dados de qualquer armazenamento de dados de origem com suporte para Azure Cosmos DB (API do SQL). Para obter uma lista de armazenamentos de dados com suporte da atividade de cópia como fontes e coletores, consulte [armazenamentos e formatos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Você pode usar o conector do Azure Cosmos DB (API do SQL) para:

- Copie dados de e para a API do Azure Cosmos DB [SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Gravar em Azure Cosmos DB como **Insert** ou **Upsert**.
- Importe e exporte documentos JSON no estado em que se encontram ou copie dados de ou para um DataSet tabular. Os exemplos incluem um banco de dados SQL e um arquivo CSV. Para copiar documentos como estão para ou de arquivos JSON ou de outra coleção de Azure Cosmos DB, consulte importar ou exportar documentos JSON.

O Data Factory integra-se com o [Azure Cosmos DB biblioteca de executores em massa](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para fornecer o melhor desempenho ao gravar em Azure Cosmos DB.

> [!TIP]
> O [vídeo de migração de dados](https://youtu.be/5-SRNiC_qOU) orienta você pelas etapas de copiar dados do armazenamento de BLOBs do Azure para Azure Cosmos DB. O vídeo também descreve as considerações de ajuste de desempenho para ingerir dados para Azure Cosmos DB em geral.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que podem ser usadas para definir Data Factory entidades específicas para Azure Cosmos DB (API do SQL).

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Azure Cosmos DB (API do SQL):

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** deve ser definida como **CosmosDb**. | Sim |
| connectionString |Especifique as informações necessárias para se conectar ao banco de dados do Azure Cosmos DB.<br />**Observação**: você deve especificar as informações do banco de dados na cadeia de conexão, conforme mostrado nos exemplos a seguir. <br/>Marque este campo como uma SecureString para armazená-lo com segurança em Data Factory. Você também pode colocar a chave de conta em Azure Key Vault e extrair a configuração de `accountKey` da cadeia de conexão. Consulte os exemplos a seguir e [armazene as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Azure Integration Runtime ou um tempo de execução de integração auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se essa propriedade não for especificada, o Azure Integration Runtime padrão será usado. |Não |

**Exemplo**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar chave de conta no Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;Database=<Database>"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades de DataSet

Esta seção fornece uma lista das propriedades às quais o conjunto de Azure Cosmos DB (API do SQL) dá suporte. 

Para obter uma lista completa das seções e propriedades que estão disponíveis para definir conjuntos de os, consulte [conjuntos de valores e serviços vinculados](concepts-datasets-linked-services.md). 

Para copiar dados de ou para Azure Cosmos DB (API do SQL), defina a propriedade **Type** do conjunto de dados como **DocumentDbCollection**. As propriedades a seguir têm suporte:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** do conjunto de conjuntos deve ser definida como **DocumentDbCollection**. |Sim |
| collectionName |O nome da coleção de documentos Azure Cosmos DB. |Sim |

**Exemplo**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Esquema por Data Factory

Para armazenamentos de dados sem esquema como Azure Cosmos DB, a atividade de cópia infere o esquema de uma das maneiras descritas na lista a seguir. A menos que você queira [importar ou exportar documentos JSON no estado em que se encontram](#import-or-export-json-documents), a prática recomendada é especificar a estrutura dos dados na seção **estrutura** .

* Se você especificar a estrutura de dados usando a propriedade de **estrutura** na definição do conjunto, data Factory honrará essa estrutura como o esquema. 

    Se uma linha não contiver um valor para uma coluna, um valor nulo será fornecido para o valor da coluna.
* Se você não especificar a estrutura de dados usando a propriedade de **estrutura** na definição do conjunto, o serviço de data Factory inferirá o esquema usando a primeira linha nos dados. 

    Se a primeira linha não contiver o esquema completo, algumas colunas estarão ausentes no resultado da operação de cópia.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Esta seção fornece uma lista de propriedades com suporte do coletor e da fonte do Azure Cosmos DB (API do SQL).

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (API do SQL) como fonte

Para copiar dados de Azure Cosmos DB (API do SQL), defina o tipo de **fonte** na atividade de cópia como **DocumentDbCollectionSource**. 

As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **DocumentDbCollectionSource**. |Sim |
| consulta |Especifique a consulta de Azure Cosmos DB para ler os dados.<br/><br/>Exemplo:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificado, essa instrução SQL será executada: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Um caractere especial que indica que o documento está aninhado e como mesclar o conjunto de resultados.<br/><br/>Por exemplo, se uma consulta Azure Cosmos DB retornar o resultado aninhado `"Name": {"First": "John"}`, a atividade de cópia identificará o nome da coluna como `Name.First`, com o valor "John", quando o valor de **nestedSeparator** for **.** (ponto). |Não<br />(o padrão é **.** (ponto)) |

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (API do SQL) como coletor

Para copiar dados para Azure Cosmos DB (API do SQL), defina o tipo de **coletor** na atividade de cópia como **DocumentDbCollectionSink**. 

As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** do coletor da atividade de cópia deve ser definida como **DocumentDbCollectionSink**. |Sim |
| WriteBehavior |Descreve como gravar dados no Azure Cosmos DB. Valores permitidos: **Insert** e **Upsert**.<br/><br/>O comportamento de **Upsert** é substituir o documento se já existir um documento com a mesma ID; caso contrário, insira o documento.<br /><br />**Observação**: data Factory gera automaticamente uma ID para um documento se uma ID não é especificada no documento original ou no mapeamento de coluna. Isso significa que você deve garantir que, para **Upsert** funcione conforme o esperado, o documento tem uma ID. |Não<br />(o padrão é **Inserir**) |
| writeBatchSize | Data Factory usa a [biblioteca de executores em massa Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para gravar dados no Azure Cosmos DB. A propriedade **writeBatchSize** controla o tamanho dos documentos que o ADF fornece à biblioteca. Você pode tentar aumentar o valor de **writeBatchSize** para melhorar o desempenho e diminuir o valor se o tamanho do documento for grande – consulte as dicas abaixo. |Não<br />(o padrão é **10.000**) |
| nestingSeparator |Um caractere especial no nome da coluna de **origem** que indica que um documento aninhado é necessário. <br/><br/>Por exemplo, `Name.First` na estrutura do conjunto de dados de saída gera a seguinte estrutura JSON no documento Azure Cosmos DB quando o **nestedSeparator** é **.** (ponto): `"Name": {"First": "[value maps to this column from source]"}`  |Não<br />(o padrão é **.** (ponto)) |
| disableMetricsCollection | Data Factory coleta métricas como Cosmos DB RUs para otimização e recomendações de desempenho de cópia. Se você estiver preocupado com esse comportamento, especifique `true` para desativá-lo. | Não (o padrão é `false`) |

>[!TIP]
>Cosmos DB limita o tamanho de uma única solicitação para 2MB. A fórmula é o tamanho da solicitação = tamanho do documento único * gravar tamanho do lote. Se você clicar em erro dizendo **"o tamanho da solicitação é muito grande".** **reduza o valor `writeBatchSize`** na configuração do coletor de cópia.

**Exemplo**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```
## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="import-or-export-json-documents"></a>Importar ou exportar documentos JSON

Você pode usar esse conector de Azure Cosmos DB (API do SQL) para facilmente:

* Importe documentos JSON de várias fontes para Azure Cosmos DB, incluindo do armazenamento de BLOBs do Azure, Azure Data Lake Store e outros repositórios baseados em arquivo que o Azure Data Factory suporta.
* Exportar documentos JSON de uma coleção de Azure Cosmos DB para vários repositórios baseados em arquivo.
* Copie documentos entre duas coleções de Azure Cosmos DB como estão.

Para obter uma cópia independente de esquema:

* Ao usar a ferramenta Copiar Dados, selecione a opção **exportar como está para arquivos JSON ou cosmos DB coleção** .
* Quando você usa a criação de atividade, não especifique a seção **estrutura** (também chamada de *esquema*) no conjunto de dados Azure Cosmos DB. Além disso, não especifique a propriedade **nestingSeparator** na fonte de Azure Cosmos DB ou no coletor na atividade de cópia. Ao importar de ou exportar para arquivos JSON, no conjunto de armazenamento do repositório de arquivos correspondente, especifique o tipo de **formato** como **JsonFormat** e configure o **padrão** de arquivo conforme descrito na seção [formato JSON](supported-file-formats-and-compression-codecs.md#json-format) . Em seguida, não especifique a seção de **estrutura** e ignore o restante das configurações de formato.

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de armazenamentos de dados com suporte da atividade de cópia como fontes e coletores no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
