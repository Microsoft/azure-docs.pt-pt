---
title: Copiar dados de/para o SQL Database do Azure | Documentos da Microsoft
description: Saiba como copiar dados de/para base de dados do SQL Azure com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a7789f9a3f3da46305a9d8cd7cda24019658f2ad
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811492"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Copiar dados para e da base de dados do SQL Azure com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-azure-sql-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-sql-database.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector de base de dados do Azure SQL no V2](../connector-azure-sql-database.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de e para a base de dados do Azure SQL. Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **da base de dados do Azure SQL** para os seguintes dados armazena:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados de arquivos de dados seguintes **à base de dados do Azure SQL**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Tipo de autenticação suportado
Conector da base de dados SQL do Azure suporta a autenticação básica.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de/para uma base de dados do SQL do Azure com ferramentas/APIs diferentes.

A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Criar uma **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines.
2. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de Blobs do Azure para uma base de dados SQL do Azure, criar dois serviços ligados para ligar a sua conta de armazenamento do Azure e a base de dados SQL do Azure à fábrica de dados. Para as propriedades do serviço ligado que são específicas para a base de dados do Azure SQL, consulte [propriedades do serviço ligado](#linked-service-properties) secção.
3. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. O exemplo mencionado no último passo, vai criar um conjunto de dados para especificar o contentor de BLOBs e a pasta que contém os dados de entrada. Além disso, crie outro conjunto de dados para especificar a tabela SQL na base de dados SQL do Azure que contém os dados copiados do armazenamento de Blobs. Para as propriedades do conjunto de dados que são específicas para o Azure Data Lake Store, consulte [propriedades do conjunto de dados](#dataset-properties) secção.
4. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, vai utilizar BlobSource como uma origem e SqlSink como sink para a atividade de cópia. Da mesma forma, se estiver a copiar na SQL Database do Azure para armazenamento de Blobs do Azure, utilize SqlSource e BlobSink a atividade de cópia. Para propriedades de atividade de cópia que são específicas para a base de dados do Azure SQL, consulte [propriedades da atividade copy](#copy-activity-properties) secção. Para obter detalhes sobre como usar um arquivo de dados como uma origem ou sink, clique na ligação na secção anterior para seu armazenamento de dados.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON. Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de/para uma base de dados do SQL do Azure, consulte [exemplos JSON](#json-examples-for-copying-data-to-and-from-sql-database) seção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON, que são utilizadas para definir entidades do Data Factory específicas para a base de dados do Azure SQL:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
Um SQL do Azure ligado serviço liga a uma base de dados SQL do Azure à fábrica de dados. A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço ligado SQL do Azure.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| type |A propriedade de tipo tem de ser definida como: **AzureSqlDatabase** |Sim |
| connectionString |Especifique as informações necessárias para ligar à instância do SQL Database do Azure para a propriedade connectionString. Apenas autenticação básica é suportada. |Sim |

> [!IMPORTANT]
> Configurar [Firewall de base de dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) o servidor de base de dados [permitir que os serviços do Azure aceder ao servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se estiver a copiar dados para a base de dados do Azure SQL fora do Azure incluindo de origens de dados no local com o gateway de fábrica de dados, configure o intervalo de endereços IP apropriado para a máquina que está a enviar dados para a base de dados do Azure SQL.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para especificar um conjunto de dados para representar os dados de entrada ou de saída numa base de dados SQL do Azure, defina a propriedade de tipo de conjunto de dados para: **AzureSqlTable**. Definir o **linkedServiceName** serviço ligado de propriedade de conjunto de dados para o nome do SQL do Azure.

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para o conjunto de dados do tipo **AzureSqlTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na instância da base de dados do Azure SQL pelo serviço ligado refere-se. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, entrada e saída de tabelas e a política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A atividade de cópia precisar apenas uma entrada e saída de apenas um.

Ao passo que, propriedades disponíveis no **typeProperties** secção da atividade varia com cada tipo de atividade. Para a atividade de cópia, elas variam consoante os tipos de origens e sinks.

Se estiver a mover dados de uma base de dados SQL do Azure, definir o tipo de origem na atividade de cópia para **SqlSource**. Da mesma forma, se estiver a mover dados para uma base de dados SQL do Azure, definir o tipo de sink na atividade de cópia para **SqlSink**. Esta seção fornece uma lista de propriedades suportadas pelo SqlSource e SqlSink.

### <a name="sqlsource"></a>SqlSource
Na atividade de cópia, quando a origem é do tipo **SqlSource**, as seguintes propriedades estão disponíveis no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados da tabela de origem. |Nome do procedimento armazenado. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado. |Pares de nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |

Se o **sqlReaderQuery** é especificado para o SqlSource, a atividade de cópia executa esta consulta em relação à origem de SQL Database do Azure para obter os dados. Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são utilizadas para criar uma consulta (`select column1, column2 from mytable`) para ser executado na base de dados do SQL Azure. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.

> [!NOTE]
> Quando utiliza **sqlReaderStoredProcedureName**, terá de especificar um valor para o **tableName** propriedade no conjunto de dados JSON. Não há nenhuma validação executada nessa tabela, no entanto.
>
>

### <a name="sqlsource-example"></a>Exemplo de SqlSource

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

**A definição do procedimento armazenado:**

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
| writeBatchTimeout |Tempo para a operação de inserção de lote seja concluída antes de atingir o tempo limite de espera. |TimeSpan<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| sqlWriterCleanupScript |Especifica uma consulta para a atividade de cópia executar de forma a que os dados de um setor específico é limpo. Para obter mais informações, consulte [cópia repetível](#repeatable-copy). |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique um nome de coluna para a atividade de cópia preencher com o identificador de setor gerado automaticamente, o que é utilizado para limpar os dados de um setor específico quando voltar a executar. Para obter mais informações, consulte [cópia repetível](#repeatable-copy). |Nome da coluna de uma coluna com o tipo de dados de binary(32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que define como aplicar a origem de dados na tabela de destino, por exemplo, para fazer upserts ou transformação usando sua própria lógica de negócios. <br/><br/>Tenha em atenção de que este procedimento armazenado será **invocado por lote**. Se pretender efetuar a operação que só é executado uma vez e não tem nada a fazer com os dados de origem, por exemplo, eliminar/truncar, utilize `sqlWriterCleanupScript` propriedade. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado. |Pares de nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |
| sqlWriterTableType |Especifique um nome de tipo de tabela a ser utilizado no procedimento armazenado. Atividade de cópia torna os dados que está a ser movidos disponíveis numa tabela temporária com este tipo de tabela. Código do procedimento armazenado pode então mesclar os dados a ser copiados com os dados existentes. |Um nome de tipo de tabela. |Não |

#### <a name="sqlsink-example"></a>Exemplo de SqlSink

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

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Exemplos JSON para copiar dados de e para base de dados SQL
Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para a base de dados do Azure SQL e armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Exemplo: Copiar dados de SQL Database do Azure para BLOBs do Azure
O mesmo define as seguintes entidades do Data Factory:

1. Um serviço ligado do tipo [AzureSqlDatabase](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureSqlTable](#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [BLOBs do Azure](data-factory-azure-blob-connector.md#dataset-properties).
5. R [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [SqlSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de séries de tempo (hora a hora, diária, etc.) de uma tabela na base de dados SQL do Azure para um blob a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

**Serviço de ligado de base de dados SQL do Azure:**

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
Consulte a secção de serviço ligado do SQL do Azure para obter a lista de propriedades suportadas por este serviço ligado.

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
Consulte a [BLOBs do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) artigo para obter a lista das propriedades compatíveis com este serviço ligado.


**Conjunto de dados de entrada SQL do Azure:**

O exemplo assume que criou uma tabela "MyTable" SQL do Azure e contém uma coluna chamada "timestampcolumn" para dados de séries de tempo.

A definição "externo": "true" informa o serviço Azure Data Factory que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

Consulte a SQL do Azure conjunto de dados tipo secção de propriedades para obter a lista de propriedades suportadas por este tipo de conjunto de dados.

**Conjunto de dados de Blobs do Azure:**

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

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
Consulte a [propriedades do tipo de conjunto de dados de Blobs do Azure](data-factory-azure-blob-connector.md#dataset-properties) secção para obter a lista de propriedades suportadas por este tipo de conjunto de dados.

**Uma atividade de cópia num pipeline com a origem SQL e de sink do Blob:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **SqlSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para o **SqlReaderQuery** propriedade seleciona os dados na hora anterior para copiar.

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
No exemplo, **sqlReaderQuery** especificado para o SqlSource. A atividade de cópia executa esta consulta em relação à origem de SQL Database do Azure para obter os dados. Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são utilizadas para criar uma consulta para ser executado na base de dados do SQL Azure. Por exemplo: `select column1, column2 from mytable`. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.

Consulte a [origem de Sql](#sqlsource) secção e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) para a lista de propriedades suportadas por SqlSource e BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Exemplo: Copiar dados de Blobs do Azure para a base de dados do Azure SQL
O exemplo define as seguintes entidades do Data Factory:

1. Um serviço ligado do tipo [AzureSqlDatabase](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureSqlTable](#dataset-properties).
5. R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlSink](#copy-activity-properties).

As cópias de exemplo séries de tempo (hora a hora, diária, etc.) de dados do Azure blob para uma tabela no SQL do Azure de base de dados a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

**Serviço ligado do SQL do Azure:**

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
Consulte a secção de serviço ligado do SQL do Azure para obter a lista de propriedades suportadas por este serviço ligado.

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
Consulte a [BLOBs do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) artigo para obter a lista das propriedades compatíveis com este serviço ligado.


**Conjunto de dados de entrada BLOBs do Azure:**

Dados são captados um blob novo a cada hora (frequência: hora, intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e nome de ficheiro utiliza a parte de hora a hora de início. "externo": "true" definição informa o serviço Data Factory que esta tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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
Consulte a [propriedades do tipo de conjunto de dados de Blobs do Azure](data-factory-azure-blob-connector.md#dataset-properties) secção para obter a lista de propriedades suportadas por este tipo de conjunto de dados.

**Conjunto de dados da base de dados SQL do Azure:**

O exemplo copia dados a uma tabela chamada "MyTable" no Azure SQL. Crie a tabela SQL do Azure com o mesmo número de colunas, como esperar que o ficheiro CSV de BLOBs para conter. Novas linhas são adicionadas à tabela de cada hora.

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
Consulte a SQL do Azure conjunto de dados tipo secção de propriedades para obter a lista de propriedades suportadas por este tipo de conjunto de dados.

**Uma atividade de cópia num pipeline com a origem do Blob e de sink do SQL:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **BlobSource** e **sink** tipo está definido como **SqlSink**.

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
Consulte a [Sink do Sql](#sqlsink) secção e [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) para a lista de propriedades suportadas por SqlSink e BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade na base de dados de destino
Esta secção fornece um exemplo para copiar dados a partir de uma tabela de origem sem uma coluna de identidade para uma tabela de destino com uma coluna de identidade.

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
Tenha em atenção que a tabela de destino tem uma coluna de identidade.

**Definição de JSON do conjunto de dados de origem**

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
**Definição de JSON do conjunto de dados de destino**

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

Tenha em atenção que, como sua tabela de origem e destino têm diferente esquema (o destino tem uma coluna adicional com identidade). Neste cenário, tem de especificar **estrutura** propriedade na definição de conjunto de dados de destino, que não inclui a coluna de identidade.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocar um procedimento armazenado do sink do SQL
Para obter um exemplo de invocação de um procedimento armazenado do sink do SQL numa atividade de cópia de um pipeline, veja [invocar um procedimento armazenado para o sink do SQL na atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md) artigo.

## <a name="type-mapping-for-azure-sql-database"></a>Mapeamento do tipo de base de dados do Azure SQL
Conforme mencionado na [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo atividade de cópia executa conversões de tipos automáticas de tipos de origem para o sink de tipos com a seguinte abordagem de passo 2:

1. Converter entre tipos de origem nativas para o tipo de .NET
2. Converter de tipo de .NET para o tipo de sink nativo

Ao mover dados de e para a base de dados do Azure SQL, são utilizados os seguintes mapeamentos de tipo SQL para o tipo .NET e vice-versa. O mapeamento é a mesmo que o mapeamento de tipo de dados do SQL Server para o ADO.NET.

| Tipo de motor de base de dados do SQL Server | Tipo de .NET framework |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numérico |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| texto |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>Origem do mapa para colunas de sink
Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem para colunas no conjunto de dados de sink, veja [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Cópia passível de repetição
Quando se copiam dados para a base de dados do SQL Server, a atividade de cópia acrescenta dados para a tabela do sink por predefinição. Para efetuar um UPSERT em vez disso, consulte [Repeatable escrever SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) artigo.

Quando armazena a cópia de dados de dados relacionais, tenha a capacidade de repetição em mente para evitar resultados indesejados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor será novamente executado de qualquer forma, terá de certificar-se de que os mesmos dados é de leitura não questão número de vezes que um setor é executado. Ver [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Otimização
Ver [desempenho de atividade de cópia e guia de ajuste](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo.
