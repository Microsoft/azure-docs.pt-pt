---
title: Mover dados da Amazon Redshift utilizando a Azure Data Factory
description: Saiba como mover dados da Amazon Redshift utilizando a Azure Data Factory Copy Activity.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c0dcaec9c8e9a310af1fd6fc319e0784694610e2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463091"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Mover dados da Amazon Redshift usando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-amazon-redshift-connector.md)
> * [Versão 2 (versão atual)](../connector-amazon-redshift.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector Amazon Redshift em V2](../connector-amazon-redshift.md).

Este artigo explica como usar a Copy Activity in Azure Data Factory para mover dados da Amazon Redshift. O artigo baseia-se no artigo das Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

A Data Factory suporta atualmente apenas a transferência de dados da Amazon Redshift para uma [loja de dados de sumidouro suportado.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) A transferência de dados de outras lojas de dados para a Amazon Redshift não é suportada.

> [!TIP]
> Para obter o melhor desempenho ao copiar grandes quantidades de dados da Amazon Redshift, considere usar o comando Redshift **UNLOAD** incorporado através do Amazon Simple Storage Service (Amazon S3). Para mais informações, consulte [Use UNLOAD para copiar dados da Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Pré-requisitos
* Se estiver a transferir dados para uma loja de dados no local, instale [o Data Management Gateway](data-factory-data-management-gateway.md) numa máquina no local. Conceder acesso a uma porta de entrada para o cluster Amazon Redshift utilizando o endereço IP da máquina no local. Para obter instruções, consulte [Autorizar o acesso ao cluster](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Para mover dados para uma loja de dados Azure, consulte o [endereço IP compute e as gamas SQL que são utilizadas pelos Datacenters microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia para mover dados de uma fonte redshift da Amazon utilizando diferentes ferramentas e APIs.

A forma mais fácil de criar um oleoduto é utilizar o Assistente de Cópia da Fábrica de Dados Azure. Para uma rápida passagem pela criação de um oleoduto utilizando o Copy Wizard, consulte o [Tutorial: Crie um oleoduto utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md).

Também pode criar um oleoduto utilizando o Visual Studio, Azure PowerShell ou outras ferramentas. Os modelos Azure Resource Manager, a API .NET ou a API REST também podem ser utilizados para criar o pipeline. Para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia, consulte o tutorial de [Atividade de Cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie serviços ligados para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie conjuntos de dados para representar dados de entrada e saída para a operação de cópia.
3. Crie um pipeline com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o Copy Wizard, as definições JSON para estas entidades da Data Factory são criadas automaticamente. Quando utiliza ferramentas ou APIs (exceto a API .NET), define as entidades da Data Factory utilizando o formato JSON. O exemplo JSON: Copiar dados da Amazon Redshift para o armazenamento Azure Blob mostra as definições JSON para as entidades da Data Factory que são usadas para copiar dados de uma loja de dados Dama Redshift.

As secções seguintes descrevem as propriedades JSON que são usadas para definir as entidades da Data Factory para a Amazon Redshift.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

A tabela seguinte fornece descrições para os elementos JSON que são específicos de um serviço ligado à Amazon Redshift.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| **tipo** |Esta propriedade deve ser definida para **AmazonRedshift.** |Sim |
| **servidor** |O endereço IP ou o nome de anfitrião do servidor Amazon Redshift. |Sim |
| **porto** |O número da porta TCP que o servidor Amazon Redshift utiliza para ouvir as ligações dos clientes. |Não (o padrão é 5439) |
| **base de dados** |O nome da base de dados Amazon Redshift. |Sim |
| **nome de utilizador** |O nome do utilizador que tem acesso à base de dados. |Sim |
| **palavra-passe** |A palavra-passe para a conta de utilizador. |Sim |

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) A **estrutura,** **disponibilidade** e secções **de política** são semelhantes para todos os tipos de conjuntos de dados. Exemplos de tipos de conjuntos de dados incluem Azure SQL, armazenamento Azure Blob e armazenamento de mesa Azure.

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja. A secção **typeProperties** para um conjunto de dados do tipo **RelationalTable,** que inclui o conjunto de dados Redshift da Amazon, tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| **tableName** |O nome da tabela na base de dados Amazon Redshift a que o serviço ligado se refere. |Não (se for especificada a propriedade de **consulta** de uma atividade de cópia do tipo **RelationalSource)** |

## <a name="copy-activity-properties"></a>Propriedades de Atividade de Cópia

Para obter uma lista de secções e propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) O **nome,** **descrição,** tabela **de entradas,** tabela **de saídas** e propriedades **políticas** estão disponíveis para todos os tipos de atividades. As propriedades disponíveis na secção **de tipoProperties** variam para cada tipo de atividade. Para a Atividade de Cópia, as propriedades variam consoante os tipos de fontes de dados e pias.

Para a Atividade de Cópia, quando a fonte é do tipo **AmazonRedshiftSource,** as seguintes propriedades estão disponíveis na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| **consulta** | Utilize a consulta personalizada para ler os dados. |Não (se for especificada a propriedade do nome de **tabela** de um conjunto de dados) |
| **redshiftUnloadSettings** | Contém o grupo de propriedade ao utilizar o comando Redshift **UNLOAD.** | Não |
| **s3LinkedServiceName** | O Amazon S3 para usar como loja provisória. O serviço ligado é especificado utilizando um nome Azure Data Factory do tipo **AwsAccessKey**. | Necessário ao utilizar a propriedade **redshiftUnloadSettings** |
| **baldeName** | Indica o balde Amazon S3 para utilizar para armazenar os dados provisórios. Se esta propriedade não for fornecida, copy Activity gera automaticamente um balde. | Necessário ao utilizar a propriedade **redshiftUnloadSettings** |

Em alternativa, pode utilizar o tipo **RelationalSource,** que inclui o Amazon Redshift, com a seguinte propriedade na secção **typeProperties.** Note que este tipo de fonte não suporta o comando Redshift **UNLOAD.**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| **consulta** |Utilize a consulta personalizada para ler os dados. | Não (se for especificada a propriedade do nome de **tabela** de um conjunto de dados) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Use o UNLOAD para copiar dados da Amazon Redshift

O comando Amazon Redshift [**UNLOAD**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) descarrega os resultados de uma consulta a um ou mais ficheiros sobre o Amazon S3. Este comando é recomendado pela Amazon para copiar grandes conjuntos de dados da Redshift.

**Exemplo: Copiar dados da Amazon Redshift para a Azure Synapse Analytics**

Este exemplo copia dados da Amazon Redshift para a Azure Synapse Analytics. O exemplo utiliza o comando Redshift **UNLOAD,** dados de cópias encenados e Microsoft PolyBase.

Para este caso de utilização de amostras, copy Activity primeiro descarrega os dados da Amazon Redshift para o Amazon S3 como configurado na opção **RedshiftUnloadSettings.** Em seguida, os dados são copiados do armazenamento Amazon S3 para Azure Blob, conforme especificado na opção **de stagingSettings.** Finalmente, a PolyBase carrega os dados no Azure Synapse Analytics. Todos os formatos provisórios são tratados pela Copy Activity.

![Copy workflow da Amazon Redshift para Azure Synapse Analytics](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Exemplo JSON: Copiar dados da Amazon Redshift para o armazenamento Azure Blob
Esta amostra mostra como copiar dados de uma base de dados Da Amazon Redshift para O Armazenamento Azure Blob. Os dados podem ser copiados diretamente para qualquer [pia suportada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a Copy Activity.

A amostra tem as seguintes entidades de fábrica de dados:

* Um serviço ligado do tipo [AmazonRedshift](#linked-service-properties)
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [RelacionalTable](#dataset-properties)
* Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Um [oleoduto](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza as propriedades [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

A amostra copia dados de uma consulta resulta em Amazon Redshift para uma bolha Azure por hora. As propriedades JSON que são utilizadas na amostra são descritas nas secções que seguem as definições da entidade.

**Serviço ligado à Amazon Redshift**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Serviço ligado ao armazenamento Azure Blob**

```json
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
**Conjunto de dados de entrada redshift da Amazon**

A propriedade **externa** está definida como "verdadeira" para informar o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados. Esta definição de propriedade indica que o conjunto de dados não é produzido por uma atividade na fábrica de dados. Defina a propriedade para ser verdadeira num conjunto de dados de entrada que não é produzido por uma atividade no pipeline.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Conjunto de dados dos Blobs do Azure**

Os dados são escritos para uma nova bolha a cada hora, definindo a propriedade **de frequência** para "Hora" e a propriedade de **intervalo** para 1. A **propriedade pastaPath** para o blob é avaliada dinamicamente. O valor da propriedade baseia-se na hora de início da fatia que está a ser processada. O percurso da pasta utiliza o ano, mês, dia e horas partes da hora de início.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Copiar a atividade num oleoduto com uma fonte Azure Redshift (do tipo RelationalSource) e uma pia Azure Blob**

O pipeline contém uma atividade de cópia configurada para utilizar os conjuntos de dados de entrada e saída. O oleoduto está programado para funcionar a cada hora. Na definição JSON para o pipeline, o tipo **de fonte** é definido para **RelationalSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **de consulta** seleciona os dados a copiar da hora passada.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
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
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Tipo de mapeamento para Amazon Redshift
Como mencionado no artigo de [atividades](data-factory-data-movement-activities.md) de movimento de dados, copy Activity realiza conversões automáticas de tipo fonte para tipo de pia. Os tipos são convertidos através de uma abordagem em duas etapas:

1. Converter de um tipo de origem nativa para um tipo .NET
2. Converter de um tipo .NET para um tipo de pia nativa

Os seguintes mapeamentos são utilizados quando a Copy Activity converte os dados de um tipo Amazon Redshift para um tipo .NET:

| Tipo Amazon Redshift | .NET tipo |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |Decimal |
| REAL |Único |
| DUPLA PRECISÃO |Double (Duplo) |
| BOOLEANA |String |
| CHAR |String |
| RIO VARCHAR |String |
| DATE |DateTime |
| TIMETAMP |DateTime |
| TEXT |String |

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas
Para aprender a mapear colunas no conjunto de dados de origem para colunas no conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Leituras repetíveis de fontes relacionais
Ao copiar dados de uma loja de dados relacional, tenha em mente a repetibilidade para evitar resultados não intencionais. Na Azure Data Factory, pode repetir manualmente uma fatia. Também pode configurar a **política** de reagem para um conjunto de dados para refazer uma fatia quando ocorre uma falha. Certifique-se de que os mesmos dados são lidos, não importa quantas vezes a fatia seja reexecutada. Certifique-se também de que os mesmos dados são lidos independentemente da forma como reexecuta a fatia. Para obter mais informações, consulte [leituras repetíveis de fontes relacionais.](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Desempenho e otimização
Conheça os factores-chave que afetam o desempenho da Copy Activity e formas de otimizar o desempenho no [Copy Activity Performance and Afinando Guide](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Passos seguintes
Para obter instruções passo a passo para a criação de um oleoduto com a Atividade de Cópia, consulte o [tutorial de Atividade de Cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
