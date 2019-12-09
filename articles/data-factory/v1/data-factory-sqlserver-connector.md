---
title: Mover dados de e para SQL Server
description: Saiba mais sobre como mover dados de/para SQL Server banco de dado local ou em uma VM do Azure usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5e4bbe1e6bd944787d47c5e3ed98de582c088a52
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928175"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Mover dados de e para SQL Server locais ou em IaaS (VM do Azure) usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-sqlserver-connector.md)
> * [Versão 2 (versão atual)](../connector-sql-server.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [SQL Server Connector na v2](../connector-sql-server.md).

Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de/para um banco de SQL Server local. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Cenários suportados
Você pode copiar dados **de um banco** de dados SQL Server para os seguintes armazenamentos:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos seguintes repositórios de dados **para um banco de SQL Server**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Versões SQL Server com suporte
Este SQL Server Connector dá suporte à cópia de dados de/para as seguintes versões da instância hospedada no local ou no Azure IaaS usando a autenticação do SQL e do Windows: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Habilitando a conectividade
Os conceitos e as etapas necessárias para a conexão com SQL Server hospedados localmente ou em VMs IaaS (infraestrutura como serviço) do Azure são iguais. Em ambos os casos, você precisa usar Gerenciamento de Dados gateway para conectividade.

Confira o artigo [movendo dados entre locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre gerenciamento de dados gateway e instruções passo a passo sobre como configurar o gateway. A configuração de uma instância de gateway é um pré-requisito para a conexão com o SQL Server.

Embora você possa instalar o gateway no mesmo computador local ou na instância de VM de nuvem que o SQL Server para melhorar o desempenho, recomendamos que você os instale em computadores separados. Ter o gateway e SQL Server em computadores separados reduz a contenção de recursos.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados de/para um banco de dado SQL Server local usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie um **Data Factory**. Um data factory pode conter um ou mais pipelines.
2. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory. Por exemplo, se você estiver copiando dados de um banco de dado SQL Server para um armazenamento de BLOBs do Azure, crie dois serviços vinculados para vincular seu banco de dados do SQL Server e a conta de armazenamento do Azure ao seu data factory. Para propriedades do serviço vinculado que são específicas para SQL Server banco de dados, consulte a seção [Propriedades do serviço vinculado](#linked-service-properties) .
3. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia. No exemplo mencionado na última etapa, você cria um conjunto de dados para especificar a tabela SQL em seu banco de dado de SQL Server que contém os dados de entrada. Além disso, você cria outro conjunto de dados para especificar o contêiner de BLOBs e a pasta que contém os dados copiados do banco de SQL Server. Para propriedades de conjunto de dados que são específicas para SQL Server Database, consulte a seção [DataSet Properties](#dataset-properties) .
4. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída. No exemplo mencionado anteriormente, você usa sqlsource como uma origem e BlobSink como um coletor para a atividade de cópia. Da mesma forma, se você estiver copiando do armazenamento de BLOBs do Azure para SQL Server banco de dados, use Blobname e sqlsink na atividade de cópia. Para propriedades da atividade de cópia que são específicas para SQL Server banco de dados, consulte a seção [Propriedades da atividade de cópia](#copy-activity-properties) . Para obter detalhes sobre como usar um armazenamento de dados como uma origem ou um coletor, clique no link na seção anterior para seu armazenamento de dados.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON. Para obter exemplos com definições de JSON para Data Factory entidades que são usadas para copiar dados de/para um banco de SQL Server local, confira a seção [exemplos de JSON](#json-examples-for-copying-data-from-and-to-sql-server) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para SQL Server:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
Você cria um serviço vinculado do tipo **OnPremisesSqlServer** para vincular um banco de dados SQL Server local a um data Factory. A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do SQL Server local.

A tabela a seguir fornece a descrição para elementos JSON específicos para SQL Server serviço vinculado.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **OnPremisesSqlServer**. |Sim |
| connectionString |Especifique as informações de connectionString necessárias para se conectar ao banco de dados local SQL Server usando a autenticação SQL ou a autenticação do Windows. |Sim |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao banco de dados SQL Server local. |Sim |
| o nome de utilizador |Especifique o nome de usuário se você estiver usando a autenticação do Windows. Exemplo: **domainname\\username**. |Não |
| palavra-passe |Especifique a senha para a conta de usuário especificada para o nome do usuário. |Não |

Você pode criptografar credenciais usando o cmdlet **New-AzDataFactoryEncryptValue** e usá-las na cadeia de conexão, conforme mostrado no exemplo a seguir (propriedade**EncryptedCredential** ):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Exemplos
**JSON para usar a autenticação do SQL**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON para usar a autenticação do Windows**

Gerenciamento de Dados gateway representará a conta de usuário especificada para se conectar ao banco de dados SQL Server local.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Nos exemplos, você usou um conjunto de dados do tipo **Sqlservertable** para representar uma tabela em um banco de SQL Server.

Para obter uma lista completa das seções & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL Server, BLOB do Azure, tabela do Azure, etc.).

A seção typeproperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção **typeproperties** do conjunto de um do tipo **sqlservertable** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela ou exibição na instância de banco de dados SQL Server à qual o serviço vinculado se refere. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Se você estiver movendo dados de um banco de dado SQL Server, defina o tipo de fonte na atividade de cópia como **sqlsource**. Da mesma forma, se você estiver movendo dados para um banco de dado SQL Server, defina o tipo de coletor na atividade de cópia como **sqlsink**. Esta seção fornece uma lista das propriedades com suporte pelo sqlsource e sqlsink.

Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e políticas estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A atividade de cópia usa apenas uma entrada e produz apenas uma saída.

Enquanto que as propriedades disponíveis na seção typeproperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

### <a name="sqlsource"></a>SqlSource
Quando a origem em uma atividade de cópia é do tipo **sqlsource**, as seguintes propriedades estão disponíveis na seção **typeproperties** :

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| sqlReaderQuery |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: selecione * em MyTable. Pode fazer referência a várias tabelas do banco de dados referenciado pelo DataSet de entrada. Se não for especificado, a instrução SQL executada: selecione from MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado. |Pares de nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |

Se o **sqlReaderQuery** for especificado para sqlsource, a atividade de cópia executará essa consulta em relação à origem do banco de dados SQL Server para obter os dados.

Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e **storedprocedureparameters** (se o procedimento armazenado usar parâmetros).

Se você não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura serão usadas para criar uma consulta Select a ser executada no banco de dados SQL Server. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas serão selecionadas da tabela.

> [!NOTE]
> Ao usar o **sqlReaderStoredProcedureName**, você ainda precisa especificar um valor para a propriedade **TableName** no JSON do conjunto de valores. No entanto, não há validações executadas nessa tabela.

### <a name="sqlsink"></a>SqlSink
O **sqlsink** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo de espera para que a operação de inserção em lote seja concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanupScript |Especifique a consulta para a atividade de cópia a ser executada, de modo que os dados de uma fatia específica sejam limpos. Para obter mais informações, consulte a seção [cópia repetível](#repeatable-copy) . |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a atividade de cópia a ser preenchida com o identificador de fatia gerado automaticamente, que é usado para limpar os dados de uma fatia específica quando executado novamente. Para obter mais informações, consulte a seção [cópia repetível](#repeatable-copy) . |Nome da coluna de uma coluna com tipo de dados binary (32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que define como aplicar dados de origem na tabela de destino, por exemplo, para fazer upserts ou transformar usando sua própria lógica de negócios. <br/><br/>Observe que esse procedimento armazenado será **invocado por lote**. Se você quiser executar a operação que só é executada uma vez e não tem nada a ver com os dados de origem, por exemplo, excluir/truncar, use `sqlWriterCleanupScript` propriedade. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado. |Pares de nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |
| sqlWriterTableType |Especifique o nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia torna os dados sendo movidos disponíveis em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados que estão sendo copiados com os dados existentes. |Um nome de tipo de tabela. |Não |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Exemplos de JSON para copiar dados de e para SQL Server
Os exemplos a seguir fornecem exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos a seguir mostram como copiar dados de e para o SQL Server e o armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Exemplo: copiar dados do SQL Server para o blob do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [OnPremisesSqlServer](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [sqlservertable](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa [sqlsource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de série temporal de uma tabela SQL Server para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados. As instruções estão no artigo [movendo dados entre os locais e a nuvem do local](data-factory-move-data-between-onprem-and-cloud.md) .

**SQL Server serviço vinculado**
```json
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
**Serviço vinculado do armazenamento de BLOBs do Azure**

```json
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
**SQL Server conjunto de dados de entrada**

O exemplo supõe que você criou uma tabela "MyTable" em SQL Server e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal. Você pode consultar várias tabelas dentro do mesmo banco de dados usando um único conjunto, mas uma única tabela deve ser usada para o tipo de tabela TableName do DataSet.

A configuração de "external": "true" informa Data Factory serviço de que o DataSet é externo ao data factory e não é produzido por uma atividade no data factory.

```json
{
  "name": "SqlServerInput",
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
**Conjunto de dados dos Blobs do Azure**

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia que é configurada para usar esses conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **sqlsource** e o tipo de **coletor** é definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
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
Neste exemplo, **sqlReaderQuery** é especificado para sqlsource. A atividade de cópia executa essa consulta em relação à origem do banco de dados SQL Server para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e **storedprocedureparameters** (se o procedimento armazenado usar parâmetros). O sqlReaderQuery pode fazer referência a várias tabelas dentro do banco de dados referenciado pelo DataSet de entrada. Ele não está limitado apenas ao conjunto de tabelas como TableName TYPEPROPERTY do DataSet.

Se você não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura serão usadas para criar uma consulta Select a ser executada no banco de dados SQL Server. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas serão selecionadas da tabela.

Consulte a seção de [origem do SQL](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) para obter a lista de propriedades com suporte por sqlsource e BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Exemplo: copiar dados do blob do Azure para SQL Server
O exemplo a seguir mostra:

1. O serviço vinculado do tipo [OnPremisesSqlServer](#linked-service-properties).
2. O serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [sqlservertable](data-factory-sqlserver-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa [blobname](data-factory-azure-blob-connector.md#copy-activity-properties) e sqlsink.

O exemplo copia dados de série temporal de um blob do Azure para uma tabela SQL Server a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

**SQL Server serviço vinculado**

```json
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
**Serviço vinculado do armazenamento de BLOBs do Azure**

```json
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
**Conjunto de dados de entrada de blob do Azure**

Os dados são coletados de um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte de ano, mês e dia da hora de início e o nome do arquivo usa a parte de hora da hora de início. a configuração "external": "true" informa ao serviço de Data Factory que o conjunto de os é externo ao data factory e não é produzido por uma atividade no data factory.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
**Conjunto de SQL Server de saída**

O exemplo copia dados para uma tabela chamada "MyTable" no SQL Server. Crie a tabela em SQL Server com o mesmo número de colunas que você espera que o arquivo CSV de blob contenha. Novas linhas são adicionadas à tabela a cada hora.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia que é configurada para usar esses conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** está definido como **blobname** e o tipo de **coletor** está definido como **sqlsink**.

```json
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
            "name": " SqlServerOutput "
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

## <a name="troubleshooting-connection-issues"></a>Resolução de problemas de ligação
1. Configure seu SQL Server para aceitar conexões remotas. Inicie o **SQL Server Management Studio**, clique com o botão direito do mouse em **servidor**e clique em **Propriedades**. Selecione **conexões** na lista e marque **permitir conexões remotas com o servidor**.

    ![Ativar ligações remotas](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Consulte [Configurar a opção de configuração de servidor de acesso remoto](https://msdn.microsoft.com/library/ms191464.aspx) para obter etapas detalhadas.
2. Iniciar **SQL Server Configuration Manager**. Expanda **SQL Server configuração de rede** para a instância desejada e selecione **protocolos para MSSQLSERVER**. Você deve ver protocolos no painel à direita. Habilite o TCP/IP clicando com o botão direito do mouse em **TCP/IP** e clicando em **habilitar**.

    ![Habilitar TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Consulte [habilitar ou desabilitar um protocolo de rede de servidor](https://msdn.microsoft.com/library/ms191294.aspx) para obter detalhes e maneiras alternativas de habilitar o protocolo TCP/IP.
3. Na mesma janela, clique duas vezes em **TCP/IP** para iniciar a janela **Propriedades de TCP/IP** .
4. Alterne para a guia **endereços IP** . Role para baixo para ver a seção **IPAll** . Anote a **porta TCP**(o padrão é **1433**).
5. Crie uma **regra para o Firewall do Windows** no computador para permitir o tráfego de entrada por essa porta.
6. **Verificar conexão**: para se conectar ao SQL Server usando o nome totalmente qualificado, use SQL Server Management Studio de um computador diferente. Por exemplo: "\<Machine\>.\<Domain\>. Corp.\<empresa\>. com, 1433. "

   > [!IMPORTANT]
   > 
   > Consulte [mover dados entre fontes locais e a nuvem com gerenciamento de dados gateway](data-factory-move-data-between-onprem-and-cloud.md) para obter informações detalhadas.
   > 
   > Consulte [solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter dicas sobre como solucionar problemas relacionados à conexão/gateway.


## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade no banco de dados de destino
Esta seção fornece um exemplo que copia dados de uma tabela de origem sem coluna de identidade para uma tabela de destino com uma coluna de identidade.

**Tabela de origem:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Tabela de destino:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

Observe que a tabela de destino tem uma coluna de identidade.

**Definição de JSON de conjunto de fonte de origem**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
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

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
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
Consulte [invocar procedimento armazenado para o coletor SQL no artigo atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md) para obter um exemplo de como invocar um procedimento armazenado do coletor SQL em uma atividade de cópia de um pipeline.

## <a name="type-mapping-for-sql-server"></a>Mapeamento de tipo para o SQL Server
Conforme mencionado no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões automáticas dos tipos de origem nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de origem nativos em tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados para & do SQL Server, os seguintes mapeamentos são usados do tipo SQL para o tipo .NET e vice-versa.

O mapeamento é o mesmo que o SQL Server mapeamento de tipo de dados para ADO.NET.

| Tipo de Mecanismo de Banco de Dados de SQL Server | Tipo de .NET Framework |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Booleano |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Flutuante |Double |
| imagem |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Único |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| texto |String, Char[] |
| hora |TimeSpan |
| carimbo de data/hora |Byte[] |
| tinyint |bytes |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

## <a name="mapping-source-to-sink-columns"></a>Mapeando origem para colunas do coletor
Para mapear colunas do conjunto de fonte de origem para colunas do conjunto de coleta, consulte [mapeando colunas do conjunto de linhas no Azure data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Cópia repetível
Durante a cópia de dados para o SQL Server Database, a atividade de cópia acrescenta dados à tabela de coletor por padrão. Para executar um UPSERT em vez disso, consulte o artigo [gravação repetida no sqlsink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) .

Ao copiar dados de armazenamentos de dados relacionais, tenha em mente a capacidade de repetição para evitar resultados indesejados. No Azure Data Factory, você pode executar novamente uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de uma para que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente de qualquer forma, você precisa garantir que os mesmos dados sejam lidos, independentemente de quantas vezes uma fatia é executada. Consulte [leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.
