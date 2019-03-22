---
title: Mover dados para e do SQL Server | Documentos da Microsoft
description: Saiba mais sobre como mover dados de/para base de dados do SQL Server que está no local ou na VM do Azure com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: be36f9ab881f2375b14ba0ea36038f9e840d199f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997504"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Mover dados para e do SQL Server no local ou em IaaS (VM do Azure) com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-sqlserver-connector.md)
> * [Versão 2 (versão atual)](../connector-sql-server.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector do SQL Server no V2](../connector-sql-server.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de/para uma base de dados do SQL Server no local. Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **de um banco de dados do SQL Server** para os seguintes dados armazena:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados de arquivos de dados seguintes **para uma base de dados do SQL Server**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Versões suportadas do SQL Server
Este suporte de conector do SQL Server copiar dados de/para as seguintes versões de instância alojada no local ou no Azure IaaS usando autenticação do SQL e autenticação do Windows: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Ativar a conectividade
Os conceitos e as etapas necessárias para conexão com o SQL Server alojado no local ou em VMs do Azure IaaS (infraestrutura-como-serviço) são os mesmos. Em ambos os casos, terá de utilizar o Data Management Gateway para a conectividade.

Ver [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo para saber mais sobre o Gateway de gestão de dados e instruções passo a passo sobre como configurar o gateway. Configurar uma instância de gateway é um pré-requisito para estabelecer ligação com o SQL Server.

Embora possa instalar o gateway no mesmo computador no local ou instância VM de cloud como o SQL Server para um melhor desempenho, recomendamos que instale-os em computadores separados. Com o gateway e o SQL Server em máquinas separadas reduz a contenção de recursos.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de/para uma base de dados do SQL Server no local através de APIs/ferramentas diferentes.

A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Criar uma **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines.
2. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados. Por exemplo, se estiver a copiar dados de um banco de dados do SQL Server para um armazenamento de Blobs do Azure, criar dois serviços ligados para ligar a sua base de dados do SQL Server e a conta de armazenamento do Azure à fábrica de dados. Para as propriedades do serviço ligado que são específicas para a base de dados do SQL Server, consulte [propriedades do serviço ligado](#linked-service-properties) secção.
3. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. O exemplo mencionado no último passo, vai criar um conjunto de dados para especificar a tabela SQL na base de dados do SQL Server que contém os dados de entrada. Além disso, crie outro conjunto de dados para especificar o contentor de BLOBs e a pasta que contém os dados copiados da base de dados do SQL Server. Para as propriedades do conjunto de dados que são específicas para a base de dados do SQL Server, consulte [propriedades do conjunto de dados](#dataset-properties) secção.
4. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, vai utilizar SqlSource como uma origem e BlobSink como sink para a atividade de cópia. Da mesma forma, se estiver a copiar de armazenamento de Blobs do Azure para a base de dados do SQL Server, utilize BlobSource e SqlSink a atividade de cópia. Para propriedades de atividade de cópia que são específicas para a base de dados do SQL Server, consulte [propriedades da atividade copy](#copy-activity-properties) secção. Para obter detalhes sobre como usar um arquivo de dados como uma origem ou sink, clique na ligação na secção anterior para seu armazenamento de dados.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON. Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de/para uma base de dados do SQL Server no local, consulte [exemplos JSON](#json-examples-for-copying-data-from-and-to-sql-server) seção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON, que são utilizadas para definir entidades do Data Factory específicas para o SQL Server:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
Vai criar um serviço ligado do tipo **OnPremisesSqlServer** para ligar uma base de dados do SQL Server no local a uma fábrica de dados. A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço de ligado do SQL Server no local.

A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço ligado do SQL Server.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo deve ser definida como: **OnPremisesSqlServer**. |Sim |
| connectionString |Especifique as informações de connectionString necessárias para se ligar à base de dados de SQL Server no local, utilizando a autenticação SQL ou autenticação do Windows. |Sim |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados do SQL Server no local. |Sim |
| o nome de utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação do Windows. Exemplo: **domainname\\nome de utilizador**. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. |Não |

Pode criptografar as credenciais com o **New-AzDataFactoryEncryptValue** cmdlet e utilizá-los na cadeia de ligação, conforme mostrado no exemplo a seguir (**EncryptedCredential** propriedade):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Amostras
**JSON para utilizar a autenticação de SQL**

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
**JSON para utilizar a autenticação do Windows**

O Data Management Gateway irá representar a conta de utilizador especificado para ligar à base de dados do SQL Server no local.

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
Os exemplos, utilizou um conjunto de dados do tipo **SqlServerTable** para representar uma tabela numa base de dados do SQL Server.

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL Server, do Azure blob, tabela do Azure, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para o conjunto de dados do tipo **SqlServerTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na instância da base de dados do SQL Server pelo serviço ligado refere-se. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Se estiver a mover dados de um banco de dados do SQL Server, defina o tipo de origem na atividade de cópia para **SqlSource**. Da mesma forma, se estiver a mover dados para uma base de dados do SQL Server, é definir o tipo de sink na atividade de cópia para **SqlSink**. Esta seção fornece uma lista de propriedades suportadas pelo SqlSource e SqlSink.

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, entrada e saída de tabelas e as políticas estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A atividade de cópia precisar apenas uma entrada e saída de apenas um.

Ao passo que, propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam consoante os tipos de origens e sinks.

### <a name="sqlsource"></a>SqlSource
Quando a origem numa atividade de cópia é do tipo **SqlSource**, as seguintes propriedades estão disponíveis no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: selecionar * de MyTable. Pode fazer referência a várias tabelas da base de dados referenciado pelo conjunto de dados de entrada. Se não for especificado, a instrução SQL que é executada: selecione a partir dos MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados da tabela de origem. |Nome do procedimento armazenado. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado. |Pares de nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |

Se o **sqlReaderQuery** é especificado para o SqlSource, a atividade de cópia executa esta consulta em relação à origem de base de dados do SQL Server para obter os dados.

Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura são utilizadas para criar uma consulta select para executar na base de dados do SQL Server. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.

> [!NOTE]
> Quando utiliza **sqlReaderStoredProcedureName**, terá de especificar um valor para o **tableName** propriedade no conjunto de dados JSON. Não há nenhuma validação executada nessa tabela, no entanto.

### <a name="sqlsink"></a>SqlSink
**SqlSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo para a operação de inserção de lote seja concluída antes de atingir o tempo limite de espera. |Período de tempo<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| sqlWriterCleanupScript |Especifique a consulta para a atividade de cópia executar de forma a que os dados de um setor específico é limpo. Para obter mais informações, consulte [cópia repetível](#repeatable-copy) secção. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a atividade de cópia preencher com o identificador de setor gerado automaticamente, o que é utilizado para limpar os dados de um setor específico quando voltar a executar. Para obter mais informações, consulte [cópia repetível](#repeatable-copy) secção. |Nome da coluna de uma coluna com o tipo de dados de binary(32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que define como aplicar a origem de dados na tabela de destino, por exemplo, para fazer upserts ou transformação usando sua própria lógica de negócios. <br/><br/>Tenha em atenção de que este procedimento armazenado será **invocado por lote**. Se pretender efetuar a operação que só é executado uma vez e não tem nada a fazer com os dados de origem, por exemplo, eliminar/truncar, utilize `sqlWriterCleanupScript` propriedade. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado. |Pares de nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |
| sqlWriterTableType |Especifique o nome de tipo de tabela para ser utilizado no procedimento armazenado. Atividade de cópia torna os dados que está a ser movidos disponíveis numa tabela temporária com este tipo de tabela. Código do procedimento armazenado pode então mesclar os dados a ser copiados com os dados existentes. |Um nome de tipo de tabela. |Não |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Exemplos JSON para copiar dados de e para o SQL Server
Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos seguintes mostram como copiar dados de e para o SQL Server e o armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Exemplo: Copiar dados do SQL Server para BLOBs do Azure
O exemplo a seguir mostra:

1. Um serviço ligado do tipo [OnPremisesSqlServer](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [SqlServerTable](#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [SqlSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de séries de tempo de uma tabela do SQL Server para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

Como primeiro passo, configure o data management gateway. As instruções estão no [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo.

**Serviço ligado do SQL Server**
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
**Serviço de ligado de armazenamento de Blobs do Azure**

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
**Conjunto de dados de entrada do SQL Server**

O exemplo pressupõe que criou uma tabela "MyTable" no SQL Server e contém uma coluna chamada "timestampcolumn" para dados de séries de tempo. Pode consultar ao longo de várias tabelas numa base de dados mesmo com um único conjunto de dados, mas uma única tabela deve ser utilizada para typeProperty de tableName o conjunto de dados.

A definição "externo": "true" informa serviço Data Factory que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

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

O pipeline contém uma atividade de cópia que está configurado para utilizar estes conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **SqlSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para o **SqlReaderQuery** propriedade seleciona os dados na hora anterior para copiar.

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
Neste exemplo, **sqlReaderQuery** especificado para o SqlSource. A atividade de cópia executa esta consulta em relação à origem de base de dados do SQL Server para obter os dados. Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros). O sqlReaderQuery pode fazer referência a várias tabelas na base de dados referenciados pelo conjunto de dados de entrada. Não se limita a apenas a tabela definir como typeProperty de tableName o conjunto de dados.

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura são utilizadas para criar uma consulta select para executar na base de dados do SQL Server. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.

Consulte a [origem de Sql](#sqlsource) secção e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) para a lista de propriedades suportadas por SqlSource e BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Exemplo: Copiar dados de Blobs do Azure para SQL Server
O exemplo a seguir mostra:

1. O serviço ligado do tipo [OnPremisesSqlServer](#linked-service-properties).
2. O serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e SqlSink.

As cópias de exemplo blob de dados a partir do Azure de série de tempo para um SQL Server a tabela a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

**Serviço ligado do SQL Server**

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
**Serviço de ligado de armazenamento de Blobs do Azure**

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
**Conjunto de dados de entrada BLOBs do Azure**

Dados são captados um blob novo a cada hora (frequência: hora, intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e nome de ficheiro utiliza a parte de hora a hora de início. "externo": "true" definição informa o serviço Data Factory, que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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
**Conjunto de dados de saída de SQL Server**

O exemplo copia dados a uma tabela chamada "MyTable" no SQL Server. Crie a tabela no SQL Server com o mesmo número de colunas, como esperar que o ficheiro CSV de BLOBs para conter. Novas linhas são adicionadas à tabela de cada hora.

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

O pipeline contém uma atividade de cópia que está configurado para utilizar estes conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **BlobSource** e **sink** tipo está definido como **SqlSink**.

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
1. Configure o seu SQL Server para aceitar ligações remotas. Inicie **SQL Server Management Studio**, clique com botão direito **servidor de**e clique em **propriedades**. Selecione **conexões** na lista e verificação **permitir ligações remotas para o servidor**.

    ![Ativar ligações remotas](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Ver [configurar a opção de configuração do servidor de acesso remoto](https://msdn.microsoft.com/library/ms191464.aspx) para obter passos detalhados.
2. Inicie **Gestor de configuração do SQL Server**. Expanda **configuração de rede do SQL Server** para a instância que pretende e selecione **protocolos para MSSQLSERVER**. Deverá ver protocolos no painel direito. Ative TCP/IP clicando **TCP/IP** e clicando em **ativar**.

    ![Ative TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Ver [ativar ou desativar um protocolo de rede do servidor](https://msdn.microsoft.com/library/ms191294.aspx) para obter detalhes e formas alternativas de ativação de protocolo TCP/IP.
3. Na janela da mesma, faça duplo clique em **TCP/IP** para iniciar **propriedades de TCP/IP** janela.
4. Mude para o **endereços IP** separador. Desloque-se para baixo para ver **IPAll** secção. Tome nota da **porta TCP**(a predefinição é **1433**).
5. Criar uma **regra da Firewall do Windows** na máquina para permitir o tráfego de entrada por essa porta.
6. **Verificar ligação**: Para ligar ao servidor SQL com o nome totalmente qualificado, utilize o SQL Server Management Studio noutra máquina. Por exemplo: "\<machine\>.\< domínio\>. corp.\<empresa\>.com, 1433. "

   > [!IMPORTANT]
   > 
   > Ver [mover dados entre origens no local e a nuvem com o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obter informações detalhadas.
   > 
   > Ver [resolver problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter sugestões sobre resolução de problemas do gateway de ligação/problemas relacionados com.


## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade na base de dados de destino
Esta seção fornece um exemplo que copia dados de uma tabela de origem com não existe nenhuma coluna de identidade para uma tabela de destino com uma coluna de identidade.

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

Tenha em atenção que a tabela de destino tem uma coluna de identidade.

**Definição de JSON do conjunto de dados de origem**

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
**Definição de JSON do conjunto de dados de destino**

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

Tenha em atenção que, como sua tabela de origem e destino têm diferente esquema (o destino tem uma coluna adicional com identidade). Neste cenário, tem de especificar **estrutura** propriedade na definição de conjunto de dados de destino, que não inclui a coluna de identidade.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocar um procedimento armazenado do sink do SQL
Ver [invocar um procedimento armazenado para o sink do SQL na atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md) artigo para obter um exemplo de um procedimento armazenado do sink do SQL numa atividade de cópia de um pipeline de invocação.

## <a name="type-mapping-for-sql-server"></a>Mapeamento de tipo para o SQL server
Conforme mencionado na [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia executa conversões de tipos automáticas de tipos de origem para o sink de tipos com a seguinte abordagem de passo 2:

1. Converter entre tipos de origem nativas para o tipo de .NET
2. Converter de tipo de .NET para o tipo de sink nativo

Ao mover dados para e a partir do SQL server, são utilizados os seguintes mapeamentos de tipo SQL para o tipo .NET e vice-versa.

O mapeamento é a mesmo que o mapeamento de tipo de dados do SQL Server para o ADO.NET.

| Tipo de motor de base de dados do SQL Server | Tipo de .NET framework |
| --- | --- |
| bigint |Int64 |
| binário |Byte[] |
| bit |Booleano |
| char |Cadeia de caracteres, Char [] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| decimal |decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte[] |
| Flutuante |Valor de duplo |
| image |Byte[] |
| int |Int32 |
| dinheiro |decimal |
| nchar |Cadeia de caracteres, Char [] |
| ntext |Cadeia de caracteres, Char [] |
| numérico |decimal |
| nvarchar |Cadeia de caracteres, Char [] |
| real |Único |
| ROWVERSION |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |decimal |
| sql_variant |Objeto * |
| texto |Cadeia de caracteres, Char [] |
| hora |Período de tempo |
| carimbo de data/hora |Byte[] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |Cadeia de caracteres, Char [] |
| xml |Xml |

## <a name="mapping-source-to-sink-columns"></a>Origem de mapeamento para colunas de sink
Para mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Cópia passível de repetição
Quando se copiam dados para a base de dados do SQL Server, a atividade de cópia acrescenta dados para a tabela do sink por predefinição. Para efetuar um UPSERT em vez disso, consulte [Repeatable escrever SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) artigo.

Quando armazena a cópia de dados de dados relacionais, tenha a capacidade de repetição em mente para evitar resultados indesejados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor será novamente executado de qualquer forma, terá de certificar-se de que os mesmos dados é de leitura não questão número de vezes que um setor é executado. Ver [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Otimização
Ver [desempenho de atividade de cópia e guia de ajuste](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo.
