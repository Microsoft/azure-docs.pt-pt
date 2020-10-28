---
title: Mover dados de/para Azure Cosmos DB
description: Saiba como mover dados de/para a a coleção DB da Azure Cosmos utilizando a Azure Data Factory
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
ms.openlocfilehash: 91f2b338ac9cda96521a5fe1d555de054826e273
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637637"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Mover dados de e para a Azure Cosmos DB usando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-azure-documentdb-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector DB Azure Cosmos em V2](../connector-azure-cosmos-db.md).

Este artigo explica como utilizar a Copy Activity in Azure Data Factory para mover dados de/para Azure Cosmos DB (SQL API). Baseia-se no artigo de Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

Pode copiar dados de qualquer loja de dados de origem suportada para Azure Cosmos DB ou de Azure Cosmos DB para qualquer loja de dados de pia suportada. Para obter uma lista de lojas de dados suportadas como fontes ou sumidouros pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

> [!IMPORTANT]
> O conector DB Azure Cosmos suporta apenas o SQL API.

Para copiar dados de/para ficheiros JSON ou outra coleção de Coss, consulte [documentos da Import/Export JSON](#importexport-json-documents).

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para Azure Cosmos DB utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard** . Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio** , **Azure PowerShell,** **Azure Resource Manager,** **.NET API** e **REST API** . Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON. Para amostras com definições JSON para entidades da Data Factory que são usadas para copiar dados de/para Cosmos DB, consulte a secção de [exemplos JSON](#json-examples) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas da Cosmos DB:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado Azure Cosmos DB.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **DocumentDb** |Sim |
| conexãoStragem |Especifique as informações necessárias para ligar à base de dados DB do Azure Cosmos. |Sim |

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, Azure blob, Azure table, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção typeProperties para o conjunto de dados do tipo **DocumentDbCollection** tem as seguintes propriedades.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| coleçãoName |Nome da coleção de documentos da Cosmos DB. |Sim |

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
### <a name="schema-by-data-factory"></a>Schema by Data Factory
Para lojas de dados isentas de esquemas, como a Azure Cosmos DB, o serviço data factory infere o esquema de uma das seguintes formas:

1. Se especificar a estrutura dos dados utilizando a propriedade da **estrutura** na definição de conjunto de dados, o serviço Data Factory honra esta estrutura como o esquema. Neste caso, se uma linha não contiver um valor para uma coluna, será fornecido um valor nulo para ela.
2. Se não especificar a estrutura dos dados utilizando a propriedade da **estrutura** na definição de conjunto de dados, o serviço Data Factory infere o esquema utilizando a primeira linha dos dados. Neste caso, se a primeira linha não contiver o esquema completo, algumas colunas desaparecerão no resultado da operação da cópia.

Portanto, para fontes de dados isentas de esquemas, a melhor prática é especificar a estrutura dos dados utilizando a propriedade da **estrutura.**

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Creating Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Atividade de Cópia leva apenas uma entrada e produz apenas uma saída.

As propriedades disponíveis na secção de tipoProperties da atividade, por outro lado, variam em função de cada tipo de atividade e em caso de atividade copy variam dependendo dos tipos de fontes e pias.

No caso de atividade de Copy quando a fonte é do tipo **DocumentDbCollectionSource,** as seguintes propriedades estão disponíveis na secção **typeProperties:**

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| consulta |Especifique a consulta para ler dados. |Linha de consulta suportada por Azure Cosmos DB. <br/><br/>Exemplo: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificado, a declaração SQL que é executada: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Personagem especial para indicar que o documento está aninhado |Qualquer personagem. <br/><br/>AZure Cosmos DB é uma loja NoSQL para documentos JSON, onde são permitidas estruturas aninhadas. A Azure Data Factory permite ao utilizador denotar hierarquia através de nidificaçãoSeparator, que é "". nos exemplos acima. Com o separador, a atividade da cópia gerará o objeto "Nome" com três elementos infantis Primeiro, Médio e Último, de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição de tabela. |Não |

**DocumentDbCollectionSink** suporta as seguintes propriedades:

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| nestingSeparator |Um carácter especial no nome da coluna de origem indica que o documento aninhado é necessário. <br/><br/>Por exemplo: `Name.First` na tabela de saída produz-se a seguinte estrutura JSON no documento Cosmos DB:<br/><br/>"Nome": {<br/>    "Primeiro": "John"<br/>}, |Caráter utilizado para separar níveis de aninhamento.<br/><br/>O valor predefinido é `.` (ponto). |Caráter utilizado para separar níveis de aninhamento. <br/><br/>O valor predefinido é `.` (ponto). |
| escreverBatchSize |Número de pedidos paralelos ao serviço DB da Azure Cosmos para criar documentos.<br/><br/>Pode afinar o desempenho ao copiar dados de/para Cosmos DB utilizando esta propriedade. Pode esperar um melhor desempenho quando aumentar o writeBatchSize porque são enviados pedidos mais paralelos à Cosmos DB. No entanto, terá de evitar estrangulamentos que possam passar a mensagem de erro: "A taxa de pedido é grande".<br/><br/>O estrangulamento é decidido por uma série de fatores, incluindo a dimensão dos documentos, o número de termos em documentos, a política de indexação da recolha de destinos, etc. Para operações de cópia, pode utilizar uma melhor recolha (por exemplo, S3) para ter o maior rendimento disponível (2.500 unidades de pedido/segundo). |Número inteiro |Não (predefinição: 5) |
| escreverBatchTimeout |Tempo de espera para a operação terminar antes que se esmua. |timespan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |

## <a name="importexport-json-documents"></a>Documentos JSON de importação/exportação
Utilizando este conector Cosmos DB, pode facilmente

* Importar documentos JSON de várias fontes para a Cosmos DB, incluindo Azure Blob, Azure Data Lake, no local Do Sistema de Ficheiros ou outras lojas baseadas em ficheiros suportadas pela Azure Data Factory.
* Exporte documentos JSON da coleção Cosmos DB em várias lojas baseadas em arquivos.
* Migrar dados entre duas coleções de DB cosmos como está.

Para obter tal cópia esquema-agnóstico,
* Ao utilizar o assistente de cópia, verifique a opção **"Export as-is to JSON files or Cosmos DB collection".**
* Ao utilizar a edição JSON, não especifique a secção "estrutura" no conjunto de dados (s) cosmos DB nem a propriedade "nestingSeparator" na fonte/afundamento da Cosmos DB na atividade de cópia. Para importar de/exportar para ficheiros JSON, no conjunto de dados da loja de ficheiros especifique o tipo de formato como "JsonFormat", config "filePattern" e salte as definições do formato de descanso, consulte a secção [de formato JSON](data-factory-supported-file-and-compression-formats.md#json-format) em detalhes.

## <a name="json-examples"></a>Exemplos JSON
Os exemplos a seguir fornecem definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para Azure Cosmos DB e Azure Blob Storage. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados usando a Atividade de Cópia na Fábrica de Dados Azure.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Exemplo: Copiar dados da Azure Cosmos DB para Azure Blob
A amostra abaixo mostra:

1. Um serviço ligado do tipo [DocumentDb](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [DocumentDbCollection](#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [DocumentDbCollectionSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados em Azure Cosmos DB para Azure Blob. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado Azure Cosmos DB:**

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
**Serviço ligado ao armazenamento Azure Blob:**

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
**Conjunto de dados de entrada de documentos Azure DB:**

A amostra pressupõe que tem uma coleção chamada **Pessoa** numa base de dados DB da Azure Cosmos.

Definição "externa": "verdadeiro" e especificando informações de política externaData o serviço Azure Data Factory que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída Azure Blob:**

Os dados são copiados para uma nova bolha a cada hora com o caminho para a bolha que reflete a data específica com granularidade de hora.

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
Prove o documento JSON na coleção Pessoa numa base de dados Cosmos DB:

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
Cosmos DB suporta documentos de consulta usando um SQL como sintaxe sobre documentos hierárquicos JSON.

Exemplo:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

O seguinte oleoduto copia dados da recolha de Pessoas na base de dados DB Azure Cosmos para uma bolha Azure. Como parte da atividade da cópia, os conjuntos de dados de entrada e saída foram especificados.

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
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Exemplo: Copiar dados de Azure Blob para Azure Cosmos DB
A amostra abaixo mostra:

1. Um serviço ligado do tipo DocumentDb.
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo DocumentDbCollection.
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e DocumentDbCollectionSink.

A amostra copia dados da blob Azure para Azure Cosmos DB. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado ao armazenamento Azure Blob:**

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
**Serviço ligado Azure Cosmos DB:**

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
**Conjunto de dados de entrada Azure Blob:**

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
**Conjunto de dados de saída da Azure Cosmos DB:**

A amostra copia dados para uma coleção chamada "Pessoa".

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
O seguinte oleoduto copia dados de Azure Blob para a coleção Pessoa no Cosmos DB. Como parte da atividade da cópia, os conjuntos de dados de entrada e saída foram especificados.

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
Se a entrada da amostra blob for como

```
1,John,,Doe
```
Em seguida, a saída JSON em Cosmos DB será como:

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
AZure Cosmos DB é uma loja NoSQL para documentos JSON, onde são permitidas estruturas aninhadas. A Azure Data Factory permite ao utilizador denotar hierarquia através de **nidificaçãoSeparator,** que é "". neste exemplo. Com o separador, a atividade da cópia gerará o objeto "Nome" com três elementos infantis Primeiro, Médio e Último, de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição de tabela.

## <a name="appendix"></a>Apêndice
1. **Pergunta:** A Atividade de Cópia suporta a atualização dos registos existentes?

    **Resposta:** Não.
2. **Pergunta:** Como é que uma nova tentou uma cópia para a Azure Cosmos DB lidar com registos já copiados?

    **Resposta:** Se os registos tiverem um campo de identificação e a operação de cópia tentar inserir um registo com o mesmo ID, a operação de cópia lança um erro.
3. **Pergunta:** A Data Factory suporta [gama ou partição de dados baseada em haxixe?](../../cosmos-db/partitioning-overview.md)

    **Resposta:** Não.
4. **Pergunta:** Posso especificar mais do que uma coleção de DB da Azure Cosmos para uma mesa?

    **Resposta:** Não. Apenas uma coleção pode ser especificada neste momento.

## <a name="performance-and-tuning"></a>Performance e Afinação
Consulte [copy Activity Performance & Guia de Afinação](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.