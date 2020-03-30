---
title: Copiar dados para/a partir da Base de Dados Azure SQL
description: Saiba como copiar dados de/para a Base de Dados Azure SQL utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7fc0b2822195d952c2a4f9c02bf3758c0e2b809a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260504"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Copiar dados de e para a Base de Dados Azure SQL utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-azure-sql-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-sql-database.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector de base de dados Azure SQL em V2](../connector-azure-sql-database.md).

Este artigo explica como usar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de e para a Base de Dados Azure SQL. Baseia-se no artigo Atividades do Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **da Base de Dados Azure SQL** para as seguintes lojas de dados:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados das seguintes lojas de dados para a Base de **Dados Azure SQL:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Tipo de autenticação suportada
O conector de base de dados Azure SQL suporta a autenticação básica.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para uma Base de Dados SQL Azure utilizando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é utilizar o **Assistente de Cópia**. Ver [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um pipeline utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos.
2. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de blob Azure para uma base de dados Azure SQL, cria dois serviços ligados para ligar a sua conta de armazenamento Azure e a base de dados Azure SQL à sua fábrica de dados. Para propriedades de serviço selecionadas específicas da Base de Dados Azure SQL, consulte a secção de propriedades de [serviço ligadas.](#linked-service-properties)
3. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado no último passo, cria-se um conjunto de dados para especificar o recipiente e a pasta blob que contém os dados de entrada. E cria outro conjunto de dados para especificar a tabela SQL na base de dados Azure SQL que detém os dados copiados do armazenamento de blob. Para propriedades de conjunto de dados específicas da Azure Data Lake Store, consulte a secção de propriedades do conjunto de [dados.](#dataset-properties)
4. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, você usa blobSource como fonte e SqlSink como um lavatório para a atividade de cópia. Da mesma forma, se estiver a copiar da Base de Dados Azure SQL para o Armazenamento De Blob Azure, utiliza o SqlSource e o BlobSink na atividade da cópia. Para propriedades de copy activity específicas para a Base de Dados Azure SQL, consulte a secção de propriedades de atividade de [cópia.](#copy-activity-properties) Para obter mais informações sobre como utilizar uma loja de dados como fonte ou pia, clique no link na secção anterior para a sua loja de dados.

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON. Para amostras com definições JSON para entidades da Fábrica de Dados que são usadas para copiar dados de/para uma base de dados Azure SQL, consulte a secção de [exemplos jSON](#json-examples-for-copying-data-to-and-from-sql-database) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Fábrica de Dados específicas da Base de Dados Azure SQL:

## <a name="linked-service-properties"></a>Propriedades de serviço seletos
Um serviço ligado ao Azure SQL liga uma base de dados Azure SQL à sua fábrica de dados. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao Azure SQL.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **AzureSqlDatabase** |Sim |
| conexãoString |Especifique as informações necessárias para se ligar à base de dados Azure SQL para a propriedade de ligaçãoString. Apenas a autenticação básica é suportada. |Sim |

> [!IMPORTANT]
> Configure firewall de base de [dados Azure SQL](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) o servidor de base de dados para [permitir que os Serviços Azure acedam ao servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se estiver a copiar dados para a Base de Dados Azure SQL de fora do Azure, incluindo a partir de fontes de dados no local com gateway de fábrica de dados, configure o intervalo de endereçoip apropriado para a máquina que está a enviar dados para a Base de Dados Azure SQL.

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para especificar um conjunto de dados para representar dados de entrada ou saída numa base de dados Azure SQL, definiu a propriedade do tipo do conjunto de dados para: **AzureSqlTable**. Detete a propriedade **linkedServiceName** do conjunto de dados para o nome do serviço ligado ao Azure SQL.

Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntos de dados (Azure SQL, Azure blob, tabela Azure, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção **TypeProperties** para o conjunto de dados do tipo **AzureSqlTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na instância de base de dados Azure SQL a que o serviço ligado se refere. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções & propriedades disponíveis para definir atividades, consulte o artigo [Creating Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e a política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Copy Activity leva apenas uma entrada e produz apenas uma saída.

Considerando que as propriedades disponíveis na secção **typeProperties** da atividade variam com cada tipo de atividade. Para a atividade de Cópia, variam dependendo dos tipos de fontes e pias.

Se estiver a mover dados de uma base de dados Azure SQL, delineie o tipo de origem na atividade de cópia para **SqlSource**. Da mesma forma, se estiver a mover dados para uma base de dados Azure SQL, delineie o tipo de pia na atividade de cópia para **SqlSink**. Esta secção fornece uma lista de propriedades suportadas por SqlSource e SqlSink.

### <a name="sqlsource"></a>SqlSource
Na atividade de cópia, quando a fonte é do tipo **SqlSource,** as seguintes propriedades estão disponíveis na secção **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. A última declaração sQL deve ser uma declaração SELECT no procedimento armazenado. |Não |
| parâmetros de procedimento saqueados |Parâmetros para o procedimento armazenado. |Nome/pares de valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. |Não |

Se o **sqlReaderQuery** for especificado para o SqlSource, a Atividade de Cópia executa esta consulta contra a fonte de base de dados Azure SQL para obter os dados. Em alternativa, pode especificar um procedimento armazenado especificando o nome de **procedimento sqlReaderStoredEdE** e **os parâmetros de procedimento armazenados** (se o procedimento armazenado tiver parâmetros).

Se não especificar o sqlReaderQuery ou o sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura`select column1, column2 from mytable`do conjunto de dados JSON são usadas para construir uma consulta () para ser executada contra a Base de Dados Azure SQL. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.

> [!NOTE]
> Quando utiliza o nome de **procedimento sqlReaderStored**, ainda precisa especificar um valor para a propriedade **tableName** no conjunto de dados JSON. No entanto, não há validações realizadas contra esta tabela.
>
>

### <a name="sqlsource-example"></a>Exemplo sqlSource

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**A definição de procedimento armazenado:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>SqlSink
**A SqlSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| escreverBatchTimeout |Aguarde o tempo para que a operação de inserção do lote esteja concluída antes de sair. |tempospan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |
| escreverBatchSize |Insere os dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanupScript |Especifique uma consulta para a Atividade de Cópia executar de tal forma que os dados de uma fatia específica são limpos. Para mais informações, consulte [a cópia repetível](#repeatable-copy). |Uma declaração de consulta. |Não |
| sliceIdentifierColumnName |Especifique um nome de coluna para a Copy Activity para preencher com identificador de fatias gerado saqueado automaticamente, que é usado para limpar dados de uma fatia específica quando reexecutado. Para mais informações, consulte [a cópia repetível](#repeatable-copy). |Nome da coluna de uma coluna com tipo de dados de binário(32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que define como aplicar dados de origem em tabela-alvo, por exemplo, fazer upserts ou transformar usando a sua própria lógica de negócio. <br/><br/>Note que este procedimento armazenado será **invocado por lote**. Se quiser fazer uma operação que só funciona uma vez e não tem nada a `sqlWriterCleanupScript` ver com dados de origem, por exemplo, eliminar/truncar, utilize a propriedade. |Nome do procedimento armazenado. |Não |
| parâmetros de procedimento saqueados |Parâmetros para o procedimento armazenado. |Nome/pares de valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. |Não |
| sqlWriterTableType |Especifique um nome de tipo de tabela a utilizar no procedimento armazenado. A atividade de cópia disponibiliza os dados disponíveis numa tabela temporária com este tipo de tabela. O código de procedimento armazenado pode então fundir os dados que estão a ser copiados com os dados existentes. |Um nome tipo de mesa. |Não |

#### <a name="sqlsink-example"></a>Exemplo sqlSink

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Exemplos jSON para copiar dados de e para a Base de Dados SQL
Os exemplos seguintes fornecem definições JSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostram como copiar dados de e para a Base de Dados Azure SQL e armazenamento de Blob Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer fonte para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Exemplo: Copiar dados da Base de Dados Azure SQL para O Blob Azure
O mesmo define as seguintes entidades data Factory:

1. Um serviço ligado do tipo [AzureSqlDatabase](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureSqlTable](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com uma atividade de Cópia que utiliza [SqlSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados da série tempora (hora, diariamente, etc.) de uma tabela na base de dados Azure SQL para uma bolha a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado à base de dados Azure SQL:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Consulte a secção de ServiçoS Ligados Azure SQL para obter a lista de imóveis suportados por este serviço ligado.

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
Consulte o artigo da [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) para a lista de imóveis suportados por este serviço ligado.


**Conjunto de dados de entrada Azure SQL:**

A amostra pressupõe que criou uma tabela "MyTable" no Azure SQL e contém uma coluna chamada "timestampcolumn" para dados da série time.

Definição "externa": "verdadeira" informa o serviço Azure Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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

Consulte a secção de propriedades do tipo de conjunto de dados Azure SQL para obter a lista de propriedades suportadas por este tipo de conjunto de dados.

**Conjunto de dados de saída de Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza partes ano, mês, dia e horas da hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
Consulte a secção de propriedades do tipo de conjunto de [dados Azure Blob](data-factory-azure-blob-connector.md#dataset-properties) para obter a lista de propriedades suportadas por este tipo de conjunto de dados.

**Uma atividade de cópia num oleoduto com fonte SQL e pia Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **SqlSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora para copiar.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
No exemplo, o **sqlReaderQuery** é especificado para o SqlSource. A Atividade de Cópia executa esta consulta contra a fonte de base de dados Azure SQL para obter os dados. Em alternativa, pode especificar um procedimento armazenado especificando o nome de **procedimento sqlReaderStoredEdE** e **os parâmetros de procedimento armazenados** (se o procedimento armazenado tiver parâmetros).

Se não especificar o sqlReaderQuery ou o sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são usadas para construir uma consulta para ser executada contra a Base de Dados Azure SQL. Por exemplo: `select column1, column2 from mytable`. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.

Consulte a secção [Sql Source](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) para obter a lista de propriedades suportadas por SqlSource e BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Exemplo: Copiar dados de Azure Blob para Base de Dados SQL Azure
A amostra define as seguintes entidades data Factory:

1. Um serviço ligado do tipo [AzureSqlDatabase](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureSqlTable](#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade copy que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlSink](#copy-activity-properties).

A amostra copia dados da série tempora (hora, diariamente, etc.) da bolha Azure para uma tabela na base de dados Azure SQL a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado ao Azure SQL:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Consulte a secção de ServiçoS Ligados Azure SQL para obter a lista de imóveis suportados por este serviço ligado.

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
Consulte o artigo da [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) para a lista de imóveis suportados por este serviço ligado.


**Conjunto de dados de entrada Azure Blob:**

Os dados são recolhidos a partir de uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e o nome do ficheiro utiliza a parte da hora da hora de início. "externa": a definição "verdadeira" informa o serviço data Factory de que esta tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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
Consulte a secção de propriedades do tipo de conjunto de [dados Azure Blob](data-factory-azure-blob-connector.md#dataset-properties) para obter a lista de propriedades suportadas por este tipo de conjunto de dados.

**Conjunto de dados de saída da Base de Dados Azure SQL:**

A amostra copia dados para uma tabela chamada "MyTable" no Azure SQL. Crie a tabela em Azure SQL com o mesmo número de colunas que espera que o ficheiro Blob CSV contenha. Novas filas são adicionadas à mesa a cada hora.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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
Consulte a secção de propriedades do tipo de conjunto de dados Azure SQL para obter a lista de propriedades suportadas por este tipo de conjunto de dados.

**Uma atividade de cópia num oleoduto com fonte Blob e pia SQL:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **BlobSource** e o tipo **de pia** é definido para **SqlSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
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
Consulte a secção [Sql Sink](#sqlsink) e [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) para obter a lista de propriedades suportadas por SqlSink e BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade na base de dados alvo
Esta secção fornece um exemplo para copiar dados de uma tabela de origem sem uma coluna de identidade para uma tabela de destino com uma coluna de identidade.

**Tabela de origem:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Tabela de destino:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
Reparem que a mesa-alvo tem uma coluna de identidade.

**Definição jSON de data de origem**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Definição jSON de data de destino**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Note que como a sua fonte e tabela alvo têm esquemas diferentes (o alvo tem uma coluna adicional com identidade). Neste cenário, é necessário especificar a **propriedade da estrutura** na definição de conjunto de dados alvo, que não inclui a coluna de identidade.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocar procedimento armazenado a partir de pia SQL
Por exemplo, invocando um procedimento armazenado a partir de um sumidouro SQL numa atividade de cópia de um gasoduto, consulte o procedimento invocado para o [sumidouro SQL no](data-factory-invoke-stored-procedure-from-copy-activity.md) artigo de atividade de cópia.

## <a name="type-mapping-for-azure-sql-database"></a>Mapeamento de tipo para Base de Dados SQL Azure
Conforme referido nas [atividades](data-factory-data-movement-activities.md) de movimento de dados do artigo Atividade de cópia realiza conversões automáticas de tipos de origem para tipos de sink com a seguinte abordagem de 2 passos:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter do tipo .NET para o tipo de pia nativa

Ao mover dados de e para a Base de Dados Azure SQL, os seguintes mapeamentos são utilizados do tipo SQL para o tipo .NET e vice-versa. O mapeamento é o mesmo que o Mapeamento do Tipo de Dados do Servidor SQL para ADO.NET.

| Tipo de motor de base de dados do servidor SQL | Tipo de quadro .NET |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Booleano |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datatimeoffset |DataTimeOffset |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| dinheiro |Decimal |
| nchar |String, Char[] |
| ntexto |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Único |
| linhaversão |Byte[] |
| tempo de data pequena |DateTime |
| smallint |Int16 |
| dinheiro pequeno |Decimal |
| sql_variant |Objeto * |
| texto |String, Char[] |
| hora |TimeSpan |
| carimbo de data/hora |Byte[] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas
Para aprender sobre as colunas de mapeamento em conjunto de dados de origem para colunas em conjunto de dados de sumidouro, consulte [mapeando colunas](data-factory-map-columns.md)de conjunto de dados na Azure Data Factory .

## <a name="repeatable-copy"></a>Cópia repetível
Ao copiar dados para a Base de Dados do Servidor SQL, a atividade de cópia anexa os dados à tabela do lavatório por padrão. Para executar um UPSERT em vez disso, consulte a escrita repetível para o artigo [da SqlSink.](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink)

Ao copiar dados de lojas de dados relacionais, tenha em mente a repetível para evitar resultados não intencionais. Na Azure Data Factory, pode reproduzir uma fatia manualmente. Também pode configurar a política de retry para um conjunto de dados para que uma fatia seja reexecutada quando ocorre uma falha. Quando uma fatia é reexecutada de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada. Ver [Leitura repetível a partir de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Afinação
Consulte o [Copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Do Azure e várias formas de o otimizar.
