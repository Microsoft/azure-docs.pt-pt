---
title: Upload grande conjunto de dados para Azure Data Lake Storage Gen1 - métodos offline
description: Utilize o serviço de importação/exportação para copiar dados do armazenamento da Azure Blob para o Armazenamento de Lagos Azure Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: aa3eb0bcd9ddd2a094563efe326f7af7e9e8708a
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73839299"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>Utilize o serviço Azure Import/Export para cópia offline de dados para Data Lake Storage Gen1

Neste artigo, você aprenderá a copiar enormes conjuntos de dados (>200 GB) em Data Lake Storage Gen1 usando métodos de cópia offline, como o [serviço de importação/exportação Azure](../storage/common/storage-import-export-service.md). Especificamente, o ficheiro usado como exemplo neste artigo é de 339.420.860.416 bytes, ou cerca de 319 GB no disco. Vamos chamar este ficheiro 319GB.tsv.

O serviço de importação/exportação Azure ajuda-o a transferir grandes quantidades de dados de forma mais segura para o armazenamento da Blob Azure, enviando discos rígidos para um datacenter Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve ter o seguinte:

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de armazenamento Azure.**
* Uma conta De Armazenamento de **Lago Azure Gen1.** Para obter instruções sobre como criar um, consulte Iniciar com [Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="prepare-the-data"></a>Preparar os dados

Antes de utilizar o serviço de importação/exportação, inverta o ficheiro de dados a transferir **para cópias com menos de 200 GB** de tamanho. A ferramenta de importação não funciona com ficheiros superiores a 200 GB. Neste artigo, dividimos o ficheiro em pedaços de 100 GB cada. Pode fazê-lo usando [Cygwin.](https://cygwin.com/install.html) Cygwin suporta comandos Linux. Neste caso, utilize o seguinte comando:

    split -b 100m 319GB.tsv

A operação dividida cria ficheiros com os seguintes nomes.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Prepare os discos com dados

Siga as instruções [em Utilizar o serviço de importação/exportação Azure](../storage/common/storage-import-export-service.md) (sob a secção Preparar os seus **discos)** para preparar os seus discos rígidos. Aqui está a sequência geral:

1. Procure um disco rígido que satisfaça a exigência de ser utilizado para o serviço de importação/exportação Azure.
2. Identifique uma conta de armazenamento Azure onde os dados serão copiados depois de enviados para o datacenter Azure.
3. Utilize a ferramenta de [importação/exportação Azure,](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)um utilitário de linha de comando. Aqui está um corte de amostra que mostra como usar a ferramenta.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Consulte [a utilização do serviço azure import/exportação](../storage/common/storage-import-export-service.md) para obter mais amostras de amostras.
4. O comando anterior cria um ficheiro de diário no local especificado. Utilize este ficheiro de diário para criar um trabalho de importação a partir do [portal Azure.](https://portal.azure.com)

## <a name="create-an-import-job"></a>Criar um emprego de importação

Pode agora criar um emprego de importação utilizando as instruções de [utilização do serviço de importação/exportação Azure](../storage/common/storage-import-export-service.md) (ao abrigo da secção **Criar a Importação).** Para este trabalho de importação, com outros detalhes, também fornece o ficheiro de diário criado durante a preparação das unidades do disco.

## <a name="physically-ship-the-disks"></a>Enviar fisicamente os discos

Agora pode enviar fisicamente os discos para um centro de dados Azure. Ali, os dados são copiados para as bolhas de armazenamento Azure que forneceu enquanto cria o emprego de importação. Além disso, ao criar o trabalho, se optou por fornecer as informações de rastreio mais tarde, pode agora voltar ao seu trabalho de importação e atualizar o número de rastreio.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Copiar dados de blobs para Data Lake Storage Gen1

Depois do estado do trabalho de importação mostrar que está concluído, pode verificar se os dados estão disponíveis nas bolhas de armazenamento azure que especificou. Em seguida, pode utilizar uma variedade de métodos para mover esses dados das bolhas para o Azure Data Lake Storage Gen1. Para todas as opções disponíveis para o upload de dados, consulte os [dados de Ingesting no Data Lake Storage Gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

Nesta secção, fornecemos-lhe as definições JSON que pode utilizar para criar um pipeline Azure Data Factory para copiar dados. Pode utilizar estas definições JSON a partir do [portal Azure](../data-factory/tutorial-copy-data-portal.md) ou [do Estúdio Visual.](../data-factory/tutorial-copy-data-dot-net.md)

### <a name="source-linked-service-azure-storage-blob"></a>Serviço ligado à fonte (Blob de Armazenamento Azure)

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

### <a name="pipeline-copy-activity"></a>Gasoduto (atividade de cópia)

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

Para mais informações, consulte [A utilização de dados do Azure Storage blob para o Azure Data Lake Storage Gen1 utilizando](../data-factory/connector-azure-data-lake-store.md)a Azure Data Factory .

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>Reconstruir os ficheiros de dados em Data Lake Storage Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Começamos com um ficheiro de 319 GB, e quebrou-o em ficheiros de tamanho menor para que pudesse ser transferido através do serviço de importação/exportação Azure. Agora que os dados estão no Azure Data Lake Storage Gen1, podemos reconstruir o ficheiro para o seu tamanho original. Pode utilizar os seguintes cmdlets Azure PowerShell para o fazer.

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
* [Use Azure Data Lake Analytics com Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
