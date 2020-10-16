---
title: Mover dados de/para a Tabela Azure
description: Saiba como mover dados de/para o Azure Table Storage utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 462d54a9d89d6f03aed5e221fa02609da786c8c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84702333"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Mover dados de e para a Azure Table usando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-azure-table-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-table-storage.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector de armazenamento de mesa Azure em V2](../connector-azure-table-storage.md).

Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de/para o Armazenamento da Tabela Azure. Baseia-se no artigo de Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia. 

Pode copiar dados de qualquer loja de dados de origem suportada para o Azure Table Storage ou do Azure Table Storage para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes ou sumidouros pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para um Azure Table Storage utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia. 

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios: 

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. 
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON. Para amostras com definições JSON para entidades da Data Factory que são usadas para copiar dados de/para um Armazenamento de Mesa Azure, consulte a secção de [exemplos JSON](#json-examples) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas para o Armazenamento de MesaS Azure: 

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
Existem dois tipos de serviços ligados que pode usar para ligar um armazenamento de bolhas Azure a uma fábrica de dados Azure. São: Serviço ligado **a AzureStorage** e serviço ligado **AzureStorageSas.** O serviço Azure Storage ligado fornece à fábrica de dados acesso global ao Azure Storage. Enquanto que o serviço ligado ao Azure Storage SAS (Shared Access Signature) fornece à fábrica de dados acesso restrito/limitado ao armazenamento Azure. Não existem outras diferenças entre estes dois serviços ligados. Escolha o serviço ligado que se adequa às suas necessidades. As seguintes secções fornecem mais detalhes sobre estes dois serviços ligados.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, Azure blob, Azure table, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção **typeProperties** para o conjunto de dados do tipo **AzureTable** tem as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na instância Azure Table Database a que o serviço ligado se refere. |Sim. Quando um nome de mesa é especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se for também especificado um azureTableSourceQuery, os registos da tabela que satisfaz a consulta são copiados para o destino. |

### <a name="schema-by-data-factory"></a>Schema by Data Factory
Para lojas de dados isentas de esquemas, como a Tabela Azure, o serviço Data Factory infere o esquema de uma das seguintes formas:

1. Se especificar a estrutura dos dados utilizando a propriedade da **estrutura** na definição de conjunto de dados, o serviço Data Factory honra esta estrutura como o esquema. Neste caso, se uma linha não contiver um valor para uma coluna, é previsto um valor nulo para ela.
2. Se não especificar a estrutura dos dados utilizando a propriedade da **estrutura** na definição de conjunto de dados, a Data Factory infere o esquema utilizando a primeira linha dos dados. Neste caso, se a primeira linha não contiver o esquema completo, algumas colunas são perdidas no resultado do funcionamento da cópia.

Portanto, para fontes de dados isentas de esquemas, a melhor prática é especificar a estrutura dos dados utilizando a propriedade da **estrutura.**

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, conjuntos de dados de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na secção de tipoProperties da atividade, por outro lado, variam com cada tipo de atividade. Para a atividade copy, variam dependendo dos tipos de fontes e pias.

**O AzureTableSource** suporta as seguintes propriedades na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableSourceQuery |Utilize a consulta personalizada para ler dados. |Corda de consulta de mesa azul. Veja os exemplos na secção seguinte. |N.º Quando um nome de mesa é especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se for também especificado um azureTableSourceQuery, os registos da tabela que satisfaz a consulta são copiados para o destino. |
| azureTableSourceIgnoreTableNotFound |Indicar se engolir a exceção da tabela não existe. |TRUE<br/>FALSE |Não |

### <a name="azuretablesourcequery-examples"></a>exemplos azureTableSourceQuery
Se a coluna da tabela Azure for do tipo de corda:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Se a coluna da tabela Azure for do tipo de data:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**O AzureTableSink** suporta as seguintes propriedades na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Valor da chave de partição padrão que pode ser usado pela pia. |Um valor de corda. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são utilizados como teclas de partição. Se não for especificado, a AzureTableDefaultPartitionKeyValue é utilizada como chave de partição. |Um nome de coluna. |Não |
| nome azureTableRowKey |Especifique o nome da coluna cujos valores de coluna são utilizados como chave de linha. Se não for especificado, utilize um GUID para cada linha. |Um nome de coluna. |Não |
| azureTableInsertType |O modo de inserir dados na tabela Azure.<br/><br/>Esta propriedade controla se as linhas existentes na tabela de saída com chaves de divisórias e linha correspondentes têm os seus valores substituídos ou fundidos. <br/><br/>Para saber como funcionam estas configurações (fundir e substituir), consulte [inserir ou fundir tópicos da Entidade](https://msdn.microsoft.com/library/azure/hh452241.aspx) e inserir ou [substituir](https://msdn.microsoft.com/library/azure/hh452242.aspx) entidades. <br/><br> Esta definição aplica-se ao nível da linha, não ao nível da tabela, e nenhuma das opções elimina linhas na tabela de saída que não existem na entrada. |fusão (padrão)<br/>substituir |Não |
| escreverBatchSize |Insere dados na tabela Azure quando a escritaBatchSize ou escreverBatchTimeout é atingida. |Inteiro (número de linhas) |Não (padrão: 10000) |
| escreverBatchTimeout |Insere dados na tabela Azure quando a escritaBatchSize ou escreverBatchTimeout é atingido |timespan<br/><br/>Exemplo: "00:20:00" (20 minutos) |Não (Padrão para armazenamento valor de tempo limite 90 seg) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mapear uma coluna de origem para uma coluna de destino usando a propriedade do tradutor JSON antes de poder usar a coluna de destino como o nome AzureTablePartitionKeyName.

No exemplo seguinte, a coluna de origem DivisionID é mapeada para a coluna de destino: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
O DivisionID é especificado como a chave de partição.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>Exemplos JSON
Os exemplos a seguir fornecem definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para Azure Table Storage e Azure Blob Database. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos lavatórios suportados. Para obter mais informações, consulte a secção "Lojas de dados suportadas e formatos" em [dados movendo-se utilizando a Copy Activity](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Exemplo: Copiar dados da Tabela Azure para Azure Blob
A seguinte amostra mostra:

1. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (utilizado para ambas as & blob de mesa).
2. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureTable](#dataset-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. O [pipeline](data-factory-create-pipelines.md) com atividade Copy que utiliza AzureTableSource e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados pertencentes à partição padrão numa Tabela Azure a uma bolha a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado a armazenamento Azure:**

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
A Azure Data Factory suporta dois tipos de serviços ligados ao Azure Storage: **AzureStorage** e **AzureStorageSas**. Para a primeira, especifique a cadeia de ligação que inclui a chave de conta e para a posterior, especifica a Assinatura de Acesso Partilhado (SAS) Uri. Consulte a secção [Serviços Ligados](#linked-service-properties) para mais detalhes.  

**Conjunto de dados de entrada da tabela Azure:**

A amostra pressupõe que criou uma tabela "MyTable" na Tabela Azure.

Definição "externa": "verdadeiro" informa o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base na hora de início da fatia que está a ser processada. O caminho da pasta utiliza partes do ano, mês, dia e horas da hora de início.

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

**Copiar a atividade num oleoduto com a AzureTableSource e blobSink:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **AzureTableSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada com a propriedade **AzureTableSourceQuery** seleciona os dados da partição padrão a cada hora para copiar.

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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Exemplo: Copiar dados da Azure Blob para a tabela Azure
A seguinte amostra mostra:

1. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (utilizado para ambos os & blob de mesa)
2. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureTable](#dataset-properties).
4. O [pipeline](data-factory-create-pipelines.md) com atividade copy que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [AzureTableSink](#copy-activity-properties).

A amostra copia os dados da série de tempo de uma bolha Azure para uma mesa Azure de hora a hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado azure (para a tabela Azure & Blob):**

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

A Azure Data Factory suporta dois tipos de serviços ligados ao Azure Storage: **AzureStorage** e **AzureStorageSas**. Para a primeira, especifique a cadeia de ligação que inclui a chave de conta e para a posterior, especifica a Assinatura de Acesso Partilhado (SAS) Uri. Consulte a secção [Serviços Ligados](#linked-service-properties) para mais detalhes.

**Conjunto de dados de entrada Azure Blob:**

Os dados são recolhidos a partir de uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base na hora de início da fatia que está a ser processada. O percurso da pasta utiliza a parte do ano, mês e dia da hora de início e o nome do ficheiro utiliza a parte da hora da hora de início. Definição "externa": a definição "verdadeira" informa o serviço data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída da tabela Azure:**

A amostra copia dados para uma tabela chamada "MyTable" na Tabela Azure. Crie uma tabela Azure com o mesmo número de colunas que espera que o ficheiro Blob CSV contenha. Novas filas são adicionadas à mesa a cada hora.

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

**Copiar a atividade num oleoduto com BlobSource e AzureTableSink:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **BlobSource** e o tipo **de pia** é definido para **AzureTableSink**.

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
## <a name="type-mapping-for-azure-table"></a>Mapeamento do tipo para a mesa Azure
Como mencionado no artigo [de atividades](data-factory-data-movement-activities.md) de movimento de dados, a atividade copy realiza conversões automáticas de tipo de origem para tipos de pia com a seguinte abordagem em duas etapas.

1. Converter de tipos de origem nativa para .NET tipo
2. Converter de tipo .NET para tipo de pia nativa

Ao mover dados para & a partir da Tabela Azure, os [seguintes mapeamentos definidos pelo serviço Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) são utilizados desde os tipos OData da Tabela Azure para .TIPO NET e vice-versa.

| Tipo de Dados OData | .NET Tipo | Detalhes |
| --- | --- | --- |
| Edm.Binary |byte[] |Uma variedade de bytes até 64 KB. |
| Edm.Boolean |bool |Um valor booleano. |
| Edm.DateTime |DateTime |Um valor de 64 bits expresso como Tempo Universal Coordenado (UTC). A gama de Datas suportada começa a partir das 12:00 da meia-noite, 1 de janeiro de 1601 d.C. UTC. O intervalo termina em 31 de dezembro de 9999. |
| Edm.Double |double |Um valor de ponto flutuante de 64 bits. |
| Edm.Guid |GUID |Um identificador globalmente único de 128 bits. |
| Edm.Int32 |Int32 |Um inteiro de 32 bits. |
| Edm.Int64 |Int64 |Um inteiro de 64 bits. |
| Edm.String |Cadeia |Um valor codificado UTF-16. Os valores das cordas podem chegar a 64 KB. |

### <a name="type-conversion-sample"></a>Amostra de conversão do tipo
A amostra a seguir destina-se a copiar dados de uma Bolha Azure para Azure Table com conversões de tipo.

Suponha que o conjunto de dados Blob está no formato CSV e contém três colunas. Um deles é uma coluna de datas com um formato de data-hora personalizado usando nomes franceses abreviados para o dia da semana.

Defina o conjunto de dados Blob Source da seguinte forma, juntamente com definições de tipo para as colunas.

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
Dado o mapeamento do tipo Azure Table OData para o tipo .NET, definiria a tabela em Azure Table com o seguinte esquema.

**Esquema da tabela Azure:**

| Nome da coluna | Tipo |
| --- | --- |
| userid |Edm.Int64 |
| name |Edm.String |
| lastlogie |Edm.DateTime |

Em seguida, defina o conjunto de dados da Tabela Azure da seguinte forma. Não é necessário especificar a secção "estrutura" com a informação do tipo, uma vez que a informação do tipo já está especificada na loja de dados subjacente.

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

Neste caso, a Data Factory faz automaticamente conversões de tipo, incluindo o campo Datetime com o formato de data-hora personalizado utilizando a cultura "fr-fr" ao mover dados de Blob para Azure Table.

> [!NOTE]
> Para mapear colunas de conjunto de dados de origem para colunas a partir do conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Performance e Afinação
Para conhecer os principais fatores que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo, consulte [copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md).
