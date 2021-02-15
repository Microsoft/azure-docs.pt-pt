---
title: Mover dados de e para o SQL Server
description: Saiba como mover dados de/para a base de dados do SQL Server que esteja no local ou num VM Azure usando a Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: fbd1e1d652db3bbd91344ea828278d057baeb060
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368817"
---
# <a name="move-data-to-and-from-sql-server-using-azure-data-factory"></a>Mover dados de e para o SQL Server usando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-sqlserver-connector.md)
> * [Versão 2 (versão atual)](../connector-sql-server.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector SQL Server em V2](../connector-sql-server.md).

Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados para/de uma base de dados do SQL Server. Baseia-se no artigo de Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **de uma base de dados do SQL Server** para as seguintes lojas de dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados das seguintes lojas de dados **para uma base de dados do SQL Server:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Versões suportadas do SQL Server
Este conector SQL Server suporta a cópia de/para as seguintes versões de instância hospedadas no local ou em Azure IaaS usando a autenticação SQL e a autenticação do Windows: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Habilitação da conectividade
Os conceitos e passos necessários para a ligação com o SQL Server hospedado no local ou em VMs Azure IaaS (Infra-estruturas-as-a-Service) são os mesmos. Em ambos os casos, você precisa usar o Data Management Gateway para conectividade.

Consulte [dados em movimento entre locais no local e](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem para saber sobre o Gateway de Gestão de Dados e instruções passo a passo sobre a configuração do gateway. Configurar uma instância de gateway é um pré-requisito para a ligação com o SQL Server.

Enquanto pode instalar gateway na mesma máquina no local ou na nuvem de VM como o SQL Server para um melhor desempenho, recomendamos que os instale em máquinas separadas. Ter o gateway e o SQL Server em máquinas separadas reduz a contenção de recursos.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para uma base de dados do SQL Server utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API** e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos.
2. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados. Por exemplo, se estiver a copiar dados de uma base de dados do SQL Server para um armazenamento de bolhas Azure, cria dois serviços ligados para ligar a sua base de dados sql Server e a conta de armazenamento Azure à sua fábrica de dados. Para propriedades de serviço ligadas específicas à base de dados do SQL Server, consulte a secção [de propriedades de serviços ligadas.](#linked-service-properties)
3. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado no último passo, cria-se um conjunto de dados para especificar a tabela SQL na base de dados do SQL Server que contém os dados de entrada. E cria outro conjunto de dados para especificar o recipiente blob e a pasta que detém os dados copiados da base de dados do SQL Server. Para obter propriedades de conjunto de dados específicas da base de dados do SQL Server, consulte a secção [de propriedades do conjunto de dados.](#dataset-properties)
4. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, utiliza o SqlSource como fonte e o BlobSink como pia para a atividade da cópia. Da mesma forma, se estiver a copiar de Azure Blob Storage para SQL Server Database, utilize BlobSource e SqlSink na atividade de cópia. Para obter propriedades de atividade de cópia específicas da Base de Dados do Servidor SQL, consulte a secção [de propriedades da atividade de cópia.](#copy-activity-properties) Para obter mais informações sobre como utilizar uma loja de dados como fonte ou pia, clique no link na secção anterior para a sua loja de dados.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON. Para amostras com definições JSON para entidades da Data Factory que são usadas para copiar dados de/para uma base de dados do SQL Server, consulte a secção de [exemplos JSON](#json-examples-for-copying-data-from-and-to-sql-server) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas do SQL Server:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
Cria um serviço de tipo ligado **OnPremisesSqlServer** para ligar uma base de dados do SQL Server a uma fábrica de dados. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao SQL Server.

A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao SQL Server.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **OnPremisesSqlServer**. |Yes |
| conexãoStragem |Especifique a informação de ligação Desaquipeia as informações necessárias para ligar à base de dados do SQL Server utilizando a autenticação SQL ou a autenticação do Windows. |Yes |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados do SQL Server. |Yes |
| nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a Autenticação do Windows. Exemplo: **nome de utilizador do nome \\ de domínio**. |No |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |No |

Pode encriptar credenciais utilizando o cmdlet **New-AzDataFactoryEncryptValue** e usá-las na cadeia de ligação, como mostrado no exemplo seguinte (Propriedade **Criptografada)**

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Amostras
**JSON para utilização de autenticação SQL**

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
**JSON para utilização de autenticação do Windows**

O Data Management Gateway personificará a conta de utilizador especificada para se ligar à base de dados do SQL Server.

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Nas amostras, utilizou um conjunto de dados do tipo **SqlServerTable** para representar uma tabela numa base de dados do SQL Server.

Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL Server, Azure blob, Azure table, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção **typeProperties** para o conjunto de dados do tipo **SqlServerTable** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela ou visualização na página da Base de Dados do Servidor SQL a que o serviço ligado se refere. |Yes |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Se estiver a mover dados de uma base de dados do SQL Server, define o tipo de origem na atividade de cópia para **SqlSource**. Da mesma forma, se estiver a transferir dados para uma base de dados do SQL Server, define o tipo de pia na atividade da cópia para **o SqlSink**. Esta secção fornece uma lista de propriedades suportadas por SqlSource e SqlSink.

Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Atividade de Cópia leva apenas uma entrada e produz apenas uma saída.

Enquanto que as propriedades disponíveis na secção de tipos de atividade variam com cada tipo de atividade. Para a atividade copy, variam dependendo dos tipos de fontes e pias.

### <a name="sqlsource"></a>SqlSource
Quando a origem numa atividade de cópia é do tipo **SqlSource,** as seguintes propriedades estão disponíveis na secção **de tipos de propriedades:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: selecione * do MyTable. Pode fazer referência a várias tabelas da base de dados referenciadas pelo conjunto de dados de entrada. Se não for especificado, a declaração SQL que é executada: selecione a partir do MyTable. |No |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados da tabela de origem. |Nome do procedimento armazenado. A última declaração SQL deve ser uma declaração SELECT no procedimento armazenado. |No |
| parametrómetros de reserva armazenados |Parâmetros para o procedimento armazenado. |Pares de nomes/valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. |No |

Se o **SqlReaderQuery** for especificado para o SqlSource, a Atividade de Cópia executa esta consulta com a fonte de Base de Dados do Servidor SQL para obter os dados.

Em alternativa, pode especificar um procedimento armazenado especificando o **nome sqlReaderStoredProcedureName** e **os Computadores Deprocedures armazenados** (se o procedimento armazenado tiver parâmetros).

Se não especificar o sqlReaderQuery ou o sqlReaderStorStoredProcedureName, as colunas definidas na secção de estrutura são utilizadas para construir uma consulta selecionada para correr contra a Base de Dados do Servidor SQL. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.

> [!NOTE]
> Quando utilizar **o nome sqlReaderStoredProcedureName,** ainda precisa de especificar um valor para a propriedade **tableName** no conjunto de dados JSON. No entanto, não há validações feitas contra esta tabela.

### <a name="sqlsink"></a>SqlSink
**SqlSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| escreverBatchTimeout |Tempo de espera para que o funcionamento do encaixe do lote esteja concluído antes de esgotar o tempo. |timespan<br/><br/> Exemplo: "00:30:00" (30 minutos). |No |
| escreverBatchSize |Insere dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanUpScript |Especifique a consulta para a Atividade de Cópia para executar de modo a que os dados de uma fatia específica seja limpo. Para mais informações, consulte a secção [de cópias repetível.](#repeatable-copy) |Uma declaração de consulta. |No |
| sliceIdentifierColumnName |Especifique o nome da coluna para a Atividade de Cópia para preencher com identificador de fatias gerados automaticamente, que é utilizado para limpar dados de uma fatia específica quando se revesse. Para mais informações, consulte a secção [de cópias repetível.](#repeatable-copy) |Nome da coluna de uma coluna com tipo de dados binário (32). |No |
| sqlWriterStorEdProcedureName |Nome do procedimento armazenado que define como aplicar dados de origem em tabela-alvo, por exemplo, para fazer upserts ou transformar usando a sua própria lógica de negócio. <br/><br/>Note que este procedimento armazenado será **invocado por lote**. Se quiser fazer uma operação que só funciona uma vez e não tem nada a ver com dados de origem, por exemplo, apagar/truncar, utilize `sqlWriterCleanupScript` a propriedade. |Nome do procedimento armazenado. |No |
| parametrómetros de reserva armazenados |Parâmetros para o procedimento armazenado. |Pares de nomes/valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. |No |
| SqlWriterTableType |Especifique o nome do tipo de mesa a ser utilizado no procedimento armazenado. A atividade de cópia torna os dados disponíveis numa tabela temporária com este tipo de tabela. O código de procedimento armazenado pode então fundir os dados que estão a ser copiados com os dados existentes. |Um nome do tipo de mesa. |No |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Exemplos JSON para copiar dados de e para SQL Server
Os exemplos a seguir fornecem definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). As seguintes amostras mostram como copiar dados de e para o SQL Server e Azure Blob Storage. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados usando a Atividade de Cópia na Fábrica de Dados Azure.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Exemplo: Copiar dados do SQL Server para Azure Blob
A seguinte amostra mostra:

1. Um serviço ligado do tipo [OnPremisesSqlServer](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [SqlServerTable](#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com atividade Copy que utiliza [SqlSource](#copy-activity-properties) e [BlobSink.](data-factory-azure-blob-connector.md#copy-activity-properties)

A amostra copia os dados da série de tempo de uma tabela SQL Server para uma bolha Azure a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

Como primeiro passo, configurar o portal de gestão de dados. As instruções estão nos [dados móveis entre locais no local e](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem.

**Serviço ligado ao SQL Server**
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
**Serviço ligado ao armazenamento Azure Blob**

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

A amostra pressupõe que criou uma tabela "MyTable" no SQL Server e contém uma coluna chamada "timetampcolumn" para dados da série de tempo. Pode consultar várias tabelas dentro da mesma base de dados utilizando um único conjunto de dados, mas uma única tabela deve ser usada para a tabela datasetName TipoProperty.

Definição "externa": "verdadeiro" informa o serviço Data Factory que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base na hora de início da fatia que está a ser processada. O caminho da pasta utiliza partes do ano, mês, dia e horas da hora de início.

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
**Pipeline com atividade de Copy**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar estes conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **SqlSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na hora passada para copiar.

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
Neste exemplo, **sqlReaderQuery** é especificado para o SqlSource. A Atividade de Cópia executa esta consulta com a fonte de Base de Dados do Servidor SQL para obter os dados. Em alternativa, pode especificar um procedimento armazenado especificando o **nome sqlReaderStoredProcedureName** e **os Computadores Deprocedures armazenados** (se o procedimento armazenado tiver parâmetros). O SqlReaderQuery pode fazer referência a várias tabelas dentro da base de dados referenciada pelo conjunto de dados de entrada. Não se limita apenas ao conjunto de tabelas como tabela Do nome de dadosProperty.

Se não especificar sqlReaderQuery ou sqlReaderStorEdProcedureName, as colunas definidas na secção de estrutura são utilizadas para construir uma consulta selecionada para correr contra a Base de Dados do Servidor SQL. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.

Consulte a secção [Sql Source](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) para obter a lista de propriedades suportadas por SqlSource e BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Exemplo: Copiar dados de Azure Blob para SQL Server
A seguinte amostra mostra:

1. O serviço ligado do tipo [OnPremisesSqlServer](#linked-service-properties).
2. O serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com atividade Copy que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e SqlSink.

A amostra copia os dados da série de tempo de uma bolha Azure para uma tabela sql server a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado ao SQL Server**

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
**Serviço ligado ao armazenamento Azure Blob**

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
**Conjunto de dados de entrada Azure Blob**

Os dados são recolhidos a partir de uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base na hora de início da fatia que está a ser processada. O percurso da pasta utiliza a parte do ano, mês e dia da hora de início e o nome do ficheiro utiliza a parte da hora da hora de início. Definição "externa": a definição "verdadeira" informa o serviço data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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
**Conjunto de dados de saída do SQL Server**

A amostra copia dados para uma tabela chamada "MyTable" no SQL Server. Crie a tabela no SQL Server com o mesmo número de colunas que espera que o ficheiro Blob CSV contenha. Novas filas são adicionadas à mesa a cada hora.

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
**Pipeline com atividade de Copy**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar estes conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **BlobSource** e o tipo **de pia** é definido para **SqlSink**.

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
1. Configure o seu SQL Server para aceitar ligações remotas. Lançar **SQL Server Management Studio,** servidor de clique **à** direita e clicar em **Propriedades**. Selecione **Ligações** da lista e verifique **Permitir ligações remotas ao servidor.**

    ![Ativar ligações remotas](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Consulte [a configuração da opção de configuração do servidor de acesso remoto](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option) para obter etapas detalhadas.
2. Lançar **gestor de configuração de servidor SQL**. Expanda a **configuração da rede de servidor SQL** para o caso que pretende e selecione **Protocolos para MSSQLSERVER**. Devias ver protocolos no painel direito. Ativar o TCP/IP clicando à direita **no TCP/IP** e clicando **em Ativar**.

    ![Ativar TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Consulte [Ativar ou Desativar um Protocolo de Rede de Servidor](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol) para obter detalhes e formas alternativas de ativar o protocolo TCP/IP.
3. Na mesma janela, clique duas vezes em **TCP/IP** para lançar a janela **TCP/IP Properties.**
4. Mude para o separador **endereços IP.** Desloque-se para baixo para ver a secção **IPAll.** Nota para baixo a **porta TCP**(padrão é **1433**).
5. Crie uma **regra para o Windows Firewall** na máquina para permitir a entrada de tráfego através desta porta.
6. **Verificar a ligação**: Para ligar ao SQL Server utilizando um nome totalmente qualificado, utilize o SQL Server Management Studio a partir de uma máquina diferente. Por exemplo: " \<machine\> \<domain\> . . . corp. \<company\> . com,1433.

   > [!IMPORTANT]
   > 
   > Consulte [os dados de movimento entre as fontes no local e a nuvem com o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obter informações detalhadas.
   > 
   > Consulte [os problemas de gateway de resolução de problemas](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter dicas sobre questões relacionadas com a ligação de resolução de problemas/gateways.


## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade na base de dados alvo
Esta secção fornece um exemplo que copia dados de uma tabela de origem sem coluna de identidade para uma tabela de destino com uma coluna de identidade.

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

Note que a tabela alvo tem uma coluna de identidade.

**Definição JSON do conjunto de dados de origem**

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
**Definição de conjunto de dados de destino JSON**

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

Note que como a sua origem e tabela alvo têm esquemas diferentes (o alvo tem uma coluna adicional com identidade). Neste cenário, é necessário especificar a propriedade da **estrutura** na definição de conjunto de dados-alvo, que não inclui a coluna de identidade.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocar procedimento armazenado a partir de pia SQL
Consulte [o procedimento de invocação para a pia SQL em](data-factory-invoke-stored-procedure-from-copy-activity.md) artigo de atividade de cópia para um exemplo de invocação de um procedimento armazenado da pia SQL numa atividade de cópia de um oleoduto.

## <a name="type-mapping-for-sql-server"></a>Mapeamento de tipo para servidor SQL
Conforme mencionado no artigo [de atividades](data-factory-data-movement-activities.md) de movimento de dados, a atividade Copy realiza conversões automáticas de tipo de origem para tipos de pia com a seguinte abordagem de 2 etapas:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter de tipo .NET para tipo de pia nativa

Ao mover dados para & do servidor SQL, os seguintes mapeamentos são usados do tipo SQL para .NET tipo e vice-versa.

O mapeamento é o mesmo que o Mapeamento do Tipo de Dados do Servidor SQL para ADO.NET.

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

## <a name="mapping-source-to-sink-columns"></a>Fonte de mapeamento para afundar colunas
Para mapear colunas de conjunto de dados de origem para colunas a partir do conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Cópia repetível
Ao copiar dados para a Base de Dados do Servidor SQL, a atividade de cópia anexa os dados à tabela do lavatório por predefinição. Para realizar um UPSERT em vez disso, Consulte [a escrita repetível para o artigo da SqlSink.](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink)

Ao copiar dados de lojas de dados relacionais, tenha em mente a repetibilidade para evitar resultados não intencionais. Na Azure Data Factory, pode repetir manualmente uma fatia. Também pode configurar a política de reagem para um conjunto de dados para que uma fatia seja re-executada quando ocorre uma falha. Quando uma fatia é reexame de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada. Ver [leitura repetível de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performance e Afinação
Consulte [copy Activity Performance & Guia de Afinação](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.