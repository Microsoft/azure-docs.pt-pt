---
title: Carregar grandes quantidades de dados para geração 1 de armazenamento do Azure Data Lake ao utilizar métodos offline | Documentos da Microsoft
description: Utilize a ferramenta de AdlCopy para copiar dados dos blobs de armazenamento do Azure para a geração 1 de armazenamento do Azure Data Lake
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4a8126d658f227d9eed372cd51cf06f8f12c99f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194982"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-azure-data-lake-storage-gen1"></a>Utilizar o serviço importar/exportar do Azure para cópia offline de dados para a geração 1 de armazenamento do Azure Data Lake
Neste artigo, aprenderá como copiar grandes conjuntos de dados (> 200 GB) para o Azure Data Lake armazenamento Gen1 utilizando métodos de cópia offline, como o [serviço importar/exportar do Azure](../storage/common/storage-import-export-service.md). Especificamente, o arquivo usado como um exemplo deste artigo é 339,420,860,416 bytes, ou cerca de 319 GB em disco. Vamos chamar 319GB.tsv este ficheiro.

O serviço importar/exportar do Azure ajuda-o a transferir grandes quantidades de dados com mais segurança para o armazenamento de Blobs do Azure envie unidades de disco rígido num Datacenter do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de armazenamento do Azure**.
* **Uma conta de geração 1 de armazenamento do Azure Data Lake**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>Preparar os dados

Antes de utilizar o serviço importar/exportar, quebrar o ficheiro de dados a serem transferidos **em cópias com menos de 200 GB** de tamanho. A ferramenta de importação não funciona com mais de 200 GB de arquivos. Neste tutorial, vamos dividir o ficheiro em segmentos de 100 GB cada. Pode fazer isso usando [Cygwin](https://cygwin.com/install.html). Cygwin suporta comandos de Linux. Neste caso, utilize o seguinte comando:

    split -b 100m 319GB.tsv

A operação de divisão cria arquivos com os seguintes nomes.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Prepare-discos com dados
Siga as instruções em [com o serviço importar/exportar do Azure](../storage/common/storage-import-export-service.md) (sob a **preparar suas unidades** secção) para preparar unidades de disco rígido. Segue-se a sequência geral:

1. Obtenha um disco rígido que satisfaz os requisitos para ser utilizado para o serviço importar/exportar do Azure.
2. Identifique uma conta de armazenamento do Azure onde os dados serão copiados depois é enviado para o datacenter do Azure.
3. Utilize o [ferramenta de importação/exportação do Azure](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), um utilitário de linha de comandos. Aqui está um trecho de código de exemplo que mostra como usar a ferramenta.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Ver [com o serviço importar/exportar do Azure](../storage/common/storage-import-export-service.md) para obter mais trechos de código de exemplo.
4. O comando anterior cria um ficheiro de diário na localização especificada. Utilizar este ficheiro de diário para criar uma tarefa de importação a partir da [portal do Azure](https://portal.azure.com).

## <a name="create-an-import-job"></a>Criar uma tarefa de importação
Agora, pode criar uma tarefa de importação utilizando as instruções em [com o serviço importar/exportar do Azure](../storage/common/storage-import-export-service.md) (sob a **criar a tarefa de importação** secção). Para esta tarefa de importação, com outros detalhes, também fornece o ficheiro de diário criado durante a preparação de unidades de disco.

## <a name="physically-ship-the-disks"></a>Envie os discos fisicamente
Fisicamente agora podem enviar os discos num Datacenter do Azure. Aqui, a dados são copiados para os blobs de armazenamento do Azure que forneceu ao criar a tarefa de importação. Além disso, ao criar a tarefa, se tiver optado por fornecer as informações de controle mais tarde, pode agora voltar para a sua tarefa de importação e atualizar o número de controlo.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Copiar dados dos blobs de armazenamento do Azure para a geração 1 de armazenamento do Azure Data Lake
Depois do Estado da tarefa de importação mostra que é concluída, pode verificar se os dados estão disponíveis nos blobs de armazenamento do Azure que tivesse especificado. Em seguida, pode utilizar uma variedade de métodos para mover dados de blobs para geração 1 de armazenamento do Azure Data Lake. Para todas as opções disponíveis para carregar dados, consulte [ingestão de dados no Data Lake Storage Gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

Nesta seção, fornecemos com as definições de JSON que pode utilizar para criar um pipeline do Azure Data Factory para copiar dados. Pode utilizar estas definições de JSON a partir da [portal do Azure](../data-factory/tutorial-copy-data-portal.md) ou [Visual Studio](../data-factory/tutorial-copy-data-dot-net.md).

### <a name="source-linked-service-azure-storage-blob"></a>Serviço de origem ligado (blob de armazenamento do Azure)
```
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

### <a name="target-linked-service-azure-data-lake-storage-gen1"></a>Serviço ligado (Azure Data Lake Storage Gen1) de destino
```
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
```
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
```
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
```
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
Para obter mais informações, consulte [mover dados de Blobs do armazenamento do Azure para a geração 1 de armazenamento do Azure Data Lake com o Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-azure-data-lake-storage-gen1"></a>Reconstruir os ficheiros de dados na geração 1 de armazenamento do Azure Data Lake

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nós começamos com um ficheiro que foi 319 GB e interrompida desligámo-lo em arquivos de tamanho mais pequeno, de modo a que pode ser transferida com o serviço importar/exportar do Azure. Agora que os dados estão na geração 1 de armazenamento do Azure Data Lake, podemos possa reconstruir o arquivo ao tamanho original. Pode utilizar os seguintes cmdlets PowerShell do Azure para fazer isso.

```
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

## <a name="next-steps"></a>Passos Seguintes
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utilizar o Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
