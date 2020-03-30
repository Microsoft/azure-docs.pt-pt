---
title: Mover dados de e para o Servidor SQL
description: Saiba como mover dados para/a partir da base de dados do SQL Server que está no local ou num VM Azure utilizando a Azure Data Factory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265769"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Mover dados de e para o SQL Server no local ou no IaaS (Azure VM) utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-sqlserver-connector.md)
> * [Versão 2 (versão atual)](../connector-sql-server.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector SQL Server em V2](../connector-sql-server.md).

Este artigo explica como usar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de/para uma base de dados do SQL Server no local. Baseia-se no artigo Atividades do Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade de cópia.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados de uma base de **dados do SQL Server** para as seguintes lojas de dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados das seguintes lojas de dados para uma base de **dados do SQL Server:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Versões SQL Server suportadas
Este conector SQL Server suporta copiar dados de/para as seguintes versões de instância alojadas no local ou no Azure IaaS utilizando a autenticação SQL e a autenticação windows: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2008, SQL Server 2008, SQL Server 2008, SQL Server 2008, SQL Server 2008, SQL Server 2008, SQL Server 2005, SQL Server 2005, SQL Server 2005, SQL Server 2005, SQL Server 2005, SQL Server 2005, SQL Server 2008, SQL Server 2008, SQL Server 2005, SQL Server 2005, SQL Server 2005, SQL Server 20058, SQL Server 20058, SQL Server 2005, SQL Server 2005, SQL Server 2005, SQL Server 2005, S

## <a name="enabling-connectivity"></a>Permitindo a conectividade
Os conceitos e passos necessários para a ligação com o SQL Server hospedado no local ou em VMs Azure IaaS (Infraestrutura-as-a-Service) são os mesmos. Em ambos os casos, é necessário utilizar o Portal de Gestão de Dados para a conectividade.

Consulte [os dados em movimento entre as localizações no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para saber sobre o Gateway de Gestão de Dados e instruções passo a passo sobre a configuração do portal. A criação de uma instância de gateway é um pré-requisito para a ligação com o Servidor SQL.

Embora possa instalar o gateway na mesma máquina no local ou na mesma instância VM em nuvem como o SQL Server para um melhor desempenho, recomendamos que os instale em máquinas separadas. Ter o gateway e o SQL Server em máquinas separadas reduz a contenção de recursos.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para uma base de dados do SQL Server no local utilizando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é utilizar o **Assistente de Cópia**. Ver [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um pipeline utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos.
2. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados. Por exemplo, se estiver a copiar dados de uma base de dados do SQL Server para um armazenamento de blob Azure, cria dois serviços ligados para ligar a sua base de dados do SQL Server e a conta de armazenamento Azure à sua fábrica de dados. Para propriedades de serviço ligadas específicas à base de dados do SQL Server, consulte a secção de [propriedades de serviço ligadas.](#linked-service-properties)
3. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado no último passo, cria-se um conjunto de dados para especificar a tabela SQL na sua base de dados do SQL Server que contém os dados de entrada. E cria outro conjunto de dados para especificar o recipiente blob e a pasta que detém os dados copiados da base de dados do SQL Server. Para propriedades de conjunto de dados específicas da base de dados do Servidor SQL, consulte a secção de propriedades do conjunto de [dados.](#dataset-properties)
4. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, você usa o SqlSource como fonte e BlobSink como um lavatório para a atividade de cópia. Da mesma forma, se estiver a copiar do Armazenamento Azure Blob para a Base de Dados do Servidor SQL, utiliza o BlobSource e o SqlSink na atividade da cópia. Para copiar propriedades de atividade específicas para a Base de Dados do Servidor SQL, consulte a secção de propriedades de atividade de [cópia.](#copy-activity-properties) Para obter mais informações sobre como utilizar uma loja de dados como fonte ou pia, clique no link na secção anterior para a sua loja de dados.

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON. Para amostras com definições JSON para entidades da Fábrica de Dados que são usadas para copiar dados de/para uma base de dados do SQL Server no local, consulte a secção de [exemplos jSON](#json-examples-for-copying-data-from-and-to-sql-server) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Fábrica de Dados específicas do Servidor SQL:

## <a name="linked-service-properties"></a>Propriedades de serviço seletos
Cria um serviço ligado do tipo **OnPremisesSqlServer** para ligar uma base de dados do SQL Server ao local a uma fábrica de dados. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao Servidor SQL no local.

A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao Servidor SQL.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade do tipo deve ser definida para: **OnPremisesSqlServer**. |Sim |
| conexãoString |Especifique as informações de ligação Necessárias à base de dados do SQL Server no local utilizando a autenticação SQL ou a autenticação do Windows. |Sim |
| nome gateway |Nome do portal que o serviço Data Factory deve utilizar para ligar à base de dados do SQL Server no local. |Sim |
| o nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a Autenticação do Windows. Exemplo: **nome\\de utilizador do nome de domínio**. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |

Pode encriptar credenciais utilizando o cmdlet **New-AzDataFactoryEncryptValue** e utilizá-las na cadeia de ligação, como mostra o seguinte exemplo (propriedade**EncryptedCredential):**

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Amostras
**JSON para a utilização da autenticação SQL**

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

O Portal de Gestão de Dados personificará a conta de utilizador especificada para se ligar à base de dados do SQL Server no local.

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

Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntode dados (SQL Server, Blob Azure, tabela Azure, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção **TypeProperties** para o conjunto de dados do tipo **SqlServerTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na instância de Base de Dados do Servidor SQL a que o serviço ligado se refere. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Se estiver a mover dados de uma base de dados do SQL Server, define o tipo de origem na atividade de cópia para **SqlSource**. Da mesma forma, se estiver a mover dados para uma base de dados do SQL Server, define o tipo de pia na atividade de cópia para **SqlSink**. Esta secção fornece uma lista de propriedades suportadas por SqlSource e SqlSink.

Para obter uma lista completa de secções & propriedades disponíveis para definir atividades, consulte o artigo [Creating Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Copy Activity leva apenas uma entrada e produz apenas uma saída.

Considerando que as propriedades disponíveis na secção typeProperties da atividade variam com cada tipo de atividade. Para a atividade de Cópia, variam dependendo dos tipos de fontes e pias.

### <a name="sqlsource"></a>SqlSource
Quando a fonte numa atividade de cópia é do tipo **SqlSource,** as seguintes propriedades estão disponíveis na secção **TypeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Por exemplo: selecione * do MyTable. Pode fazer referência a várias tabelas da base de dados referenciada pelo conjunto de dados de entrada. Se não especificada, a declaração SQL que é executada: selecione a partir do MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. A última declaração sQL deve ser uma declaração SELECT no procedimento armazenado. |Não |
| parâmetros de procedimento saqueados |Parâmetros para o procedimento armazenado. |Nome/pares de valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. |Não |

Se o **sqlReaderQuery** for especificado para o SqlSource, a Atividade de Cópia executa esta consulta contra a fonte da Base de Dados do Servidor SQL para obter os dados.

Em alternativa, pode especificar um procedimento armazenado especificando o nome de **procedimento sqlReaderStoredEdE** e **os parâmetros de procedimento armazenados** (se o procedimento armazenado tiver parâmetros).

Se não especificar o sqlReaderQuery ou o sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura são usadas para construir uma consulta selecionada para ser executada contra a Base de Dados do Servidor SQL. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.

> [!NOTE]
> Quando utiliza o nome de **procedimento sqlReaderStored**, ainda precisa especificar um valor para a propriedade **tableName** no conjunto de dados JSON. No entanto, não há validações realizadas contra esta tabela.

### <a name="sqlsink"></a>SqlSink
**A SqlSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| escreverBatchTimeout |Aguarde o tempo para que a operação de inserção do lote esteja concluída antes de sair. |tempospan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |
| escreverBatchSize |Insere os dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanupScript |Especifique a consulta para a Atividade de Cópia executar de tal forma que os dados de uma fatia específica são limpos. Para mais informações, consulte a secção [de cópias repetível.](#repeatable-copy) |Uma declaração de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a Atividade de Cópia para preencher com identificador de fatias gerado automaticamente, que é usado para limpar dados de uma fatia específica quando reexecutado. Para mais informações, consulte a secção [de cópias repetível.](#repeatable-copy) |Nome da coluna de uma coluna com tipo de dados de binário(32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que define como aplicar dados de origem em tabela-alvo, por exemplo, fazer upserts ou transformar usando a sua própria lógica de negócio. <br/><br/>Note que este procedimento armazenado será **invocado por lote**. Se quiser fazer uma operação que só funciona uma vez e não tem nada a `sqlWriterCleanupScript` ver com dados de origem, por exemplo, eliminar/truncar, utilize a propriedade. |Nome do procedimento armazenado. |Não |
| parâmetros de procedimento saqueados |Parâmetros para o procedimento armazenado. |Nome/pares de valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. |Não |
| sqlWriterTableType |Especifique o nome do tipo de tabela a utilizar no procedimento armazenado. A atividade de cópia disponibiliza os dados disponíveis numa tabela temporária com este tipo de tabela. O código de procedimento armazenado pode então fundir os dados que estão a ser copiados com os dados existentes. |Um nome tipo de mesa. |Não |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Exemplos jSON para copiar dados de e para SQL Server
Os exemplos seguintes fornecem definições JSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). As seguintes amostras mostram como copiar dados de e para o SQL Server e armazenamento de Blob Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer fonte para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Exemplo: Copiar dados do SQL Server para O Blob Azure
A amostra que se segue mostra:

1. Um serviço ligado do tipo [OnPremisesSqlServer](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [SqlServerTable](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [gasoduto](data-factory-create-pipelines.md) com a atividade Copy que utiliza [o SqlSource](#copy-activity-properties) e o [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados da série de tempo de uma tabela SQL Server para uma bolha Azure a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

Como primeiro passo, configurar o portal de gestão de dados. As instruções estão nos [dados em movimento entre os locais no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem.

**Serviço ligado ao Servidor SQL**
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
**Conjunto de dados de entrada do Servidor SQL**

A amostra pressupõe que criou uma tabela "MyTable" no SQL Server e contém uma coluna chamada "timestampcolumn" para dados da série time. Pode consultar várias tabelas dentro da mesma base de dados utilizando um único conjunto de dados, mas uma única tabela deve ser utilizada para o tipo de nome de dados do conjunto de dadosProperty.

Definição "externa": "verdadeira" informa o serviço data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza partes ano, mês, dia e horas da hora de início.

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
**Pipeline com atividade de Cópia**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar estes conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **SqlSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora para copiar.

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
Neste exemplo, o **sqlReaderQuery** é especificado para o SqlSource. A Atividade de Cópia executa esta consulta contra a fonte da Base de Dados do Servidor SQL para obter os dados. Em alternativa, pode especificar um procedimento armazenado especificando o nome de **procedimento sqlReaderStoredEdE** e **os parâmetros de procedimento armazenados** (se o procedimento armazenado tiver parâmetros). O sqlReaderQuery pode fazer referência a várias tabelas dentro da base de dados referenciada pelo conjunto de dados de entrada. Não se limita apenas ao conjunto de tabelas como o conjunto de dados NameTypeProperty.

Se não especificar o sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura são usadas para construir uma consulta selecionada para ser executada contra a Base de Dados do Servidor SQL. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.

Consulte a secção [Sql Source](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) para obter a lista de propriedades suportadas por SqlSource e BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Exemplo: Copiar dados de Azure Blob para SQL Server
A amostra que se segue mostra:

1. O serviço ligado do tipo [OnPremisesSqlServer](#linked-service-properties).
2. O serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a atividade Copy que utiliza [blobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e SqlSink.

A amostra copia dados da série de tempo de uma bolha Azure para uma tabela SQL Server a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado ao Servidor SQL**

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
**Conjunto de dados de entrada de Azure Blob**

Os dados são recolhidos a partir de uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e o nome do ficheiro utiliza a parte da hora da hora de início. "externa": a definição "verdadeira" informa o serviço data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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
**Conjunto de dados de saída do Servidor SQL**

A amostra copia dados para uma tabela chamada "MyTable" no Servidor SQL. Crie a tabela no SQL Server com o mesmo número de colunas que espera que o ficheiro Blob CSV contenha. Novas filas são adicionadas à mesa a cada hora.

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
**Pipeline com atividade de Cópia**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar estes conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **BlobSource** e o tipo **de pia** é definido para **SqlSink**.

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
1. Configure o seu Servidor SQL para aceitar ligações remotas. Lance o Estúdio de **Gestão do Servidor SQL,** **servidor**de clique satislo, e clique em **Propriedades**. Selecione **Ligações** da lista e verifique **Permitir ligações remotas ao servidor**.

    ![Ativar ligações remotas](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Consulte [a opção de configuração do servidor](https://msdn.microsoft.com/library/ms191464.aspx) de acesso remoto para obter passos detalhados.
2. Lance o Gestor de Configuração do **Servidor SQL**. Expandir a configuração da **rede de servidores SQL** para a instância que deseja e selecionar **Protocolos para MSSQLSERVER**. Devia ver protocolos no painel certo. Ativar o TCP/IP clicando no **TCP/IP** e clicando em **Ativar**.

    ![Ativar TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Consulte [ativar ou desativar um Protocolo](https://msdn.microsoft.com/library/ms191294.aspx) de Rede de Servidores para obter detalhes e formas alternativas de ativar o protocolo TCP/IP.
3. Na mesma janela, clique duplo **no TCP/IP** para lançar a janela **TCP/IP Properties.**
4. Mude para o separador **endereços IP.** Desloque-se para baixo para ver a secção **IPAll.** Note a **porta TCP**(o predefinido é **de 1433**).
5. Crie uma **regra para o Windows Firewall** na máquina para permitir a entrada de tráfego através desta porta.
6. **Verifique a ligação**: Para se ligar ao Servidor SQL utilizando um nome totalmente qualificado, utilize o Estúdio de Gestão de Servidores SQL de uma máquina diferente. Por exemplo:\<\>" máquina . \<domínio\>\<.corp.\>empresa .com,1433."

   > [!IMPORTANT]
   > 
   > Consulte os [dados entre as fontes no local e a nuvem com](data-factory-move-data-between-onprem-and-cloud.md) gateway de gestão de dados para obter informações detalhadas.
   > 
   > Consulte problemas de [gateway de Troubleshoot](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter dicas sobre questões relacionadas com ligação a problemas/gateway.


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

Reparem que a mesa-alvo tem uma coluna de identidade.

**Definição jSON de data de origem**

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
**Definição jSON de data de destino**

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

Note que como a sua fonte e tabela alvo têm esquemas diferentes (o alvo tem uma coluna adicional com identidade). Neste cenário, é necessário especificar a **propriedade da estrutura** na definição de conjunto de dados alvo, que não inclui a coluna de identidade.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocar procedimento armazenado a partir de pia SQL
Consulte o [procedimento armazenado invoke para o sumidouro SQL no](data-factory-invoke-stored-procedure-from-copy-activity.md) artigo de atividade de cópia, como exemplo, invocando um procedimento armazenado a partir de um lavatório SQL numa atividade de cópia de um gasoduto.

## <a name="type-mapping-for-sql-server"></a>Tipo de mapeamento para servidor SQL
Conforme mencionado no artigo de atividades de movimento de [dados,](data-factory-data-movement-activities.md) a atividade Copy realiza conversões automáticas de tipos de origem para tipos de sink com a seguinte abordagem de 2 passos:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter do tipo .NET para o tipo de pia nativa

Ao mover dados para & do servidor SQL, os seguintes mapeamentos são utilizados do tipo SQL para o tipo .NET e vice-versa.

O mapeamento é o mesmo que o Mapeamento do Tipo de Dados do Servidor SQL para ADO.NET.

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

## <a name="mapping-source-to-sink-columns"></a>Fonte de mapeamento para afundar colunas
Para mapear colunas desde o conjunto de dados de origem até colunas a partir de conjunto de dados de sumidouro, consulte [colunas de conjunto de dados de mapeamento na Fábrica](data-factory-map-columns.md)de Dados Azure .

## <a name="repeatable-copy"></a>Cópia repetível
Ao copiar dados para a Base de Dados do Servidor SQL, a atividade de cópia anexa os dados à tabela do lavatório por padrão. Para executar um UPSERT em vez disso, consulte a escrita repetível para o artigo [da SqlSink.](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink)

Ao copiar dados de lojas de dados relacionais, tenha em mente a repetível para evitar resultados não intencionais. Na Azure Data Factory, pode reproduzir uma fatia manualmente. Também pode configurar a política de retry para um conjunto de dados para que uma fatia seja reexecutada quando ocorre uma falha. Quando uma fatia é reexecutada de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada. Ver [Leitura repetível a partir de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Afinação
Consulte o [Copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Do Azure e várias formas de o otimizar.
