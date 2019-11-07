---
title: Mover dados do Amazon redshift usando Azure Data Factory
description: Saiba como mover dados do Amazon redshift usando Azure Data Factory atividade de cópia.
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
ms.openlocfilehash: 707061f523e5e991c851abfe7960a9aa66fb2066
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683262"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Mover dados do Amazon redshift usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-amazon-redshift-connector.md)
> * [Versão 2 (versão atual)](../connector-amazon-redshift.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector do Amazon redshift na v2](../connector-amazon-redshift.md).

Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados do Amazon redshift. O artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Atualmente, Data Factory dá suporte apenas à movimentação de dados do Amazon redshift para um [armazenamento de dados de coletor com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Não há suporte para a movimentação de dados de outros repositórios de dados para o Amazon redshift.

> [!TIP]
> Para obter o melhor desempenho ao copiar grandes quantidades de dados do Amazon redshift, considere o uso do comando redshift **Unload** interno por meio do Amazon S3 (serviço de armazenamento simples). Para obter detalhes, consulte [usar Unload para copiar dados do Amazon redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Pré-requisitos
* Se você estiver movendo dados para um armazenamento de dados local, instale [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) em um computador local. Conceda acesso a um gateway para o cluster do Amazon redshift usando o endereço IP do computador local. Para obter instruções, consulte [autorizar o acesso ao cluster](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Para mover dados para um armazenamento de dados do Azure, consulte o [endereço IP de computação e os intervalos SQL que são usados pelo Microsoft Azure data centers](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia para mover dados de uma fonte do Amazon redshift usando diferentes ferramentas e APIs.

A maneira mais fácil de criar um pipeline é usar o assistente de Azure Data Factory Copy. Para obter instruções rápidas sobre como criar um pipeline usando o assistente de cópia, consulte o [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md).

Você também pode criar um pipeline usando o Visual Studio, Azure PowerShell ou outras ferramentas. Azure Resource Manager modelos, a API do .NET ou a API REST também podem ser usados para criar o pipeline. Para obter instruções detalhadas sobre como criar um pipeline com uma atividade de cópia, consulte o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie serviços vinculados para vincular armazenamentos de dados de entrada e saída ao seu data factory.
2. Crie conjuntos de dados para representar o dado de entrada e saída para a operação de cópia.
3. Crie um pipeline com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída.

Quando você usa o assistente de cópia, as definições de JSON para essas Data Factory entidades são criadas automaticamente. Ao usar ferramentas ou APIs (exceto a API do .NET), você define as entidades de Data Factory usando o formato JSON. O exemplo de JSON: copiar dados do Amazon redshift para o armazenamento de BLOBs do Azure mostra as definições de JSON para as entidades de Data Factory que são usadas para copiar dados de um armazenamento de dados do Amazon redshift.

As seções a seguir descrevem as propriedades JSON que são usadas para definir as entidades de Data Factory para o Amazon redshift.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

A tabela a seguir fornece descrições para os elementos JSON que são específicos de um serviço vinculado do Amazon redshift.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tipo** |Essa propriedade deve ser definida como **AmazonRedshift**. |Sim |
| **servidor** |O endereço IP ou nome do host do servidor Amazon redshift. |Sim |
| **Porto** |O número da porta TCP que o servidor Amazon redshift usa para escutar conexões de cliente. |Não (o padrão é 5439) |
| **banco** |O nome do banco de dados do Amazon redshift. |Sim |
| **usu** |O nome do usuário que tem acesso ao banco de dados. |Sim |
| **la** |A senha da conta de usuário. |Sim |

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista das seções e propriedades que estão disponíveis para definir conjuntos de os, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções **estrutura**, **disponibilidade**e **política** são semelhantes para todos os tipos de conjunto de dados. Exemplos de tipos de conjuntos de conjunto de um SQL Azure, armazenamento de BLOBs do Azure e armazenamento de tabelas do Azure.

A seção **typeproperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório. **A seção typeproperties** de um conjunto de um DataSet do tipo **RelationalTable**, que inclui o conjunto de conRedshift do Amazon, tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tableName** |O nome da tabela no banco de dados do Amazon redshift ao qual o serviço vinculado se refere. |Não (se a propriedade de **consulta** de uma atividade de cópia do tipo **RelationalSource** for especificada) |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista de seções e propriedades que estão disponíveis para definir atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . O **nome**, a **Descrição**, a tabela de **entradas** , a tabela de **saídas** e as propriedades de **política** estão disponíveis para todos os tipos de atividades. As propriedades que estão disponíveis na seção **typeproperties** variam para cada tipo de atividade. Para a atividade de cópia, as propriedades variam de acordo com os tipos de fontes de dados e coletores.

Para a atividade de cópia, quando a fonte é do tipo **AmazonRedshiftSource**, as seguintes propriedades estão disponíveis na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **consultá** | Use a consulta personalizada para ler os dados. |Não (se a propriedade **TableName** de um conjunto de um DataSet for especificada) |
| **redshiftUnloadSettings** | Contém o grupo de propriedades ao usar o comando **Unload** redshift. | Não |
| **s3LinkedServiceName** | O Amazon S3 para usar como um armazenamento provisório. O serviço vinculado é especificado usando um nome de Azure Data Factory do tipo **AwsAccessKey**. | Necessário ao usar a propriedade **redshiftUnloadSettings** |
| **bucketName** | Indica o Bucket do Amazon S3 a ser usado para armazenar os dados provisórios. Se essa propriedade não for fornecida, a atividade de cópia gerará automaticamente um Bucket. | Necessário ao usar a propriedade **redshiftUnloadSettings** |

Como alternativa, você pode usar o tipo **RelationalSource** , que inclui o Amazon redshift, com a seguinte propriedade na seção **typeproperties** . Observe que esse tipo de fonte não dá suporte ao comando **Unload** redshift.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **consultá** |Use a consulta personalizada para ler os dados. | Não (se a propriedade **TableName** de um conjunto de um DataSet for especificada) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Usar UNLOAD para copiar dados do Amazon redshift

O comando [**Unload**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) do Amazon redshift descarrega os resultados de uma consulta em um ou mais arquivos no Amazon S3. Esse comando é recomendado pela Amazon para copiar grandes conjuntos de altos do redshift.

**Exemplo: copiar dados do Amazon redshift para o Azure SQL Data Warehouse**

Este exemplo copia dados do Amazon redshift para o Azure SQL Data Warehouse. O exemplo usa o comando **Unload** redshift, os dados de cópia em etapas e o Microsoft polybase.

Para este caso de uso de exemplo, a atividade de cópia primeiro descarrega os dados do Amazon redshift para o Amazon S3, conforme configurado na opção **redshiftUnloadSettings** . Em seguida, os dados são copiados do Amazon S3 para o armazenamento de BLOBs do Azure, conforme especificado na opção **stagingSettings** . Por fim, o polybase carrega os dados em SQL Data Warehouse. Todos os formatos provisórios são manipulados pela atividade de cópia.

![Copiar fluxo de trabalho do Amazon redshift para SQL Data Warehouse](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Exemplo de JSON: copiar dados do Amazon redshift para o armazenamento de BLOBs do Azure
Este exemplo mostra como copiar dados de um redshift do Amazon no armazenamento de BLOBs do Azure. Os dados podem ser copiados diretamente para qualquer [coletor com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia.

O exemplo tem as seguintes entidades de data factory:

* Um serviço vinculado do tipo [AmazonRedshift](#linked-service-properties)
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [RelationalTable](#dataset-properties)
* Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que usa as propriedades [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties)

O exemplo copia dados de um resultado de consulta no Amazon redshift para um blob do Azure a cada hora. As propriedades JSON que são usadas no exemplo são descritas nas seções que seguem as definições de entidade.

**Serviço vinculado do Amazon redshift**

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

**Serviço vinculado do armazenamento de BLOBs do Azure**

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
**Conjunto de dados de entrada do Amazon redshift**

A propriedade **external** é definida como "true" para informar ao serviço de data Factory que o conjunto de informação é externo ao data Factory. Essa configuração de propriedade indica que o conjunto de os não é produzido por uma atividade no data factory. Defina a propriedade como true em um conjunto de dados de entrada que não seja produzido por uma atividade no pipeline.

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

Os dados são gravados em um novo BLOB a cada hora, definindo a propriedade **Frequency** como "Hour" e a propriedade **Interval** como 1. A propriedade **FolderPath** do blob é avaliada dinamicamente. O valor da propriedade é baseado na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

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

**Atividade de cópia em um pipeline com uma fonte de redshift do Azure (do tipo RelationalSource) e um coletor de blob do Azure**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída. O pipeline está agendado para ser executado a cada hora. Na definição de JSON para o pipeline, o tipo de **origem** é definido como **RelationalSource** e o tipo de **coletor** é definido como **BlobSink**. A consulta SQL especificada para a propriedade **Query** seleciona os dados a serem copiados da última hora.

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
### <a name="type-mapping-for-amazon-redshift"></a>Mapeamento de tipo para Amazon redshift
Conforme mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões automáticas do tipo de origem para o tipo de coletor. Os tipos são convertidos usando uma abordagem de duas etapas:

1. Converter de um tipo de fonte nativa em um tipo .NET
2. Converter de um tipo .NET em um tipo de coletor nativo

Os seguintes mapeamentos são usados quando a atividade de cópia converte os dados de um tipo de redshift do Amazon em um tipo .NET:

| Tipo de redshift do Amazon | Tipo .NET |
| --- | --- |
| SMALLINT |Int16 |
| VALORES |Int32 |
| BIGINT |Int64 |
| VÍRGULA |Vírgula |
| REAL |Único |
| PRECISÃO DUPLA |Clique |
| BOOLEAN |String |
| º |String |
| VARCHAR |String |
| DATA |DateTime |
| ESTAMPA |DateTime |
| TEXTO |String |

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas do coletor
Para saber como mapear colunas no conjunto de informações de origem para colunas no conjunto de informações do coletor, consulte [mapeando colunas do conjunto de informações no Azure data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Leituras repetidas de fontes relacionais
Ao copiar dados de um repositório de dados relacional, mantenha a capacidade de repetição em mente para evitar resultados indesejados. No Azure Data Factory, você pode executar novamente uma fatia manualmente. Você também pode configurar a **política** de repetição de um conjunto de um para executar novamente uma fatia quando ocorrer uma falha. Certifique-se de que os mesmos dados sejam lidos, independentemente de quantas vezes a fatia é executada novamente. Além disso, certifique-se de que os mesmos dados sejam lidos independentemente de como você executa novamente a fatia. Para obter mais informações, consulte [leituras repetidas de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e otimização
Saiba mais sobre os principais fatores que afetam o desempenho da atividade de cópia e maneiras de otimizar o desempenho no [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Passos seguintes
Para obter as instruções passo a passo para criar um pipeline com a atividade de cópia, consulte o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
