---
title: Mover dados do Amazon Redshift através do Azure Data Factory | Documentos da Microsoft
description: Aprenda a mover dados do Amazon Redshift, utilizando a atividade de cópia de fábrica de dados do Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: dc72ec9bf2e7e7c5c77685368167357a0108f2d3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541932"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Mover dados do Amazon Redshift com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-amazon-redshift-connector.md)
> * [Versão 2 (versão atual)](../connector-amazon-redshift.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector Amazon Redshift no V2](../connector-amazon-redshift.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados do Amazon Redshift. O artigo baseia-se a [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Atualmente, o Data Factory suporta apenas mover dados do Amazon Redshift para um [arquivo de dados sink suportado](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Mover dados de outros arquivos de dados para o Amazon Redshift não é suportada.

> [!TIP]
> Para obter o melhor desempenho ao copiar grandes quantidades de dados do Amazon Redshift, considere a utilização o Redshift incorporado **UNLOAD** comando por meio do Amazon Simple Storage Service (Amazon S3). Para obter detalhes, consulte [descarregamento de utilização para copiar dados do Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Pré-requisitos
* Se estiver a mover dados para um arquivo de dados no local, instale [Data Management Gateway](data-factory-data-management-gateway.md) numa máquina no local. Conceder acesso de um gateway ao cluster do Amazon Redshift através do endereço IP de máquinas no local. Para obter instruções, consulte [autorizar o acesso ao cluster](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Para mover dados para um arquivo de dados do Azure, consulte a [intervalos SQL que são utilizados por Datacenters do Azure da Microsoft e de endereço IP de computação](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia para mover dados de uma origem do Amazon Redshift através de APIs e ferramentas diferentes.

A maneira mais fácil para criar um pipeline é usar o Assistente de cópia de fábrica de dados do Azure. Para uma passo a passo rápido sobre como criar um pipeline com o Assistente de cópia, consulte o [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md).

Também pode criar um pipeline com o portal do Azure, o Visual Studio, o Azure PowerShell ou outras ferramentas. Modelos Azure Resource Manager, a API .NET ou a API de REST também pode ser utilizada para criar o pipeline. Para obter instruções passo a passo Criar um pipeline com uma atividade de cópia, consulte a [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Crie serviços ligados para ligar a entrada e saída de arquivos de dados à fábrica de dados.
2. Crie conjuntos de dados para representar os dados de entrada e saídos da operação de cópia.
3. Crie um pipeline com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o Assistente para copiar, definições de JSON para estas entidades do Data Factory são criadas automaticamente. Ao utilizar ferramentas ou APIs (exceto a API .NET), define as entidades da fábrica de dados utilizando o formato JSON. O exemplo JSON: Copiar dados do Amazon Redshift para o armazenamento de Blobs do Azure mostram as definições de JSON para as entidades da fábrica de dados que são utilizadas para copiar dados de um arquivo de dados do Amazon Redshift.

As secções seguintes descrevem as propriedades JSON utilizadas para definir as entidades do Data Factory para o Amazon Redshift.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

A tabela seguinte fornece descrições para os elementos JSON que são específicas para um serviço ligado do Amazon Redshift.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tipo** |Esta propriedade tem de ser definida **AmazonRedshift**. |Sim |
| **server** |O nome ou endereço IP anfitrião do servidor do Amazon Redshift. |Sim |
| **port** |O número da porta TCP que o servidor do Amazon Redshift utiliza para escutar ligações de cliente. |Não (a predefinição é 5439) |
| **database** |O nome da base de dados do Amazon Redshift. |Sim |
| **username** |O nome do utilizador que tem acesso à base de dados. |Sim |
| **password** |A palavra-passe da conta de utilizador. |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista das seções e as propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. O **estrutura**, **disponibilidade**, e **política** secções são semelhantes para todos os tipos de conjunto de dados. Exemplos de tipos de conjunto de dados incluem o SQL do Azure, o armazenamento de Blobs do Azure e o armazenamento de tabelas do Azure.

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo. **O typeProperties** secção para um conjunto de dados do tipo **RelationalTable**, que inclui o conjunto de dados do Amazon Redshift, tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tableName** |O nome da tabela na base de dados do Amazon Redshift que o serviço ligado refere-se a. |Não (se o **consulta** propriedade de uma atividade de cópia do tipo **RelationalSource** for especificado) |

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Para obter uma lista de seções e as propriedades que estão disponíveis para a definição de atividades, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. O **name**, **Descrição**, **entradas** tabela, **produz** tabela, e **política** propriedades são disponível para todos os tipos de atividades. As propriedades que estão disponíveis no **typeProperties** secção variar para cada tipo de atividade. Para a atividade de cópia, as propriedades variam consoante os tipos de origens de dados e sinks.

Para a atividade de cópia, quando a origem é do tipo **AmazonRedshiftSource**, as seguintes propriedades estão disponíveis no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **query** | Utilize a consulta personalizada para ler os dados. |Não (se o **tableName** é especificada a propriedade de um conjunto de dados) |
| **redshiftUnloadSettings** | Contém o grupo de propriedade ao utilizar o Redshift **UNLOAD** comando. | Não |
| **s3LinkedServiceName** | O Amazon S3 para utilizar como um armazenamento provisório. O serviço ligado é especificado por meio de um nome da fábrica de dados do Azure do tipo **AwsAccessKey**. | É necessário quando utilizar o **redshiftUnloadSettings** propriedade |
| **bucketName** | Indica o bucket do Amazon S3 para utilizar para armazenar os dados intermediárias. Se esta propriedade não for fornecida, atividade de cópia gera automaticamente um registo. | É necessário quando utilizar o **redshiftUnloadSettings** propriedade |

Em alternativa, pode utilizar o **RelationalSource** tipo, que inclui o Amazon Redshift, com a seguinte propriedade no **typeProperties** secção. Observe que este tipo de origem não suporta o Redshift **UNLOAD** comando.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **query** |Utilize a consulta personalizada para ler os dados. | Não (se o **tableName** é especificada a propriedade de um conjunto de dados) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Utilizar o descarregamento para copiar dados do Amazon Redshift

O Amazon Redshift [ **UNLOAD** ](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) comando descarrega os resultados de uma consulta para um ou mais ficheiros no Amazon S3. Este comando é recomendado pelo Amazon copiar grandes conjuntos de dados entre Redshift.

**Exemplo: Copiar dados do Amazon Redshift para o Azure SQL Data Warehouse**

Neste exemplo copia dados do Amazon Redshift para o Azure SQL Data Warehouse. O exemplo utiliza o Redshift **UNLOAD** comando, dados de cópia faseada e PolyBase da Microsoft.

Para este caso de utilização de exemplo, atividade de cópia primeiro descarrega os dados do Amazon Redshift, Amazon S3 conforme configurado na **redshiftUnloadSettings** opção. Em seguida, os dados são copiados do Amazon S3 para o armazenamento de Blobs do Azure, conforme especificado na **stagingSettings** opção. Por fim, o PolyBase carrega os dados para o SQL Data Warehouse. Todos os formatos provisórias são processados pela atividade de cópia.

![Copie o fluxo de trabalho do Amazon Redshift para o SQL Data Warehouse](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Exemplo JSON: Copiar dados do Amazon Redshift para o armazenamento de Blobs do Azure
Este exemplo mostra como copiar dados de uma base de dados do Amazon Redshift para o armazenamento de Blobs do Azure. Dados podem ser copiados diretamente a qualquer [suportado sink](data-factory-data-movement-activities.md#supported-data-stores-and-formats) através da atividade de cópia.

O exemplo possui as seguintes entidades do data factory:

* Um serviço ligado do tipo [AmazonRedshift](#linked-service-properties)
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [RelationalTable](#dataset-properties)
* Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* R [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza o [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties) propriedades

O exemplo copia dados de um resultado da consulta no Amazon Redshift para um blob do Azure à hora. As propriedades JSON utilizadas no exemplo são descritas nas secções que se seguem as definições de entidade.

**Serviço ligado do Amazon Redshift**

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

**Serviço de ligado de armazenamento de Blobs do Azure**

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
**Conjunto de dados de entrada do Amazon Redshift**

O **externo** propriedade está definida como "true" para informar o serviço Data Factory, que o conjunto de dados é externo à fábrica de dados. Esta definição de propriedade indica que o conjunto de dados não é produzido por uma atividade na fábrica de dados. Defina a propriedade como true num conjunto de dados de entrada que não é produzido por uma atividade no pipeline.

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

Dados são escritos para um blob novo a cada hora, definindo a **frequência** propriedade como "Hour" e o **intervalo** propriedade como 1. O **folderPath** propriedade para o blob é avaliada dinamicamente. O valor da propriedade baseia-se a hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês, dia e partes de horas da hora de início.

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

**Atividade de cópia num pipeline com uma origem de Azure Redshift (do tipo RelationalSource) e um coletor de Blobs do Azure**

O pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e saídos. O pipeline está agendado para ser executado a cada hora. Na definição de JSON do pipeline, o **origem** tipo está definido como **RelationalSource** e o **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para o **consulta** propriedade seleciona os dados para copiar de última hora.

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
### <a name="type-mapping-for-amazon-redshift"></a>Mapeamento do tipo de Amazon Redshift
Conforme mencionado na [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, atividade de cópia executa conversões de tipo automática do tipo de origem para o tipo de sink. Os tipos são convertidos com uma abordagem de dois passos:

1. Converter a partir de um tipo de origem nativas para um tipo de .NET
2. Converter de um tipo .NET para um tipo de sink nativo

Os seguintes mapeamentos são utilizados quando a atividade de cópia converte os dados de um tipo de Amazon Redshift para um tipo .NET:

| Tipo do Amazon Redshift | Tipo de .NET |
| --- | --- |
| SMALLINT |Int16 |
| NÚMERO INTEIRO |Int32 |
| BIGINT |Int64 |
| DECIMAL |Decimal |
| REAL |Single |
| PRECISÃO DUPLA |Double |
| VALOR BOOLEANO |String |
| CHAR |String |
| VARCHAR |String |
| DATA |DateTime |
| TIMESTAMP |DateTime |
| TEXTO |String |

## <a name="map-source-to-sink-columns"></a>Origem do mapa para colunas de sink
Para saber como mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de sink, veja [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Leituras repetíveis de fontes relacionais
Quando copia dados a partir de um arquivo de dados relacionais, tenha a capacidade de repetição em mente para evitar resultados indesejados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a repetição **política** para um conjunto de dados voltar a executar um setor quando ocorre uma falha. Certifique-se de que os mesmos dados é de leitura, não importa quantas vezes o setor será novamente executado. Além disso, certifique-se de que os mesmos dados é de leitura, independentemente de como voltar a executar o setor. Para obter mais informações, consulte [Repeatable lê a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e otimização
Saiba mais sobre os principais fatores que afetam o desempenho de atividade de cópia e formas de otimizar o desempenho na [guia de ajuste e de desempenho de atividade de cópia](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Passos Seguintes
Para obter instruções passo a passo para criar um pipeline com atividade de cópia, consulte a [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
