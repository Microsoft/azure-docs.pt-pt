---
title: Copiar dados de ou para o Oráculo utilizando data factory
description: Saiba como copiar dados de ou para uma base de dados da Oracle no local utilizando a Azure Data Factory.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265860"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Copiar dados de ou para a Oracle no local utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-onprem-oracle-connector.md)
> * [Versão 2 (versão atual)](../connector-oracle.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se estiver a utilizar a versão atual do serviço Azure Data Factory, consulte o [conector Oracle em V2](../connector-oracle.md).


Este artigo explica como usar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de ou para uma base de dados da Oracle no local. O artigo baseia-se nas atividades de [movimento de dados,](data-factory-data-movement-activities.md)que apresenta uma visão geral do movimento de dados utilizando a Atividade de Cópia.

## <a name="supported-scenarios"></a>Cenários suportados

Pode copiar dados *de uma base de dados da Oracle* para as seguintes lojas de dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados das seguintes lojas de dados *para uma base de dados da Oracle:*

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Pré-requisitos

Data Factory suporta a ligação às fontes oracle no local utilizando gateway de gestão de dados. Consulte o Portal de [Gestão](data-factory-data-management-gateway.md) de Dados para saber mais sobre gateway de gestão de dados. Para obter instruções passo a passo sobre como configurar a porta de entrada num pipeline de dados para mover dados, consulte [mover dados do local para a nuvem](data-factory-move-data-between-onprem-and-cloud.md).

O portal é necessário mesmo que o Oráculo esteja hospedado numa infraestrutura Azure como um VM de serviço (IaaS). Pode instalar o portal no mesmo IaaS VM que o depósito de dados ou num VM diferente, desde que o portal possa ligar-se à base de dados.

> [!NOTE]
> Para obter dicas sobre problemas de resolução de problemas relacionados com a ligação e o portal, consulte problemas de [gateway da Troubleshoot](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Versões e instalação suportadas

Este conector Oracle suporta duas versões de condutores:

- **Controlador da Microsoft para o Oracle (recomendado)** : A partir da versão Gateway de Gestão de Dados 2.7, um controlador da Microsoft para a Oracle é automaticamente instalado com o gateway. Não é necessário instalar ou atualizar o controlador para estabelecer conectividade com a Oracle. Também pode experimentar um melhor desempenho de cópia utilizando este controlador. Estas versões das bases de dados oracle são suportadas:
  - Oráculo 12c R1 (12.1)
  - Oráculo 11g R1, R2 (11.1, 11.2)
  - Oráculo 10g R1, R2 (10.1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oráculo 8i R3 (8.1.7)

    > [!NOTE]
    > O servidor proxy da Oracle não é suportado.

    > [!IMPORTANT]
    > Atualmente, o controlador da Microsoft para a Oracle suporta apenas copiar dados da Oracle. O motorista não apoia escrever para a Oracle. A capacidade de ligação de teste no separador de **diagnóstico** gateway de gestão de dados não suporta este controlador. Em alternativa, pode utilizar o assistente Copy para validar a conectividade.
    >

- **Oracle Data Provider para .NET**: Pode utilizar o Oráculo Data Provider para copiar dados de ou para a Oracle. Este componente está incluído nos Componentes de Acesso a [Dados oracle para Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/). Instale a versão relevante (32 bits ou 64 bits) na máquina onde o portal está instalado. [O Provedor de Dados oracle .NET 12.1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) pode aceder ao Oracle Database 10g Release 2 e versões posteriores.

    Se selecionar a **Instalação XCopy,** complete os passos descritos no ficheiro readme.htm. Recomendamos selecionar o instalador que tem o UI (não o instalador XCopy).

    Depois de instalar o fornecedor, reinicie o serviço de hospedagem gateway de gestão de dados na sua máquina utilizando o Gestor de Configuração de Gateway de Gestão de Dados de Serviços.

Se utilizar o assistente copiar para autor do gasoduto de cópia, o tipo de condutor é autodeterminado. O controlador da Microsoft é utilizado por padrão, a menos que a sua versão gateway seja mais cedo do que a versão 2.7 ou selecione oracle como a pia.

## <a name="get-started"></a>Introdução

Pode criar um oleoduto que tenha uma atividade de cópia. O pipeline move dados de ou para uma base de dados oracle no local utilizando diferentes ferramentas ou APIs.

A maneira mais fácil de criar um pipeline é utilizar o assistente copiar. Consulte [tutorial: Crie um pipeline utilizando o assistente copy](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um pipeline utilizando o assistente de dados de cópia.

Também pode utilizar uma das seguintes ferramentas para criar um pipeline: **Visual Studio,** **Azure PowerShell,** um **modelo de Gestor de Recursos Azure,** a **API .NET**ou a **API REST**. Consulte o [tutorial copy activity](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre como criar um pipeline que tenha uma atividade de cópia.

Quer utilize as ferramentas ou APIs, complete os seguintes passos para criar um pipeline que transe os dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos.
2. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados. Por exemplo, se estiver a copiar dados de uma base de dados da Oracle para o armazenamento da Blob, crie dois serviços ligados para ligar a sua base de dados Oracle e a conta de armazenamento Azure à sua fábrica de dados. Para propriedades de serviço ligadas específicas à Oracle, consulte [propriedades de serviço Linked](#linked-service-properties).
3. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia. No exemplo da etapa anterior, cria-se um conjunto de dados para especificar a tabela na sua base de dados Oracle que contém os dados de entrada. Cria outro conjunto de dados para especificar o recipiente blob e a pasta que contém os dados copiados da base de dados do Oracle. Para propriedades de conjunto de dados específicas da Oracle, consulte [propriedades dataset](#dataset-properties).
4. Crie um **pipeline** que tenha uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo anterior, utiliza o **OracleSource** como fonte e **blobSink** como pia para a atividade da cópia. Da mesma forma, se estiver a copiar do armazenamento da Blob Azure para uma base de dados da Oracle, utiliza o **BlobSource** e o **OracleSink** na atividade de cópia. Para propriedades de Copy Activity específicas de uma base de dados da Oracle, consulte [propriedades da Atividade de Cópia](#copy-activity-properties). Para mais detalhes sobre como utilizar uma loja de dados como fonte ou pia, selecione o link para a sua loja de dados na secção anterior.

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados são automaticamente criadas para si: serviços ligados, conjuntos de dados e o pipeline. Quando utiliza ferramentas ou APIs (exceto a API .NET), define estas entidades da Fábrica de Dados utilizando o formato JSON. Para amostras que possuam definições JSON para entidades da Fábrica de Dados que utiliza para copiar dados de ou para uma base de dados da Oracle no local, consulte os exemplos da JSON.

As seguintes secções fornecem detalhes sobre as propriedades jSON que utiliza para definir entidades da Fábrica de Dados.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

A tabela seguinte descreve elementos JSON específicos do serviço ligado ao Oráculo:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade **tipo** deve ser definida para **OnPremisesOracle**. |Sim |
| driverType | Especifique qual o controlador a utilizar para copiar dados de ou para uma base de dados da Oracle. Os valores permitidos são **Microsoft** e **ODP** (predefinição). Consulte a [versão suportada e a instalação](#supported-versions-and-installation) para obter detalhes do condutor. | Não |
| connectionString | Especifique as informações necessárias para se ligar à base de dados oracle, por exemplo, para a **propriedade de ligaçãoString.** | Sim |
| gatewayName | O nome do portal que é usado para ligar ao servidor Oracle no local. |Sim |

**Exemplo: Utilização do controlador da Microsoft**

> [!TIP]
> Se vir um erro que diga "ORA-01025: Parâmetro UPI fora de alcance" e o seu Oracle é a versão 8i, adicione `WireProtocolMode=1` à sua cadeia de ligação e tente novamente:

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

**Exemplo: Utilização do condutor ODP**

Para saber sobre os formatos permitidos, consulte o fornecedor de [dados oracle para .NET ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Criar conjuntos](data-factory-create-datasets.md)de dados .

As secções de um conjunto de dados FicheiroJSON, tais como estrutura, disponibilidade e política, são semelhantes para todos os tipos de conjuntos de dados (por exemplo, para o armazenamento Oracle, Azure Blob e armazenamento de mesa azure).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção **typeProperties** para o conjunto de dados do tipo **OracleTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |O nome da tabela na base de dados oracle a que o serviço ligado se refere. |Não (se o **oracleReaderQuery** ou **OracleSource** forespecificado) |

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Para obter uma lista completa de secções e propriedades disponíveis para definir [atividades, consulte Criar oleodutos.](data-factory-create-pipelines.md)

Propriedades como nome, descrição, tabelas de entrada e saída, e a política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Copy Activity leva apenas uma entrada e produz apenas uma saída.

As propriedades disponíveis na secção **tipoPropriedades** da atividade variam com cada tipo de atividade. As propriedades da Atividade de Cópia variam consoante o tipo de fonte e pia.

### <a name="oraclesource"></a>OracleSource

Na Atividade de Cópia, quando a fonte é do tipo **OracleSource,** as seguintes propriedades estão disponíveis na secção **TypeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| oracleReaderQuery |Use a consulta personalizada para ler dados. |Uma corda de consulta SQL. Por exemplo, "selecione \* do **MyTable".** <br/><br/>Se não especificado, esta declaração sQL é executada: "selecione \* do **MyTable"** |Não<br />(se for especificado **o nome** do conjunto de **dados)** |

### <a name="oraclesink"></a>OracleSink

**A OracleSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |O tempo de espera para a operação de inserção do lote esteja concluído antes de sair. |**tempospan**<br/><br/> Exemplo: 00:30:00 (30 minutos) |Não |
| writeBatchSize |Insere os dados na tabela SQL quando o tamanho do tampão atinge o valor de **writeBatchSize**. |Inteiro (número de linhas) |Não (padrão: 100) |
| sqlWriterCleanupScript |Especifica uma consulta para a Atividade de Cópia executar de modo a que os dados de uma fatia específica sejam limpos. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifica o nome da coluna para a Atividade de Cópia para preencher com um identificador de fatias autogerada. O valor da **fatiaIdentifierColumnName** é utilizado para limpar dados de uma fatia específica quando reexecutado. |O nome da coluna de uma coluna que tem tipo de dados de **binário(32)** . |Não |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>Exemplos jSON para copiar dados de e para a base de dados Oracle

Os exemplos seguintes fornecem definições JSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados de ou para uma base de dados da Oracle e de ou para o armazenamento de Azure Blob. No entanto, os dados podem ser copiados para qualquer um dos lavatórios listados em [lojas e formatos](data-factory-data-movement-activities.md#supported-data-stores-and-formats) de dados suportados utilizando a Copy Activity na Azure Data Factory.

**Exemplo: Copiar dados da Oracle para o armazenamento de Blob Azure**

A amostra tem as seguintes entidades data Factory:

* Um serviço de tipo [onPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [oleoduto](data-factory-create-pipelines.md) com uma atividade de cópia que usa o [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) como fonte e [blobSink](data-factory-azure-blob-connector.md#copy-activity-properties) como pia.

A amostra copia dados de uma tabela numa base de dados do Oracle no local para uma bolha de hora a hora. Para obter mais informações sobre várias propriedades que são usadas na amostra, consulte as secções que seguem as amostras.

**Serviço ligado ao Oráculo**

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

**Serviço ligado ao armazenamento Azure Blob**

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

**Conjunto de dados de entrada oracle**

A amostra pressupõe que criou uma tabela chamada **MyTable** in Oracle. Contém uma coluna chamada **timestampcolumn** para dados da série time.

Definição **externa**: **informa verdadeiramente** o serviço data Factory de que o conjunto de dados é externo à fábrica de dados e que o conjunto de dados não é produzido por uma atividade na fábrica de dados.

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

Os dados são escritos para uma nova bolha a cada hora **(frequência:** **hora,** **intervalo:** **1**). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza o ano, mês, dia e hora parte da hora de início.

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

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e programado para ser executado de hora a hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **OracleSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL que especifica utilizando a propriedade **oracleReaderQuery** seleciona os dados na última hora para copiar.

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

**Exemplo: Copiar dados do armazenamento da Blob Azure para a Oracle**

Esta amostra mostra como copiar dados de uma conta de armazenamento Azure Blob para uma base de dados da Oracle no local. No entanto, pode copiar dados *diretamente* de qualquer uma das fontes listadas em [lojas e formatos](data-factory-data-movement-activities.md#supported-data-stores-and-formats) de dados suportados utilizando a Copy Activity na Azure Data Factory.

A amostra tem as seguintes entidades data Factory:

* Um serviço de tipo [onPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Um [oleoduto](data-factory-create-pipelines.md) que tem uma atividade de cópia que usa [blobSource](data-factory-azure-blob-connector.md#copy-activity-properties) como fonte [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) como pia.

A amostra copia dados de uma bolha para uma mesa numa base de dados do Oracle no local a cada hora. Para obter mais informações sobre várias propriedades que são usadas na amostra, consulte as secções que seguem as amostras.

**Serviço ligado ao Oráculo**

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

**Serviço ligado ao armazenamento Azure Blob**

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

Os dados são recolhidos a partir de uma nova bolha a cada hora **(frequência:** **hora,** **intervalo**: **1**). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza o ano, mês e parte do dia da hora de início. O nome do ficheiro usa a parte de hora da hora de início. A definição **externa**: **verdadeira** informa o serviço data Factory de que este quadro é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída do Oráculo**

A amostra pressupõe que criou uma tabela chamada **MyTable** in Oracle. Crie a tabela em Oráculo com o mesmo número de colunas que espera que o ficheiro CSV blob contenha. Novas filas são adicionadas à mesa a cada hora.

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

**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **BlobSource** e o tipo **de pia** está definido para **OracleSink**.

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

### <a name="problem-1-net-framework-data-provider"></a>Problema 1: .FORNECEDOR de Dados-Quadro da REDE

**Mensagem de erro**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**Possíveis causas**

* O Fornecedor de Dados-Quadro .NET da Oracle não foi instalado.
* O Fornecedor de Dados-Quadro .NET para a Oracle foi instalado na .NET Framework 2.0 e não se encontra nas pastas .NET Framework 4.0.

**Resolução**

* Se não tiver instalado o Fornecedor .NET para a Oracle, [instale-o](https://www.oracle.com/technetwork/topics/dotnet/downloads/)e, em seguida, tente novamente o cenário.
* Se vir a mensagem de erro mesmo depois de instalar o fornecedor, complete os seguintes passos:
    1. Abra o ficheiro config da máquina para .NET 2.0 a partir da pasta <system disk\>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Pesquisa por **Oracle Data Provider para .NET**. Deverá ser possível encontrar uma entrada, como mostra a seguinte amostra no **sistema.data** > **DbProviderFactories**: `<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Copie esta entrada para o ficheiro machine.config na seguinte pasta .NET 4.0: <system disk\>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Em seguida, mude a versão para 4.xxx.x.x.
* Instalação <ODP.NET Caminho instalado\>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll na cache de montagem global (GAC) executando **gacutil /i [caminho do fornecedor]** .

### <a name="problem-2-datetime-formatting"></a>Problema 2: Formatação de data/hora

**Mensagem de erro**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Resolução**

Pode ser necessário ajustar a cadeia de consulta na sua atividade de cópia com base na configuração das datas na sua base de dados Oracle. Aqui está um exemplo (usando a função **to_date):**

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapeamento de tipo para Oráculo

Conforme mencionado nas atividades de movimento de [dados,](data-factory-data-movement-activities.md)a Atividade de Cópia realiza conversões automáticas de tipos de origem para tipos de sink, utilizando a seguinte abordagem em duas etapas:

1. Converter de tipos de origem nativa para o tipo .NET.
2. Converta do tipo .NET para o tipo de pia nativa.

Quando transfere dados da Oracle, os seguintes mapeamentos são utilizados do tipo de dados Oracle para o tipo .NET e vice-versa:

| Tipo de dados oráculo | Tipo de dados de enquadramento .NET |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(apenas suportado em versões Oracle 10g e posteriores quando utilizar um controlador da Microsoft) |
| CHAR |String |
| CLOB |String |
| DATA |DateTime |
| FLOAT |Decimal, String (se precisão > 28) |
| INTEGER |Decimal, String (se precisão > 28) |
| INTERVALO DE ANO PARA MÊS |Int32 |
| DIA DE INTERVALO PARA SEGUNDO |TimeSpan |
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
> Os tipos de dados **INTERVAL ANO A MÊS** e INTERVAL DAY TO **SECOND** não são suportados quando se utiliza um controlador da Microsoft.

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas

Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem para colunas no conjunto de dados do lavatório, consulte [mapeando colunas](data-factory-map-columns.md)de conjunto de dados na Fábrica de Dados .

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetível de fontes relacionais

Quando copiar dados de lojas de dados relacionais, tenha em mente a repetível para evitar resultados não intencionais. Na Azure Data Factory, pode reexecutar manualmente uma fatia. Também pode configurar uma política de retry para um conjunto de dados para que uma fatia seja reexecutada quando ocorre uma falha. Quando uma fatia é reexecutada, manualmente ou por uma política de retry, certifique-se de que os mesmos dados são lidos, não importa quantas vezes uma fatia seja executada. Para mais informações, consulte [A leitura repetível de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e otimização

Consulte o guia de [desempenho e afinação](data-factory-copy-activity-performance.md) da Atividade de Cópia para conhecer os factores-chave que afetam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Azure. Também pode aprender sobre várias formas de otimizar.
