---
title: Transformar dados com o Hadoop de transmissão em fluxo atividade - Azure | Documentos da Microsoft
description: Saiba como pode utilizar a atividade de transmissão em fluxo do Hadoop de uma fábrica de dados do Azure para transformar dados através da execução de programas de transmissão em fluxo do Hadoop num cluster do HDInsight no, a pedido/suas próprio.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: dd00c0a2998009ce6c39ca19abb25a2548682cee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60486350"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformar dados com a atividade de transmissão em fluxo do Hadoop no Azure Data Factory
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Atividade do Hive](data-factory-hive-activity.md) 
> * [Atividade PIg](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [transformar dados com o Hadoop atividade numa fábrica de dados de transmissão em fluxo](../transform-data-using-hadoop-streaming.md).


Pode usar a atividade de HDInsightStreamingActivity invocar uma tarefa de transmissão em fluxo do Hadoop de um pipeline do Azure Data Factory. O fragmento JSON seguinte mostra a sintaxe para usar o HDInsightStreamingActivity num ficheiro JSON do pipeline. 

A atividade Streaming do HDInsight numa fábrica de dados [pipeline](data-factory-create-pipelines.md) executa programas de transmissão em fluxo do Hadoop no [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight baseado em Windows/Linux. Este artigo baseia-se a [atividades de transformação de dados](data-factory-data-transformation-activities.md) artigo, que apresenta uma visão geral de transformação de dados e as atividades de transformação suportados.

> [!NOTE] 
> Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](data-factory-introduction.md) e fazer o tutorial: [Crie seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

## <a name="json-sample"></a>Exemplo JSON
O cluster do HDInsight é preenchido automaticamente com os programas de exemplo (wc.exe e cat.exe) e (davinci.txt). Por predefinição, o nome do contentor que é utilizado pelo cluster do HDInsight é o nome do cluster em si. Por exemplo, se o nome do cluster é myhdicluster, o nome do contentor de blob associado seria myhdicluster. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Tenha em atenção os seguintes pontos:

1. Definir o **linkedServiceName** para o nome do serviço ligado que aponta para o HDInsight cluster no qual a tarefa de mapreduce de transmissão em fluxo é executada.
2. Definir o tipo da atividade como **HDInsightStreaming**.
3. Para o **mapeador** propriedade, especifique o nome do mapeador de executável. No exemplo, cat.exe é o mapeador de executável.
4. Para o **reducer** propriedade, especifique o nome do reducer executável. No exemplo, wc.exe é reducer executável.
5. Para o **entrada** a propriedade do tipo, especifique o ficheiro de entrada (incluindo a localização) para o mapeador de pontos. No exemplo: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt`: adfsample é o contentor de BLOBs, exemplo/dados/Gutenberg é a pasta e davinci.txt é o blob.
6. Para o **saída** a propriedade do tipo, especifique o ficheiro de saída (incluindo a localização) para o reducer. O resultado da tarefa de transmissão em fluxo do Hadoop é escrito para a localização especificada para esta propriedade.
7. Na **filePaths** secção, especifique os caminhos para os mapeador de pontos e reducer executáveis. No exemplo: "adfsample/example/apps/wc.exe", adfsample é o contentor de BLOBs/aplicações de exemplo é a pasta e wc.exe é o executável.
8. Para o **fileLinkedService** propriedade, especifique o serviço ligado do armazenamento do Azure, que representa o armazenamento do Azure que contém os ficheiros especificados na secção filePaths.
9. Para o **argumentos** propriedade, especifique os argumentos para a tarefa de transmissão em fluxo.
10. O **getDebugInfo** propriedade é um elemento opcional. Quando é definido como falha, os registos são transferidos apenas em caso de falha. Quando é definido como sempre, os registos serão sempre transferidos, independentemente do Estado de execução.

> [!NOTE]
> Conforme mostrado no exemplo, especifique um conjunto de dados de saída para a atividade de transmissão em fluxo do Hadoop para o **produz** propriedade. Este conjunto de dados é apenas um dataset fictício que é necessário para orientar a agenda do pipeline. Não é necessário especificar qualquer conjunto de dados de entrada para a atividade para o **entradas** propriedade.  
> 
> 

## <a name="example"></a>Exemplo
O pipeline nesta explicação passo a passo é executado o programa de mapa/redução de transmissão em fluxo de contagem de palavras no seu cluster Azure HDInsight. 

### <a name="linked-services"></a>Serviços ligados
#### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure
Em primeiro lugar, criar um serviço ligado para ligar o armazenamento do Azure que é utilizado pelo cluster do HDInsight do Azure à fábrica de dados do Azure. Se copiar/colar o código a seguir, não se esqueça de substituir o nome da conta e chave da conta com o nome e chave de armazenamento do Azure. 

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Serviço ligado de HDInsight do Azure
Em seguida, criar um serviço ligado para ligar o seu cluster do HDInsight do Azure à fábrica de dados do Azure. Se copiar/colar o código a seguir, substitua o nome de cluster do HDInsight com o nome do cluster do HDInsight e alterar os valores de nome e palavra-passe do utilizador. 

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Conjuntos de dados
#### <a name="output-dataset"></a>Conjunto de dados de saída
O pipeline neste exemplo não tem quaisquer entradas. Especifique um conjunto de dados de saída para a atividade de transmissão em fluxo do HDInsight. Este conjunto de dados é apenas um dataset fictício que é necessário para orientar a agenda do pipeline. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
O pipeline neste exemplo tem apenas uma atividade que é do tipo: **HDInsightStreaming**. 

O cluster do HDInsight é preenchido automaticamente com os programas de exemplo (wc.exe e cat.exe) e (davinci.txt). Por predefinição, o nome do contentor que é utilizado pelo cluster do HDInsight é o nome do cluster em si. Por exemplo, se o nome do cluster é myhdicluster, o nome do contentor de blob associado seria myhdicluster.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Consultar Também
* [Atividade do Hive](data-factory-hive-activity.md)
* [Atividade PIg](data-factory-pig-activity.md)
* [Atividade MapReduce](data-factory-map-reduce.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

