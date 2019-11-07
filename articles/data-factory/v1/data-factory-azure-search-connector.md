---
title: Enviar dados por push para o índice de pesquisa usando Data Factory
description: Saiba mais sobre como enviar dados por push para Azure Search índice usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 09b891ba753291511bb1f203b7ac4437e6b2c542
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683113"
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Enviar dados por push a um índice de Azure Search usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-azure-search-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-search.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [Azure Search Connector na v2](../connector-azure-search.md).

Este artigo descreve como usar a atividade de cópia para enviar dados por push de um armazenamento de dados de origem com suporte para Azure Search índice. Os armazenamentos de dados de origem com suporte são listados na coluna origem da tabela [fontes e coletores com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

## <a name="enabling-connectivity"></a>Habilitando a conectividade
Para permitir que Data Factory serviço se conecte a um armazenamento de dados local, instale o Gerenciamento de Dados gateway no seu ambiente local. Você pode instalar o gateway no mesmo computador que hospeda o armazenamento de dados de origem ou em um computador separado para evitar a competição por recursos com o armazenamento de dados.

Gerenciamento de Dados gateway conecta fontes de dados locais a serviços de nuvem de maneira segura e gerenciada. Confira o artigo [mover dados entre o local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre gerenciamento de dados gateway.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que envia dados de um armazenamento de dados de origem para Azure Search índice usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Confira o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções detalhadas para criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para Data Factory entidades usadas para copiar dados para Azure Search índice, consulte o [exemplo de JSON: copiar dados do SQL Server local para a](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) seção de índice de Azure Search deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para Azure Search índice:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

A tabela a seguir fornece descrições para elementos JSON que são específicos para o serviço vinculado Azure Search.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| tipo | A propriedade Type deve ser definida como: **AzureSearch**. | Sim |
| url | URL para o serviço de Azure Search. | Sim |
| key | Chave de administração para o serviço de Azure Search. | Sim |

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades que estão disponíveis para definir conjuntos de os, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de os. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados. A seção **typeproperties** é diferente para cada tipo de conjunto de texto. A seção typeproperties para um DataSet do tipo **AzureSearchIndex** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| tipo | A propriedade Type deve ser definida como **AzureSearchIndex**.| Sim |
| indexName | Nome do índice de Azure Search. Data Factory não cria o índice. O índice deve existir no Azure Search. | Sim |


## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e várias políticas estão disponíveis para todos os tipos de atividades. Enquanto que as propriedades disponíveis na seção typeproperties variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

Para a atividade de cópia, quando o coletor é do tipo **AzureSearchIndexSink**, as seguintes propriedades estão disponíveis na seção typeproperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Especifica se é para mesclar ou substituir quando um documento já existe no índice. Consulte a [Propriedade WriteBehavior](#writebehavior-property).| Mesclar (padrão)<br/>Carregar| Não |
| writeBatchSize | Carrega dados no índice de Azure Search quando o tamanho do buffer atinge writeBatchSize. Consulte a [Propriedade WriteBatchSize](#writebatchsize-property) para obter detalhes. | 1 a 1.000. O valor padrão é 1000. | Não |

### <a name="writebehavior-property"></a>Propriedade WriteBehavior
AzureSearchSink upserts ao gravar dados. Em outras palavras, ao gravar um documento, se a chave de documento já existir no índice de Azure Search, Azure Search atualizará o documento existente em vez de lançar uma exceção de conflito.

O AzureSearchSink fornece os dois comportamentos de Upsert a seguir (usando o SDK do AzureSearch):

- **Mesclagem**: combine todas as colunas no novo documento com a existente. Para colunas com valor nulo no novo documento, o valor do existente é preservado.
- **Carregar**: o novo documento substitui o existente. Para colunas não especificadas no novo documento, o valor é definido como nulo se há um valor não nulo no documento existente ou não.

O comportamento padrão é **mesclar**.

### <a name="writebatchsize-property"></a>Propriedade WriteBatchSize
Azure Search serviço dá suporte à gravação de documentos como um lote. Um lote pode conter de 1 a 1.000 ações. Uma ação manipula um documento para executar a operação de upload/mesclagem.

### <a name="data-type-support"></a>Suporte ao tipo de dados
A tabela a seguir especifica se um tipo de dados Azure Search tem suporte ou não.

| Tipo de dados Azure Search | Com suporte no coletor Azure Search |
| ---------------------- | ------------------------------ |
| String | S |
| Int32 | S |
| Int64 | S |
| Clique | S |
| Booleano | S |
| DataTimeOffset | S |
| Matriz de cadeia de caracteres | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>Exemplo de JSON: copiar dados do SQL Server local para Azure Search índice

O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureSearch](#linked-service-properties).
2. Um serviço vinculado do tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [sqlservertable](data-factory-sqlserver-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureSearchIndex](#dataset-properties).
4. Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que usa [sqlsource](data-factory-sqlserver-connector.md#copy-activity-properties) e [AzureSearchIndexSink](#copy-activity-properties).

O exemplo copia dados de série temporal de um banco de dado SQL Server local para um índice de Azure Search por hora. As propriedades JSON usadas neste exemplo são descritas nas seções que seguem os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados em seu computador local. As instruções estão no artigo [movendo dados entre os locais e a nuvem do local](data-factory-move-data-between-onprem-and-cloud.md) .

**Azure Search serviço vinculado:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**SQL Server serviço vinculado**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**SQL Server conjunto de dados de entrada**

O exemplo supõe que você criou uma tabela "MyTable" em SQL Server e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal. Você pode consultar várias tabelas dentro do mesmo banco de dados usando um único conjunto, mas uma única tabela deve ser usada para o tipo de tabela TableName do DataSet.

A configuração de "external": "true" informa Data Factory serviço de que o DataSet é externo ao data factory e não é produzido por uma atividade no data factory.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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

**Conjunto de Azure Search de saída:**

O exemplo copia dados para um índice de Azure Search chamado **Products**. Data Factory não cria o índice. Para testar o exemplo, crie um índice com esse nome. Crie o índice de Azure Search com o mesmo número de colunas do conjunto de dados de entrada. Novas entradas são adicionadas ao índice de Azure Search a cada hora.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

**Atividade de cópia em um pipeline com fonte SQL e coletor de índice Azure Search:**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **sqlsource** e o tipo de **coletor** é definido como **AzureSearchIndexSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
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

Se você estiver copiando dados de um armazenamento de dados de nuvem para Azure Search, `executionLocation` propriedade será necessária. O trecho JSON a seguir mostra a alteração necessária na atividade de cópia `typeProperties` como um exemplo. Marque a seção [copiar dados entre armazenamentos de dados de nuvem](data-factory-data-movement-activities.md#global) para obter os valores com suporte e mais detalhes.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Copiar de uma fonte de nuvem
Se você estiver copiando dados de um armazenamento de dados de nuvem para Azure Search, `executionLocation` propriedade será necessária. O trecho JSON a seguir mostra a alteração necessária na atividade de cópia `typeProperties` como um exemplo. Marque a seção [copiar dados entre armazenamentos de dados de nuvem](data-factory-data-movement-activities.md#global) para obter os valores com suporte e mais detalhes.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

Você também pode mapear colunas do conjunto de fonte de origem para colunas do conjunto de coleta da atividade de cópia. Para obter detalhes, consulte [mapeando colunas do conjunto de informações em Azure data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização
Consulte o [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) e várias maneiras de otimizá-lo.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

* [Tutorial de atividade da cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.
