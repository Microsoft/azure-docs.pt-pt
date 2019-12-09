---
title: Copiar dados de ou para o Oracle usando Data Factory
description: Saiba como copiar dados de ou para um Oracle Database local usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 066e32d5ab21f88b170498173606043c54fec586
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928158"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Copiar dados de ou para o Oracle local usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-onprem-oracle-connector.md)
> * [Versão 2 (versão atual)](../connector-oracle.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se você estiver usando a versão atual do serviço de Azure Data Factory, consulte [conector do Oracle na v2](../connector-oracle.md).


Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de ou para um banco de dado Oracle local. O artigo se baseia em [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados usando a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados

Você pode copiar dados *de um banco* de dados Oracle para os seguintes armazenamentos:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos seguintes repositórios de dados *para um Oracle Database*:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Pré-requisitos

O Data Factory dá suporte à conexão com fontes Oracle locais usando o gateway Gerenciamento de Dados. Consulte [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) para saber mais sobre o gateway de gerenciamento de dados. Para obter instruções detalhadas sobre como configurar o gateway em um pipeline de dados para mover dados, consulte [mover dados do local para a nuvem](data-factory-move-data-between-onprem-and-cloud.md).

O gateway é necessário mesmo se o Oracle estiver hospedado em uma VM de IaaS (infraestrutura como serviço) do Azure. Você pode instalar o gateway na mesma VM IaaS que o armazenamento de dados ou em uma VM diferente, contanto que o gateway possa se conectar ao banco de dado.

> [!NOTE]
> Para obter dicas sobre como solucionar problemas relacionados à conexão e ao gateway, consulte [solucionar problemas do gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Versões e instalação com suporte

Este conector Oracle dá suporte a duas versões de drivers:

- **Microsoft driver para Oracle (recomendado)** : a partir do gerenciamento de dados Gateway versão 2,7, um driver da Microsoft para Oracle é instalado automaticamente com o gateway. Você não precisa instalar ou atualizar o driver para estabelecer conectividade com o Oracle. Você também pode experimentar um melhor desempenho de cópia usando esse driver. Há suporte para essas versões de bancos de dados Oracle:
  - Oracle 12c R1 (12,1)
  - Oracle 11g R1, R2 (11,1, 11,2)
  - Oracle 10g R1, R2 (10,1, 10,2)
  - Oracle 9i R1, R2 (9.0.1, 9,2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Não há suporte para o Oracle Proxy Server.

    > [!IMPORTANT]
    > Atualmente, o driver da Microsoft para Oracle dá suporte apenas à cópia de dados do Oracle. O driver não dá suporte à gravação no Oracle. O recurso testar conexão na guia **diagnóstico** do gerenciamento de dados gateway não dá suporte a este driver. Como alternativa, você pode usar o assistente de cópia para validar a conectividade.
    >

- **Oracle provedor de dados para .net**: você pode usar o Oracle provedor de dados para copiar dados do ou para o Oracle. Esse componente está incluído nos [componentes de acesso a dados do Oracle para Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/). Instale a versão relevante (32 bits ou 64 bits) no computador em que o gateway está instalado. O [Oracle provedor de dados .net 12,1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) pode acessar Oracle Database 10g versão 2 e versões posteriores.

    Se você selecionar **instalação XCopy**, conclua as etapas descritas no arquivo readme. htm. É recomendável selecionar o instalador que tem a interface do usuário (não o instalador do XCopy).

    Depois de instalar o provedor, reinicie o serviço de host Gerenciamento de Dados gateway em seu computador usando o miniaplicativo de serviços ou Configuration Manager de gateway Gerenciamento de Dados.

Se você usar o assistente de cópia para criar o pipeline de cópia, o tipo de driver será autodeterminado. O driver da Microsoft é usado por padrão, a menos que a versão do gateway seja anterior à versão 2,7 ou que você selecione Oracle como o coletor.

## <a name="get-started"></a>Introdução

Você pode criar um pipeline que tenha uma atividade de cópia. O pipeline move dados de ou para um banco de dados Oracle local usando diferentes ferramentas ou APIs.

A maneira mais fácil de criar um pipeline é usar o assistente de cópia. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente de copiar dados.

Você também pode usar uma das seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, um **modelo de Azure Resource Manager**, a **API do .net**ou a **API REST**. Consulte a [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre como criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, conclua as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie um **Data Factory**. Um data factory pode conter um ou mais pipelines.
2. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory. Por exemplo, se você estiver copiando dados de um Oracle Database para o armazenamento de BLOBs do Azure, crie dois serviços vinculados para vincular seu banco de dados Oracle e a conta de armazenamento do Azure ao seu data factory. Para propriedades do serviço vinculado que são específicas do Oracle, consulte [Propriedades do serviço vinculado](#linked-service-properties).
3. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia. No exemplo na etapa anterior, você cria um conjunto de dados para especificar a tabela em seu banco de dado Oracle que contém os dados de entrada. Você cria outro conjunto de dados para especificar o contêiner de BLOBs e a pasta que contém o dado copiado do Oracle Database. Para propriedades do conjunto de informações que são específicas do Oracle, consulte [Propriedades do conjunto](#dataset-properties)de informações.
4. Crie um **pipeline** que tenha uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída. No exemplo anterior, você usa **Oracle** , como uma fonte e **BlobSink** como um coletor para a atividade de cópia. Da mesma forma, se você estiver copiando do armazenamento de BLOBs do Azure para um banco de dados Oracle, use o **blobname** e o **OracleSink** na atividade de cópia. Para propriedades da atividade de cópia que são específicas de um banco de dados Oracle, consulte [Propriedades da atividade de cópia](#copy-activity-properties). Para obter detalhes sobre como usar um armazenamento de dados como origem ou coletor, selecione o link para o armazenamento de dados na seção anterior.

Quando você usa o assistente, as definições de JSON para essas Data Factory entidades são criadas automaticamente para você: serviços vinculados, conjuntos de itens e o pipeline. Ao usar ferramentas ou APIs (exceto para a API do .NET), você define essas entidades de Data Factory usando o formato JSON. Para obter exemplos que têm definições de JSON para Data Factory entidades que você usa para copiar dados de ou para um Oracle Database local, consulte exemplos de JSON.

As seções a seguir fornecem detalhes sobre as propriedades JSON que você usa para definir Data Factory entidades.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

A tabela a seguir descreve os elementos JSON que são específicos para o serviço vinculado do Oracle:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade **Type** deve ser definida como **OnPremisesOracle**. |Sim |
| Driver | Especifique qual driver usar para copiar dados de ou para um Oracle Database. Os valores permitidos são **Microsoft** e **odp** (padrão). Consulte [versão com suporte e instalação](#supported-versions-and-installation) para obter detalhes do driver. | Não |
| connectionString | Especifique as informações necessárias para se conectar à instância do banco de dados Oracle para a propriedade **ConnectionString** . | Sim |
| gatewayName | O nome do gateway que é usado para se conectar ao servidor Oracle local. |Sim |

**Exemplo: usando o driver da Microsoft**

> [!TIP]
> Se você vir um erro que diga "ORA-01025: parâmetro UPI fora do intervalo" e seu Oracle é a versão 8i, adicione `WireProtocolMode=1` à sua cadeia de conexão e tente novamente:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Exemplo: usando o driver ODP**

Para saber mais sobre os formatos permitidos, consulte [provedor de dados Oracle para .net odp](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades que estão disponíveis para definir conjuntos de os, consulte [criando conjuntos](data-factory-create-datasets.md)de os.

As seções de um arquivo JSON de conjunto de dados, como estrutura, disponibilidade e política, são semelhantes para todos os tipos de conjunto de dados (por exemplo, para Oracle, armazenamento de BLOBs do Azure e armazenamento de tabelas do Azure).

A seção **typeproperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção **typeproperties** do conjunto de um do tipo **oracletable** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |O nome da tabela no banco de dados Oracle ao qual o serviço vinculado se refere. |Não (se **oracleReaderQuery** ou **oraclename** for especificado) |

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [criando pipelines](data-factory-create-pipelines.md).

Propriedades como nome, descrição, tabelas de entrada e saída e política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A atividade de cópia usa apenas uma entrada e produz apenas uma saída.

As propriedades que estão disponíveis na seção **typeproperties** da atividade variam de acordo com cada tipo de atividade. As propriedades da atividade de cópia variam dependendo do tipo de origem e do coletor.

### <a name="oraclesource"></a>OracleSource

Na atividade de cópia, quando a origem é o tipo **Oracle** , as seguintes propriedades estão disponíveis na seção **typeproperties** :

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| oracleReaderQuery |Use a consulta personalizada para ler os dados. |Uma cadeia de caracteres de consulta SQL. Por exemplo, "selecionar \* de **MyTable**". <br/><br/>Se não for especificado, essa instrução SQL será executada: "selecionar \* de **MyTable**" |Não<br />(se **TableName** de **DataSet** for especificado) |

### <a name="oraclesink"></a>OracleSink

O **OracleSink** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| writeBatchTimeout |O tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. |**timespan**<br/><br/> Exemplo: 00:30:00 (30 minutos) |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge o valor de **writeBatchSize**. |Inteiro (número de linhas) |Não (padrão: 100) |
| sqlWriterCleanupScript |Especifica uma consulta para a atividade de cópia ser executada para que os dados de uma fatia específica sejam limpos. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifica o nome da coluna para a atividade de cópia preencher com um identificador de fatia gerado automaticamente. O valor de **sliceIdentifierColumnName** é usado para limpar os dados de uma fatia específica quando executado novamente. |O nome da coluna de uma coluna que tem o tipo de dados **binary (32)** . |Não |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>Exemplos de JSON para copiar dados de e para o Oracle Database

Os exemplos a seguir fornecem exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados de ou para um Oracle Database e para ou do armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores listados em [formatos e armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

**Exemplo: copiar dados do Oracle para o armazenamento de BLOBs do Azure**

O exemplo tem as seguintes entidades de Data Factory:

* Um serviço vinculado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [oracletable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que usa [oraclename](data-factory-onprem-oracle-connector.md#copy-activity-properties) como fonte e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) como coletor.

O exemplo copia dados de uma tabela em um banco de dado Oracle local para um blob por hora. Para obter mais informações sobre várias propriedades que são usadas no exemplo, consulte as seções que seguem os exemplos.

**Serviço vinculado do Oracle**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
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
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Conjunto de dados de entrada Oracle**

O exemplo supõe que você criou uma tabela chamada **MyTable** no Oracle. Ele contém uma coluna chamada **timestampcolumn** para dados de série temporal.

Configuração **externa**: **true** informa o serviço de data Factory de que o dataset é externo ao data Factory e que o conjunto de os não é produzido por uma atividade no data Factory.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
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

Os dados são gravados em um novo BLOB a cada hora (**frequência**: **hora**, **intervalo**: **1**). O caminho da pasta e o nome do arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte de ano, mês, dia e hora da hora de início.

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

**Pipeline com uma atividade de cópia**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e agendada para execução por hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **oraclename** e o tipo de **coletor** é definido como **BlobSink**. A consulta SQL que você especifica usando a propriedade **oracleReaderQuery** seleciona os dados na última hora a serem copiados.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
        "activities":[
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

**Exemplo: copiar dados do armazenamento de BLOBs do Azure para o Oracle**

Este exemplo mostra como copiar dados de uma conta de armazenamento de BLOBs do Azure para um banco de dados Oracle local. No entanto, você pode copiar dados *diretamente* de qualquer uma das fontes listadas em [formatos e armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

O exemplo tem as seguintes entidades de Data Factory:

* Um serviço vinculado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [oracletable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) que tem uma atividade de cópia que usa o [blobname](data-factory-azure-blob-connector.md#copy-activity-properties) como fonte [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) como coletor.

O exemplo copia dados de um blob para uma tabela em um banco de dado Oracle local a cada hora. Para obter mais informações sobre várias propriedades que são usadas no exemplo, consulte as seções que seguem os exemplos.

**Serviço vinculado do Oracle**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
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
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Conjunto de dados de entrada do blobs do Azure**

Os dados são coletados de um novo BLOB a cada hora (**frequência**: **hora**, **intervalo**: **1**). O caminho da pasta e o nome do arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte de ano, mês e dia da hora de início. O nome do arquivo usa a parte de hora da hora de início. A configuração **external**: **true** informa ao serviço de data Factory que essa tabela é externa à data Factory e não é produzida por uma atividade no data Factory.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
            "frequency": "Day",
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

**Conjunto de Oracle de saída**

O exemplo supõe que você criou uma tabela chamada **MyTable** no Oracle. Crie a tabela no Oracle com o mesmo número de colunas que você espera que o arquivo CSV de blob contenha. Novas linhas são adicionadas à tabela a cada hora.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Pipeline com uma atividade de cópia**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e agendada para execução a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **blobname** e o tipo de **coletor** é definido como **OracleSink**.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
        "activities":[
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
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


## <a name="troubleshooting-tips"></a>Sugestões para a resolução de problemas

### <a name="problem-1-net-framework-data-provider"></a>Problema 1: .NET Framework Provedor de Dados

**Mensagem de erro**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**Causas possíveis**

* O Provedor de Dados .NET Framework para Oracle não foi instalado.
* O .NET Framework Provedor de Dados para Oracle foi instalado para .NET Framework 2,0 e não foi encontrado nas pastas .NET Framework 4,0.

**Resolução**

* Se você ainda não instalou o provedor .NET para Oracle, [Instale-](https://www.oracle.com/technetwork/topics/dotnet/downloads/)o e repita o cenário.
* Se você vir a mensagem de erro mesmo depois de instalar o provedor, conclua as seguintes etapas:
    1. Abra o arquivo de configuração do computador para .NET 2,0 na pasta < disco do sistema\>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Pesquise por **Oracle provedor de dados para .net**. Você deve ser capaz de encontrar uma entrada, conforme mostrado no exemplo a seguir em **System. data** > **DbProviderFactories**: `<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Copie essa entrada para o arquivo Machine. config na seguinte pasta .NET 4,0: < disco do sistema\>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Em seguida, altere a versão para 4. xxx. x.x.
* Instale o caminho do < ODP. NET instalado\>\ 11.2.0 \ client_1 \odp.net\bin\4\Oracle.DataAccess.dll no GAC (cache de assembly global) executando **gacutil/i [caminho do provedor]** .

### <a name="problem-2-datetime-formatting"></a>Problema 2: formatação de data/hora

**Mensagem de erro**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Resolução**

Talvez seja necessário ajustar a cadeia de caracteres de consulta em sua atividade de cópia com base em como as datas são configuradas no banco de dados Oracle. Veja um exemplo (usando a função **TO_DATE** ):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapeamento de tipo para Oracle

Conforme mencionado nas [atividades de movimentação de dados](data-factory-data-movement-activities.md), a atividade de cópia executa conversões automáticas de tipos de origem em tipos de coletor usando a seguinte abordagem de duas etapas:

1. Converter de tipos de origem nativa para o tipo .NET.
2. Converta do tipo .NET para o tipo de coletor nativo.

Quando você move dados do Oracle, os seguintes mapeamentos são usados do tipo de dados Oracle para o tipo .NET e vice-versa:

| Tipo de dados Oracle | Tipo de dados .NET Framework |
| --- | --- |
| BArquivo |Byte[] |
| BLOB |Byte[]<br/>(com suporte apenas no Oracle 10g e versões posteriores quando você usa um driver da Microsoft) |
| º |String |
| CLOB |String |
| DATA |DateTime |
| FLOAT |Decimal, String (se precisão > 28) |
| INTEGER |Decimal, String (se precisão > 28) |
| INTERVALO DE ANO PARA MÊS |Int32 |
| INTERVALO DIA A SEGUNDO |TimeSpan |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, String (se precisão > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |DateTime |
| TIMESTAMP WITH TIME ZONE |DateTime |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Não há suporte para o intervalo dos tipos **de dados ano a mês** e o **intervalo de dias para segundo** quando você usa um driver da Microsoft.

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas do coletor

Para saber mais sobre como mapear colunas no conjunto de informações de origem para colunas no conjunto de informações do coletor, consulte [mapeando colunas do conjunto de informações no data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais

Ao copiar dados de armazenamentos de dados relacionais, tenha em mente a capacidade de repetição para evitar resultados indesejados. No Azure Data Factory, você pode executar manualmente uma fatia novamente. Você também pode configurar uma política de repetição para um conjunto de uma, de forma que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente, manualmente ou por uma política de repetição, certifique-se de que os mesmos dados sejam lidos, independentemente de quantas vezes uma fatia é executada. Para obter mais informações, consulte [leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e otimização

Consulte o [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory. Você também pode aprender sobre várias maneiras de otimizá-lo.
