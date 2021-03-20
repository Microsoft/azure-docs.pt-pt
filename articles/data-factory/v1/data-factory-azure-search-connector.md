---
title: Empurre os dados para o índice de pesquisa utilizando a Data Factory
description: Saiba como empurrar os dados para o Azure Cognitive Search Index utilizando a Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0226ab75d53733b94a9ae5734b42b7340998759c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379272"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Empurre os dados para um índice de pesquisa cognitiva Azure utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-azure-search-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-search.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector Azure Cognitive Search em V2](../connector-azure-search.md).

Este artigo descreve como usar a Atividade de Cópia para empurrar dados de uma loja de dados de origem suportada para um índice de Pesquisa Cognitiva Azure. As lojas de dados de origem suportadas estão listadas na coluna Fonte das [fontes suportadas e](data-factory-data-movement-activities.md#supported-data-stores-and-formats) da tabela de pias. Este artigo baseia-se no artigo de [atividades](data-factory-data-movement-activities.md) de movimento de dados, que apresenta uma visão geral do movimento de dados com a Copy Activity e combinações de lojas de dados suportadas.

## <a name="enabling-connectivity"></a>Habilitação da conectividade
Para permitir que o serviço Data Factory se conecte a uma loja de dados no local, instale o Data Management Gateway no seu ambiente no local. Pode instalar gateway na mesma máquina que hospeda a loja de dados de origem ou numa máquina separada para evitar competir por recursos com a loja de dados.

O Data Management Gateway liga fontes de dados no local aos serviços na nuvem de forma segura e gerida. Consulte [os dados de Move entre as instalações e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter mais informações sobre o Data Management Gateway.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que empurra os dados de uma loja de dados de origem para um índice de pesquisa utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API** e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON.  Para obter uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados para índice de pesquisa, consulte [o exemplo JSON: Copiar dados do SQL Server para uma](#json-example-copy-data-from-sql-server-to-azure-cognitive-search-index) secção de índice de pesquisa cognitiva Azure deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas de um Índice de Pesquisa:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

A tabela seguinte fornece descrições para elementos JSON específicos do serviço Azure Cognitive Search.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| tipo | A propriedade tipo deve ser definida para: **AzureSearch**. | Yes |
| url | URL para o serviço de pesquisa. | Yes |
| key | Chave de administração para o serviço de pesquisa. | Yes |

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados. A secção **typeProperties** é diferente para cada tipo de conjunto de dados. A secção typeProperties para um conjunto de dados do tipo **AzureSearchIndex** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| tipo | A propriedade tipo deve ser definida para **AzureSearchIndex**.| Yes |
| nome do índice | Nome do índice de pesquisa. A Data Factory não cria o índice. O índice deve existir na Pesquisa Cognitiva Azure. | Yes |


## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Criar oleodutos.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e várias políticas estão disponíveis para todos os tipos de atividades. Enquanto que as propriedades disponíveis na secção de tipos de produtos variam com cada tipo de atividade. Para a Atividade de Cópia, variam consoante os tipos de fontes e pias.

Para a Atividade de Cópia, quando a pia é do tipo **AzureSearchIndexSink,** as seguintes propriedades estão disponíveis na secção de tipos de propriedade:

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| Escrever Comportamento | Especifica se deve fundir ou substituir quando um documento já existe no índice. Consulte a [propriedade WriteBehavior](#writebehavior-property).| Fusão (padrão)<br/>Carregar| No |
| WriteBatchsize | Envia dados para o índice de pesquisa quando o tamanho do tampão atinge o writeBatchSize. Consulte a [propriedade WriteBatchSize](#writebatchsize-property) para mais detalhes. | De 1 a 1.000. O valor predefinido é de 1000. | No |

### <a name="writebehavior-property"></a>Propriedade WriteBehavior
AzureSearchSinksersers ao escrever dados. Por outras palavras, ao escrever um documento, se a chave de documento já existe no índice de pesquisa, a Azure Cognitive Search atualiza o documento existente em vez de lançar uma exceção de conflito.

O AzureSearchSink fornece os seguintes dois comportamentos mais abrangentes (utilizando a AzureSearch SDK):

- **Fusão:** combine todas as colunas do novo documento com a existente. Para colunas com valor nulo no novo documento, o valor do existente é preservado.
- **Upload**: O novo documento substitui o existente. Para as colunas não especificadas no novo documento, o valor é definido para nula se existe ou não um valor não nulo no documento existente.

O comportamento padrão é **a Fusão.**

### <a name="writebatchsize-property"></a>Propriedade WriteBatchsize
O serviço de Pesquisa Cognitiva Azure suporta a escrita de documentos como um lote. Um lote pode conter 1 a 1.000 Ações. Uma ação trata de um documento para executar a operação de upload/fusão.

### <a name="data-type-support"></a>Suporte ao tipo de dados
A tabela seguinte especifica se um tipo de dados de pesquisa cognitiva Azure é suportado ou não.

| Tipo de dados de pesquisa cognitiva Azure | Suportado em Azure Cognitive Search Sink |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double (Duplo) | Y |
| Booleano | Y |
| DataTimeOffset | Y |
| Matriz de Cordas | N |
| GeografiaPoint | N |

## <a name="json-example-copy-data-from-sql-server-to-azure-cognitive-search-index"></a>Exemplo JSON: Copiar dados do SQL Server para o índice de pesquisa cognitiva Azure

A seguinte amostra mostra:

1. Um serviço ligado do tipo [AzureSearch](#linked-service-properties).
2. Um serviço ligado do tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureSearchIndex](#dataset-properties).
4. Um [pipeline](data-factory-create-pipelines.md) com uma atividade copy que utiliza [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) e [AzureSearchIndexSink](#copy-activity-properties).

A amostra copia os dados da série de tempo de uma base de dados do SQL Server para pesquisar o índice de hora em hora. As propriedades JSON utilizadas nesta amostra são descritas em secções que seguem as amostras.

Como primeiro passo, confiem o portal de gestão de dados na sua máquina de bordo. As instruções estão nos [dados móveis entre locais no local e](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem.

**Serviço ligado à Pesquisa Cognitiva Azure:**

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

**Serviço ligado ao SQL Server**

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

**Conjunto de dados de entrada do SQL Server**

A amostra pressupõe que criou uma tabela "MyTable" no SQL Server e contém uma coluna chamada "timetampcolumn" para dados da série de tempo. Pode consultar várias tabelas dentro da mesma base de dados utilizando um único conjunto de dados, mas uma única tabela deve ser usada para a tabela datasetName TipoProperty.

Definição "externa": "verdadeiro" informa o serviço Data Factory que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída de pesquisa cognitiva Azure:**

A amostra copia dados para um índice de pesquisa cognitiva Azure **nomeados produtos**. A Data Factory não cria o índice. Para testar a amostra, crie um índice com este nome. Crie o índice de pesquisa com o mesmo número de colunas que no conjunto de dados de entrada. Novas entradas são adicionadas ao índice de pesquisa a cada hora.

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

**Copiar atividade em um oleoduto com fonte SQL e Azure Cognitive Search Index pia:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **SqlSource** e o tipo **de pia** é definido para **AzureSearchIndexSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na hora passada para copiar.

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

Se estiver a copiar dados de uma loja de dados em nuvem para a Azure Cognitive Search, `executionLocation` é necessária propriedade. O seguinte snippet JSON mostra a mudança necessária na Copy Activity `typeProperties` como exemplo. Consulte [os dados da Cópia entre as lojas de dados em nuvem](data-factory-data-movement-activities.md#global) para obter valores suportados e mais detalhes.

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


## <a name="copy-from-a-cloud-source"></a>Cópia de uma fonte de nuvem
Se estiver a copiar dados de uma loja de dados em nuvem para a Azure Cognitive Search, `executionLocation` é necessária propriedade. O seguinte snippet JSON mostra a mudança necessária na Copy Activity `typeProperties` como exemplo. Consulte [os dados da Cópia entre as lojas de dados em nuvem](data-factory-data-movement-activities.md#global) para obter valores suportados e mais detalhes.

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

Também pode mapear colunas de conjunto de dados de origem para colunas a partir do conjunto de dados da pia na definição de atividade de cópia. Para mais informações, consulte [as colunas mapping dataset na Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização
Consulte o guia de [desempenho e afinação da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber sobre os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) e várias formas de otimizá-lo.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

* [Copiar tutorial de atividade](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para instruções passo a passo para a criação de um oleoduto com uma Atividade de Cópia.
