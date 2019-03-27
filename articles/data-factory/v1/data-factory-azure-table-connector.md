---
title: Mover dados de/para tabelas do Azure | Documentos da Microsoft
description: Aprenda a mover dados de/para o armazenamento de tabelas do Azure com o Azure Data Factory.
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
ms.openlocfilehash: aed341c50332b424a1149c129629cd451a4e5133
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500088"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Mover dados de e para tabelas do Azure com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-azure-table-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-table-storage.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector de armazenamento de tabelas do Azure no V2](../connector-azure-table-storage.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de/para o armazenamento de tabelas do Azure. Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia. 

Pode copiar dados a partir de qualquer arquivo de dados de origem suportada para o armazenamento de tabelas do Azure ou de armazenamento de tabelas do Azure para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de/para um armazenamento de tabelas do Azure com ferramentas/APIs diferentes.

A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink: 

1. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. 
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON. Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de/para o armazenamento de tabela do Azure, consulte [exemplos JSON](#json-examples) seção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON, que são utilizadas para definir entidades do Data Factory específicas para o armazenamento de tabelas do Azure: 

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
Existem dois tipos de serviços ligados, que pode utilizar para ligar um armazenamento de Blobs do Azure a uma fábrica de dados do Azure. São: **AzureStorage** serviço ligado e **AzureStorageSas** serviço ligado. O serviço ligado do armazenamento do Azure fornece a fábrica de dados com o acesso global para o armazenamento do Azure. Ao passo que o Azure Storage SAS (assinatura de acesso partilhado) ligado o serviço fornece a fábrica de dados com acesso restrito/tempo-limite para o armazenamento do Azure. Não existem outras diferenças entre estes dois serviços ligados. Escolha o serviço ligado que atenda às suas necessidades. As secções seguintes fornecem mais detalhes sobre estes dois serviços ligados.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para o conjunto de dados do tipo **Azuretable{0}name** tem as seguintes propriedades.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância da base de dados do Azure tabela pelo serviço ligado refere-se. |Sim. Quando um tableName é especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se um azureTableSourceQuery também for especificado, os registos da tabela que satisfaz a consulta são copiados para o destino. |

### <a name="schema-by-data-factory"></a>Esquema pelo Data Factory
Para arquivos de dados sem esquema, como tabelas do Azure, o serviço Data Factory infere o esquema de uma das seguintes formas:

1. Se especificar a estrutura de dados utilizando o **estrutura** propriedade na definição do conjunto de dados, o serviço Data Factory respeita essa estrutura como o esquema. Neste caso, se uma linha não contém um valor para uma coluna, é fornecido um valor nulo para o mesmo.
2. Se não especificar a estrutura dos dados ao utilizar o **estrutura** propriedade na definição do conjunto de dados, o Data Factory infere o esquema ao utilizar a primeira linha dos dados. Neste caso, se a primeira linha contém o esquema completo, algumas colunas seja perdidas no resultado da operação de cópia.

Por conseguinte, para origens de dados sem esquema, a prática recomendada é especificar a estrutura de dados com o **estrutura** propriedade.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades como nome, descrição, entrada e saída de conjuntos de dados e as políticas estão disponíveis para todos os tipos de atividades.

Propriedades disponíveis na secção typeProperties da atividade variam, por outro lado, com cada tipo de atividade. Para a atividade de cópia, elas variam consoante os tipos de origens e sinks.

**AzureTableSource** suporta as seguintes propriedades na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableSourceQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta de tabela do Azure. Veja exemplos na próxima seção. |Não. Quando um tableName é especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se um azureTableSourceQuery também for especificado, os registos da tabela que satisfaz a consulta são copiados para o destino. |
| azureTableSourceIgnoreTableNotFound |Indica se assimilar a exceção da tabela não existe. |VERDADEIRO<br/>FALSO |Não |

### <a name="azuretablesourcequery-examples"></a>Exemplos de azureTableSourceQuery
Se a coluna de tabelas do Azure é do tipo de cadeia de caracteres:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Se a coluna de tabelas do Azure é do tipo datetime:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** suporta as seguintes propriedades na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Predefinição partição valor da chave que pode ser utilizado pelo sink. |Um valor de cadeia de caracteres. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são utilizados como as chaves de partição. Se não for especificado, AzureTableDefaultPartitionKeyValue é utilizado como a chave de partição. |Um nome de coluna. |Não |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores de coluna são utilizados como chave de linha. Se não for especificado, utilize um GUID para cada linha. |Um nome de coluna. |Não |
| azureTableInsertType |O modo de inserir dados na tabela do Azure.<br/><br/>Esta propriedade controla se as linhas existentes na tabela de saída com correspondentes chaves de partição e de linha têm seus valores substituído ou intercalado. <br/><br/>Para saber mais sobre o funcionamento destas definições (intercalação e substituir), veja [Insert ou entidade de intercalação](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [Insert ou substituir entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx) tópicos. <br/><br> Esta definição aplica-se ao nível da linha, não é o nível de tabela, e nenhuma dessas opções elimina linhas da tabela de saída que não existem na entrada. |Intercalar (predefinição)<br/>substituir |Não |
| writeBatchSize |Insere dados na tabela do Azure quando for atingido o writeBatchSize ou writeBatchTimeout. |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| writeBatchTimeout |Insere dados na tabela do Azure quando for atingido o writeBatchSize ou writeBatchTimeout |Período de tempo<br/><br/>Exemplo: "00: 20:00" (20 minutos) |Não (seg 90 de valor predefinido para o tempo limite de padrão de cliente de armazenamento) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mapear uma coluna de origem para uma coluna de destino usando o tradutor de propriedade JSON antes de poder utilizar a coluna de destino como o azureTablePartitionKeyName.

No exemplo seguinte, a coluna de origem DivisionID é mapeada para a coluna de destino: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
O DivisionID está especificada como a chave de partição.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>Exemplos JSON
Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para armazenamento de tabelas do Azure e base de dados do Azure Blob. No entanto, os dados podem ser copiados **diretamente** Coletores de partir de qualquer uma das origens qualquer suportadas. Para obter mais informações, consulte a secção "arquivos de dados suportados e formatos" em [mover dados com a atividade de cópia](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Exemplo: Copiar dados de tabelas do Azure para BLOBs do Azure
O exemplo a seguir mostra:

1. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (utilizado para tabelas e BLOBs).
2. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [Azuretable{0}name](#dataset-properties).
3. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza AzureTableSource e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia os dados que pertencem à partição predefinido numa tabela do Azure para um blob a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

**Serviço ligado do armazenamento do Azure:**

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
O Azure Data Factory suporta dois tipos de serviços de armazenamento do Azure ligada: **AzureStorage** e **AzureStorageSas**. Para o primeiro, especifica a cadeia de ligação que inclui a chave da conta e para aquele posterior, especifique o Uri de assinatura de acesso partilhado (SAS). Ver [serviços ligados](#linked-service-properties) secção para obter detalhes.  

**Conjunto de dados de entrada tabela do Azure:**

O exemplo parte do princípio de que criou uma tabela "MyTable" na tabela do Azure.

A definição "externo": "true" informa o serviço Data Factory que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

**Conjunto de dados de Blobs do Azure:**

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

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

**Atividade de cópia num pipeline com AzureTableSource e BlobSink:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **AzureTableSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada com **AzureTableSourceQuery** propriedade seleciona os dados da partição predefinida a cada hora para copiar.

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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Exemplo: Copiar dados de Blobs do Azure para tabelas do Azure
O exemplo a seguir mostra:

1. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (utilizado para tabelas e BLOBs)
2. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [Azuretable{0}name](#dataset-properties).
4. O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [AzureTableSink](#copy-activity-properties).

As cópias de exemplo blob de dados a partir do Azure de séries de tempo para do Azure de tabela por hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

**Serviço ligado do armazenamento do Azure (para tabelas do Azure e BLOBs):**

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

O Azure Data Factory suporta dois tipos de serviços de armazenamento do Azure ligada: **AzureStorage** e **AzureStorageSas**. Para o primeiro, especifica a cadeia de ligação que inclui a chave da conta e para aquele posterior, especifique o Uri de assinatura de acesso partilhado (SAS). Ver [serviços ligados](#linked-service-properties) secção para obter detalhes.

**Conjunto de dados de entrada BLOBs do Azure:**

Dados são captados um blob novo a cada hora (frequência: hora, intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e nome de ficheiro utiliza a parte de hora a hora de início. "externo": "true" definição informa o serviço Data Factory, que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída de tabelas do Azure:**

O exemplo copia dados a uma tabela chamada "MyTable" na tabela do Azure. Crie uma tabela do Azure com o mesmo número de colunas, como esperar que o ficheiro CSV de BLOBs para conter. Novas linhas são adicionadas à tabela de cada hora.

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

**Atividade de cópia num pipeline com BlobSource e AzureTableSink:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **BlobSource** e **sink** tipo está definido como **AzureTableSink**.

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
## <a name="type-mapping-for-azure-table"></a>Mapeamento do tipo de tabela do Azure
Conforme mencionado na [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia executa conversões de tipos automáticas de tipos de origem para o sink de tipos com a abordagem de dois passos seguintes.

1. Converter entre tipos de origem nativas para o tipo de .NET
2. Converter de tipo de .NET para o tipo de sink nativo

Ao mover dados para e a partir de tabelas do Azure, o seguinte procedimento [mapeamentos definidos pelo serviço de tabelas do Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) servem de tipos de OData de tabela do Azure para o tipo .NET e vice-versa.

| Tipo de dados do OData | Tipo de .NET | Detalhes |
| --- | --- | --- |
| Edm.Binary |byte[] |Uma matriz de bytes até 64 KB. |
| Edm.Boolean |booleano |Um valor booleano. |
| Edm.DateTime |DateTime |Um valor de 64 bits, expressado como Hora Universal Coordenada (UTC). O intervalo suportado de DateTime começa de 12:00 1 de Janeiro, de 1601 a.d. (E.C.), UTC. O intervalo de termina a 31 de Dezembro, 9999. |
| Edm.Double |double |Um valor de ponto flutuante de 64 bits. |
| Edm.Guid |Guid |Um identificador exclusivo global de 128 bits. |
| Edm.Int32 |Int32 |Um inteiro de 32 bits. |
| Edm.Int64 |Int64 |Um inteiro de 64 bits. |
| Edm.String |String |Um valor codificado em UTF-16. Valores de cadeia de caracteres podem ser até 64 KB. |

### <a name="type-conversion-sample"></a>Exemplo de conversão de tipo
O exemplo a seguir é para copiar dados de um Blob do Azure para tabelas do Azure com conversões de tipo.

Suponha que o conjunto de dados do Blob está num formato CSV e contém três colunas. Uma delas é uma coluna de datetime com um formato de datetime personalizadas com nomes abreviados de francês para o dia da semana.

Defina o conjunto de dados de origem do Blob da seguinte forma, juntamente com definições de tipo para as colunas.

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
Tendo em conta o mapeamento do tipo de tipo de OData de tabela do Azure para o tipo .NET, definiria a tabela em tabelas do Azure com o esquema abaixo.

**Esquema de tabela do Azure:**

| Nome da coluna | Type |
| --- | --- |
| ID de utilizador |Edm.Int64 |
| nome |Edm.String |
| lastlogindate |Edm.DateTime |

Em seguida, defina o conjunto de dados de tabelas do Azure da seguinte forma. Não é necessário especificar a seção de "estrutura" com as informações de tipo, uma vez que as informações de tipo já estão especificadas no arquivo de dados subjacente.

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

Neste caso, a fábrica de dados automaticamente escreva conversões, incluindo o campo de Datetime com o formato de datetime personalizadas utilizando a cultura "fr-fr", ao mover dados de Blob para tabelas do Azure.

> [!NOTE]
> Para mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e Otimização
Para saber mais sobre os principais fatores que afetará o desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo, consulte [desempenho de atividade de cópia e guia de ajuste](data-factory-copy-activity-performance.md).
