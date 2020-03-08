---
title: Mover dados para/a partir da Tabela Azure
description: Saiba como mover dados de/para o Armazenamento de Mesa Azure utilizando a Azure Data Factory.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387432"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Mover dados de e para a Tabela Azure utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-azure-table-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-table-storage.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector de armazenamento de mesa Azure em V2](../connector-azure-table-storage.md).

Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de/para o Armazenamento de Mesa Azure. Baseia-se no artigo Atividades do Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade de cópia. 

Pode copiar dados de qualquer loja de dados de origem suportada para o Armazenamento de Mesa Azure ou de Armazenamento de Mesa Azure para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados suportadas como fontes ou pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para um Armazenamento de Mesa Azure utilizando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é utilizar o **Assistente de Cópia**. Ver [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um pipeline utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia. 

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro: 

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia. 
3. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON. Para amostras com definições JSON para entidades da Fábrica de Dados que são usadas para copiar dados de/para um Armazenamento de Mesa Azure, consulte a secção de [exemplos jSON](#json-examples) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Fábrica de Dados específicas do Armazenamento de Tabelas Azure: 

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
Existem dois tipos de serviços ligados que pode utilizar para ligar um armazenamento de blob Azure a uma fábrica de dados Azure. São eles: Serviço ligado ao **AzureStorage** e serviço ligado ao **AzureStorageSas.** O serviço ligado ao Armazenamento Azure fornece à fábrica de dados acesso global ao Armazenamento Azure. Considerando que o serviço ligado ao armazenamento Azure SAS (Assinatura de Acesso Partilhado) fornece à fábrica de dados um acesso restrito/limitado ao armazenamento do Azure. Não existem outras diferenças entre estes dois serviços ligados. Escolha o serviço vinculado que se adapte às suas necessidades. As seguintes secções fornecem mais detalhes sobre estes dois serviços ligados.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntos de dados (Azure SQL, Azure blob, tabela Azure, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção **typeProperties** para o conjunto de dados do tipo **AzureTable** tem as seguintes propriedades.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância de base de dados de mesa azure a que o serviço ligado se refere. |Sim. Quando um nome de mesa é especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se for também especificado um azureTableSourceQuery, os registos da tabela que satisfaz a consulta são copiados para o destino. |

### <a name="schema-by-data-factory"></a>Esquema pelo Data Factory
Para lojas de dados sem esquemas, como a Tabela Azure, o serviço Data Factory infere o esquema de uma das seguintes formas:

1. Se especificar a estrutura dos dados utilizando a propriedade da **estrutura** na definição de conjunto de dados, o serviço Data Factory honra esta estrutura como o esquema. Neste caso, se uma linha não contiver um valor para uma coluna, é-lhe previsto um valor nulo.
2. Se não especificar a estrutura dos dados utilizando a propriedade da **estrutura** na definição de conjunto de dados, a Data Factory infere o esquema utilizando a primeira linha dos dados. Neste caso, se a primeira linha não contiver o esquema completo, algumas colunas são perdidas no resultado da operação de cópia.

Por conseguinte, para fontes de dados sem esquemas, a melhor prática é especificar a estrutura dos dados utilizando a propriedade da **estrutura.**

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Creating Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, conjuntos de dados de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na secção tipoPropriedades da atividade por outro lado variam com cada tipo de atividade. Para a atividade de Cópia, variam dependendo dos tipos de fontes e pias.

**O AzureTableSource** suporta as seguintes propriedades na secção TypeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableSourceQuery |Use a consulta personalizada para ler dados. |Corda de consulta de mesa azul. Veja exemplos na secção seguinte. |Não. Quando um nome de mesa é especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se for também especificado um azureTableSourceQuery, os registos da tabela que satisfaz a consulta são copiados para o destino. |
| azureTableSourceIgnoreTableNotFound |Indique se não existe a exceção da mesa. |TRUE<br/>FALSO |Não |

### <a name="azuretablesourcequery-examples"></a>exemplos azureTableSourceQuery
Se a coluna da tabela azure for do tipo de corda:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Se a coluna Azure Table for do tipo data:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**O AzureTableSink** suporta as seguintes propriedades na secção TypeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Valor da chave da divisória padrão que pode ser usado pela pia. |Um valor de corda. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são utilizados como teclas de partição. Se não especificado, o AzureTableDefaultPartitionKeyValue é utilizado como chave de partição. |Um nome de coluna. |Não |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores de coluna são utilizados como chave de linha. Se não especificado, utilize um GUID para cada linha. |Um nome de coluna. |Não |
| azureTableInsertType |O modo de inserir dados na tabela Azure.<br/><br/>Esta propriedade controla se as linhas existentes na tabela de saída com divisória seletiva e chaves de linha correspondentes têm os seus valores substituídos ou fundidos. <br/><br/>Para saber como funcionam estas definições (fundir e substituir), consulte [inserir ou fundir tópicos de Entidade](https://msdn.microsoft.com/library/azure/hh452241.aspx) e Inserir ou Substituir tópicos da [Entidade.](https://msdn.microsoft.com/library/azure/hh452242.aspx) <br/><br> Esta definição aplica-se ao nível da linha, não ao nível da tabela, e nenhuma das opções elimina linhas no quadro de saída que não existam na entrada. |fusão (padrão)<br/>substituir |Não |
| writeBatchSize |Insere os dados na tabela Azure quando o writeBatchSize ou o writeBatchTimeout for atingido. |Inteiro (número de linhas) |Não (padrão: 10000) |
| writeBatchTimeout |Insere os dados na tabela Azure quando o writeBatchSize ou o writeBatchTimeout são atingidos |TimeSpan<br/><br/>Exemplo: "00:20:00" (20 minutos) |Não (Predefinido para armazenar valor de tempo de saída do cliente 90 seg) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mapeie uma coluna de origem para uma coluna de destino utilizando a propriedade JSON do tradutor antes de poder utilizar a coluna de destino como o nome chave da tabela de divisórias azureTable.

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
## <a name="json-examples"></a>Exemplos jSON
Os exemplos seguintes fornecem definições JSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostram como copiar dados de e para o Azure Table Storage e Azure Blob Database. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer uma das pias suportadas. Para mais informações, consulte a secção "Lojas e formatos de dados suportados" em Mover dados utilizando a [Atividade de Cópia](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Exemplo: Copiar dados da tabela Azure para o Blob Azure
A amostra que se segue mostra:

1. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (utilizado tanto para mesa como para blob).
2. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureTable](#dataset-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. O [gasoduto](data-factory-create-pipelines.md) com a atividade Copy que utiliza o AzureTableSource e o [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados pertencentes à divisória padrão numa tabela Azure a uma bolha a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado ao armazenamento Azure:**

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
A Azure Data Factory suporta dois tipos de serviços ligados ao Armazenamento Azure: **AzureStorage** e **AzureStorageSas.** Para a primeira, especifica a cadeia de ligação que inclui a chave da conta e para a posterior, especifica a Assinatura de Acesso Partilhado (SAS) Uri. Consulte a secção [de Serviços Ligados](#linked-service-properties) para obter mais detalhes.  

**Conjunto de dados de entrada de tabela azure:**

A amostra pressupõe que criou uma tabela "MyTable" em Mesa Azure.

Definição "externa": "verdadeira" informa o serviço data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída de Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza partes ano, mês, dia e horas da hora de início.

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

**Copiar atividade num oleoduto com AzureTableSource e BlobSink:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **AzureTableSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada com a propriedade **AzureTableSourceQuery** seleciona os dados da divisória predefinida de hora a hora para copiar.

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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Exemplo: Copiar dados de Azure Blob para Tabela Azure
A amostra que se segue mostra:

1. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (utilizado tanto para mesa como para blob)
2. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureTable](#dataset-properties).
4. O [gasoduto](data-factory-create-pipelines.md) com a atividade Copy que utiliza [blobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [AzureTableSink](#copy-activity-properties).

A amostra copia dados da série temporal de uma bolha Azure para uma mesa Azure de hora a hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado ao armazenamento Azure (tanto para a Tabela Azure como para a Blob):**

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

A Azure Data Factory suporta dois tipos de serviços ligados ao Armazenamento Azure: **AzureStorage** e **AzureStorageSas.** Para a primeira, especifica a cadeia de ligação que inclui a chave da conta e para a posterior, especifica a Assinatura de Acesso Partilhado (SAS) Uri. Consulte a secção [de Serviços Ligados](#linked-service-properties) para obter mais detalhes.

**Conjunto de dados de entrada Azure Blob:**

Os dados são recolhidos a partir de uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e o nome do ficheiro utiliza a parte da hora da hora de início. "externa": a definição "verdadeira" informa o serviço data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

A amostra copia dados para uma tabela chamada "MyTable" em Tabela Azure. Crie uma tabela Azure com o mesmo número de colunas que espera que o ficheiro Blob CSV contenha. Novas filas são adicionadas à mesa a cada hora.

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

**Copiar atividade num oleoduto com blobSource e AzureTableSink:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **BlobSource** e o tipo **de pia** está definido para **AzureTableSink**.

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
## <a name="type-mapping-for-azure-table"></a>Tipo de mapeamento para mesa azure
Conforme mencionado no artigo de atividades de movimento de [dados,](data-factory-data-movement-activities.md) a atividade de cópia realiza conversões automáticas de tipos de origem para tipos de sink com a seguinte abordagem de dois passos.

1. Converter de tipos de origem nativa para .NET tipo
2. Converter do tipo .NET para o tipo de pia nativa

Ao mover dados para & a partir da Tabela Azure, os [seguintes mapeamentos definidos pelo serviço Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) são utilizados desde os tipos OData de Tabela Azure para o tipo .NET e vice-versa.

| Tipo de dados odados | Tipo .NET | Detalhes |
| --- | --- | --- |
| Edm.Binary |byte[] |Uma série de bytes até 64 KB. |
| Edm.Boolean |bool |Um valor booleano. |
| Edm.DateTime |DateTime |Um valor de 64 bits expresso como Tempo Universal Coordenado (UTC). A gama DateTime suportada começa a partir das 12:00, 1 de janeiro de 1601 d.A. (C.E.), UTC. A gama termina em 31 de dezembro de 9999. |
| Edm.Double |double |Um valor de ponto flutuante de 64 bits. |
| Edm.Guid |Guid |Um identificador globalmente único de 128 bits. |
| Edm.Int32 |Int32 |Um inteiro de 32 bits. |
| Edm.Int64 |Int64 |Um inteiro de 64 bits. |
| Edm.String |String |Um valor codificado uTF-16. Os valores das cordas podem chegar a 64 KB. |

### <a name="type-conversion-sample"></a>Amostra de conversão de tipo
A amostra que se segue destina-se a copiar dados de um Azure Blob para a Tabela Azure com conversões de tipo.

Suponha que o conjunto de dados Blob esteja no formato CSV e contenha três colunas. Um deles é uma coluna de data com um formato de data personalizada usando nomes franceses abreviados para o dia da semana.

Defina o conjunto de dados Blob Source da seguinte forma, juntamente com as definições de tipo para as colunas.

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
Dado o mapeamento do tipo de tipo do tipo OData de Tabela Azure para o tipo .NET, definiria a tabela em Tabela Azure com o seguinte esquema.

**Esquema de mesa azure:**

| Nome da coluna | Tipo |
| --- | --- |
| ID de utilizador |Edm.Int64 |
| nome |Edm.String |
| lastlogindate |Edm.DateTime |

Em seguida, defina o conjunto de dados da Tabela Azure da seguinte forma. Não é necessário especificar a secção "estrutura" com a informação do tipo, uma vez que a informação do tipo já está especificada no armazém de dados subjacente.

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

Neste caso, a Data Factory faz automaticamente conversões de tipo, incluindo o campo Datetime com o formato data-data personalizado utilizando a cultura "fr-fr" ao mover dados de Blob para Azure Table.

> [!NOTE]
> Para mapear colunas desde o conjunto de dados de origem até colunas a partir de conjunto de dados de sumidouro, consulte [colunas de conjunto de dados de mapeamento na Fábrica](data-factory-map-columns.md)de Dados Azure .

## <a name="performance-and-tuning"></a>Desempenho e Afinação
Para conhecer os factores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Do Azure e várias formas de o otimizar, consulte [o Copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md).
