---
title: Enviar dados para o índice de pesquisa com o Data Factory | Documentos da Microsoft
description: Saiba mais sobre como enviar dados para o índice da Azure Search utilizando o Azure Data Factory.
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
ms.openlocfilehash: 7ad328eec7e16b5368b78a0dfccbf5c09adb5c13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567236"
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Enviar dados por push para um índice da Azure Search utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-azure-search-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-search.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector de Azure Search no V2](../connector-azure-search.md).

Este artigo descreve como utilizar a atividade de cópia para enviar dados por push de um arquivo de dados de origem suportada para o índice da Azure Search. Arquivos de dados de origem suportada estão listados na coluna de origem do [suportado origens e sinks](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Este artigo baseia-se a [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral de movimento de dados com a atividade de cópia e combinações de loja de dados suportados.

## <a name="enabling-connectivity"></a>Ativar a conectividade
Para permitir que o serviço de ligar a um arquivo de dados no local do Data Factory, instalar o Data Management Gateway no seu ambiente no local. É possível instalar o gateway na mesma máquina que armazenam os anfitriões a origem de dados ou numa máquina separada para evitar competindo por recursos com o arquivo de dados.

O Data Management Gateway liga-se origens de dados no local para serviços cloud de forma segura e gerida. Ver [mover dados entre locais e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter detalhes sobre o Data Management Gateway.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que emite dados de um arquivo de dados de origem para o índice da Azure Search utilizando ferramentas/APIs diferentes.

A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia.
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados para o índice da Azure Search, consulte [exemplo de JSON: Copiar dados do SQL Server no local para o índice da Azure Search](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) seção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON, que são utilizadas para definir entidades do Data Factory específicas para o índice da Azure Search:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

A tabela seguinte fornece descrições para os elementos JSON que são específicas para o serviço ligado do Azure Search.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| type | A propriedade de tipo tem de ser definida como: **AzureSearch**. | Sim |
| url | URL para o serviço Azure Search. | Sim |
| key | Chave de administrador para o serviço Azure Search. | Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados. O **typeProperties** secção é diferente para cada tipo de conjunto de dados. Os typeProperties secção para um conjunto de dados do tipo **AzureSearchIndex** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| type | A propriedade de tipo deve ser definida como **AzureSearchIndex**.| Sim |
| indexName | Nome do índice da Azure Search. Fábrica de dados não cria o índice. O índice tem de existir no Azure Search. | Sim |


## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de seções e as propriedades que estão disponíveis para a definição de atividades, consulte a [Criar pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, entrada e saída de tabelas e várias políticas estão disponíveis para todos os tipos de atividades. Ao passo que, propriedades disponíveis na secção typeProperties variar de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam consoante os tipos de origens e sinks.

Para a atividade de cópia, quando o sink é do tipo **AzureSearchIndexSink**, as seguintes propriedades estão disponíveis na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Especifica se deve intercalar ou substituir quando um documento já existe no índice. Consulte a [WriteBehavior propriedade](#writebehavior-property).| Intercalar (predefinição)<br/>Carregar| Não |
| WriteBatchSize | Carrega dados para o índice da Azure Search, quando o tamanho do buffer atinge writeBatchSize. Consulte a [WriteBatchSize propriedade](#writebatchsize-property) para obter detalhes. | 1 a 1000. Valor predefinido é 1000. | Não |

### <a name="writebehavior-property"></a>Propriedade de WriteBehavior
AzureSearchSink upserts ao gravar dados. Em outras palavras, ao escrever um documento, se a chave do documento já existe no índice de pesquisa do Azure, o Azure Search atualiza o documento existente, em vez de gerar uma exceção de conflito.

O AzureSearchSink fornece os seguintes dois comportamentos de upsert (ao utilizar o SDK do AzureSearch):

- **Intercalar**: combinar todas as colunas no novo documento com a já existente. Para colunas com um valor nulo no documento novo, o valor a já existente é preservado.
- **Carregar**: O novo documento substitui a já existente. Para colunas não especificadas no novo documento, o valor é definido como nulo se existe um valor não nulo no documento existente ou não.

O comportamento predefinido é **intercalar**.

### <a name="writebatchsize-property"></a>Propriedade de WriteBatchSize
O serviço de pesquisa do Azure suporta a escrita de documentos como um lote. Um batch pode conter ações de 1 a 1000. Uma ação processa um documento para executar a operação de carregamento/intercalação.

### <a name="data-type-support"></a>Tipo de dados de suporte
A seguinte tabela especifica se um tipo de dados do Azure Search é suportado ou não.

| Tipo de dados do Azure Search | Suportado no Sink do Azure Search |
| ---------------------- | ------------------------------ |
| String | S |
| Int32 | S |
| Int64 | S |
| Double | S |
| Boolean | S |
| DataTimeOffset | S |
| String Array | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>Exemplo JSON: Copiar dados do SQL Server no local para o índice da Azure Search

O exemplo a seguir mostra:

1. Um serviço ligado do tipo [AzureSearch](#linked-service-properties).
2. Um serviço ligado do tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureSearchIndex](#dataset-properties).
4. R [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) e [AzureSearchIndexSink](#copy-activity-properties).

O exemplo copia dados de séries de tempo de uma base de dados do SQL Server no local para um índice da Azure Search à hora. As propriedades JSON utilizadas neste exemplo são descritas nas seções a seguir os exemplos.

Como primeiro passo, configure o gateway de gestão de dados no seu computador no local. As instruções estão no [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo.

**Serviço ligado de pesquisa do Azure:**

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

**Serviço ligado do SQL Server**

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

O exemplo pressupõe que criou uma tabela "MyTable" no SQL Server e contém uma coluna chamada "timestampcolumn" para dados de séries de tempo. Pode consultar ao longo de várias tabelas numa base de dados mesmo com um único conjunto de dados, mas uma única tabela deve ser utilizada para typeProperty de tableName o conjunto de dados.

A definição "externo": "true" informa serviço Data Factory que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

**Conjunto de dados de pesquisa do Azure:**

O exemplo copia dados para um índice da Azure Search com o nome **produtos**. Fábrica de dados não cria o índice. Para testar o exemplo, crie um índice com este nome. Crie o índice da Azure Search com o mesmo número de colunas, tal como o conjunto de dados de entrada. São adicionadas novas entradas para o índice da Azure Search a cada hora.

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

**Atividade de cópia num pipeline com a origem SQL e de sink de índice da Azure Search:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **SqlSource** e **sink** tipo está definido como **AzureSearchIndexSink**. A consulta SQL especificada para o **SqlReaderQuery** propriedade seleciona os dados na hora anterior para copiar.

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

Se estiver a copiar dados de um arquivo de dados na cloud para o Azure Search, `executionLocation` propriedade é necessária. O fragmento JSON seguinte mostra a alteração necessária na atividade de cópia `typeProperties` como exemplo. Verifique [copiar dados entre arquivos de dados de cloud](data-factory-data-movement-activities.md#global) secção para valores suportados e mais detalhes.

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


## <a name="copy-from-a-cloud-source"></a>Copiar de uma origem de cloud
Se estiver a copiar dados de um arquivo de dados na cloud para o Azure Search, `executionLocation` propriedade é necessária. O fragmento JSON seguinte mostra a alteração necessária na atividade de cópia `typeProperties` como exemplo. Verifique [copiar dados entre arquivos de dados de cloud](data-factory-data-movement-activities.md#global) secção para valores suportados e mais detalhes.

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

Também pode mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de sink na definição da atividade de cópia. Para obter detalhes, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização
Consulte a [guia de sintonização de desempenho de atividade de cópia e](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) e diversas maneiras para otimizá-lo.

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

* [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.
