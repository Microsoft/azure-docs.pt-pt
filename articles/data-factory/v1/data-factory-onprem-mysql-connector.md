---
title: Mover dados do MySQL usando a Azure Data Factory
description: Saiba como mover dados da base de dados MySQL usando a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 452f4fce-9eb5-40a0-92f8-1e98691bea4c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 90fccba016a3db9ff85f8ec7c8fd426ef3c896a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91872107"
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Mover dados do MySQL usando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-onprem-mysql-connector.md)
> * [Versão 2 (versão atual)](../connector-mysql.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector MySQL em V2](../connector-mysql.md).


Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de uma base de dados MySQL no local. Baseia-se no artigo de Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

Pode copiar dados de uma loja de dados MySQL no local para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Atualmente, a fábrica de dados suporta apenas a transferência de dados de uma loja de dados MySQL para outras lojas de dados, mas não para transferir dados de outras lojas de dados para uma loja de dados MySQL. 

## <a name="prerequisites"></a>Pré-requisitos
O serviço Data Factory suporta a ligação a fontes MySQL no local utilizando o Gateway de Gestão de Dados. Consulte [dados em movimento entre locais no local e](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem para saber sobre o Gateway de Gestão de Dados e instruções passo a passo sobre a configuração do gateway.

O Gateway é necessário mesmo que a base de dados MySQL esteja hospedada numa máquina virtual Azure IaaS (VM). Pode instalar o gateway no mesmo VM que a loja de dados ou num VM diferente, desde que o gateway possa ligar-se à base de dados.

> [!NOTE]
> Consulte [os problemas de gateway de resolução de problemas](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter dicas sobre questões relacionadas com a ligação de resolução de problemas/gateways.

## <a name="supported-versions-and-installation"></a>Versões e instalação suportadas
Para que o Gateway de Gestão de Dados se conecte à Base de Dados MySQL, é necessário instalar o [Conector/NET mySQL para](https://dev.mysql.com/downloads/connector/net/) o Microsoft Windows (versão entre 6.6.5 e 6.10.7) no mesmo sistema que o Gateway de Gestão de Dados. Este controlador de 32 bits é compatível com gateway de gestão de dados de 64 bits. A versão 5.1 e superior da MySQL é suportada.

> [!TIP]
> Se tiver atingido um erro em "Autenticação falhou porque a parte remota fechou o fluxo de transporte.", considere atualizar o Conector/NET do MySQL para a versão superior.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de uma loja de dados cassandra no local, utilizando diferentes ferramentas/APIs. 

- A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy. 
- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia. 

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. 
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON.  Para obter uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados de uma loja de dados MySQL no local, consulte [o exemplo JSON: Copiar dados do MySQL para a secção Azure Blob](#json-example-copy-data-from-mysql-to-azure-blob) deste artigo. 

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas de uma loja de dados MySQL:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado mySQL.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **OnPremisesMySql** |Sim |
| servidor |Nome do servidor MySQL. |Sim |
| base de dados |Nome da base de dados MySQL. |Sim |
| esquema |O nome do esquema na base de dados. |Não |
| authenticationType |Tipo de autenticação usada para ligar à base de dados MySQL. Os valores possíveis são: `Basic` . |Sim |
| userName |Especifique o nome do utilizador para ligar à base de dados MySQL. |Sim |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador que especificou. |Sim |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados MySQL no local. |Sim |

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, Azure blob, Azure table, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção de tipos depropertações para conjunto de dados do tipo **RelationalTable** (que inclui conjunto de dados MySQL) tem as seguintes propriedades

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na página da Base de Dados MySQL a que o serviço ligado se refere. |Não (se a **consulta** de **RelationalSource** for especificada) |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, entradas e tabelas de saída, são políticas disponíveis para todos os tipos de atividades.

Enquanto que as propriedades disponíveis na secção **de tipos de atividade** variam com cada tipo de atividade. Para a atividade copy, variam dependendo dos tipos de fontes e pias.

Quando a origem na atividade de cópia é do tipo **RelationalSource** (que inclui o MySQL), as seguintes propriedades estão disponíveis na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: selecione * do MyTable. |Não (se **o nome** de tabela do conjunto de **dados** for especificado) |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>Exemplo JSON: Copiar dados do MySQL para Azure Blob
Este exemplo fornece definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostra como copiar dados de uma base de dados MySQL no local para um Azure Blob Storage. No entanto, os dados podem ser copiados para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.

> [!IMPORTANT]
> Esta amostra fornece snippets JSON. Não inclui instruções passo a passo para a criação da fábrica de dados. Consulte [os dados em movimento entre as localizações no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter instruções passo a passo.

A amostra tem as seguintes entidades de fábrica de dados:

1. Um serviço ligado do tipo [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [RelacionalTable](data-factory-onprem-mysql-connector.md#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [RelationalSource](data-factory-onprem-mysql-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de uma consulta resulta na base de dados MySQL para uma bolha de hora a hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

Como primeiro passo, configurar o portal de gestão de dados. As instruções estão nos [dados móveis entre locais no local e](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem.

**Serviço ligado mySQL:**

```JSON
    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
        }
      }
    }
```

**Serviço ligado a Azure Storage:**

```JSON
    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }
```

**Conjunto de dados de entrada MySQL:**

A amostra pressupõe que criou uma tabela "MyTable" no MySQL e contém uma coluna chamada "timetampcolumn" para dados da série de tempo.

Definição "externa": "verdadeiro" informa o serviço data Factory que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

```JSON
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
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

**Conjunto de dados de saída Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base na hora de início da fatia que está a ser processada. O caminho da pasta utiliza partes do ano, mês, dia e horas da hora de início.

```JSON
    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
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
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
```

**Pipeline com atividade de Copy:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **RelationalSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **de consulta** seleciona os dados na hora passada para copiar.

```JSON
    {
        "name": "CopyMySqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }
```


### <a name="type-mapping-for-mysql"></a>Mapeamento de tipo para MySQL
Conforme mencionado no artigo [de atividades](data-factory-data-movement-activities.md) de movimento de dados, a atividade copy realiza conversões automáticas de tipo de origem para tipos de pia com a seguinte abordagem em duas etapas:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter de tipo .NET para tipo de pia nativa

Ao mover dados para o MySQL, os seguintes mapeamentos são usados dos tipos MySQL para os tipos .NET.

| Tipo de base de dados MySQL | .NET Tipo de quadro |
| --- | --- |
| bigint não assinado |Decimal |
| bigint |Int64 |
| bit |Decimal |
| blob |Byte[] |
| bool |Booleano |
| char |String |
| date |Datetime |
| datetime |Datetime |
| decimal |Decimal |
| dupla precisão |Double (Duplo) |
| double |Double (Duplo) |
| enum |String |
| float |Único |
| int não assinado |Int64 |
| int |Int32 |
| inteiro não assinado |Int64 |
| número inteiro |Int32 |
| varbinário longo |Byte[] |
| varchar longo |String |
| longblob |Byte[] |
| texto longo |String |
| mediumblob |Byte[] |
| mediumint não assinado |Int64 |
| mediumint |Int32 |
| tex médio |String |
| numeric |Decimal |
| real |Double (Duplo) |
| set |String |
| pequeno não assinado |Int32 |
| smallint |Int16 |
| texto |String |
| hora |TimeSpan |
| carimbo de data/hora |Datetime |
| tinyblob |Byte[] |
| minúsculo não assinado |Int16 |
| tinyint |Int16 |
| texto minúsculo |String |
| varchar |String |
| ano |int |

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas
Para obter informações sobre as colunas de mapeamento em conjunto de dados de origem para colunas no conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetível de fontes relacionais
Ao copiar dados de lojas de dados relacionais, tenha em mente a repetibilidade para evitar resultados não intencionais. Na Azure Data Factory, pode repetir manualmente uma fatia. Também pode configurar a política de reagem para um conjunto de dados para que uma fatia seja re-executada quando ocorre uma falha. Quando uma fatia é reexame de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada. Ver [leitura repetível de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performance e Afinação
Consulte [copy Activity Performance & Guia de Afinação](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.
