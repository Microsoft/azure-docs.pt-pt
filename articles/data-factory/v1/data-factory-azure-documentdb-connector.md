---
title: Mover dados de/para Azure Cosmos DB
description: Saiba como mover dados de/para a coleção de Azure Cosmos DB usando Azure Data Factory
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a638184d5232de916ebd25360147301a93309dd9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930094"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Mover dados de e para Azure Cosmos DB usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-azure-documentdb-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [Azure Cosmos DB Connector na v2](../connector-azure-cosmos-db.md).

Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de/para o Azure Cosmos DB (API do SQL). Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para Azure Cosmos DB ou de Azure Cosmos DB para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes ou coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) .

> [!IMPORTANT]
> O conector de Azure Cosmos DB dá suporte apenas à API do SQL.

Para copiar dados como estão de/para arquivos JSON ou outra coleção de Cosmos DB, consulte [importar/exportar documentos JSON](#importexport-json-documents).

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados de/para Azure Cosmos DB usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON. Para obter exemplos com definições de JSON para Data Factory entidades usadas para copiar dados de/para Cosmos DB, consulte a seção [exemplos de JSON](#json-examples) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para Cosmos DB:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela a seguir fornece a descrição para elementos JSON específicos para Azure Cosmos DB serviço vinculado.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **DocumentDb** |Sim |
| connectionString |Especifique as informações necessárias para se conectar ao banco de dados Azure Cosmos DB. |Sim |

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
Para obter uma lista completa das seções & as propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos de valores](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOB do Azure, tabela do Azure, etc.).

A seção typeproperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeproperties do conjunto de um do tipo **DocumentDbCollection** tem as propriedades a seguir.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| collectionName |Nome da coleção de documentos Cosmos DB. |Sim |

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
Para armazenamentos de dados sem esquema, como Azure Cosmos DB, o serviço Data Factory infere o esquema de uma das seguintes maneiras:

1. Se você especificar a estrutura de dados usando a propriedade de **estrutura** na definição do conjunto, o serviço data Factory honrará essa estrutura como o esquema. Nesse caso, se uma linha não contiver um valor para uma coluna, um valor nulo será fornecido para ele.
2. Se você não especificar a estrutura de dados usando a propriedade de **estrutura** na definição do conjunto, o serviço de data Factory inferirá o esquema usando a primeira linha nos dados. Nesse caso, se a primeira linha não contiver o esquema completo, algumas colunas estarão ausentes no resultado da operação de cópia.

Portanto, para fontes de dados sem esquema, a prática recomendada é especificar a estrutura de dados usando a propriedade **Structure** .

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A atividade de cópia usa apenas uma entrada e produz apenas uma saída.

As propriedades disponíveis na seção typeproperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso da atividade de cópia, elas variam dependendo dos tipos de fontes e coletores.

No caso da atividade de cópia quando a fonte é do tipo **DocumentDbCollectionSource** , as seguintes propriedades estão disponíveis na seção **typeproperties** :

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| consulta |Especifique a consulta para ler os dados. |Cadeia de caracteres de consulta com suporte pelo Azure Cosmos DB. <br/><br/>Exemplo: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificado, a instrução SQL executada: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Caractere especial para indicar que o documento está aninhado |Qualquer caractere. <br/><br/>Azure Cosmos DB é um repositório NoSQL para documentos JSON, em que estruturas aninhadas são permitidas. Azure Data Factory permite que o usuário denotasse hierarquia via nestingSeparator, que é "." nos exemplos acima. Com o separador, a atividade de cópia gerará o objeto "nome" com três elementos filhos primeiro, meio e último, de acordo com "nome. primeiro", "nome. meio" e "nome. último" na definição da tabela. |Não |

O **DocumentDbCollectionSink** dá suporte às seguintes propriedades:

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| nestingSeparator |Um caractere especial no nome da coluna de origem para indicar que o documento aninhado é necessário. <br/><br/>Por exemplo acima: `Name.First` na tabela de saída produz a seguinte estrutura JSON no documento Cosmos DB:<br/><br/>"Name": {<br/>    "First": "John"<br/>}, |Caráter utilizado para separar níveis de aninhamento.<br/><br/>O valor padrão é `.` (ponto). |Caráter utilizado para separar níveis de aninhamento. <br/><br/>O valor padrão é `.` (ponto). |
| writeBatchSize |Número de solicitações paralelas para Azure Cosmos DB serviço para criar documentos.<br/><br/>Você pode ajustar o desempenho ao copiar dados de/para Cosmos DB usando essa propriedade. Você pode esperar um melhor desempenho ao aumentar o writeBatchSize porque mais solicitações paralelas para Cosmos DB são enviadas. No entanto, você precisará evitar a limitação que pode gerar a mensagem de erro: "a taxa de solicitação é grande".<br/><br/>A limitação é decidida por vários fatores, incluindo o tamanho dos documentos, o número de termos em documentos, a política de indexação da coleção de destino, etc. Para operações de cópia, você pode usar uma coleção melhor (por exemplo, S3) para ter a maior taxa de transferência disponível (2.500 unidades de solicitação/segundo). |Número inteiro |Não (padrão: 5) |
| writeBatchTimeout |Tempo de espera para a operação ser concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |

## <a name="importexport-json-documents"></a>Importar/exportar documentos JSON
Usando esse conector de Cosmos DB, você pode facilmente

* Importe documentos JSON de várias fontes para Cosmos DB, incluindo blob do Azure, Azure Data Lake, sistema de arquivos local ou outros repositórios com base em arquivo com suporte pelo Azure Data Factory.
* Exportar documentos JSON da coleção Cosmos DB em vários repositórios baseados em arquivo.
* Migre dados entre duas coleções de Cosmos DB como estão.

Para obter essa cópia independente de esquema,
* Ao usar o assistente de cópia, marque a opção **"exportar como está para arquivos JSON ou coleção de Cosmos DB"** .
* Ao usar a edição JSON, não especifique a seção "estrutura" na Cosmos DB DataSet (s) nem na propriedade "nestingSeparator" em Cosmos DB origem/coletor na atividade de cópia. Para importar/exportar para arquivos JSON, no conjunto de informações do repositório de arquivos, especifique o tipo de formato como "JsonFormat", config "filepattern" e ignore as configurações de formato REST, consulte a seção [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format) em detalhes.

## <a name="json-examples"></a>Exemplos de JSON
Os exemplos a seguir fornecem exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para Azure Cosmos DB e o armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Exemplo: copiar dados do Azure Cosmos DB para o blob do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [DocumentDb](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [DocumentDbCollection](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [DocumentDbCollectionSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados em Azure Cosmos DB para o blob do Azure. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

**Azure Cosmos DB serviço vinculado:**

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
**Serviço vinculado do armazenamento de BLOBs do Azure:**

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
**Conjunto de dados de entrada do BD de documentos do Azure:**

O exemplo supõe que você tenha uma coleção chamada **Person** em um banco de dados Azure Cosmos DB.

Definir "external": "true" e especificar informações de política de externalData o serviço de Azure Data Factory que a tabela é externo à data factory e não é produzido por uma atividade no data factory.

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

**Conjunto de resultados de saída de blob do Azure:**

Os dados são copiados para um novo BLOB a cada hora com o caminho para o blob que reflete o DateTime específico com granularidade de hora.

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
Documento JSON de exemplo na coleção Person em um banco de dados Cosmos DB:

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
Cosmos DB dá suporte à consulta de documentos usando uma sintaxe SQL like sobre documentos JSON hierárquicos.

Exemplo:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

O pipeline a seguir copia dados da coleção Person no banco de Azure Cosmos DB para um blob do Azure. Como parte da atividade de cópia, os conjuntos de dados de entrada e saída foram especificados.

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
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Exemplo: copiar dados do blob do Azure para Azure Cosmos DB
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo DocumentDb.
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo DocumentDbCollection.
5. Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [blobname](data-factory-azure-blob-connector.md#copy-activity-properties) e DocumentDbCollectionSink.

O exemplo copia dados do blob do Azure para Azure Cosmos DB. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

**Serviço vinculado do armazenamento de BLOBs do Azure:**

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
**Azure Cosmos DB serviço vinculado:**

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
**Conjunto de dados de entrada de blob do Azure:**

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
**Conjunto de Azure Cosmos DB de saída:**

O exemplo copia dados para uma coleção chamada "Person".

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
O pipeline a seguir copia dados do blob do Azure para a coleção Person no Cosmos DB. Como parte da atividade de cópia, os conjuntos de dados de entrada e saída foram especificados.

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
Se a entrada de blob de exemplo for como

```
1,John,,Doe
```
Em seguida, o JSON de saída em Cosmos DB será como:

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
Azure Cosmos DB é um repositório NoSQL para documentos JSON, em que estruturas aninhadas são permitidas. Azure Data Factory permite que o usuário denotasse hierarquia via **nestingSeparator**, que é "." Neste exemplo. Com o separador, a atividade de cópia gerará o objeto "nome" com três elementos filhos primeiro, meio e último, de acordo com "nome. primeiro", "nome. meio" e "nome. último" na definição da tabela.

## <a name="appendix"></a>Anexo
1. **Pergunta:** A atividade de cópia dá suporte à atualização de registros existentes?

    **Resposta:** não.
2. **Pergunta:** Como uma nova tentativa de uma cópia para Azure Cosmos DB lida com registros já copiados?

    **Resposta:** Se os registros tiverem um campo "ID" e a operação de cópia tentar inserir um registro com a mesma ID, a operação de cópia lançará um erro.
3. **Pergunta:** Data Factory o intervalo de suporte [ou o particionamento de dados baseado em hash](../../cosmos-db/sql-api-partition-data.md)?

    **Resposta:** não.
4. **Pergunta:** Posso especificar mais de uma coleção de Azure Cosmos DB para uma tabela?

    **Resposta:** não. Somente uma coleção pode ser especificada no momento.

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.
