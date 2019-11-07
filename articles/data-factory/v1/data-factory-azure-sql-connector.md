---
title: Copiar dados de/para o banco de dado SQL do Azure
description: Saiba como copiar dados de/para o Azure SQL Database usando Azure Data Factory.
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
ms.openlocfilehash: 8d53d61991f191d2cd0636dba918e9499c4f5d2b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683093"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Copiar dados de e para o Azure SQL Database usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-azure-sql-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-sql-database.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector do banco de dados SQL do Azure na v2](../connector-azure-sql-database.md).

Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados para dentro e para o Azure SQL Database. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados
Você pode copiar dados **do banco** de dados SQL do Azure para os seguintes armazenamentos:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos seguintes repositórios de dados para o banco de dado **SQL do Azure**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Tipo de autenticação com suporte
O conector do banco de dados SQL do Azure dá suporte à autenticação básica.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados de/para um banco de dado SQL do Azure usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Confira o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções detalhadas para criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie um **Data Factory**. Um data factory pode conter um ou mais pipelines.
2. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory. Por exemplo, se estiver copiando dados de um armazenamento de blob do Azure para um banco de dados SQL do Azure, você criará dois serviços vinculados para vincular sua conta de armazenamento do Azure e o banco de dados SQL do Azure ao seu data factory. Para propriedades do serviço vinculado que são específicas do banco de dados SQL do Azure, consulte a seção [Propriedades do serviço vinculado](#linked-service-properties) .
3. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia. No exemplo mencionado na última etapa, você cria um conjunto de dados para especificar o contêiner de BLOBs e a pasta que contém o dado de entrada. Além disso, você cria outro conjunto de dados para especificar a tabela SQL no banco de dado SQL do Azure que contém a data copiada do armazenamento de BLOBs. Para propriedades do conjunto de informações que são específicas para Azure Data Lake Store, consulte a seção [Propriedades do conjunto](#dataset-properties) de informações.
4. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída. No exemplo mencionado anteriormente, você usa o Blobname como uma origem e sqlsink como um coletor para a atividade de cópia. Da mesma forma, se você estiver copiando do banco de dados SQL do Azure para o armazenamento de BLOBs do Azure, use sqlsource e BlobSink na atividade de cópia. Para propriedades da atividade de cópia que são específicas do banco de dados SQL do Azure, consulte a seção [Propriedades da atividade de cópia](#copy-activity-properties) . Para obter detalhes sobre como usar um armazenamento de dados como uma origem ou um coletor, clique no link na seção anterior para seu armazenamento de dados.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON. Para obter exemplos com definições de JSON para Data Factory entidades que são usadas para copiar dados de/para um Azure SQL Database, consulte a seção [exemplos de JSON](#json-examples-for-copying-data-to-and-from-sql-database) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para o banco de dados SQL do Azure:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
Um serviço vinculado do SQL do Azure vincula um banco de dados SQL do Azure ao seu data factory. A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do SQL do Azure.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **AzureSqlDatabase** |Sim |
| connectionString |Especifique as informações necessárias para se conectar à instância do banco de dados SQL do Azure para a propriedade connectionString. Somente a autenticação básica tem suporte. |Sim |

> [!IMPORTANT]
> Configure o [Firewall do banco de dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) no servidor de banco de dados para [permitir que os serviços do Azure acessem o servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se você estiver copiando dados para o banco de dados SQL do Azure de fora do Azure, incluindo de fontes de dados locais com data factory gateway, configure o intervalo de endereços IP apropriado para o computador que está enviando dados para o Azure SQL Database.

## <a name="dataset-properties"></a>Propriedades de DataSet
Para especificar um DataSet para representar dados de entrada ou de saída em um banco de dado SQL do Azure, defina a propriedade Type do conjunto para: **AzureSqlTable**. Defina a propriedade **linkedServiceName** do conjunto de um como o nome do serviço vinculado do SQL do Azure.

Para obter uma lista completa das seções & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOB do Azure, tabela do Azure, etc.).

A seção typeproperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção **typeproperties** do conjunto de um do tipo **AzureSqlTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou exibição na instância do banco de dados SQL do Azure à qual o serviço vinculado se refere. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A atividade de cópia usa apenas uma entrada e produz apenas uma saída.

Enquanto que as propriedades disponíveis na seção **typeproperties** da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

Se você estiver movendo dados de um banco de dado SQL do Azure, defina o tipo de fonte na atividade de cópia como **sqlsource**. Da mesma forma, se você estiver movendo dados para um banco de dado SQL do Azure, defina o tipo de coletor na atividade de cópia como **sqlsink**. Esta seção fornece uma lista das propriedades com suporte pelo sqlsource e sqlsink.

### <a name="sqlsource"></a>SqlSource
Na atividade de cópia, quando a origem é do tipo **sqlsource**, as seguintes propriedades estão disponíveis na seção **typeproperties** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Os nomes e maiúsculas e minúsculas dos parâmetros devem corresponder aos nomes e maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

Se o **sqlReaderQuery** for especificado para sqlsource, a atividade de cópia executará essa consulta em relação à origem do banco de dados SQL do Azure para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e **storedprocedureparameters** (se o procedimento armazenado usar parâmetros).

Se você não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na seção Structure do conjunto de dados JSON serão usadas para criar uma consulta (`select column1, column2 from mytable`) a ser executada no banco de dados SQL do Azure. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas serão selecionadas da tabela.

> [!NOTE]
> Ao usar o **sqlReaderStoredProcedureName**, você ainda precisa especificar um valor para a propriedade **TableName** no JSON do conjunto de valores. No entanto, não há validações executadas nessa tabela.
>
>

### <a name="sqlsource-example"></a>Exemplo de sqlsource

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
O **sqlsink** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo de espera para que a operação de inserção em lote seja concluída antes de atingir o tempo limite. |período<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanupScript |Especifique uma consulta para a atividade de cópia a ser executada, de modo que os dados de uma fatia específica sejam limpos. Para obter mais informações, consulte [cópia repetível](#repeatable-copy). |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique um nome de coluna para a atividade de cópia preencher com o identificador de fatia gerado automaticamente, que é usado para limpar os dados de uma fatia específica quando executado novamente. Para obter mais informações, consulte [cópia repetível](#repeatable-copy). |Nome da coluna de uma coluna com tipo de dados binary (32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que define como aplicar dados de origem na tabela de destino, por exemplo, para fazer upserts ou transformar usando sua própria lógica de negócios. <br/><br/>Observe que esse procedimento armazenado será **invocado por lote**. Se você quiser executar a operação que só é executada uma vez e não tem nada a ver com os dados de origem, por exemplo, excluir/truncar, use `sqlWriterCleanupScript` propriedade. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Os nomes e maiúsculas e minúsculas dos parâmetros devem corresponder aos nomes e maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |
| sqlWriterTableType |Especifique um nome de tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia torna os dados sendo movidos disponíveis em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados que estão sendo copiados com os dados existentes. |Um nome de tipo de tabela. |Não |

#### <a name="sqlsink-example"></a>Exemplo de sqlsink

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

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Exemplos de JSON para cópia de dados de e para o SQL Database
Os exemplos a seguir fornecem exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para o Azure SQL Database e o armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Exemplo: copiar dados do banco de dados SQL do Azure para o blob do Azure
O mesmo define as seguintes entidades de Data Factory:

1. Um serviço vinculado do tipo [AzureSqlDatabase](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [AzureSqlTable](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [blob do Azure](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que usa [sqlsource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia os dados de série temporal (por hora, diariamente, etc.) de uma tabela no Azure SQL Database para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

**Serviço vinculado do banco de dados SQL do Azure:**

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
Consulte a seção serviço vinculado do SQL do Azure para obter a lista de propriedades com suporte por esse serviço vinculado.

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
Consulte o artigo [blob do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter a lista de propriedades com suporte por esse serviço vinculado.


**Conjunto de dados de entrada do SQL Azure:**

O exemplo supõe que você criou uma tabela "MyTable" no Azure SQL e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

A configuração "external": "true" informa ao serviço de Azure Data Factory que o conjunto de os é externo ao data factory e não é produzido por uma atividade no data factory.

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

Consulte a seção Propriedades do tipo de conjunto de texto do Azure SQL para obter a lista de propriedades com suporte neste tipo de conjunto de tipos.

**Conjunto de resultados de saída de blob do Azure:**

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

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
Consulte a seção [Propriedades do tipo de conjunto de conjuntos de blob do Azure](data-factory-azure-blob-connector.md#dataset-properties) para obter a lista de propriedades com suporte por este tipo de conjunto de tipos.

**Uma atividade de cópia em um pipeline com fonte SQL e coletor de blob:**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **sqlsource** e o tipo de **coletor** é definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

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
No exemplo, **sqlReaderQuery** é especificado para sqlsource. A atividade de cópia executa essa consulta em relação à origem do banco de dados SQL do Azure para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e **storedprocedureparameters** (se o procedimento armazenado usar parâmetros).

Se você não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura do conjunto de dados JSON serão usadas para criar uma consulta a ser executada no Azure SQL Database. Por exemplo: `select column1, column2 from mytable`. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas serão selecionadas da tabela.

Consulte a seção de [origem do SQL](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) para obter a lista de propriedades com suporte por sqlsource e BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Exemplo: copiar dados do blob do Azure para o banco de dados SQL do Azure
O exemplo define as seguintes entidades de Data Factory:

1. Um serviço vinculado do tipo [AzureSqlDatabase](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureSqlTable](#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [blobname](data-factory-azure-blob-connector.md#copy-activity-properties) e [sqlsink](#copy-activity-properties).

O exemplo copia dados de série temporal (por hora, diariamente, etc.) do blob do Azure para uma tabela no banco de dados SQL do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

**Serviço vinculado do SQL do Azure:**

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
Consulte a seção serviço vinculado do SQL do Azure para obter a lista de propriedades com suporte por esse serviço vinculado.

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
Consulte o artigo [blob do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter a lista de propriedades com suporte por esse serviço vinculado.


**Conjunto de dados de entrada de blob do Azure:**

Os dados são coletados de um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte de ano, mês e dia da hora de início e o nome do arquivo usa a parte de hora da hora de início. a configuração "external": "true" informa ao serviço de Data Factory que essa tabela é externa à data factory e não é produzida por uma atividade no data factory.

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
Consulte a seção [Propriedades do tipo de conjunto de conjuntos de blob do Azure](data-factory-azure-blob-connector.md#dataset-properties) para obter a lista de propriedades com suporte por este tipo de conjunto de tipos.

**DataSet de saída do banco de dados SQL do Azure:**

O exemplo copia dados para uma tabela chamada "MyTable" no SQL do Azure. Crie a tabela no SQL do Azure com o mesmo número de colunas que você espera que o arquivo CSV de blob contenha. Novas linhas são adicionadas à tabela a cada hora.

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
Consulte a seção Propriedades do tipo de conjunto de texto do Azure SQL para obter a lista de propriedades com suporte neste tipo de conjunto de tipos.

**Uma atividade de cópia em um pipeline com origem de BLOB e coletor de SQL:**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** está definido como **blobname** e o tipo de **coletor** está definido como **sqlsink**.

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
Consulte a seção [SQL Sink](#sqlsink) e o [blobname](data-factory-azure-blob-connector.md#copy-activity-properties) para obter a lista de propriedades com suporte por sqlsink e blobname.

## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade no banco de dados de destino
Esta seção fornece um exemplo para copiar dados de uma tabela de origem sem uma coluna de identidade para uma tabela de destino com uma coluna de identidade.

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
Observe que a tabela de destino tem uma coluna de identidade.

**Definição de JSON de conjunto de fonte de origem**

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
**Definição de JSON de DataSet de destino**

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

Observe que, como sua tabela de origem e de destino têm um esquema diferente (o destino tem uma coluna adicional com identidade). Nesse cenário, você precisa especificar a propriedade **Structure** na definição do conjunto de dados de destino, que não inclui a coluna de identidade.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocar procedimento armazenado do coletor SQL
Para obter um exemplo de como invocar um procedimento armazenado do coletor SQL em uma atividade de cópia de um pipeline, consulte o artigo [invocar procedimento armazenado para o coletor SQL no atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md) .

## <a name="type-mapping-for-azure-sql-database"></a>Mapeamento de tipo para o banco de dados SQL do Azure
Conforme mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões automáticas dos tipos de origem nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de origem nativos em tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados de e para o Azure SQL Database, os seguintes mapeamentos são usados do tipo SQL para o tipo .NET e vice-versa. O mapeamento é o mesmo que o SQL Server mapeamento de tipo de dados para ADO.NET.

| Tipo de Mecanismo de Banco de Dados de SQL Server | Tipo de .NET Framework |
| --- | --- |
| bigint |Int64 |
| binário |Byte [] |
| parte |Booleano |
| º |Cadeia de caracteres, Char [] |
| date |DateTime |
| Horário |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Vírgula |Vírgula |
| Atributo FILESTREAM (varbinary (max)) |Byte [] |
| Barra |Clique |
| image |Byte [] |
| int |Int32 |
| gastar |Vírgula |
| nchar |Cadeia de caracteres, Char [] |
| ntext |Cadeia de caracteres, Char [] |
| numeric |Vírgula |
| nvarchar |Cadeia de caracteres, Char [] |
| foto |Único |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Vírgula |
| sql_variant |Objeto |
| texto |Cadeia de caracteres, Char [] |
| hora |Período |
| carimbo de data/hora |Byte [] |
| tinyint |Minuciosa |
| uniqueidentifier |GUID |
| varbinary |Byte [] |
| varchar |Cadeia de caracteres, Char [] |
| xml |XML |

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas do coletor
Para saber mais sobre como mapear colunas no conjunto de informações de origem para colunas no conjunto de informações do coletor, confira [mapeando colunas Azure data Factory do conjunto de](data-factory-map-columns.md)informações

## <a name="repeatable-copy"></a>Cópia repetível
Durante a cópia de dados para o SQL Server Database, a atividade de cópia acrescenta dados à tabela de coletor por padrão. Para executar um UPSERT em vez disso, consulte o artigo [gravação repetida no sqlsink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) .

Ao copiar dados de armazenamentos de dados relacionais, tenha em mente a capacidade de repetição para evitar resultados indesejados. No Azure Data Factory, você pode executar novamente uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de uma para que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente de qualquer forma, você precisa garantir que os mesmos dados sejam lidos, independentemente de quantas vezes uma fatia é executada. Consulte [leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.
