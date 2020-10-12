---
title: Mover dados do Serviço de Armazenamento Simples da Amazon utilizando a Data Factory
description: Saiba como mover dados do Amazon Simple Storage Service (S3) utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b23be9901df7ca435f412d9f49e1a7ad88382ade
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711923"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Mover dados do Serviço de Armazenamento Simples da Amazon utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Versão 2 (versão atual)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector Amazon S3 em V2](../connector-amazon-simple-storage-service.md).

Este artigo explica como usar a atividade de cópia na Azure Data Factory para mover dados do Amazon Simple Storage Service (S3). Baseia-se no artigo de atividades de [movimento de dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

Pode copiar dados do Amazon S3 para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) A Data Factory suporta atualmente apenas a transferência de dados do Amazon S3 para outras lojas de dados, mas não a transferência de dados de outras lojas de dados para o Amazon S3.

## <a name="required-permissions"></a>Permissões obrigatórias
Para copiar dados do Amazon S3, certifique-se de que lhe foram concedidas as seguintes permissões:

* `s3:GetObject` e `s3:GetObjectVersion` para a Amazon S3 Object Operations.
* `s3:ListBucket` para a Amazon S3 Bucket Operations. Se estiver a utilizar o Assistente de Cópia da Fábrica de Dados, `s3:ListAllMyBuckets` também é necessário.

Para obter mais informações sobre a lista completa das permissões do Amazon S3, consulte [especificar permissões numa Política.](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de uma fonte Amazon S3 utilizando diferentes ferramentas ou APIs.

A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Para uma rápida passagem, consulte [Tutorial: Crie um oleoduto utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md).

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API**e **REST API**. Para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia, consulte o tutorial de atividade da [Cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Quer utilize ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas ou APIs (exceto.NET API), define estas entidades da Data Factory utilizando o formato JSON. Para obter uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados de uma loja de dados Dazon S3, consulte o [exemplo JSON: Copiar dados da amazon S3 para a secção Azure Blob](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) deste artigo.

> [!NOTE]
> Para obter detalhes sobre formatos de ficheiros e compressão suportados para uma atividade de cópia, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

As secções seguintes fornecem detalhes sobre propriedades JSON que são usadas para definir entidades da Data Factory específicas do Amazon S3.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
Um serviço ligado liga uma loja de dados a uma fábrica de dados. Cria um serviço ligado do tipo **AwsAccessKey** para ligar a sua loja de dados Amazon S3 à sua fábrica de dados. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao Amazon S3 (AwsAccessKey).

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| accessKeyID |Identificação da chave de acesso secreta. |string |Sim |
| SecretAccessKey |A chave de acesso secreto em si. |Cadeia secreta encriptada |Sim |

>[!NOTE]
>Este conector requer chaves de acesso para conta IAM para copiar dados do Amazon S3. [A Credencial de Segurança Temporária](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) não é apoiada.
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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para especificar um conjunto de dados para representar dados de entrada no armazenamento do Azure Blob, defina a propriedade tipo do conjunto de dados para o **AmazonS3**. Defina a propriedade **linkedServiceName** do conjunto de dados com o nome do serviço ligado ao Amazon S3. Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md). 

Secções como estrutura, disponibilidade e política são semelhantes para todos os tipos de conjuntos de dados (tais como base de dados SQL, blob Azure e tabela Azure). A secção **typeProperties** é diferente para cada tipo de conjunto de dados, e fornece informações sobre a localização dos dados na loja de dados. A secção **typeProperties** para um conjunto de dados do tipo **AmazonS3** (que inclui o conjunto de dados Amazon S3) tem as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| baldeName |O nome do balde S3. |Cadeia |Sim |
| key |A chave do objeto S3. |Cadeia |Não |
| prefixo |Prefixo para a tecla de objeto S3. São selecionados objetos cujas teclas começam com este prefixo. Só se aplica quando a chave estiver vazia. |Cadeia |Não |
| versão |A versão do objeto S3, se a versão S3 estiver ativada. |Cadeia |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte o [formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [formato JSON,](data-factory-supported-file-and-compression-formats.md#json-format) [formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e secções [de formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender copiar ficheiros como-está entre lojas baseadas em ficheiros (cópia binária), salte a secção de formato nas definições de conjunto de dados de entrada e saída. | |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2**e **ZipDeflate**. Os níveis suportados são: **Ideal** e **Mais rápido.** Para obter mais informações, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). | |Não |


> [!NOTE]
> **bucketName + tecla** especifica a localização do objeto S3, onde o balde é o recipiente raiz para objetos S3, e a chave é o caminho completo para o objeto S3.

### <a name="sample-dataset-with-prefix"></a>Conjunto de dados de amostra com prefixo

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
### <a name="sample-dataset-with-version"></a>Conjunto de dados da amostra (com versão)

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
A amostra anterior utiliza valores fixos para as propriedades **chave** e **baldename** no conjunto de dados Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Pode fazer com que a Data Factory calcule estas propriedades dinamicamente no tempo de execução, utilizando variáveis do sistema como o SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Pode fazer o mesmo pela propriedade **prefixada** de um conjunto de dados Amazon S3. Para obter uma lista de funções e variáveis suportadas, consulte [as funções da Data Factory e as variáveis do sistema.](data-factory-functions-variables.md)

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [criar oleodutos.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades. As propriedades disponíveis na secção **de tipoProperties** da atividade variam com cada tipo de atividade. Para a atividade da cópia, as propriedades variam consoante os tipos de fontes e pias. Quando uma fonte na atividade de cópia é do tipo **FileSystemSource** (que inclui o Amazon S3), a seguinte propriedade está disponível na secção **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Especifica se deve listar novamente os objetos S3 sob o diretório. |verdadeiro/falso |Não |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Exemplo JSON: Copiar dados do Amazon S3 para o armazenamento Azure Blob
Esta amostra mostra como copiar dados do Amazon S3 para um armazenamento Azure Blob. No entanto, os dados podem ser copiados diretamente para [qualquer um dos lavatórios que são suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) através da atividade de cópia na Data Factory.

A amostra fornece definições JSON para as seguintes entidades da Data Factory. Pode utilizar estas definições para criar um pipeline para copiar dados do Amazon S3 para o armazenamento Blob, utilizando o [Estúdio Visual](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [o PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md)   

* Um serviço ligado do tipo [AwsAccessKey](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AmazonS3.](#dataset-properties)
* Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink.](data-factory-azure-blob-connector.md#copy-activity-properties)

A amostra copia dados da Amazon S3 para uma bolha Azure a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

### <a name="amazon-s3-linked-service"></a>Serviço ligado amazon S3

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

Definição **"externa": informa verdadeiramente** o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados. Defina esta propriedade para ser verdadeira num conjunto de dados de entrada que não é produzido por uma atividade no pipeline.

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

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base na hora de início da fatia que está a ser processada. O percurso da pasta utiliza o ano, mês, dia e horas partes da hora de início.

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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Copiar atividade em um oleoduto com uma fonte Amazon S3 e um lavatório blob

O pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e saída, e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **FileSystemSource,** e o tipo **de pia** é definido para **BlobSink**.

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
> Para mapear colunas de um conjunto de dados de origem para colunas de um conjunto de dados de sumidouro, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

* Para conhecer os factores-chave que impactam o desempenho do movimento de dados (atividade de cópia) na Data Factory, e várias formas de otimizá-lo, consulte o guia de [desempenho e afinação](data-factory-copy-activity-performance.md)da atividade copy .

* Para obter instruções passo a passo para a criação de um oleoduto com uma atividade de cópia, consulte o tutorial de atividade da [Cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
