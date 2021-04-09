---
title: Faça upload de grandes dados definidos para Azure Data Lake Storage Gen1 - métodos offline
description: Utilize o serviço de importação/exportação para copiar dados do armazenamento da Azure Blob para a Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 940b7ac90f85e0254d59459b70ccc15312cd69f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98700844"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>Utilize o serviço Azure Import/Export para cópia offline de dados para data lake storage gen1

Neste artigo, você aprenderá a copiar enormes conjuntos de dados (>200 GB) na Data Lake Storage Gen1 utilizando métodos de cópia offline, como o [serviço Azure Import/Export](../import-export/storage-import-export-service.md). Especificamente, o ficheiro usado como exemplo neste artigo é de 339.420.860.416 bytes, ou cerca de 319 GB no disco. Vamos chamar este ficheiro 319GB.tsv.

O serviço Azure Import/Export ajuda-o a transferir grandes quantidades de dados de forma mais segura para o armazenamento da Azure Blob, enviando discos rígidos para um datacenter Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve ter o seguinte:

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de armazenamento Azure.**
* **Uma conta Azure Data Lake Storage Gen1**. Para obter instruções sobre como criar um, consulte [Começar com a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="prepare-the-data"></a>Preparar os dados

Antes de utilizar o serviço De importação/exportação, quebre o ficheiro de dados para ser transferido **em cópias com menos de 200 GB** de tamanho. A ferramenta de importação não funciona com ficheiros superiores a 200 GB. Neste artigo, dividimos o ficheiro em pedaços de 100 GB cada. Pode fazê-lo usando [Cygwin.](https://cygwin.com/install.html) Cygwin apoia os comandos Linux. Neste caso, utilize o seguinte comando:

```console
split -b 100m 319GB.tsv
```

A operação dividida cria ficheiros com os seguintes nomes.

* *319GB.tsv-part-aa*
* *319GB.tsv-part-ab*
* *319GB.tsv-part-ac*
* *319GB.tsv-part-ad*

## <a name="get-disks-ready-with-data"></a>Prepare os discos com dados

Siga as instruções na [Utilização do serviço Azure Import/Export](../import-export/storage-import-export-service.md) (na secção Prepare os **seus discos)** para preparar os seus discos rígidos. Aqui está a sequência geral:

1. Procure um disco rígido que satisfaça a exigência de ser utilizado para o serviço de importação/exportação Azure.
2. Identifique uma conta de armazenamento Azure onde os dados serão copiados depois de enviados para o datacenter Azure.
3. Utilize a [Ferramenta de Importação/Exportação Azure,](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)uma utilidade de linha de comando. Aqui está uma amostra que mostra como usar a ferramenta.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Consulte [o serviço de importação/exportação Azure](../import-export/storage-import-export-service.md) para mais amostras de fragmentos.
4. O comando anterior cria um ficheiro de diário no local especificado. Utilize este ficheiro diário para criar uma tarefa de importação a partir do [portal Azure](https://portal.azure.com).

## <a name="create-an-import-job"></a>Criar um trabalho de importação

Pode agora criar um trabalho de importação utilizando as instruções de [Utilização do serviço Azure Import/Export](../import-export/storage-import-export-service.md) (na secção Criar a Secção **de Importação).** Para este trabalho de importação, com outros detalhes, também forneça o ficheiro de diário criado enquanto prepara as unidades de disco.

## <a name="physically-ship-the-disks"></a>Enviar fisicamente os discos

Agora pode enviar fisicamente os discos para um datacenter Azure. Lá, os dados são copiados para as bolhas de armazenamento Azure que forneceu enquanto cria o trabalho de importação. Além disso, ao criar o trabalho, se optou por fornecer as informações de rastreio mais tarde, pode agora voltar ao seu trabalho de importação e atualizar o número de rastreio.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Copiar dados de bolhas para Data Lake Storage Gen1

Após o estado do trabalho de importação mostrar que está concluído, você pode verificar se os dados estão disponíveis nas bolhas de armazenamento Azure que você especificou. Em seguida, pode utilizar uma variedade de métodos para mover esses dados das bolhas para a Azure Data Lake Storage Gen1. Para todas as opções disponíveis para o upload de dados, consulte [Ingerir dados na Data Lake Storage Gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

Nesta secção, fornecemos-lhe as definições JSON que pode utilizar para criar um pipeline Azure Data Factory para copiar dados. Pode utilizar estas definições JSON a partir do [portal Azure](../data-factory/tutorial-copy-data-portal.md) ou [do Visual Studio](../data-factory/tutorial-copy-data-dot-net.md).

### <a name="source-linked-service-azure-storage-blob"></a>Serviço ligado à fonte (bolha de armazenamento Azure)

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="target-linked-service-data-lake-storage-gen1"></a>Serviço ligado ao alvo (Data Lake Storage Gen1)

```JSON
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```

### <a name="input-data-set"></a>Conjunto de dados de entrada

```JSON
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

### <a name="output-data-set"></a>Conjunto de dados de saída

```JSON
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```

### <a name="pipeline-copy-activity"></a>Pipeline (atividade de cópia)

```JSON
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
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
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

Para obter mais informações, consulte [os dados da bolha de armazenamento Azure para a Azure Data Lake Storage Gen1 utilizando a Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>Reconstruir os ficheiros de dados na Data Lake Storage Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Começamos com um ficheiro que era de 319 GB, e o decompôs em ficheiros de menor dimensão para que pudesse ser transferido através do serviço Azure Import/Export. Agora que os dados estão no Azure Data Lake Storage Gen1, podemos reconstruir o ficheiro ao seu tamanho original. Pode utilizar os seguintes cmdlets Azure PowerShell para o fazer.

```PowerShell
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>Passos seguintes

* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Use Azure Data Lake Analytics com data lake storage gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight com data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
