---
title: Copiar dados de ou para a Oracle utilizando a Data Factory
description: Saiba como copiar dados de ou para uma base de dados oracle no local utilizando a Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 02fc142a08176aa577250417c0e394218e832f34
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387347"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Copiar dados de ou para a Oracle no local através da Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-onprem-oracle-connector.md)
> * [Versão 2 (versão atual)](../connector-oracle.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se estiver a utilizar a versão atual do serviço Azure Data Factory, consulte o [conector Oracle em V2](../connector-oracle.md).


Este artigo explica como utilizar a Copy Activity na Azure Data Factory para mover dados para ou de uma base de dados oracle no local. O artigo baseia-se em atividades de [movimento de dados,](data-factory-data-movement-activities.md)que apresenta uma visão geral do movimento de dados através da utilização da Copy Activity.

## <a name="supported-scenarios"></a>Cenários suportados

Pode copiar dados *de uma base de dados oracle* para as seguintes lojas de dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados das seguintes lojas de dados *para uma base de dados Oracle:*

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Pré-requisitos

A Data Factory suporta a ligação às fontes da Oracle no local utilizando o Data Management Gateway. Consulte [o Data Management Gateway](data-factory-data-management-gateway.md) para saber mais sobre o Gateway de Gestão de Dados. Para obter instruções passo a passo sobre como configurar o gateway num pipeline de dados para mover [dados,](data-factory-move-data-between-onprem-and-cloud.md)consulte mover dados do local para a nuvem .

O gateway é necessário mesmo que o Oráculo esteja hospedado numa infraestrutura Azure como um VM de serviço (IaaS). Pode instalar o gateway no mesmo IaaS VM que a loja de dados ou num VM diferente, desde que o gateway possa ligar-se à base de dados.

> [!NOTE]
> Para obter dicas sobre questões relacionadas com a ligação e o gateway, consulte [problemas de gateway de resolução de problemas](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Versões e instalação suportadas

Este conector Oracle suporta duas versões de condutores:

- **Controlador da Microsoft para a Oracle (recomendado)**: A partir da versão 2.7 do Data Management Gateway, um controlador microsoft para a Oracle é automaticamente instalado com o gateway. Não é necessário instalar ou atualizar o controlador para estabelecer conectividade com a Oracle. Também pode experimentar um melhor desempenho de cópia utilizando este controlador. Estas versões das bases de dados da Oracle são suportadas:
  - Oráculo 12c R1 (12.1)
  - Oráculo 11g R1, R2 (11.1, 11.2)
  - Oráculo 10g R1, R2 (10.1, 10.2)
  - Oráculo 9i R1, R2 (9.0.1, 9.2)
  - Oráculo 8i R3 (8.1.7)

    > [!NOTE]
    > O servidor de procuração da Oracle não é suportado.

    > [!IMPORTANT]
    > Atualmente, o controlador da Microsoft para a Oracle suporta apenas a cópia de dados da Oracle. O motorista não suporta escrever ao Oráculo. A capacidade de ligação de teste no separador Data Management Gateway **Diagnostics** não suporta este controlador. Em alternativa, pode utilizar o assistente copy para validar a conectividade.
    >

- **Oracle Data Provider for.NET**: Pode utilizar o Oracle Data Provider para copiar dados de ou para a Oracle. Este componente está incluído nos [componentes de acesso a dados da Oracle para windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/). Instale a versão relevante (32 bits ou 64 bits) na máquina onde o gateway está instalado. [O Oracle Data Provider .NET 12.1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) pode aceder à Oracle Database 10g Versões 2 e versões posteriores.

    Se selecionar **A Instalação XCopy,** preencha os passos descritos no ficheiro readme.htm. Recomendamos selecionar o instalador que tenha o UI (não o instalador XCopy).

    Depois de instalar o fornecedor, reinicie o serviço de anfitrião Data Management Gateway na sua máquina utilizando o Applet ou Data Management Gateway Configuration Manager.

Se utilizar o assistente copy para autoria do pipeline de cópia, o tipo de controlador é autodeterminado. O controlador Microsoft é utilizado por padrão, a menos que a sua versão gateway seja anterior à versão 2.7 ou selecione o Oracle como pia.

## <a name="get-started"></a>Introdução

Pode criar um oleoduto com uma atividade de cópia. O pipeline move dados para ou a partir de uma base de dados Oracle no local, utilizando diferentes ferramentas ou APIs.

A forma mais fácil de criar um oleoduto é utilizar o assistente copy. Consulte [Tutorial: Crie um oleoduto utilizando o assistente copy](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de Dados de Cópia.

Também pode utilizar uma das seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, um **modelo Azure Resource Manager**, a **API .NET** ou a **API REST**. Consulte o tutorial de [Atividade de Cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre como criar um pipeline que tenha uma atividade de cópia.

Quer utilize as ferramentas ou APIs, complete os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos.
2. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados. Por exemplo, se estiver a copiar dados de uma base de dados da Oracle para o armazenamento Azure Blob, crie dois serviços ligados para ligar a sua base de dados Oracle e a conta de armazenamento Azure à sua fábrica de dados. Para propriedades de serviço ligadas específicas à Oracle, consulte [as propriedades de serviço ligadas](#linked-service-properties).
3. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo do passo anterior, cria-se um conjunto de dados para especificar a tabela na base de dados da Oracle que contém os dados de entrada. Cria outro conjunto de dados para especificar o recipiente blob e a pasta que detém os dados copiados da base de dados do Oráculo. Para obter propriedades de conjunto de dados específicas da Oracle, consulte [as propriedades do Dataset](#dataset-properties).
4. Crie um **pipeline** que tenha uma atividade de cópia que leva um conjunto de dados como uma entrada e um conjunto de dados como uma saída. No exemplo anterior, utiliza o **OracleSource** como fonte e **o BlobSink** como pia para a atividade da cópia. Da mesma forma, se estiver a copiar do armazenamento da Azure Blob para uma base de dados oracle, utilize **o BlobSource** e o **OracleSink** na atividade da cópia. Para propriedades de Atividade de Cópia específicas de uma base de dados Oracle, consulte [as propriedades da Atividade de Cópias.](#copy-activity-properties) Para obter mais informações sobre como utilizar uma loja de dados como fonte ou pia, selecione o link para a sua loja de dados na secção anterior.

Quando utiliza o assistente, as definições JSON para estas entidades da Data Factory são criadas automaticamente para si: serviços ligados, conjuntos de dados e o pipeline. Quando utiliza ferramentas ou APIs (exceto a API .NET), define estas entidades da Data Factory utilizando o formato JSON. Para amostras que tenham definições JSON para entidades da Data Factory que utilize para copiar dados de ou para uma base de dados oracle no local, consulte exemplos JSON.

As secções seguintes fornecem detalhes sobre as propriedades JSON que utiliza para definir entidades da Data Factory.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

A tabela a seguir descreve elementos JSON específicos do serviço ligado à Oráculo:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade **tipo** deve ser definida para **OnPremisesOracle**. |Yes |
| Tipo de motorista | Especificar qual o condutor a utilizar para copiar dados de ou para uma base de dados oracle. Os valores permitidos são **Microsoft** e **ODP** (predefinição). Consulte [a versão e a instalação suportadas](#supported-versions-and-installation) para obter detalhes do condutor. | No |
| conexãoStragem | Especifique as informações necessárias para ligar à placa de base de dados oracle para a **propriedade connectionString.** | Yes |
| gatewayName | O nome do portal que é usado para ligar ao servidor Oracle no local. |Yes |

**Exemplo: Utilizar o controlador da Microsoft**

> [!TIP]
> Se vir um erro que diga "ORA-01025: Parâmetro UPI fora do alcance" e o seu Oráculo é a versão 8i, adicione `WireProtocolMode=1` a sua cadeia de ligação e tente novamente:

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

**Exemplo: Utilização do controlador ODP**

Para saber mais sobre os formatos permitidos, consulte [o fornecedor de dados da Oracle para .NET ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md).

As secções de um ficheiro JSON do conjunto de dados, tais como estrutura, disponibilidade e política, são semelhantes para todos os tipos de conjuntos de dados (por exemplo, para o armazenamento da Oracle, Azure Blob e armazenamento da Tabela Azure).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção **de tipos de direitos** para o conjunto de dados do tipo **OracleTable** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |O nome da tabela na base de dados Oráculo a que o serviço ligado se refere. |Não (se **o oracleReaderQuery** ou **OracleSource** for especificado) |

## <a name="copy-activity-properties"></a>Propriedades de Atividade de Cópia

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [criar oleodutos.](data-factory-create-pipelines.md)

Propriedades como nome, descrição, tabelas de entrada e saída, e a política está disponível para todos os tipos de atividades.

> [!NOTE]
> A Copy Activity requer apenas uma entrada e produz apenas uma saída.

As propriedades que estão disponíveis na secção **de tipoProperties** da atividade variam com cada tipo de atividade. As propriedades da Atividade de Cópia variam consoante o tipo de fonte e a pia.

### <a name="oraclesource"></a>OracleSource

Na Atividade de Cópia, quando a fonte é o tipo **OracleSource,** as seguintes propriedades estão disponíveis na secção **de tipoProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| OracleReaderQuery |Utilize a consulta personalizada para ler dados. |Uma cadeia de consulta SQL. Por exemplo, "selecione \* a partir de **MyTable".** <br/><br/>Se não for especificado, esta declaração SQL é executada: "selecione \* from **MyTable"** |No<br />(se **o nome** de tabela do conjunto de **dados** for especificado) |

### <a name="oraclesink"></a>Oráculosink

**A OracleSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| escreverBatchTimeout |O tempo de espera para o funcionamento do inserção do lote deve ser concluído antes do tempo de se esgotar. |**timespan**<br/><br/> Exemplo: 00:30:00 (30 minutos) |No |
| escreverBatchSize |Insere dados na tabela SQL quando o tamanho do tampão atinge o valor da **writeBatchSize**. |Inteiro (número de linhas) |Não (padrão: 100) |
| sqlWriterCleanUpScript |Especifica uma consulta para a Atividade de Cópia a executar de modo a que os dados de uma fatia específica seja limpo. |Uma declaração de consulta. |No |
| sliceIdentifierColumnName |Especifica o nome da coluna para a Atividade de Cópia para preencher com um identificador de fatias autogerado. O valor para **sliceIdentifierColumnName** é utilizado para limpar dados de uma fatia específica quando reexecutivo. |O nome da coluna de uma coluna que tem o tipo de dados **binário(32)**. |No |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>Exemplos JSON para copiar dados de e para a base de dados oracle

Os exemplos a seguir fornecem definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados de ou para uma base de dados oracle e de ou para o armazenamento de Azure Blob. No entanto, os dados podem ser copiados para qualquer uma das pias listadas em [lojas e formatos de dados suportados,](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a Copy Activity na Azure Data Factory.

**Exemplo: Copiar dados da Oracle para o armazenamento da Azure Blob**

A amostra tem as seguintes entidades da Data Factory:

* Um serviço ligado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [oleoduto](data-factory-create-pipelines.md) com uma atividade de cópia que usa [o OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) como fonte e [blobSink](data-factory-azure-blob-connector.md#copy-activity-properties) como pia.

A amostra copia dados de uma tabela numa base de dados oracle no local para uma bolha de hora a hora. Para obter mais informações sobre várias propriedades que são utilizadas na amostra, consulte as secções que seguem as amostras.

**Serviço ligado à Oracle**

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

A amostra assume que criou uma tabela chamada **MyTable** em Oráculo. Contém uma coluna chamada **timetampcolumn** para dados da série de tempo.

Definição **externa:** **informa** o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados e que o conjunto de dados não é produzido por uma atividade na fábrica de dados.

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

Os dados são escritos para uma nova bolha a cada hora **(frequência:** **hora,** **intervalo**: **1**). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base na hora de início da fatia que está a ser processada. O percurso da pasta utiliza o ano, mês, dia e hora parte da hora de início.

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

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e programado para funcionar de hora a hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **OracleSource** e o tipo **de pia** está definido para **BlobSink**. A consulta SQL que especifica através da propriedade **oracleReaderQuery** seleciona os dados na hora anterior para copiar.

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

**Exemplo: Copiar dados do armazenamento da Azure Blob para a Oracle**

Esta amostra mostra como copiar dados de uma conta de armazenamento Azure Blob para uma base de dados Oracle no local. No entanto, pode copiar dados *diretamente* de qualquer uma das fontes listadas em [lojas e formatos de dados suportados,](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a Copy Activity na Azure Data Factory.

A amostra tem as seguintes entidades da Data Factory:

* Um serviço ligado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Um [oleoduto](data-factory-create-pipelines.md) que tem uma atividade de cópia que usa [blobSource](data-factory-azure-blob-connector.md#copy-activity-properties) como fonte [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) como pia.

A amostra copia dados de uma bolha para uma mesa numa base de dados oracle no local a cada hora. Para obter mais informações sobre várias propriedades que são utilizadas na amostra, consulte as secções que seguem as amostras.

**Serviço ligado à Oracle**

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

Os dados são recolhidos a partir de uma nova bolha a cada hora **(frequência:** **hora,** **intervalo**: **1**). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base na hora de início da fatia que está a ser processada. O percurso da pasta utiliza o ano, o mês e o dia parte da hora de início. O nome do ficheiro utiliza a parte da hora da hora de início. A definição **externa**: **informa verdadeiramente** o serviço data factory que esta tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída da Oracle**

A amostra assume que criou uma mesa chamada **MyTable** em Oráculo. Crie a tabela em Oráculo com o mesmo número de colunas que espera que o ficheiro CSV blob contenha. Novas filas são adicionadas à mesa a cada hora.

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

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **BlobSource** e o tipo **de pia** é definido para **OracleSink**.

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

### <a name="problem-1-net-framework-data-provider"></a>Problema 1: .NET-se-quadro fornecedor de dados

**Mensagem de erro**

```text
Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.
```

**Possíveis causas**

* O Fornecedor de Dados-Quadro .NET da Oracle não foi instalado.
* O Fornecedor de Dados-Quadro .NET da Oracle foi instalado no .NET Framework 2.0 e não se encontra nas pastas .NET Framework 4.0.

**Resolução**

* Se ainda não instalou o Fornecedor .NET para a Oracle, [instale-o](https://www.oracle.com/technetwork/topics/dotnet/downloads/)e, em seguida, recandida o cenário.
* Se vir a mensagem de erro mesmo depois de instalar o fornecedor, complete os seguintes passos:
    1. Abra o ficheiro de configuração da máquina para .NET 2.0 da pasta <disco de sistema \>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Procure por **.NET**. Deverá ser possível encontrar uma entrada como mostrada na seguinte amostra no âmbito do **system.data**  >  **DbProviderFactories**:`<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Copie esta entrada no ficheiro machine.config na seguinte pasta .NET 4.0: <disco de sistema \>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Em seguida, mude a versão para 4.xxx.x.x.
* Instale <ODP.NET Caminho Instalado \>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll na cache de montagem global (GAC) executando **gacutil /i [caminho do provedor]**.

### <a name="problem-2-datetime-formatting"></a>Problema 2: Formatação de data/hora

**Mensagem de erro**

```text
Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.
```

**Resolução**

Poderá ser necessário ajustar o string de consulta na sua atividade de cópia com base na configuração das datas na base de dados do Oráculo. Aqui está um exemplo (usando a função **to_date):**

```console   
"oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"
```


## <a name="type-mapping-for-oracle"></a>Tipo de mapeamento para Oráculo

Conforme mencionado nas [atividades](data-factory-data-movement-activities.md)de movimento de dados, copy Activity realiza conversões automáticas de tipo de origem para tipos de pia, utilizando a seguinte abordagem em duas etapas:

1. Converter de tipos de origem nativo para o tipo .NET.
2. Converter do tipo .NET para o tipo de pia nativa.

Quando se move dados da Oracle, os seguintes mapeamentos são usados do tipo de dados Oráculo para o tipo .NET e vice-versa:

| Tipo de dados oráculo | .NET Tipo de dados-quadro |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(suportado apenas nas versões Oracle 10g e posteriores quando utiliza um controlador Microsoft) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLUTUAR |Decimal, String (se a precisão > 28) |
| INTEGER |Decimal, String (se a precisão > 28) |
| INTERVALO DE ANO PARA MÊS |Int32 |
| DIA DE INTERVALO PARA SEGUNDO |TimeSpan |
| Longo |String |
| LONGO CRU |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NÚMERO |Decimal, String (se a precisão > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| CARIMBO DE DATA/HORA |DateTime |
| TIMETAMP COM FUSO HORÁRIO LOCAL |DateTime |
| TIMETAMP COM FUSO HORÁRIO |DateTime |
| INTEIRO NÃO ASSINADO |Número |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Os tipos de **dados INTERVALO DE ANO A MÊS** e **INTERVALO DIA A SEGUNDO** não são suportados quando utiliza um controlador Microsoft.

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas

Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem para colunas no conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetível de fontes relacionais

Ao copiar dados de lojas de dados relacionais, tenha em mente a repetibilidade para evitar resultados não intencionais. Na Azure Data Factory, pode repetir manualmente uma fatia. Também pode configurar uma política de reagem para um conjunto de dados para que uma fatia seja re-executada quando ocorre uma falha. Quando uma fatia é reexame, manualmente ou por uma política de relagem, certifique-se de que os mesmos dados são lidos independentemente do número de vezes que uma fatia é executada. Para obter mais informações, consulte [Repetível de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e otimização

Consulte o guia de [desempenho e afinação da Atividade](data-factory-copy-activity-performance.md) de Cópia para saber sobre os factores-chave que afetam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory. Também pode aprender sobre várias formas de otimizá-lo.
