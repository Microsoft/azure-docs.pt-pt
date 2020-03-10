---
title: Mover dados de/para Azure Cosmos DB
description: Saiba como mover dados de/para a coleção Azure Cosmos DB usando a Azure Data Factory
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387469"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Mova dados de e para azure Cosmos DB usando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-azure-documentdb-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector Azure Cosmos DB em V2](../connector-azure-cosmos-db.md).

Este artigo explica como usar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de/para O Azure Cosmos DB (SQL API). Baseia-se no artigo Atividades do Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados de qualquer loja de dados de origem suportada para o Azure Cosmos DB ou da Azure Cosmos DB para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados suportadas como fontes ou pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

> [!IMPORTANT]
> O conector Azure Cosmos DB suporta apenas a API SQL.

Para copiar dados como estão de/para ficheiros JSON ou outra coleção Cosmos DB, consulte [documentos Import/Export JSON](#importexport-json-documents).

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para o Azure Cosmos DB utilizando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é utilizar o **Assistente de Cópia**. Ver [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um pipeline utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON. Para amostras com definições JSON para entidades da Fábrica de Dados que são usadas para copiar dados de/para cosmos DB, consulte a secção de [exemplos jSON](#json-examples) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades jSON que são usadas para definir entidades data factory específicas da Cosmos DB:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado à Azure Cosmos DB.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **DocumentDb** |Sim |
| connectionString |Especifique as informações necessárias para se ligar à base de dados Do MBD Azure Cosmos. |Sim |

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
Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntos de dados (Azure SQL, Azure blob, tabela Azure, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção typeProperties para o conjunto de dados do tipo **DocumentDbCollection** tem as seguintes propriedades.

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
Para lojas de dados sem esquemas como a Azure Cosmos DB, o serviço Data Factory infere o esquema de uma das seguintes formas:

1. Se especificar a estrutura dos dados utilizando a propriedade da **estrutura** na definição de conjunto de dados, o serviço Data Factory honra esta estrutura como o esquema. Neste caso, se uma linha não contiver um valor para uma coluna, será previsto um valor nulo.
2. Se não especificar a estrutura dos dados utilizando a propriedade da **estrutura** na definição de conjunto de dados, o serviço Data Factory infere o esquema utilizando a primeira linha dos dados. Neste caso, se a primeira linha não contiver o esquema completo, algumas colunas desaparecerão no resultado da operação de cópia.

Por conseguinte, para fontes de dados sem esquemas, a melhor prática é especificar a estrutura dos dados utilizando a propriedade da **estrutura.**

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Creating Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e a política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Copy Activity leva apenas uma entrada e produz apenas uma saída.

As propriedades disponíveis na secção tipoPropriedades da atividade por outro lado variam com cada tipo de atividade e no caso de atividade de Cópia variam dependendo dos tipos de fontes e pias.

No caso de Copy quando a fonte é do tipo **DocumentDbCollectionSource** as seguintes propriedades estão disponíveis na secção **TypeProperties:**

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| consulta |Especifique a consulta para ler dados. |Corda de consulta apoiada por Azure Cosmos DB. <br/><br/>Exemplo: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não especificada, a declaração SQL que é executada: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Caráter especial para indicar que o documento está aninhado |Qualquer personagem. <br/><br/>Azure Cosmos DB é uma loja NoSQL para documentos JSON, onde são permitidas estruturas aninhadas. A Azure Data Factory permite ao utilizador denotar a hierarquia através do nidificação DoSeparator, que é "". nos exemplos acima. Com o separador, a atividade de cópia gerará o objeto "Nome" com três elementos infantis Primeiro, Médio e Último, de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição da tabela. |Não |

**DocumentDbCollectionSink** suporta as seguintes propriedades:

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| nestingSeparator |Um personagem especial no nome da coluna de origem para indicar que o documento aninhado é necessário. <br/><br/>Por exemplo: `Name.First` na tabela de saída produz a seguinte estrutura JSON no documento Cosmos DB:<br/><br/>"Nome": {<br/>    "First": "John"<br/>}, |Caráter utilizado para separar níveis de aninhamento.<br/><br/>O valor predefinido é `.` (ponto). |Caráter utilizado para separar níveis de aninhamento. <br/><br/>O valor predefinido é `.` (ponto). |
| writeBatchSize |Número de pedidos paralelos ao serviço Azure Cosmos DB para criar documentos.<br/><br/>Pode afinar o desempenho ao copiar dados de/para cosmos DB utilizando esta propriedade. Você pode esperar um melhor desempenho quando você aumentar writeBatchSize porque são enviados pedidos mais paralelos para Cosmos DB. No entanto, terá de evitar estrangulamentos que possam lançar a mensagem de erro: "A taxa de pedido é grande".<br/><br/>O estrangulamento é decidido por uma série de fatores, incluindo a dimensão dos documentos, o número de termos em documentos, a política de indexação da recolha de alvos, etc. Para operações de cópia, pode utilizar uma melhor recolha (por exemplo, S3) para ter a maior entrada disponível (2.500 unidades de pedido/segundo). |Número inteiro |Não (padrão: 5) |
| writeBatchTimeout |Aguarde o tempo para que a operação esteja concluída antes de sair. |TimeSpan<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |

## <a name="importexport-json-documents"></a>Documentos JSON de Importação/Exportação
Usando este conector Cosmos DB, você pode facilmente

* Importar documentos JSON de várias fontes para cosmos DB, incluindo Azure Blob, Azure Data Lake, no local do Sistema de Ficheiros ou outras lojas baseadas em ficheiros suportadas pela Azure Data Factory.
* Exportar documentos JSON da coleção Cosmos DB em várias lojas baseadas em ficheiros.
* Migrar dados entre duas coleções cosmos DB como está.

Para obter tal cópia schema-agnóstica,
* Ao utilizar o assistente de cópia, verifique a opção **"Exportar as és para ficheiros JSON ou recolha Cosmos DB".**
* Ao utilizar a edição jSON, não especifique a secção "estrutura" no conjunto de dados cosmos DB nem a propriedade "nestingSeparator" na fonte/pia da Cosmos DB na atividade de cópia. Para importar de/exportar para ficheiros JSON, no conjunto de dados da loja de ficheiros especifique o tipo de formato como "JsonFormat", config "filePattern" e ignore as definições do formato de repouso, consulte a secção de [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format) em detalhes.

## <a name="json-examples"></a>Exemplos jSON
Os exemplos seguintes fornecem definições JSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostram como copiar dados de e para o Armazenamento de Blob Azure Cosmos. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Exemplo: Copiar dados do Azure Cosmos DB para O Blob Azure
A amostra abaixo mostra:

1. Um serviço ligado do tipo [DocumentDb](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [DocumentDbCollection](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [documentDbCollectionSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados em Azure Cosmos DB para Azure Blob. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado à Azure Cosmos DB:**

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
**Conjunto de dados de entrada de input do Documento Azure DB:**

A amostra pressupõe que você tem uma coleção chamada **Pessoa** numa base de dados Azure Cosmos DB.

Definição "externa": "verdadeira" e especificação de informações externas sobre a política de Dados, o serviço Azure Data Factory de que o quadro é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída de Azure Blob:**

Os dados são copiados para uma nova bolha a cada hora com o caminho para a bolha que reflete a hora específica com granularidade de hora.

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
Cosmos DB apoia documentos de consulta usando um SQL como sintaxe sobre documentos hierárquicos JSON.

Exemplo:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

O seguinte pipeline copia dados da recolha da Pessoa na base de dados Azure Cosmos DB para uma bolha Azure. Como parte da atividade de cópia, os conjuntos de dados de entrada e de saída foram especificados.

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
2. Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo DocumentDbCollection.
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [blobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e DocumentDbCollectionSink.

A amostra copia dados de Azure blob para Azure Cosmos DB. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

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
**Serviço ligado à Azure Cosmos DB:**

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
O seguinte pipeline copia dados de Azure Blob para a recolha da Pessoa no Cosmos DB. Como parte da atividade de cópia, os conjuntos de dados de entrada e de saída foram especificados.

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
Se a entrada de bolha da amostra for como

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
Azure Cosmos DB é uma loja NoSQL para documentos JSON, onde são permitidas estruturas aninhadas. A Azure Data Factory permite ao utilizador denotar a hierarquia através do **nidificação DoSeparator**, que é "". neste exemplo. Com o separador, a atividade de cópia gerará o objeto "Nome" com três elementos infantis Primeiro, Médio e Último, de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição da tabela.

## <a name="appendix"></a>Anexo
1. **Pergunta:** A Copy Activity suporta a atualização dos registos existentes?

    **Resposta:** Não.
2. **Pergunta:** Como é que uma nova tentativa de uma cópia para a Azure Cosmos DB lida com registos já copiados?

    **Resposta:** Se os registos tiverem um campo de identificação e a operação de cópia tentar inserir um registo com o mesmo ID, a operação de cópia lança um erro.
3. **Pergunta:** A data factory suporta [a gama ou a divisão de dados baseada em hash?](../../cosmos-db/sql-api-partition-data.md)

    **Resposta:** Não.
4. **Pergunta:** Posso especificar mais do que uma coleção Azure Cosmos DB para uma mesa?

    **Resposta:** Não. Apenas uma coleção pode ser especificada neste momento.

## <a name="performance-and-tuning"></a>Desempenho e Afinação
Consulte o [Copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Do Azure e várias formas de o otimizar.
