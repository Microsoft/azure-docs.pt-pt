---
title: Mover dados do Amazon Simple Storage Service usando Data Factory
description: Saiba mais sobre como mover dados do Amazon S3 (Simple Storage Service) usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 970e8d2b960c3a4be1c5208d7fa3a21bc05d9e9a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683208"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Mover dados do Amazon Simple Storage Service usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Versão 2 (versão atual)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector do Amazon S3 na v2](../connector-amazon-simple-storage-service.md).

Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados do Amazon S3 (Simple Storage Service). Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados do Amazon S3 para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Atualmente, Data Factory dá suporte apenas à movimentação de dados do Amazon S3 para outros armazenamentos de dados, mas não à movimentação de dados de outros armazenamentos de dados para o Amazon S3.

## <a name="required-permissions"></a>Permissões obrigatórias
Para copiar dados do Amazon S3, verifique se você recebeu as seguintes permissões:

* `s3:GetObject` e `s3:GetObjectVersion` para operações de objeto do Amazon S3.
* `s3:ListBucket` para operações de buckets do Amazon S3. Se você estiver usando o assistente de cópia Data Factory, o `s3:ListAllMyBuckets` também será necessário.

Para obter detalhes sobre a lista completa de permissões do Amazon S3, consulte [especificando permissões em uma política](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados de uma fonte do Amazon S3 usando diferentes ferramentas ou APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Para obter uma explicação rápida, consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md).

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Para obter instruções detalhadas sobre como criar um pipeline com uma atividade de cópia, consulte o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Se você usar ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas ou APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON. Para obter um exemplo com definições de JSON para Data Factory entidades usadas para copiar dados de um armazenamento de dados do Amazon S3, confira a seção [exemplo de JSON: copiar dados do Amazon S3 para o blob do Azure](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) deste artigo.

> [!NOTE]
> Para obter detalhes sobre os formatos de arquivo e compactação com suporte para uma atividade de cópia, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md).

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas ao Amazon S3.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
Um serviço vinculado vincula um armazenamento de dados a um data factory. Você cria um serviço vinculado do tipo **AwsAccessKey** para vincular seu armazenamento de dados do Amazon S3 ao seu data Factory. A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do Amazon S3 (AwsAccessKey).

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| accessKeyid |ID da chave de acesso secreta. |string |Sim |
| secretAccessKey |A própria chave de acesso de segredo. |Cadeia de caracteres secreta criptografada |Sim |

>[!NOTE]
>Este conector requer chaves de acesso para a conta IAM copiar dados do Amazon S3. Não há suporte para [credenciais de segurança temporárias](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) .
>

Segue-se um exemplo:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades de DataSet
Para especificar um DataSet para representar os dados de entrada no armazenamento de BLOBs do Azure, defina a propriedade Type do DataSet como **AmazonS3**. Defina a propriedade **linkedServiceName** do conjunto de um como o nome do serviço vinculado do Amazon S3. Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte [criando conjuntos](data-factory-create-datasets.md)de os. 

As seções, como estrutura, disponibilidade e política são semelhantes para todos os tipos de conjunto de dados (como o SQL Database, o blob do Azure e a tabela do Azure). A seção **typeproperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção **typeproperties** para um DataSet do tipo **AmazonS3** (que inclui o conjunto de um Amazon S3) tem as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| bucketName |O nome do Bucket S3. |String |Sim |
| key |A chave do objeto S3. |String |Não |
| prefixo |Prefixo da chave do objeto S3. Os objetos cujas chaves começam com esse prefixo são selecionados. Aplica-se somente quando a chave está vazia. |String |Não |
| versão |A versão do objeto S3, se o controle de versão S3 estiver habilitado. |String |Não |
| ao | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **Type** em Format para um desses valores. Para obter mais informações, consulte as seções [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), formato [Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [formato parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção formato nas definições do conjunto de dados de entrada e saída. | |Não |
| çã | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **gzip**, **deflate**, **bzip2**e **ZipDeflate**. Os níveis com suporte são: **ideal** e **mais rápido**. Para obter mais informações, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). | |Não |


> [!NOTE]
> **bucketname + Key** especifica o local do objeto S3, em que Bucket é o contêiner raiz para objetos S3, e Key é o caminho completo para o objeto S3.

### <a name="sample-dataset-with-prefix"></a>Conjunto de exemplo com prefixo

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>Conjunto de exemplo (com versão)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>Caminhos dinâmicos para S3
O exemplo anterior usa valores fixos para as propriedades **Key** e **bucketname** no conjunto de teste do Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Você pode fazer Data Factory calcular essas propriedades dinamicamente no tempo de execução, usando variáveis de sistema como SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Você pode fazer o mesmo para a propriedade **prefix** de um conjunto de um Amazon S3. Para obter uma lista de funções e variáveis com suporte, consulte [funções de data Factory e variáveis de sistema](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte [criando pipelines](data-factory-create-pipelines.md). Propriedades como nome, descrição, tabelas de entrada e saída e políticas estão disponíveis para todos os tipos de atividades. As propriedades disponíveis na seção **typeproperties** da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, as propriedades variam de acordo com os tipos de fontes e coletores. Quando uma origem na atividade de cópia é do tipo **FileSystemName** (que inclui o Amazon S3), a seguinte propriedade está disponível na seção **typeproperties** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Especifica se os objetos S3 devem ser recursivamente listados no diretório. |verdadeiro/falso |Não |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Exemplo de JSON: copiar dados do Amazon S3 para o armazenamento de BLOBs do Azure
Este exemplo mostra como copiar dados do Amazon S3 para um armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados diretamente para [qualquer um dos coletores com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no data Factory.

O exemplo fornece definições de JSON para as entidades de Data Factory a seguir. Você pode usar essas definições para criar um pipeline para copiar dados do Amazon S3 para o armazenamento de BLOBs usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* Um serviço vinculado do tipo [AwsAccessKey](#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [AmazonS3](#dataset-properties).
* Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa o [FileSystemName](#copy-activity-properties) e o [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados do Amazon S3 para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

### <a name="amazon-s3-linked-service"></a>Serviço vinculado do Amazon S3

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure

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

### <a name="amazon-s3-input-dataset"></a>Conjunto de dados de entrada do Amazon S3

A configuração **"external": true** informa ao serviço de data Factory que o conjunto de os é externo ao data Factory. Defina essa propriedade como true em um conjunto de dados de entrada que não seja produzido por uma atividade no pipeline.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída do Blob do Azure

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Atividade de cópia em um pipeline com uma origem do Amazon S3 e um coletor de BLOB

O pipeline contém uma atividade de cópia que é configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **FileSystemName**e o tipo de **coletor** é definido como **BlobSink**.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
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
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Para mapear colunas de um conjunto de código de origem para colunas de um conjunto de um, consulte [mapeando colunas de DataSet em Azure data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

* Para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Data Factory e várias maneiras de otimizá-lo, consulte o [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md).

* Para obter as instruções passo a passo para criar um pipeline com uma atividade de cópia, consulte o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
