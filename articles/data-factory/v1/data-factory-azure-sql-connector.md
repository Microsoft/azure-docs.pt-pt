---
title: Copiar dados de/para a Base de Dados Azure SQL
description: Saiba como copiar dados de/para a Base de Dados Azure SQL utilizando a Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 738b875a273faddd20a67be0f6feb90825f66c9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370517"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Copiar dados de e para a Base de Dados Azure SQL usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-azure-sql-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-sql-database.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector Azure SQL Database em V2](../connector-azure-sql-database.md).

Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de e para a Base de Dados Azure SQL. Baseia-se no artigo de Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **da Base de Dados Azure SQL** para as seguintes lojas de dados:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados das seguintes lojas de dados **para a Base de Dados Azure SQL:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Tipo de autenticação suportada
O conector Azure SQL Database suporta a autenticação básica.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para uma Base de Dados Azure SQL utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API** e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos.
2. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de blob Azure para Azure SQL Database, cria dois serviços ligados para ligar a sua conta de armazenamento Azure e a Base de Dados Azure SQL à sua fábrica de dados. Para propriedades de serviços ligadas específicas à Base de Dados Azure SQL, consulte a secção [de propriedades de serviços ligadas.](#linked-service-properties)
3. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado no último passo, cria-se um conjunto de dados para especificar o recipiente blob e a pasta que contém os dados de entrada. E cria outro conjunto de dados para especificar a tabela SQL na Base de Dados Azure SQL que detém os dados copiados do armazenamento do blob. Para obter propriedades de conjunto de dados específicas da Azure Data Lake Store, consulte a secção [de propriedades do conjunto de dados.](#dataset-properties)
4. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, você usa BlobSource como uma fonte e SqlSink como um lavatório para a atividade da cópia. Da mesma forma, se estiver a copiar da Base de Dados Azure SQL para o Azure Blob Storage, utilize o SqlSource e o BlobSink na atividade da cópia. Para obter propriedades de atividade de cópia específicas da Base de Dados Azure SQL, consulte a secção [de propriedades da atividade de cópia.](#copy-activity-properties) Para obter mais informações sobre como utilizar uma loja de dados como fonte ou pia, clique no link na secção anterior para a sua loja de dados.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON. Para amostras com definições JSON para entidades da Data Factory que são usadas para copiar dados de/para uma Base de Dados Azure SQL, consulte a secção de [exemplos JSON](#json-examples-for-copying-data-to-and-from-sql-database) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas da Base de Dados Azure SQL:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
Um serviço ligado Azure SQL liga a Base de Dados Azure SQL à sua fábrica de dados. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado Azure SQL.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **AzureSqlDatabase** |Yes |
| conexãoStragem |Especifique as informações necessárias para ligar à instância de Base de Dados Azure SQL para a propriedade connectionString. Apenas a autenticação básica é suportada. |Yes |

> [!IMPORTANT]
> Configure [Azure SQL Database Firewall](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) o servidor de base de dados para permitir que os [Serviços Azure acedam ao servidor](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure). Além disso, se estiver a copiar dados para a Base de Dados Azure SQL de fora de Azure, incluindo de fontes de dados no local com gateway de data factory, configufique a gama de endereços IP apropriada para a máquina que está a enviar dados para a Base de Dados Azure SQL.

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para especificar um conjunto de dados para representar dados de entrada ou saída na Base de Dados Azure SQL, define a propriedade tipo do conjunto de dados para: **AzureSqlTable**. Defina a propriedade **linkedServiceName** do conjunto de dados com o nome do serviço ligado Azure SQL.

Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, Azure blob, Azure table, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção **de tipos de direitos** para o conjunto de dados do tipo **AzureSqlTable** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na página da Base de Dados Azure SQL a que o serviço ligado se refere. |Yes |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Atividade de Cópia leva apenas uma entrada e produz apenas uma saída.

Enquanto que as propriedades disponíveis na secção **de tipos de atividade** variam com cada tipo de atividade. Para a atividade copy, variam dependendo dos tipos de fontes e pias.

Se estiver a mover dados da Base de Dados Azure SQL, deteta o tipo de origem na atividade de cópia para **SqlSource**. Da mesma forma, se estiver a transferir dados para a Base de Dados Azure SQL, define o tipo de pia na atividade da cópia para **o SqlSink**. Esta secção fornece uma lista de propriedades suportadas por SqlSource e SqlSink.

### <a name="sqlsource"></a>SqlSource
Na atividade de cópia, quando a fonte é do tipo **SqlSource,** as seguintes propriedades estão disponíveis na secção **de tipos de propriedades:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Exemplo: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados da tabela de origem. |Nome do procedimento armazenado. A última declaração SQL deve ser uma declaração SELECT no procedimento armazenado. |No |
| parametrómetros de reserva armazenados |Parâmetros para o procedimento armazenado. |Pares de nomes/valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. |No |

Se o **SqlReaderQuery** for especificado para o SqlSource, a Atividade de Cópia executa esta consulta com a fonte de Base de Dados Azure SQL para obter os dados. Em alternativa, pode especificar um procedimento armazenado especificando o **nome sqlReaderStoredProcedureName** e **os Computadores Deprocedures armazenados** (se o procedimento armazenado tiver parâmetros).

Se não especificar nem sqlReaderQuery ou sqlReaderStorStoredProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são utilizadas para construir uma consulta `select column1, column2 from mytable` () para correr contra a Base de Dados Azure SQL. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.

> [!NOTE]
> Quando utilizar **o nome sqlReaderStoredProcedureName,** ainda precisa de especificar um valor para a propriedade **tableName** no conjunto de dados JSON. No entanto, não há validações feitas contra esta tabela.
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

**Definição de procedimento armazenado:**

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
**SqlSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| escreverBatchTimeout |Tempo de espera para que o funcionamento do encaixe do lote esteja concluído antes de esgotar o tempo. |timespan<br/><br/> Exemplo: "00:30:00" (30 minutos). |No |
| escreverBatchSize |Insere dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanUpScript |Especifique uma consulta para a Copy Activity para executar de modo a que os dados de uma fatia específica seja limpo. Para mais informações, consulte [a cópia repetível.](#repeatable-copy) |Uma declaração de consulta. |No |
| sliceIdentifierColumnName |Especifique um nome de coluna para a Atividade de Cópia para preencher com identificador de fatias gerados automaticamente, que é utilizado para limpar dados de uma fatia específica quando se revesse. Para mais informações, consulte [a cópia repetível.](#repeatable-copy) |Nome da coluna de uma coluna com tipo de dados binário (32). |No |
| sqlWriterStorEdProcedureName |Nome do procedimento armazenado que define como aplicar dados de origem em tabela-alvo, por exemplo, para fazer upserts ou transformar usando a sua própria lógica de negócio. <br/><br/>Note que este procedimento armazenado será **invocado por lote**. Se quiser fazer uma operação que só funciona uma vez e não tem nada a ver com dados de origem, por exemplo, apagar/truncar, utilize `sqlWriterCleanupScript` a propriedade. |Nome do procedimento armazenado. |No |
| parametrómetros de reserva armazenados |Parâmetros para o procedimento armazenado. |Pares de nomes/valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. |No |
| SqlWriterTableType |Especifique um nome de tipo de mesa a ser utilizado no procedimento armazenado. A atividade de cópia torna os dados disponíveis numa tabela temporária com este tipo de tabela. O código de procedimento armazenado pode então fundir os dados que estão a ser copiados com os dados existentes. |Um nome do tipo de mesa. |No |

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

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Exemplos JSON para copiar dados de e para a Base de Dados SQL
Os exemplos a seguir fornecem definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para a Azure SQL Database e Azure Blob Storage. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados usando a Atividade de Cópia na Fábrica de Dados Azure.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Exemplo: Copiar dados da Base de Dados Azure SQL para Azure Blob
O mesmo define as seguintes entidades da Data Factory:

1. Um serviço ligado do tipo [AzureSqlDatabase](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureSqlTable](#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [oleoduto](data-factory-create-pipelines.md) com uma atividade Copy que utiliza [SqlSource](#copy-activity-properties) e [BlobSink.](data-factory-azure-blob-connector.md#copy-activity-properties)

A amostra copia os dados da série de tempo (hora a hora, diariamente, etc.) de uma tabela na Base de Dados Azure SQL para uma bolha a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço de base de dados Azure SQL ligado:**

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
Consulte a secção Azure SQL Linked Service para obter a lista de imóveis suportados por este serviço ligado.

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
Consulte o artigo [da Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) para a lista de imóveis suportados por este serviço ligado.


**Conjunto de dados de entrada Azure SQL:**

A amostra pressupõe que criou uma tabela "MyTable" em Azure SQL e contém uma coluna chamada "timetampcolumn" para dados da série de tempo.

Definição "externa": "verdadeiro" informa o serviço Azure Data Factory que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

Consulte a secção de propriedades do tipo de conjunto de dados Azure SQL para a lista de propriedades suportadas por este tipo de conjunto de dados.

**Conjunto de dados de saída Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base na hora de início da fatia que está a ser processada. O caminho da pasta utiliza partes do ano, mês, dia e horas da hora de início.

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
Consulte a secção de propriedades do [tipo Azure Blob](data-factory-azure-blob-connector.md#dataset-properties) para a lista de propriedades suportadas por este tipo de conjunto de dados.

**Uma atividade de cópia num oleoduto com fonte SQL e pia Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **SqlSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na hora passada para copiar.

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
No exemplo, **sqlReaderQuery** é especificado para o SqlSource. A Copy Activity executa esta consulta com a fonte de Base de Dados Azure SQL para obter os dados. Em alternativa, pode especificar um procedimento armazenado especificando o **nome sqlReaderStoredProcedureName** e **os Computadores Deprocedures armazenados** (se o procedimento armazenado tiver parâmetros).

Se não especificar nem sqlReaderQuery nem sqlReaderStorStoredProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são utilizadas para construir uma consulta para correr contra a Base de Dados Azure SQL. Por exemplo: `select column1, column2 from mytable`. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.

Consulte a secção [Sql Source](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) para obter a lista de propriedades suportadas por SqlSource e BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Exemplo: Copiar dados da Azure Blob para a Base de Dados Azure SQL
A amostra define as seguintes entidades da Data Factory:

1. Um serviço ligado do tipo [AzureSqlDatabase](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureSqlTable](#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade copy que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlSink](#copy-activity-properties).

A amostra copia os dados da série de tempo (hora a hora, diariamente, etc.) da bolha de Azure para uma tabela na Base de Dados Azure SQL a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado Azure SQL:**

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
Consulte a secção Azure SQL Linked Service para obter a lista de imóveis suportados por este serviço ligado.

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
Consulte o artigo [da Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) para a lista de imóveis suportados por este serviço ligado.


**Conjunto de dados de entrada Azure Blob:**

Os dados são recolhidos a partir de uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base na hora de início da fatia que está a ser processada. O percurso da pasta utiliza a parte do ano, mês e dia da hora de início e o nome do ficheiro utiliza a parte da hora da hora de início. Definição "externa": a definição "verdadeira" informa o serviço data Factory de que esta tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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
Consulte a secção de propriedades do [tipo Azure Blob](data-factory-azure-blob-connector.md#dataset-properties) para a lista de propriedades suportadas por este tipo de conjunto de dados.

**Conjunto de dados de saída da base de dados Azure SQL:**

A amostra copia dados para uma tabela chamada "MyTable" em Azure SQL. Crie a tabela em Azure SQL com o mesmo número de colunas que espera que o ficheiro Blob CSV contenha. Novas filas são adicionadas à mesa a cada hora.

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
Consulte a secção de propriedades do tipo de conjunto de dados Azure SQL para a lista de propriedades suportadas por este tipo de conjunto de dados.

**Uma atividade de cópia num oleoduto com fonte Blob e pia SQL:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **BlobSource** e o tipo **de pia** é definido para **SqlSink**.

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
Note que a tabela alvo tem uma coluna de identidade.

**Definição JSON do conjunto de dados de origem**

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
**Definição de conjunto de dados de destino JSON**

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

Note que como a sua origem e tabela alvo têm esquemas diferentes (o alvo tem uma coluna adicional com identidade). Neste cenário, é necessário especificar a propriedade da **estrutura** na definição de conjunto de dados-alvo, que não inclui a coluna de identidade.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocar procedimento armazenado a partir de pia SQL
Para um exemplo de invocação de um procedimento armazenado da pia SQL numa atividade de cópia de um oleoduto, consulte [o procedimento de inexernação de Invoke para a pia SQL em](data-factory-invoke-stored-procedure-from-copy-activity.md) artigo de atividade de cópia.

## <a name="type-mapping-for-azure-sql-database"></a>Mapeamento de tipo para Base de Dados Azure SQL
Como mencionado nas atividades de [movimento de dados](data-factory-data-movement-activities.md) artigo A atividade copy executa conversões automáticas de tipo de origem para tipos de pia com a seguinte abordagem de 2 etapas:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter de tipo .NET para tipo de pia nativa

Ao mover dados de e para a Base de Dados Azure SQL, os seguintes mapeamentos são usados do tipo SQL para .NET tipo e vice-versa. O mapeamento é o mesmo que o Mapeamento do Tipo de Dados do Servidor SQL para ADO.NET.

| Tipo de motor de base de dados de servidor SQL | .NET Tipo de quadro |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Booleano |
| char |String, Char[] |
| data |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datatimeoff |Início de execução de tempo de data |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinário(máx)) |Byte[] |
| Float |Double (Duplo) |
| image |Byte[] |
| int |Int32 |
| dinheiro |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Único |
| rowversão |Byte[] |
| hora pequena |DateTime |
| smallint |Int16 |
| pequeno dinheiro |Decimal |
| sql_variant |Objeto * |
| texto |String, Char[] |
| hora |TimeSpan |
| carimbo de data/hora |Byte[] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinário |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas
Para obter informações sobre as colunas de mapeamento em conjunto de dados de origem para colunas no conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Cópia repetível
Ao copiar dados para a Base de Dados do Servidor SQL, a atividade de cópia anexa os dados à tabela do lavatório por predefinição. Para realizar um UPSERT em vez disso, Consulte [a escrita repetível para o artigo da SqlSink.](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink)

Ao copiar dados de lojas de dados relacionais, tenha em mente a repetibilidade para evitar resultados não intencionais. Na Azure Data Factory, pode repetir manualmente uma fatia. Também pode configurar a política de reagem para um conjunto de dados para que uma fatia seja re-executada quando ocorre uma falha. Quando uma fatia é reexame de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada. Ver [leitura repetível de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performance e Afinação
Consulte [copy Activity Performance & Guia de Afinação](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.