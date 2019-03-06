---
title: Mover dados de/para o Azure Cosmos DB | Documentos da Microsoft
description: Saiba como mover dados de/para o conjunto do Azure Cosmos DB com o Azure Data Factory
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: bda3df3ce869d7717f572f72c38472e7eae4a0ef
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437433"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Mover dados para e do Azure Cosmos DB com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-azure-documentdb-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector do Azure Cosmos DB no V2](../connector-azure-cosmos-db.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de/para Azure Cosmos DB (SQL API). Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados de qualquer arquivo de dados de origem suportada para o Azure Cosmos DB ou do Azure Cosmos DB para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela.

> [!IMPORTANT]
> Conector do Azure Cosmos DB suporta apenas a API de SQL.

Para copiar dados como-é de/para ficheiros JSON ou de outra coleção do Cosmos DB, veja [documentos JSON de importação/exportação](#importexport-json-documents).

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de/para Azure Cosmos DB ao utilizar ferramentas/APIs diferentes.

A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia.
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON. Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de/para o Cosmos DB, consulte [exemplos JSON](#json-examples) seção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON, que são utilizadas para definir entidades do Data Factory específicas para o Cosmos DB:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço ligado do Azure Cosmos DB.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida como: **DocumentDb** |Sim |
| connectionString |Especifica as informações necessárias para ligar à base de dados do Azure Cosmos DB. |Sim |

Exemplo:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **DocumentDbCollection** tem as seguintes propriedades.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| CollectionName |Nome da coleção de documento do Cosmos DB. |Sim |

Exemplo:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Esquema pelo Data Factory
Para arquivos de dados sem esquema, como o Azure Cosmos DB, o serviço Data Factory infere o esquema de uma das seguintes formas:

1. Se especificar a estrutura de dados utilizando o **estrutura** propriedade na definição do conjunto de dados, o serviço Data Factory respeita essa estrutura como o esquema. Neste caso, se uma linha não contém um valor para uma coluna, irá ser fornecido um valor nulo para o mesmo.
2. Se não especificar a estrutura de dados utilizando o **estrutura** propriedade na definição do conjunto de dados, o serviço Data Factory infere o esquema ao utilizar a primeira linha dos dados. Neste caso, se a primeira linha contém o esquema completo, algumas colunas estará em falta no resultado da operação de cópia.

Por conseguinte, para origens de dados sem esquema, a prática recomendada é especificar a estrutura de dados com o **estrutura** propriedade.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, entrada e saída de tabelas e a política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A atividade de cópia precisar apenas uma entrada e saída de apenas um.

Propriedades disponíveis na secção typeProperties da atividade variam por outro lado, com cada tipo de atividade e, em caso de atividade de cópia variam consoante os tipos de origens e sinks.

Em caso de atividade de cópia quando a origem é do tipo **DocumentDbCollectionSource** as seguintes propriedades estão disponíveis no **typeProperties** secção:

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| consulta |Especifique a consulta para ler os dados. |Suportado pelo Azure Cosmos DB de cadeia de consulta. <br/><br/>Exemplo: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificado, a instrução SQL que é executada: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Caráter especial para indicar que o documento está aninhado |Qualquer caráter. <br/><br/>O Azure Cosmos DB é um arquivo de NoSQL para documentos JSON, onde as estruturas aninhadas são permitidas. O Azure Data Factory permite que o utilizador denotar hierarquia via nestingSeparator, que é "." nos exemplos acima. Com o separador, a atividade de cópia irá gerar o objeto "Nome" com elementos de três filhos em primeiro lugar, média e por último, a fim de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição da tabela. |Não |

**DocumentDbCollectionSink** suporta as seguintes propriedades:

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| nestingSeparator |É necessário um caráter especial no nome da coluna de origem para indicar esse documento aninhado. <br/><br/>Por exemplo acima: `Name.First` na saída da tabela produz a seguinte estrutura JSON no documento do Cosmos DB:<br/><br/>"Name": {<br/>    "First": "João"<br/>}, |Caráter utilizado para separar níveis de aninhamento.<br/><br/>Valor predefinido é `.` (ponto). |Caráter utilizado para separar níveis de aninhamento. <br/><br/>Valor predefinido é `.` (ponto). |
| writeBatchSize |Número de pedidos paralelos para o serviço do Azure Cosmos DB para criar documentos.<br/><br/>Pode ajustar o desempenho ao copiar dados de/para Cosmos DB ao utilizar esta propriedade. Pode esperar um melhor desempenho quando aumenta writeBatchSize porque são enviados os pedidos paralelos mais ao Cosmos DB. No entanto, terá de evitar a limitação que pode gerar a mensagem de erro: "Taxa de pedidos é grande".<br/><br/>A limitação é decidida por um número de fatores, incluindo o tamanho de documentos, o número de termos em documentos, a indexação de política de coleção de destino, etc. Para operações de cópia, pode utilizar uma coleção de melhor (por exemplo, S3) que tenha o máximo débito disponível (pedido de 2500 unidades por segundo). |Número inteiro |Não (predefinição: 5) |
| writeBatchTimeout |Tempo para a operação seja concluída antes de atingir o tempo limite de espera. |Período de tempo<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |

## <a name="importexport-json-documents"></a>Documentos JSON de importação/exportação
Utilizar este conector do Cosmos DB, pode facilmente

* Importe documentos JSON de várias origens para o Cosmos DB, incluindo BLOBs do Azure, Azure Data Lake, sistema de ficheiros no local ou outros arquivos baseados em ficheiros do Azure Data Factory suportados.
* Exporte documentos JSON de coleção do Cosmos DB em vários arquivos baseados em ficheiros.
* Migrar dados entre duas coleções de Cosmos DB como-é.

Para alcançar essa cópia sem esquema,
* Ao utilizar o Assistente de cópia, consulte a **"Exportar como-é ficheiros JSON ou coleção do Cosmos DB"** opção.
* Quando utilizar a edição de JSON, não especifique a seção de "estrutura" em conjuntos de dados do Cosmos DB nem propriedade de "nestingSeparator" no Cosmos DB origem/sink na atividade de cópia. Para importar a partir do / exportar para ficheiros JSON, o conjunto de dados do armazenamento de ficheiros de especificar o tipo de formato como "JsonFormat" configuração "filePattern" e ignorar as definições do formato de rest, consulte [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format) secção em detalhes.

## <a name="json-examples"></a>Exemplos JSON
Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para o Azure Cosmos DB e armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Exemplo: Copiar dados do Azure Cosmos DB para BLOBs do Azure
O exemplo abaixo mostra:

1. Um serviço ligado do tipo [DocumentDb](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [DocumentDbCollection](#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [DocumentDbCollectionSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia os dados no Azure Cosmos DB para BLOBs do Azure. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

**Serviço ligado do Azure Cosmos DB:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Serviço ligado do armazenamento de Blobs do Azure:**

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
**Conjunto de dados de entrada Document DB do Azure:**

O exemplo parte do princípio de que tem uma coleção designada **pessoa** numa base de dados do Azure Cosmos DB.

A definição "externo": "true" e especificando externalData informações de política do serviço do Azure Data Factory que a tabela é externa à fábrica de dados e não são produzidos por uma atividade na fábrica de dados.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Conjunto de dados de Blobs do Azure:**

Dados são copiados para um blob novo a cada hora, com o caminho para o blob que reflete a datetime específica com granularidade de hora.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Documento de JSON de exemplo da coleção de pessoa num banco de dados do Cosmos DB:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
O cosmos DB suporta a consulta de documentos usando uma sintaxe de SQL sobre hierárquicos de documentos JSON.

Exemplo:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

O pipeline seguinte copia dados da coleção pessoa na base de dados do Azure Cosmos DB para um blob do Azure. Como parte da atividade copiar a entrada e saída, conjuntos de dados tem sido especificados.

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Exemplo: Copiar dados de Blobs do Azure para o Azure Cosmos DB
O exemplo abaixo mostra:

1. Um serviço ligado do tipo DocumentDb.
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo DocumentDbCollection.
5. R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e DocumentDbCollectionSink.

O exemplo copia dados do Azure blob para o Azure Cosmos DB. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

**Serviço ligado do armazenamento de Blobs do Azure:**

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
**Serviço ligado do Azure Cosmos DB:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Conjunto de dados de entrada BLOBs do Azure:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Conjunto de dados do Azure Cosmos DB:**

O exemplo copia dados para uma coleção designada "Person".

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
O pipeline seguinte copia dados de Blobs do Azure para a coleção de pessoa no Cosmos DB. Como parte da atividade copiar a entrada e saída, conjuntos de dados tem sido especificados.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          },
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Se a entrada de BLOBs de exemplo é como

```
1,John,,Doe
```
Em seguida, a saída JSON no Cosmos DB será como:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
O Azure Cosmos DB é um arquivo de NoSQL para documentos JSON, onde as estruturas aninhadas são permitidas. O Azure Data Factory permite que o utilizador denotar hierarquia através de **nestingSeparator**, que é "." Neste exemplo. Com o separador, a atividade de cópia irá gerar o objeto "Nome" com elementos de três filhos em primeiro lugar, média e por último, a fim de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição da tabela.

## <a name="appendix"></a>Anexo
1. **Pergunta:** O suporte de atividade de cópia é atualizada de registos existentes?

    **Resposta:** Não.
2. **Pergunta:** Como uma nova tentativa de uma cópia ao Azure Cosmos DB lidar com registos já copiados?

    **Resposta:** Se registos têm um campo "ID" e a operação de cópia tenta inserir um registo com o mesmo ID, a operação de cópia emite um erro.
3. **Pergunta:** O Data Factory suporta [intervalo ou a criação de partições de dados com base em hash](../../cosmos-db/sql-api-partition-data.md)?

    **Resposta:** Não.
4. **Pergunta:** Pode especificar mais do que uma coleção do Azure Cosmos DB para uma tabela?

    **Resposta:** Não. Apenas uma coleção pode ser especificada neste momento.

## <a name="performance-and-tuning"></a>Desempenho e Otimização
Ver [desempenho de atividade de cópia e guia de ajuste](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo.
