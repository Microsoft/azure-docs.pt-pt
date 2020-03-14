---
title: Empurre dados para o índice de pesquisa utilizando data factory
description: Saiba como empurrar dados para o Índice de Pesquisa Cognitiva Azure utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5b1170f721cf8521cfe1762df0cc616c938ddf28
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281564"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Empurre dados para um índice de pesquisa cognitiva Azure usando a Fábrica de Dados Azure
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-azure-search-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-search.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector De pesquisa cognitiva Azure em V2](../connector-azure-search.md).

Este artigo descreve como usar a Atividade de Cópia para empurrar dados de uma loja de dados de origem suportada para um índice de Pesquisa Cognitiva Azure. As lojas de dados de origem suportada estão listadas na coluna Fonte das [fontes suportadas e na tabela de pias.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Este artigo baseia-se no artigo de atividades de movimento de [dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a Copy Activity e combinações de lojas de dados suportadas.

## <a name="enabling-connectivity"></a>Permitindo a conectividade
Para permitir a ligação do serviço Data Factory a uma loja de dados no local, instala o Portal de Gestão de Dados no seu ambiente no local. Pode instalar o portal na mesma máquina que acolhe a loja de dados de origem ou numa máquina separada para evitar competir por recursos com a loja de dados.

O Portal de Gestão de Dados liga as fontes de dados no local aos serviços em nuvem de forma segura e gerida. Consulte [os dados entre as instalações e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter detalhes sobre gateway de gestão de dados.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que empurra os dados de uma loja de dados de origem para um índice de pesquisa utilizando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é utilizar o **Assistente de Cópia**. Ver [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um pipeline utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON.  Para obter uma amostra com definições JSON para entidades da Fábrica de Dados que são usadas para copiar dados para índice de pesquisa, consulte o [exemplo jSON: Copiar dados do SQL Server no local para uma](#json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index) secção do índice de pesquisa cognitiva Azure deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades data Factory específicas para um Índice de pesquisa:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

A tabela seguinte fornece descrições para elementos JSON específicos do serviço ligado à Pesquisa Cognitiva Azure.

| Propriedade | Descrição | Required |
| -------- | ----------- | -------- |
| tipo | A propriedade tipo deve ser definida para: **AzureSearch**. | Sim |
| URL | URL para o serviço de pesquisa. | Sim |
| key | Chave de administrador para o serviço de pesquisa. | Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntode dados. A secção **typeProperties** é diferente para cada tipo de conjunto de dados. A secção TypeProperties para um conjunto de dados do tipo **AzureSearchIndex** tem as seguintes propriedades:

| Propriedade | Descrição | Required |
| -------- | ----------- | -------- |
| tipo | A propriedade tipo deve ser definida para **AzureSearchIndex**.| Sim |
| indexName | Nome do índice de pesquisa. Data Factory não cria o índice. O índice deve existir na Pesquisa Cognitiva Azure. | Sim |


## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo Criação de [pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e várias políticas estão disponíveis para todos os tipos de atividades. Considerando que as propriedades disponíveis na secção typeProperties variam com cada tipo de atividade. Para a Atividade de Cópia, variam consoante os tipos de fontes e pias.

Para a Atividade de Cópia, quando o lavatório é do tipo **AzureSearchIndexSink,** as seguintes propriedades estão disponíveis na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Required |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Especifica se deve fundir ou substituir quando um documento já existe no índice. Consulte a [propriedade WriteBehavior](#writebehavior-property).| fusão (padrão)<br/>Carregar| Não |
| WriteBatchSize | Envia dados para o índice de pesquisa quando o tamanho do tampão atinge o writeBatchSize. Consulte a [propriedade WriteBatchSize](#writebatchsize-property) para mais detalhes. | 1 a 1.000. O valor padrão é de 1000. | Não |

### <a name="writebehavior-property"></a>WriteBehavior propriedade
AzureSearchSink upserts ao escrever dados. Por outras palavras, ao escrever um documento, se a chave do documento já existir no índice de pesquisa, a Pesquisa Cognitiva Azure atualiza o documento existente em vez de lançar uma exceção de conflito.

O AzureSearchSink fornece os seguintes dois comportamentos upsert (utilizando o AzureSearch SDK):

- **Fusão**: combine todas as colunas do novo documento com a existente. Para colunas com valor nulo no novo documento, o valor no existente é preservado.
- **Upload**: O novo documento substitui o existente. Para colunas não especificadas no novo documento, o valor é definido para anular se existe ou não um valor não nulo no documento existente.

O comportamento padrão é **merge**.

### <a name="writebatchsize-property"></a>Propriedade WriteBatchSize
O serviço de Pesquisa Cognitiva Azure suporta a escrita de documentos como um lote. Um lote pode conter 1 a 1.000 Ações. Uma ação trata de um documento para realizar a operação de upload/fusão.

### <a name="data-type-support"></a>Suporte de tipo de dados
A tabela seguinte especifica se um tipo de dados de Pesquisa Cognitiva Azure é suportado ou não.

| Tipo de dados de pesquisa cognitiva azure | Suportado em Pia de Pesquisa Cognitiva Azure |
| ---------------------- | ------------------------------ |
| String | S |
| Int32 | S |
| Int64 | S |
| Valor de duplo | S |
| Booleano | S |
| DataTimeOffset | S |
| String Array | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index"></a>Exemplo jSON: Copiar dados do SQL Server no local para o índice de pesquisa cognitiva Azure

A amostra que se segue mostra:

1. Um serviço ligado do tipo [AzureSearch.](#linked-service-properties)
2. Um serviço ligado do tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureSearchIndex](#dataset-properties).
4. Um [pipeline](data-factory-create-pipelines.md) com uma atividade de Cópia que utiliza [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) e [AzureSearchIndexSink](#copy-activity-properties).

A amostra copia dados da série de tempo de uma base de dados do SQL Server no local para pesquisar o índice de hora a hora. As propriedades JSON utilizadas nesta amostra são descritas em secções que seguem as amostras.

Como primeiro passo, instale a porta de entrada de gestão de dados na sua máquina no local. As instruções estão nos [dados em movimento entre os locais no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem.

**Serviço ligado à pesquisa cognitiva Azure:**

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

**Serviço ligado ao Servidor SQL**

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

**Conjunto de dados de entrada do Servidor SQL**

A amostra pressupõe que criou uma tabela "MyTable" no SQL Server e contém uma coluna chamada "timestampcolumn" para dados da série time. Pode consultar várias tabelas dentro da mesma base de dados utilizando um único conjunto de dados, mas uma única tabela deve ser utilizada para o tipo de nome de dados do conjunto de dadosProperty.

Definição "externa": "verdadeira" informa o serviço data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída de pesquisa cognitiva azure:**

A amostra copia dados para um índice de pesquisa cognitiva Azure nomeado **produtos**. Data Factory não cria o índice. Para testar a amostra, crie um índice com este nome. Crie o índice de pesquisa com o mesmo número de colunas que no conjunto de dados de entrada. Novas entradas são adicionadas ao índice de pesquisa a cada hora.

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

**Copiar atividade num oleoduto com fonte SQL e sink Do Índice de Pesquisa Cognitiva Azure:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do pipeline, o tipo de **origem** é definido para **SqlSource** e o tipo **de pia** é definido para **AzureSearchIndexSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora para copiar.

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

Se estiver a copiar dados de uma loja de dados em nuvem para a Pesquisa Cognitiva Azure, `executionLocation` propriedade é necessária. O seguinte corte JSON mostra a mudança necessária no âmbito da Atividade de Cópia `typeProperties` como exemplo. Verifique [os dados de cópia entre as lojas de dados em nuvem](data-factory-data-movement-activities.md#global) para obter valores suportados e mais detalhes.

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
Se estiver a copiar dados de uma loja de dados em nuvem para a Pesquisa Cognitiva Azure, `executionLocation` propriedade é necessária. O seguinte corte JSON mostra a mudança necessária no âmbito da Atividade de Cópia `typeProperties` como exemplo. Verifique [os dados de cópia entre as lojas de dados em nuvem](data-factory-data-movement-activities.md#global) para obter valores suportados e mais detalhes.

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

Também pode mapear colunas desde o conjunto de dados de origem até colunas a partir do conjunto de dados do sumidouro na definição de atividade da cópia. Para mais detalhes, consulte [mapeiar colunas de conjuntos](data-factory-map-columns.md)de dados na Azure Data Factory .

## <a name="performance-and-tuning"></a>Desempenho e otimização
Consulte o guia de [desempenho e afinação](data-factory-copy-activity-performance.md) da Atividade de Cópia para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) e várias formas de o otimizar.

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

* [Copy Activity tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para instruções passo a passo para criar um pipeline com uma Atividade de Cópia.
