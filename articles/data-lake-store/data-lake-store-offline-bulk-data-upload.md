---
title: Carregar um conjunto de dados grandes em métodos Azure Data Lake Storage Gen1-offline
description: Use o serviço de importação/exportação para copiar dados do armazenamento de BLOBs do Azure para Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: aa3eb0bcd9ddd2a094563efe326f7af7e9e8708a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839299"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>Usar o serviço de importação/exportação do Azure para cópia offline de dados para Data Lake Storage Gen1

Neste artigo, você aprenderá a copiar grandes conjuntos de dados (> 200 GB) para Data Lake Storage Gen1 usando métodos de cópia offline, como o [serviço de importação/exportação do Azure](../storage/common/storage-import-export-service.md). Especificamente, o arquivo usado como exemplo neste artigo é de 339.420.860.416 bytes ou cerca de 319 GB em disco. Vamos chamar esse arquivo de 319GB. TSV.

O serviço de importação/exportação do Azure ajuda a transferir grandes quantidades de dados com mais segurança para o armazenamento de BLOBs do Azure enviando unidades de disco rígido para um datacenter do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de armazenamento do Azure**.
* **Uma conta de Azure data Lake Storage Gen1**. Para obter instruções sobre como criar uma, consulte Introdução [ao Azure data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="prepare-the-data"></a>Preparar os dados

Antes de usar o serviço de importação/exportação, quebre o arquivo de dados a ser transferido para **cópias com menos de 200 GB** de tamanho. A ferramenta de importação não funciona com arquivos maiores que 200 GB. Neste artigo, dividimos o arquivo em partes de 100 GB cada. Você pode fazer isso usando [Cygwin](https://cygwin.com/install.html). O Cygwin dá suporte a comandos do Linux. Nesse caso, use o seguinte comando:

    split -b 100m 319GB.tsv

A operação Split cria arquivos com os nomes a seguir.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Obter os discos prontos com os dados

Siga as instruções em [usando o serviço de importação/exportação do Azure](../storage/common/storage-import-export-service.md) (na seção **preparar suas unidades** ) para preparar seus discos rígidos. Aqui está a sequência geral:

1. Adquira um disco rígido que atenda ao requisito a ser usado para o serviço de importação/exportação do Azure.
2. Identifique uma conta de armazenamento do Azure na qual os dados serão copiados após serem enviados para o datacenter do Azure.
3. Use a [ferramenta de importação/exportação do Azure](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), um utilitário de linha de comando. Aqui está um trecho de código de exemplo que mostra como usar a ferramenta.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Consulte [usando o serviço de importação/exportação do Azure](../storage/common/storage-import-export-service.md) para obter mais trechos de código.
4. O comando anterior cria um arquivo de diário no local especificado. Use esse arquivo de diário para criar um trabalho de importação do [portal do Azure](https://portal.azure.com).

## <a name="create-an-import-job"></a>Criar um trabalho de importação

Agora você pode criar um trabalho de importação usando as instruções em [usando o serviço de importação/exportação do Azure](../storage/common/storage-import-export-service.md) (na seção **criar o trabalho de importação** ). Para esse trabalho de importação, com outros detalhes, forneça também o arquivo de diário criado ao preparar as unidades de disco.

## <a name="physically-ship-the-disks"></a>Enviar fisicamente os discos

Agora você pode enviar fisicamente os discos para um datacenter do Azure. Lá, os dados são copiados para os blobs de armazenamento do Azure que você forneceu ao criar o trabalho de importação. Além disso, ao criar o trabalho, se você optou por fornecer as informações de acompanhamento posteriormente, agora você pode voltar para o trabalho de importação e atualizar o número de acompanhamento.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Copiar dados de BLOBs para Data Lake Storage Gen1

Depois que o status do trabalho de importação mostrar que ele está concluído, você poderá verificar se os dados estão disponíveis nos blobs de armazenamento do Azure que você especificou. Você pode usar uma variedade de métodos para mover esses dados dos BLOBs para Azure Data Lake Storage Gen1. Para obter todas as opções disponíveis para carregar dados, consulte [ingerindo dados em data Lake Storage Gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

Nesta seção, fornecemos as definições de JSON que você pode usar para criar um pipeline de Azure Data Factory para copiar dados. Você pode usar essas definições de JSON do [portal do Azure](../data-factory/tutorial-copy-data-portal.md) ou do [Visual Studio](../data-factory/tutorial-copy-data-dot-net.md).

### <a name="source-linked-service-azure-storage-blob"></a>Serviço vinculado de origem (blob de armazenamento do Azure)

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

### <a name="target-linked-service-data-lake-storage-gen1"></a>Serviço vinculado de destino (Data Lake Storage Gen1)

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

Para obter mais informações, consulte [mover dados do blob de armazenamento do Azure para Azure data Lake Storage Gen1 usando Azure data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>Reconstruir os arquivos de dados no Data Lake Storage Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Começamos com um arquivo que era de 319 GB e o dividi em arquivos de tamanho menor para que pudesse ser transferido usando o serviço de importação/exportação do Azure. Agora que os dados estão em Azure Data Lake Storage Gen1, podemos reconstruir o arquivo para seu tamanho original. Você pode usar os cmdlets Azure PowerShell a seguir para fazer isso.

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
* [Usar Azure Data Lake Analytics com Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
