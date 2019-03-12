---
title: Mover dados do Amazon Simple Storage Service com o Data Factory | Documentos da Microsoft
description: Saiba mais sobre como mover dados do Amazon Simple Storage Service (S3), utilizando o Azure Data Factory.
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
ms.openlocfilehash: 1f5064cece32cfc38f149816961e5156ff20974a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57536713"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Mover dados do Amazon Simple Storage Service através do Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Versão 2 (versão atual)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector Amazon S3 no V2](../connector-amazon-simple-storage-service.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados do Amazon Simple Storage Service (S3). Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados do Amazon S3 para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados suportados como sinks a atividade de cópia, consulte a [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Atualmente, o Data Factory suporta apenas mover dados do Amazon S3 para outros arquivos de dados, mas não mover dados de outros dados armazena Amazon S3.

## <a name="required-permissions"></a>Permissões obrigatórias
Para copiar dados do Amazon S3, certificar-se de que lhe foram concedidas as permissões seguintes:

* `s3:GetObject` e `s3:GetObjectVersion` para operações de objeto do Amazon S3.
* `s3:ListBucket` para operações de Bucket do Amazon S3. Se estiver a utilizar o Assistente de cópia do Data Factory, `s3:ListAllMyBuckets` também é necessário.

Para obter detalhes sobre a lista completa de permissões do Amazon S3, consulte [especificar permissões numa política](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de uma origem do Amazon S3 através de APIs ou ferramentas diferentes.

A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Para um rápido passo a passo, consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md).

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Para obter instruções passo a passo Criar um pipeline com uma atividade de cópia, consulte a [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Se usar APIs ou ferramentas, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia.
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas ou APIs (exceto a .NET API), define essas entidades do Data Factory, utilizando o formato JSON. Para obter um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um arquivo de dados do Amazon S3, veja o [exemplo de JSON: Copiar dados do Amazon S3 para BLOBs do Azure](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) seção deste artigo.

> [!NOTE]
> Para obter mais informações sobre formatos de ficheiro e de compressão suportados para uma atividade de cópia, veja [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

As secções seguintes fornecem detalhes sobre as propriedades JSON utilizadas para definir entidades do Data Factory específicas para o Amazon S3.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
Os serviços ligados ligam um arquivo de dados para uma fábrica de dados. Vai criar um serviço ligado do tipo **AwsAccessKey** para ligar o seu armazenamento de dados do Amazon S3 à fábrica de dados. A tabela seguinte fornece uma descrição para elementos JSON específicos para o Amazon S3 (AwsAccessKey) de serviço ligado.

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| accessKeyID |ID da chave de acesso a segredos. |string |Sim |
| secretAccessKey |A chave de acesso a segredos em si. |Cadeia secreta encriptada |Sim |

>[!NOTE]
>Este conector requer chaves de acesso para a conta IAM copiar dados do Amazon S3. [Credencial de segurança temporário](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) não é suportada.
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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para especificar um conjunto de dados para representar dados de entrada no armazenamento de Blobs do Azure, defina a propriedade de tipo de conjunto de dados para **AmazonS3**. Definir o **linkedServiceName** serviço ligado de propriedade de conjunto de dados para o nome do Amazon S3. Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md). 

Seções, como a estrutura, disponibilidade e a política são semelhantes para todos os tipos de conjunto de dados (por exemplo, a base de dados SQL, BLOBs do Azure e tabelas do Azure). O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para um conjunto de dados do tipo **AmazonS3** (que inclui o conjunto de dados do Amazon S3) tem as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| bucketName |O nome do registo de S3. |String |Sim |
| chave |A chave de objeto de S3. |String |Não |
| prefixo |Prefixo para a chave de objeto de S3. Objetos cujas chaves iniciados com este prefixo estão selecionados. Aplica-se apenas quando o chave está vazia. |String |Não |
| versão |A versão do objeto, S3, se o controlo de versões de S3 está ativado. |String |Não |
| Formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte a [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet ](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender copiar ficheiros como-é entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não | |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Os tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Os níveis suportados são: **Ideal** e **mais rápida**. Para obter mais informações, consulte [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não | |


> [!NOTE]
> **bucketName + tecla** Especifica a localização do objeto S3, em que o bucket é o contêiner raiz para objetos de S3, e a chave é o caminho completo para o objeto de S3.

### <a name="sample-dataset-with-prefix"></a>Conjunto de dados de exemplo com o prefixo

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
### <a name="sample-dataset-with-version"></a>Conjunto de dados de exemplo (com a versão)

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

### <a name="dynamic-paths-for-s3"></a>Caminhos de dinâmicos para S3
O exemplo anterior utiliza valores fixos para o **chave** e **bucketName** propriedades do conjunto de dados do Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Pode ter o Data Factory calcular essas propriedades dinamicamente no tempo de execução, utilizando variáveis de sistema, como do SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Pode fazer o mesmo o **prefixo** propriedade de um conjunto de dados do Amazon S3. Para obter uma lista de funções e variáveis, consulte [funções de Data Factory e variáveis de sistema](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte [Criar pipelines](data-factory-create-pipelines.md). Propriedades, tais como o nome, descrição, entrada e saída de tabelas e as políticas estão disponíveis para todos os tipos de atividades. Propriedades disponíveis no **typeProperties** secção da atividade varia com cada tipo de atividade. Para a atividade de cópia, propriedades variam consoante os tipos de origens e sinks. Quando uma origem na atividade de cópia é do tipo **FileSystemSource** (que inclui o Amazon S3), a seguinte propriedade está disponível no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Especifica se recursivamente lista S3 objetos sob o diretório. |Verdadeiro/Falso |Não |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Exemplo JSON: Copiar dados do Amazon S3 para o armazenamento de Blobs do Azure
Este exemplo mostra como copiar dados do Amazon S3 para um armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados diretamente à [qualquer um dos sinks de que são suportadas](data-factory-data-movement-activities.md#supported-data-stores-and-formats) através da atividade de cópia na fábrica de dados.

O exemplo fornece definições de JSON para as seguintes entidades do Data Factory. Pode utilizar estas definições para criar um pipeline para copiar dados do Amazon S3 para armazenamento de BLOBs, utilizando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* Um serviço ligado do tipo [AwsAccessKey](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [AmazonS3](#dataset-properties).
* Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados do Amazon S3 para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

### <a name="amazon-s3-linked-service"></a>Serviço ligado do Amazon S3

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

### <a name="amazon-s3-input-dataset"></a>Conjunto de dados de entrada Amazon S3

A definição **"externo": true** informa o serviço Data Factory, que o conjunto de dados é externo à fábrica de dados. Defina esta propriedade como true num conjunto de dados de entrada que não é produzido por uma atividade no pipeline.

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

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês, dia e partes de horas da hora de início.

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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Atividade de cópia num pipeline com uma origem do Amazon S3 e um sink de blob

O pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **FileSystemSource**, e **sink** tipo está definido como **BlobSink**.

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
> Para mapear as colunas a partir de um conjunto de dados de origem para colunas a partir de um conjunto de dados de sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

* Para saber mais sobre os principais fatores que afetará o desempenho de movimento de dados (atividade de cópia) no Data Factory e várias formas para otimizá-lo, consulte a [copie o guia de sintonização de desempenho de atividade e](data-factory-copy-activity-performance.md).

* Para obter instruções passo a passo para criar um pipeline com uma atividade de cópia, consulte a [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
