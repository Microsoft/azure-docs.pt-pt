---
title: Copiar dados de ou para Oracle com o Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de ou para uma base de dados do Oracle no local com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4ff7f92d1d13966be5d17f37210bef961f64faf2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084611"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Copiar dados de ou para Oracle no local com o Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-onprem-oracle-connector.md)
> * [Versão 2 (versão atual)](../connector-oracle.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se estiver a utilizar a versão atual do serviço do Azure Data Factory, veja [conector Oracle no V2](../connector-oracle.md).


Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de ou para uma base de dados do Oracle no local. O artigo se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimento de dados através da atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados

Pode copiar dados *uma base de dados do Oracle* para os seguintes dados armazena:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados de arquivos de dados seguintes *para uma base de dados do Oracle*:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Pré-requisitos

O Data Factory suporta a ligar a origens de Oracle no local com o Data Management Gateway. Ver [Data Management Gateway](data-factory-data-management-gateway.md) para saber mais sobre o Data Management Gateway. Para obter instruções passo a passo sobre como configurar o gateway num pipeline de dados para mover dados, consulte [mover dados do local para a cloud](data-factory-move-data-between-onprem-and-cloud.md).

O gateway é necessário, mesmo que o Oracle está hospedado numa infraestrutura do Azure como um serviço (IaaS) VM. Pode instalar o gateway na mesma VM de IaaS, como o arquivo de dados ou numa VM diferente, desde que o gateway consiga estabelecer ligação à base de dados.

> [!NOTE]
> Para obter dicas sobre como resolver problemas relacionados com a ligação e o gateway, veja [resolver problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Versões suportadas e instalação

Este conector Oracle suporta duas versões de controladores:

- **Microsoft driver para Oracle (recomendado)**: A partir Data Management Gateway versão 2.7, um controlador de Microsoft para Oracle é instalado automaticamente com o gateway. Não precisa de instalar ou atualizar o driver para estabelecer conectividade a Oracle. Também pode assistir a melhor desempenho de cópia utilizando este controlador. Estas versões de bases de dados do Oracle são suportados:
  - R1 Oracle 12c (12.1)
  - Oracle 11g R1, R2 (11.1, 11.2)
  - Oracle 10g R1, R2 (10.1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Servidor de proxy do Oracle não é suportada.

    > [!IMPORTANT]
    > Atualmente, o Microsoft driver para Oracle suporta apenas cópia de dados do Oracle. O controlador não suporta escrita a Oracle. O recurso de ligação de teste sobre o Data Management Gateway **diagnóstico** separador não suporta este controlador. Em alternativa, pode utilizar o Assistente para copiar para validar a conectividade.
    >

- **Fornecedor de dados do Oracle para .NET**: Pode utilizar o fornecedor de dados Oracle para copiar dados de ou para Oracle. Este componente está incluído no [Oracle Data Access componentes para o Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/). Instale a versão relevante (32 bits ou 64 bits) na máquina onde o gateway está instalado. [Fornecedor de dados Oracle .NET 12.1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) pode aceder à base de dados do Oracle 10g versão 2 e versões posteriores.

    Se selecionou **instalação XCopy**, conclua os passos descritos no arquivo Readme htm. Recomendamos que selecione o instalador com a interface do Usuário (não o instalador de XCopy).

    Depois de instalar o fornecedor, reinicie o serviço de anfitrião de Gateway de gestão de dados no seu computador com o miniaplicativo serviços ou o Gestor de configuração do Data Management Gateway.

Se utilizar o Assistente para copiar para criar o pipeline da cópia, o tipo de controlador é autodetermined. O controlador Microsoft é utilizado por predefinição, a menos que a sua versão do gateway é anterior à versão 2.7 ou selecione Oracle como o sink.

## <a name="get-started"></a>Introdução

Pode criar um pipeline com uma atividade de cópia. O pipeline que move os dados de ou para uma base de dados do Oracle no local através de APIs ou ferramentas diferentes.

A maneira mais fácil para criar um pipeline é usar o Assistente para copiar. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente de cópia de dados.

Também pode utilizar uma das seguintes ferramentas para criar um pipeline: o **portal do Azure**, **Visual Studio**, **Azure PowerShell**, um **do Azure Resource Manager modelo**, o **API de .NET**, ou a **REST API**. Consulte a [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre como criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, conclua os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Criar uma **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines.
2. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados. Por exemplo, se estiver a copiar dados de uma base de dados Oracle para o armazenamento de Blobs do Azure, crie dois serviços ligados para ligar a sua base de dados Oracle e a conta de armazenamento do Azure à fábrica de dados. Para propriedades de serviço ligado que são específicas a Oracle, consulte [propriedades do serviço ligado](#linked-service-properties).
3. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. O exemplo no passo anterior, vai criar um conjunto de dados para especificar a tabela na base de dados do Oracle que contém os dados de entrada. Criar outro conjunto de dados para especificar o contentor de BLOBs e a pasta que contém os dados copiados da base de dados Oracle. Para as propriedades do conjunto de dados que são específicas para Oracle, consulte [propriedades do conjunto de dados](#dataset-properties).
4. Criar uma **pipeline** que tem uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída. O exemplo anterior, usa **OracleSource** como uma origem e **BlobSink** como sink para a atividade de cópia. Da mesma forma, se estiver a copiar do armazenamento de Blobs do Azure para uma base de dados do Oracle, usar **BlobSource** e **OracleSink** na atividade de cópia. Para propriedades de atividade de cópia que são específicas para uma base de dados do Oracle, consulte [propriedades da atividade de cópia](#copy-activity-properties). Para obter detalhes sobre como usar um arquivo de dados como uma origem ou sink, selecione a ligação para seu armazenamento de dados na secção anterior.

Quando utiliza o assistente, as definições de JSON para estas entidades do Data Factory são criadas automaticamente para: ligada a serviços, conjuntos de dados e o pipeline. Ao utilizar ferramentas ou APIs (exceto para a API .NET), define essas entidades do Data Factory, utilizando o formato JSON. Para exemplos que têm definições de JSON para entidades do Data Factory que utilizar para copiar dados de ou para uma base de dados do Oracle no local, consulte exemplos JSON.

As secções seguintes fornecem detalhes sobre as propriedades JSON que utilizar para definir entidades do Data Factory.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

A tabela seguinte descreve os elementos JSON que são específicos para o serviço ligado do Oracle:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |O **tipo** propriedade tem de ser definida como **OnPremisesOracle**. |Sim |
| driverType | Especifique qual driver para utilizar para copiar dados de ou para uma base de dados Oracle. Valores permitidos são **Microsoft** e **ODP** (predefinição). Ver [suportada a instalação e a versão](#supported-versions-and-installation) para obter detalhes de driver. | Não |
| connectionString | Especifique as informações necessárias para ligar à instância de base de dados do Oracle para o **connectionString** propriedade. | Sim |
| gatewayName | O nome do gateway que é utilizado para ligar ao servidor Oracle no local. |Sim |

**Exemplo: Com o controlador da Microsoft**

> [!TIP]
> Se vir um erro que diz "ORA 01025: Parâmetro de UPI fora do intervalo"e o Oracle versões 8i, adicionar `WireProtocolMode=1` para sua cadeia de ligação e tente novamente:

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

**Exemplo: Com o controlador ODP**

Para saber mais sobre os formatos permitidos, consulte [fornecedor de dados do Oracle para .NET ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

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

Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md).

As secções de um ficheiro JSON do conjunto de dados, tais como a estrutura, disponibilidade e a política, são semelhantes para todos os tipos de conjunto de dados (por exemplo, para o Oracle, o armazenamento de Blobs do Azure e o armazenamento de tabelas do Azure).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para o conjunto de dados do tipo **OracleTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |O nome da tabela na base de dados Oracle que o serviço ligado refere-se a. |Não (se **oracleReaderQuery** ou **OracleSource** for especificado) |

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Para obter uma lista completa de seções e as propriedades que estão disponíveis para a definição de atividades, consulte [Criar pipelines](data-factory-create-pipelines.md).

As propriedades, como o nome, descrição, entrada e saída de tabelas e a política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> Atividade de cópia precisar apenas uma entrada e saída de apenas um.

Propriedades que estão disponíveis no **typeProperties** secção da atividade varia com cada tipo de atividade. Propriedades da atividade de cópia variam consoante o tipo de origem e sink.

### <a name="oraclesource"></a>OracleSource

Na atividade de cópia, quando a origem é o **OracleSource** tipo, as seguintes propriedades estão disponíveis no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| oracleReaderQuery |Utilize a consulta personalizada para ler dados. |Uma cadeia de caracteres de consulta SQL. Por exemplo, "selecionar \* partir **MyTable**". <br/><br/>Se não for especificado, essa instrução de SQL é executada: "selecionar \* partir **MyTable**" |Não<br />(se **tableName** dos **conjunto de dados** for especificado) |

### <a name="oraclesink"></a>OracleSink

**OracleSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |O tempo de espera para o lote de inserção operação seja concluída antes de atingir o tempo limite. |**timespan**<br/><br/> Exemplo: 30: 00:00 (30 minutos) |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge o valor de **writeBatchSize**. |Número inteiro (número de linhas) |Não (predefinição: 100) |
| sqlWriterCleanupScript |Especifica uma consulta para a atividade de cópia executar, para que os dados de um setor específico é limpo. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifica o nome de coluna para a atividade de cópia preencher com um identificador de setor geradas automaticamente. O valor para **sliceIdentifierColumnName** é utilizado para limpar os dados de um setor específico quando voltar a executar. |O nome da coluna de uma coluna com o tipo de dados do **binary(32)**. |Não |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>Exemplos JSON para copiar dados de e para a base de dados Oracle

Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados de ou para uma base de dados Oracle e de ou para armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos sinks listados na [arquivos de dados e formatos suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) através da atividade de cópia no Azure Data Factory.

**Exemplo: Copiar dados do Oracle para o armazenamento de Blobs do Azure**

O exemplo possui as seguintes entidades do Data Factory:

* Um serviço ligado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* R [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) como origem e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) como sink.

O exemplo copia dados de uma tabela numa base de dados Oracle no local para um blob de hora a hora. Para obter mais informações sobre várias propriedades que são utilizadas no exemplo, veja as secções que se seguem os exemplos.

**Serviço ligado do Oracle**

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

**Serviço de ligado de armazenamento de Blobs do Azure**

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

**Conjunto de dados de entrada de Oracle**

O exemplo parte do princípio de que criou uma tabela chamada **MyTable** no Oracle. Ele contém uma coluna denominada **timestampcolumn** para dados de séries de tempo.

A definição **externo**: **verdadeiro** informa o serviço Data Factory, que o conjunto de dados é externo à fábrica de dados e que o conjunto de dados não é produzido por uma atividade na fábrica de dados.

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

Os dados são escritos para um blob novo a cada hora (**frequência**: **hora**, **intervalo**: **1**). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês, dia e parte de hora da hora de início.

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

O pipeline contém uma atividade de cópia que tenha configurado para utilizar os conjuntos de dados de entrada e saídos e agendada para ser executada uma vez por hora. No pipeline de definição de JSON, o **origem** tipo está definido como **OracleSource** e o **sink** tipo está definido como **BlobSink**. A consulta SQL que especificou utilizando o **oracleReaderQuery** propriedade seleciona os dados na hora anterior para copiar.

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

**Exemplo: Copiar dados de armazenamento de Blobs do Azure para Oracle**

Este exemplo mostra como copiar dados de uma conta de armazenamento de Blobs do Azure para uma base de dados do Oracle no local. No entanto, pode copiar dados *diretamente* de qualquer uma das origens listadas na [arquivos de dados e formatos suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) através da atividade de cópia no Azure Data Factory.

O exemplo possui as seguintes entidades do Data Factory:

* Um serviço ligado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* R [pipeline](data-factory-create-pipelines.md) que tem uma atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) como origem [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) como sink.

O exemplo copia dados de um blob para uma tabela numa base de dados Oracle no local a cada hora. Para obter mais informações sobre várias propriedades que são utilizadas no exemplo, veja as secções que se seguem os exemplos.

**Serviço ligado do Oracle**

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

**Serviço de ligado de armazenamento de Blobs do Azure**

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

Dados são captados um blob novo a cada hora (**frequência**: **hora**, **intervalo**: **1**). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês e parte do dia da hora de início. O nome de ficheiro utiliza a parte de hora a hora de início. A definição **externo**: **verdadeiro** informa o serviço Data Factory que esta tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída de Oracle**

O exemplo parte do princípio de que criou uma tabela chamada **MyTable** no Oracle. Crie a tabela no Oracle com o mesmo número de colunas que pretende que o ficheiro CSV de BLOBs para conter. Novas linhas são adicionadas à tabela de cada hora.

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

O pipeline contém uma atividade de cópia que tenha configurado para utilizar os conjuntos de dados de entrada e saídos e agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **BlobSource** e o **sink** tipo está definido como **OracleSink**.

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


## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

### <a name="problem-1-net-framework-data-provider"></a>Problema 1: Fornecedor de dados .NET Framework

**Mensagem de erro**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**Causas possíveis**

* O fornecedor de dados do .NET Framework para Oracle não foi instalado.
* O .NET Framework Data Provider Pro Oracle foi instalado para o .NET Framework 2.0 e não for encontrado nas pastas do .NET Framework 4.0.

**Resolução**

* Se ainda não instalou o fornecedor de .NET para o Oracle, [instalá-lo](https://www.oracle.com/technetwork/topics/dotnet/downloads/)e, em seguida, repita o cenário.
* Se vir a mensagem de erro mesmo depois de instalar o fornecedor, conclua os seguintes passos:
    1. Abra o ficheiro de configuração de máquina para o .NET 2.0 a partir da pasta < disco do sistema\>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Procure **fornecedor de dados do Oracle para .NET**. Deverá conseguir localizar uma entrada, conforme mostrado no exemplo a seguir sob **System. data** > **DbProviderFactories**: `<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Copie esta entrada para o arquivo Machine config na seguinte pasta .NET 4.0: < disco do sistema\>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Em seguida, altere a versão para 4.xxx.x.x.
* Instalar < caminho de instalação ODP.NET\>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll no cache de global assembly (GAC) executando **gacutil /i [provider path]**.

### <a name="problem-2-datetime-formatting"></a>Problema 2: Formatação de data/hora

**Mensagem de erro**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Resolução**

Poderá ter de ajustar a cadeia de consulta em sua atividade de cópia com base em como as datas são configuradas na sua base de dados Oracle. Eis um exemplo (com o **to_date** função):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapeamento de tipo para Oracle

Conforme mencionado na [atividades de movimento de dados](data-factory-data-movement-activities.md), atividade de cópia executa conversões de tipos automáticas de tipos de origem para o sink de tipos usando a abordagem de dois passos seguintes:

1. Converta entre tipos de origem nativas para o tipo de .NET.
2. Converta o tipo de .NET para o tipo de sink nativo.

Ao mover dados do Oracle, são utilizados os seguintes mapeamentos do tipo de dados Oracle para o tipo de .NET e vice-versa:

| Tipo de dados Oracle | Tipo de dados do .NET framework |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(com suporte apenas no Oracle 10g e versões posteriores ao utilizar um controlador Microsoft) |
| CHAR |String |
| CLOB |String |
| DATA |DateTime |
| NÚMERO DE VÍRGULA FLUTUANTE |Número decimal, cadeia de caracteres (se precisão > 28) |
| NÚMERO INTEIRO |Número decimal, cadeia de caracteres (se precisão > 28) |
| INTERVALO DE ANO PARA MÊS |Int32 |
| DIA DO INTERVALO COMO SEGUNDO |TimeSpan |
| LONGA |String |
| HÁ MUITO TEMPO NÃO PROCESSADOS |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NÚMERO |Número decimal, cadeia de caracteres (se precisão > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| CARIMBO DE DATA/HORA |DateTime |
| TIMESTAMP COM O FUSO HORÁRIO LOCAL |DateTime |
| TIMESTAMP COM O FUSO HORÁRIO |DateTime |
| NÚMERO INTEIRO NÃO ASSINADO |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Tipos de dados **intervalo ano para mês** e **intervalo dia TO segundo** não são suportados quando utiliza um controlador de Microsoft.

## <a name="map-source-to-sink-columns"></a>Origem do mapa para colunas de sink

Para saber mais sobre as colunas de mapeamento do conjunto de dados de origem para colunas do conjunto de dados de sink, veja [mapeamento de colunas do conjunto de dados no Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura passível de repetição de fontes relacionais

Quando copia dados de arquivos de dados relacional, tenha a capacidade de repetição em mente para evitar resultados indesejados. No Azure Data Factory, pode executar manualmente novamente um setor. Também pode configurar uma política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor será novamente executada, ou manualmente ou por uma política de repetição, certifique-se de que os mesmos dados são de leitura, não importa como número de vezes que um setor é executado. Para obter mais informações, consulte [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e otimização

Consulte a [guia de sintonização de desempenho de atividade de cópia e](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho de movimento de dados (atividade de cópia) no Azure Data Factory. Também pode saber sobre várias formas para otimizá-lo.
