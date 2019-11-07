---
title: Mover dados para/da tabela do Azure
description: Saiba como mover dados de/para o armazenamento de tabelas do Azure usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 83f3a34a9b902b3a0e3b3ded34e36c8cbf50ed89
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683065"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Mover dados de e para a tabela do Azure usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-azure-table-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-table-storage.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector de armazenamento de tabela do Azure na v2](../connector-azure-table-storage.md).

Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de/para o armazenamento de tabelas do Azure. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia. 

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o armazenamento de tabelas do Azure ou do armazenamento de tabelas do Azure para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes ou coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados de/para um armazenamento de tabelas do Azure usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Confira o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções detalhadas para criar um pipeline com uma atividade de cópia. 

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor: 

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia. 
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída. 

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON. Para obter exemplos com definições de JSON para Data Factory entidades que são usadas para copiar dados de/para um armazenamento de tabelas do Azure, consulte a seção [exemplos de JSON](#json-examples) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para o armazenamento de tabelas do Azure: 

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
Há dois tipos de serviços vinculados que você pode usar para vincular um armazenamento de BLOBs do Azure a uma data factory do Azure. Eles são: serviço vinculado **AzureStorage** e serviço vinculado **AzureStorageSas** . O serviço vinculado do armazenamento do Azure fornece o data factory com acesso global ao armazenamento do Azure. Enquanto que o serviço vinculado do SAS do armazenamento do Azure (assinatura de acesso compartilhado) fornece o data factory com acesso restrito/limitado ao armazenamento do Azure. Não há outras diferenças entre esses dois serviços vinculados. Escolha o serviço vinculado que atenda às suas necessidades. As seções a seguir fornecem mais detalhes sobre esses dois serviços vinculados.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Propriedades de DataSet
Para obter uma lista completa das seções & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOB do Azure, tabela do Azure, etc.).

A seção typeproperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção **typeproperties** do conjunto de um do tipo **azuretable** tem as propriedades a seguir.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância do banco de dados de tabela do Azure à qual o serviço vinculado se refere. |Sim. Quando um TableName é especificado sem um azureTableSourceQuery, todos os registros da tabela são copiados para o destino. Se um azureTableSourceQuery também for especificado, os registros da tabela que satisfazem a consulta serão copiados para o destino. |

### <a name="schema-by-data-factory"></a>Esquema por Data Factory
Para armazenamentos de dados sem esquema, como a tabela do Azure, o serviço de Data Factory infere o esquema de uma das seguintes maneiras:

1. Se você especificar a estrutura de dados usando a propriedade de **estrutura** na definição do conjunto, o serviço data Factory honrará essa estrutura como o esquema. Nesse caso, se uma linha não contiver um valor para uma coluna, um valor nulo será fornecido para ele.
2. Se você não especificar a estrutura de dados usando a propriedade de **estrutura** na definição do conjunto, data Factory inferirá o esquema usando a primeira linha nos dados. Nesse caso, se a primeira linha não contiver o esquema completo, algumas colunas serão perdidas no resultado da operação de cópia.

Portanto, para fontes de dados sem esquema, a prática recomendada é especificar a estrutura de dados usando a propriedade **Structure** .

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, conjuntos de dados de entrada e saída e políticas estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção typeproperties da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

**AzureTableSource** dá suporte às seguintes propriedades na seção typeproperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableSourceQuery |Use a consulta personalizada para ler os dados. |Cadeia de consulta de tabela do Azure. Consulte os exemplos na próxima seção. |Não. Quando um TableName é especificado sem um azureTableSourceQuery, todos os registros da tabela são copiados para o destino. Se um azureTableSourceQuery também for especificado, os registros da tabela que satisfazem a consulta serão copiados para o destino. |
| azureTableSourceIgnoreTableNotFound |Indica se a assimilação da exceção da tabela não existe. |TRUE<br/>FOR |Não |

### <a name="azuretablesourcequery-examples"></a>exemplos de azureTableSourceQuery
Se a coluna da tabela do Azure for do tipo cadeia de caracteres:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Se a coluna da tabela do Azure for do tipo DateTime:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** dá suporte às seguintes propriedades na seção typeproperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Valor de chave de partição padrão que pode ser usado pelo coletor. |Um valor de cadeia de caracteres. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são usados como chaves de partição. Se não for especificado, AzureTableDefaultPartitionKeyValue será usado como a chave de partição. |Um nome de coluna. |Não |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores de coluna são usados como chave de linha. Se não for especificado, use um GUID para cada linha. |Um nome de coluna. |Não |
| azureTableInsertType |O modo para inserir dados na tabela do Azure.<br/><br/>Essa propriedade controla se as linhas existentes na tabela de saída com as chaves de partição e de linha correspondentes têm seus valores substituídos ou mesclados. <br/><br/>Para saber mais sobre como essas configurações (Mesclar e substituir) funcionam, confira os tópicos [Inserir ou mesclar entidade](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [Inserir ou substituir entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx) . <br/><br> Essa configuração se aplica ao nível de linha, não ao nível de tabela e nenhuma das opções exclui as linhas na tabela de saída que não existem na entrada. |Mesclar (padrão)<br/>Substitua |Não |
| writeBatchSize |Insere dados na tabela do Azure quando writeBatchSize ou writeBatchTimeout é atingido. |Inteiro (número de linhas) |Não (padrão: 10000) |
| writeBatchTimeout |Insere dados na tabela do Azure quando writeBatchSize ou writeBatchTimeout é atingido |período<br/><br/>Exemplo: "00:20:00" (20 minutos) |Não (padrão para o valor de tempo limite padrão do cliente de armazenamento 90 s) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mapeie uma coluna de origem para uma coluna de destino usando a propriedade JSON do tradutor antes de poder usar a coluna de destino como azureTablePartitionKeyName.

No exemplo a seguir, a coluna de origem divisionid é mapeada para a coluna de destino: divisionid.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
O divisionid é especificado como a chave de partição.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>Exemplos de JSON
Os exemplos a seguir fornecem exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para o armazenamento de tabelas do Azure e o banco de dados de blob do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores com suporte. Para obter mais informações, consulte a seção "armazenamentos e formatos de dados com suporte" em [mover dados usando a atividade de cópia](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Exemplo: copiar dados da tabela do Azure para o blob do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (usado para a tabela & BLOB).
2. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [azuretable](#dataset-properties).
3. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa AzureTableSource e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados que pertencem à partição padrão em uma tabela do Azure para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

**Serviço vinculado do armazenamento do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
O Azure Data Factory dá suporte a dois tipos de serviços vinculados do armazenamento do Azure: **AzureStorage** e **AzureStorageSas**. Para o primeiro, especifique a cadeia de conexão que inclui a chave de conta e, para o mais recente, especifique o URI de SAS (assinatura de acesso compartilhado). Consulte a seção [Serviços vinculados](#linked-service-properties) para obter detalhes.  

**Conjunto de dados de entrada de tabela do Azure:**

O exemplo supõe que você criou uma tabela "MyTable" na tabela do Azure.

A configuração "external": "true" informa ao serviço de Data Factory que o conjunto de os é externo ao data factory e não é produzido por uma atividade no data factory.

```JSON
{
  "name": "AzureTableInput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de resultados de saída de blob do Azure:**

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Atividade de cópia em um pipeline com AzureTableSource e BlobSink:**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **AzureTableSource** e o tipo de **coletor** está definido como **BlobSink**. A consulta SQL especificada com a propriedade **AzureTableSourceQuery** seleciona os dados da partição padrão a cada hora para copiar.

```JSON
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureTableSource",
                        "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Exemplo: copiar dados do blob do Azure para a tabela do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (usado para o blob de & de tabela)
2. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Um [conjunto](data-factory-create-datasets.md) de resultados de saída do tipo [azuretable](#dataset-properties).
4. O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa [blobname](data-factory-azure-blob-connector.md#copy-activity-properties) e [AzureTableSink](#copy-activity-properties).

O exemplo copia dados de série temporal de um blob do Azure para uma tabela do Azure por hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

**Serviço vinculado do armazenamento do Azure (para o blob de & de tabelas do Azure):**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

O Azure Data Factory dá suporte a dois tipos de serviços vinculados do armazenamento do Azure: **AzureStorage** e **AzureStorageSas**. Para o primeiro, especifique a cadeia de conexão que inclui a chave de conta e, para o mais recente, especifique o URI de SAS (assinatura de acesso compartilhado). Consulte a seção [Serviços vinculados](#linked-service-properties) para obter detalhes.

**Conjunto de dados de entrada de blob do Azure:**

Os dados são coletados de um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte de ano, mês e dia da hora de início e o nome do arquivo usa a parte de hora da hora de início. a configuração "external": "true" informa ao serviço de Data Factory que o conjunto de os é externo ao data factory e não é produzido por uma atividade no data factory.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de resultados de saída de tabela do Azure:**

O exemplo copia dados para uma tabela chamada "MyTable" na tabela do Azure. Crie uma tabela do Azure com o mesmo número de colunas que você espera que o arquivo CSV de blob contenha. Novas linhas são adicionadas à tabela a cada hora.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Atividade de cópia em um pipeline com Blobname e AzureTableSink:**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** está definido como **blobname** e o tipo de **coletor** está definido como **AzureTableSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "AzureTableSink",
            "writeBatchSize": 100,
            "writeBatchTimeout": "01:00:00"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
## <a name="type-mapping-for-azure-table"></a>Mapeamento de tipo para tabela do Azure
Conforme mencionado no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões automáticas dos tipos de origem nos tipos de coletor com a seguinte abordagem de duas etapas.

1. Converter de tipos de origem nativos em tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados para & da tabela do Azure, os seguintes [mapeamentos definidos pelo serviço tabela do Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) são usados dos tipos OData de tabela do Azure para o tipo .net e vice-versa.

| Tipo de dados OData | Tipo .NET | Detalhes |
| --- | --- | --- |
| EDM. Binary |Byte [] |Uma matriz de bytes de até 64 KB. |
| Edm.Boolean |booleano |Um valor booliano. |
| EDM. DateTime |DateTime |Um valor de 64 bits expresso como UTC (tempo Universal Coordenado). O intervalo de DateTime com suporte começa de 12:00 a meia-noite, 1º de janeiro de 1601 D.C. (C.E.), UTC. O intervalo termina em 31 de dezembro de 9999. |
| Edm.Double |double |Um valor de ponto flutuante de 64 bits. |
| EDM. GUID |GUID |Um identificador global exclusivo de 128 bits. |
| Edm.Int32 |Int32 |Um inteiro de 32 bits. |
| Edm.Int64 |Int64 |Um inteiro de 64 bits. |
| Edm.String |String |Um valor codificado em UTF-16. Os valores de cadeia de caracteres podem ser de até 64 KB. |

### <a name="type-conversion-sample"></a>Exemplo de conversão de tipo
O exemplo a seguir é para copiar dados de um blob do Azure para uma tabela do Azure com conversões de tipo.

Suponha que o conjunto de BLOBs esteja no formato CSV e contenha três colunas. Uma delas é uma coluna DateTime com um formato de data e hora personalizado usando nomes abreviados em francês para o dia da semana.

Defina o conjunto de fonte do blob como segue com definições de tipo para as colunas.

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
        "structure":
        [
            { "name": "userid", "type": "Int64"},
            { "name": "name", "type": "String"},
            { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Considerando o mapeamento de tipo do tipo OData de tabela do Azure para o tipo .NET, você definiria a tabela na tabela do Azure com o esquema a seguir.

**Esquema de tabela do Azure:**

| Nome da coluna | Tipo |
| --- | --- |
| ID |Edm.Int64 |
| nome |Edm.String |
| lastlogindate |EDM. DateTime |

Em seguida, defina o conjunto de banco de uma tabela do Azure da seguinte maneira. Você não precisa especificar a seção "estrutura" com as informações de tipo, pois as informações de tipo já estão especificadas no armazenamento de dados subjacente.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

Nesse caso, Data Factory automaticamente faz conversões de tipo, incluindo o campo DateTime com o formato DateTime personalizado usando a cultura "fr-fr" ao mover dados do blob para a tabela do Azure.

> [!NOTE]
> Para mapear colunas do conjunto de fonte de origem para colunas do conjunto de coleta, consulte [mapeando colunas do conjunto de linhas no Azure data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure Data Factory e várias maneiras de otimizá-lo, consulte [Guia de ajuste do desempenho da atividade de cópia &](data-factory-copy-activity-performance.md).
