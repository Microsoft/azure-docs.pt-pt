---
title: Copiar dados de ou para o Azure Cosmos DB (API de SQL) com o Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de arquivos de dados de origem suportada para ou do Azure Cosmos DB (SQL API) para lojas de sink suportado com o Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: eca5e4cc96996c35e7c2181746cdb3de2e5a602c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61259522"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Copiar dados de ou para o Azure Cosmos DB (API de SQL) com o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory, que está a utilizar:"]
> * [Versão 1](v1/data-factory-azure-documentdb-connector.md)
> * [Versão atual](connector-azure-cosmos-db.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de e para o Azure Cosmos DB (SQL API). O artigo se baseia no [atividade de cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

>[!NOTE]
>Esse suporte apenas do conector copiar dados de/para Cosmos DB SQL API. Para a API de MongoDB, consulte [conector de API do Azure Cosmos DB para o MongoDB](connector-azure-cosmos-db-mongodb-api.md). Outros tipos de API não são agora suportados.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados do Azure Cosmos DB (SQL API) para qualquer arquivo de dados de sink suportados ou copiar dados de qualquer arquivo de dados de origem suportada para o Azure Cosmos DB (SQL API). Para obter uma lista de dados armazena se a atividade de cópia suporta como origens e sinks, consulte [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).

Pode utilizar o conector do Azure Cosmos DB (SQL API) para:

- Copiar dados de e para o Azure Cosmos DB [API do SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Escrever para o Azure Cosmos DB como **inserir** ou **upsert**.
- Importar e exportar documentos JSON como-é ou copiar dados de ou para um conjunto de dados em tabela. Os exemplos incluem uma base de dados SQL e um ficheiro CSV. Para copiar documentos como-é a partir de ficheiros JSON ou para ou a partir de outra coleção do Azure Cosmos DB, consulte a secção importar ou exportar documentos JSON.

Fábrica de dados se integra com o [biblioteca de executor do Azure Cosmos DB em massa](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para proporcionar o melhor desempenho quando escreve para o Azure Cosmos DB.

> [!TIP]
> O [vídeo de migração de dados](https://youtu.be/5-SRNiC_qOU) explica-lhe os passos para copiar dados do armazenamento de Blobs do Azure para o Azure Cosmos DB. O vídeo também descreve considerações de ajuste de desempenho para a ingestão de dados para o Azure Cosmos DB em geral.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que pode usar para definir entidades do Data Factory que são específicas para o Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço do Azure Cosmos DB (API de SQL) ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O **tipo** propriedade tem de ser definida como **CosmosDb**. | Sim |
| connectionString |Especifique as informações necessárias para ligar à base de dados do Azure Cosmos DB.<br />**Nota**: Tem de especificar informações de base de dados na cadeia de ligação, conforme mostrado nos exemplos que se seguem. <br/>Marca esse campo como uma SecureString armazena de forma segura no Data Factory. Também é possível incluir a chave da conta no Azure Key Vault e obter o `accountKey` configuração fora de cadeia de ligação. Consulte os exemplos seguintes e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a utilizar para ligar ao arquivo de dados. Pode usar o Runtime de integração do Azure ou um runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se esta propriedade não for especificada, é utilizada a predefinição de Runtime de integração do Azure. |Não |

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

**Exemplo: armazenar a chave de conta no Azure Key Vault**

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Esta seção fornece uma lista de propriedades que suporta o conjunto de dados do Azure Cosmos DB (SQL API). 

Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte [conjuntos de dados e serviços ligados](concepts-datasets-linked-services.md). 

Para copiar dados de ou para o Azure Cosmos DB (SQL API), defina o **tipo** propriedade do conjunto de dados para **DocumentDbCollection**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O **tipo** propriedade do conjunto de dados tem de ser definida como **DocumentDbCollection**. |Sim |
| collectionName |O nome da coleção de documentos do Azure Cosmos DB. |Sim |

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

### <a name="schema-by-data-factory"></a>Esquema pelo Data Factory

Para arquivos de dados sem esquema, como o Azure Cosmos DB, a atividade de cópia infere o esquema de uma das formas descritas na lista seguinte. A menos que queira [importar ou exportar documentos JSON como-é](#import-or-export-json-documents), a prática recomendada é especificar a estrutura de dados no **estrutura** secção.

* Se especificar a estrutura de dados utilizando o **estrutura** propriedade na definição do conjunto de dados, o Data Factory respeita essa estrutura como o esquema. 

    Se uma linha não contém um valor para uma coluna, é fornecido um valor nulo para o valor da coluna.
* Se não especificar a estrutura dos dados ao utilizar o **estrutura** propriedade na definição do conjunto de dados, o serviço Data Factory infere o esquema ao utilizar a primeira linha dos dados. 

    Se a primeira linha não contém o esquema completo, algumas colunas estará em falta no resultado da operação de cópia.

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Esta seção fornece uma lista de propriedades que suportam o Azure Cosmos DB (API de SQL) origem e sink.

Para obter uma lista completa de seções e as propriedades que estão disponíveis para a definição de atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>O Azure Cosmos DB (SQL API) como origem

Para copiar dados do Azure Cosmos DB (SQL API), defina o **origem** tipo de atividade de cópia para **DocumentDbCollectionSource**. 

As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O **tipo** propriedade da origem de atividade de cópia tem de ser definida como **DocumentDbCollectionSource**. |Sim |
| query |Especifique a consulta do Azure Cosmos DB para ler os dados.<br/><br/>Exemplo:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificado, essa instrução de SQL é executada: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Um caráter especial que indica que o documento está aninhado e como nivelamento o conjunto de resultados.<br/><br/>Por exemplo, se uma consulta do Azure Cosmos DB devolve o resultado aninhado `"Name": {"First": "John"}`, atividade de cópia identifica o nome da coluna como `Name.First`, com o valor "João", quando o **nestedSeparator** valor é **.** (ponto). |Não<br />(a predefinição é **.** (ponto)) |

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

### <a name="azure-cosmos-db-sql-api-as-sink"></a>O Azure Cosmos DB (SQL API) como sink

Para copiar dados para o Azure Cosmos DB (SQL API), defina o **sink** tipo de atividade de cópia para **DocumentDbCollectionSink**. 

As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O **tipo** propriedade do coletor de atividade de cópia tem de ser definida como **DocumentDbCollectionSink**. |Sim |
| writeBehavior |Descreve como escrever dados do Azure Cosmos DB. Valores permitidos: **inserir** e **upsert**.<br/><br/>O comportamento das **upsert** é substituir o documento se um documento com o mesmo ID já existe; caso contrário, insira o documento.<br /><br />**Nota**: Fábrica de dados gera automaticamente um ID de um documento se não for especificado um ID do documento original ou por mapeamento de colunas. Isso significa que é necessário garantir que, para **upsert** a funcionar conforme esperado, o seu documento tem um ID. |Não<br />(a predefinição é **inserir**) |
| writeBatchSize | O Data Factory utiliza a [biblioteca de executor do Azure Cosmos DB em massa](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para escrever dados do Azure Cosmos DB. O **writeBatchSize** propriedade controla o tamanho de documentos que fornecem do ADF para a biblioteca. Pode experimentar aumentar o valor para **writeBatchSize** para melhorar o desempenho e a diminuição do valor se o documento ser grandes de tamanho - veja abaixo dicas. |Não<br />(a predefinição é **10.000**) |
| nestingSeparator |Um caráter especial no **origem** nome da coluna que indica que um documento aninhado é necessária. <br/><br/>Por exemplo, `Name.First` do conjunto de dados de saída a estrutura gera a seguinte estrutura JSON no Azure Cosmos DB documentar quando o **nestedSeparator** é **.** (ponto): `"Name": {"First": "[value maps to this column from source]"}`  |Não<br />(a predefinição é **.** (ponto)) |

>[!TIP]
>O cosmos DB limita o tamanho de único pedido a 2MB. A fórmula é o tamanho do pedido = único Document Size * o tamanho do lote de escrita. Se tiver atingido o ditado de erro **"Tamanho pedido é demasiado grande."** , **reduzir a `writeBatchSize` valor** na configuração de sink de cópia.

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

## <a name="import-or-export-json-documents"></a>Importar ou exportar documentos JSON

Pode utilizar este conector do Azure Cosmos DB (SQL API) para facilmente:

* Importe documentos JSON de várias origens para o Azure Cosmos DB, incluindo a partir do armazenamento de Blobs do Azure, Azure Data Lake Store e outros arquivos baseados em ficheiros que suporte o Azure Data Factory.
* Exporte documentos JSON de uma coleção do Azure Cosmos DB para vários arquivos baseados em ficheiros.
* Copiar documentos entre duas coleções do Azure Cosmos DB como-é.

Para alcançar a cópia de esquema desconhecido:

* Quando utiliza a ferramenta copiar dados, selecione o **exportar como-é ficheiros JSON ou coleção do Cosmos DB** opção.
* Quando utiliza a atividade de criação, não especifique o **estrutura** (também denominado *esquema*) secção no conjunto de dados do Azure Cosmos DB. Além disso, não especifique o **nestingSeparator** propriedade na Azure Cosmos DB origem ou sink na atividade de cópia. Ao importar a partir de ou exportar para ficheiros JSON, no ficheiro correspondente armazenar o conjunto de dados, especifique a **formato** escreva como **JsonFormat** e configurar o **filePattern** como descrito no [formato JSON](supported-file-formats-and-compression-codecs.md#json-format) secção. Em seguida, não especifique o **estrutura** secção e ignore o resto das definições do formato.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista dos arquivos de dados que a atividade de cópia suporta como origens e sinks no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
