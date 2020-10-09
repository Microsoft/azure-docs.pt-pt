---
title: Transformar dados usando a Atividade de Streaming Hadoop - Azure
description: Saiba como pode utilizar a Atividade de Streaming Hadoop numa fábrica de dados Azure para transformar dados executando programas de streaming Hadoop num cluster on-demand/seu próprio HDInsight.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: a7f07365da699a40f5b51917104a68a62affa3d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74703371"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformar dados usando a atividade de streaming hadoop na fábrica de dados Azure
> [!div class="op_single_selector" title1="Atividades de Transformação"]
> * [Atividade da Colmeia](data-factory-hive-activity.md) 
> * [Atividade do Porco](data-factory-pig-activity.md)
> * [Atividade mapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade de Faísca](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [.NET Atividade Personalizada](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte os dados de [transformação utilizando a atividade de streaming hadoop na Data Factory.](../transform-data-using-hadoop-streaming.md)


Pode utilizar a Atividade hdInsightStreamingActivity invocando um trabalho de Streaming Hadoop a partir de um oleoduto Azure Data Factory. O seguinte corte JSON mostra a sintaxe para a utilização do HDInsightStreamingActivity num ficheiro JSON de pipeline. 

A Atividade de Streaming HDInsight num [oleoduto](data-factory-create-pipelines.md) de Data Factory executa programas de streaming Hadoop no [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) do grupo HDInsight baseado em Windows/Linux. Este artigo baseia-se no artigo de atividades de [transformação](data-factory-data-transformation-activities.md) de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

> [!NOTE] 
> Se é novo na Azure Data Factory, leia através [da Introdução à Azure Data Factory](data-factory-introduction.md) e faça o tutorial: Construa o seu primeiro pipeline de [dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

## <a name="json-sample"></a>Amostra JSON
O cluster HDInsight é automaticamente povoado com programas de exemplo (wc.exe e cat.exe) e dados (davinci.txt). Por predefinição, o nome do recipiente que é utilizado pelo cluster HDInsight é o nome do próprio cluster. Por exemplo, se o seu nome de cluster for myhdicluster, o nome do recipiente de bolhas associado seria myhdicluster. 

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

1. Defina o **nome linkedServiceName** para o nome do serviço ligado que aponta para o seu cluster HDInsight no qual o trabalho de streaming mapreduce é executado.
2. Defina o tipo de atividade para **HDInsightStreaming**.
3. Para a propriedade **mapper,** especifique o nome do mapper executável. No exemplo, cat.exe é o mapper executável.
4. Para a propriedade **redutor,** especifique o nome do redutor executável. No exemplo, wc.exe é o redutor executável.
5. Para a propriedade tipo **entrada,** especifique o ficheiro de entrada (incluindo a localização) para o mapper. No exemplo: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt` adfsample é o recipiente blob, exemplo/dados/Gutenberg é a pasta, e davinci.txt é a bolha.
6. Para a propriedade do tipo **de saída,** especifique o ficheiro de saída (incluindo a localização) para o redutor. A saída do trabalho de streaming Hadoop é escrita para o local especificado para esta propriedade.
7. Na secção **ficheiroPaths,** especifique os caminhos para o mapper e executáveis redutores. No exemplo: "adfsample/exemplo/apps/wc.exe", adfsample é o recipiente blob, exemplo/apps é a pasta, e wc.exe é o executável.
8. Para a propriedade **fileLinkedService,** especifique o serviço ligado ao Armazenamento Azure que representa o armazenamento Azure que contém os ficheiros especificados na secção filePaths.
9. Para a propriedade dos **argumentos,** especifique os argumentos para o trabalho de streaming.
10. A propriedade **getDebugInfo** é um elemento opcional. Quando está definido para Falha, os registos são descarregados apenas por falha. Quando está definido para Sempre, os registos são sempre descarregados independentemente do estado de execução.

> [!NOTE]
> Como mostrado no exemplo, você especifica um conjunto de dados de saída para a Atividade de Streaming Hadoop para a propriedade **de saídas.** Este conjunto de dados é apenas um conjunto de dados falso que é necessário para conduzir o calendário do pipeline. Não precisa de especificar nenhum conjunto de dados de entrada para a atividade para a propriedade **de entradas.**  
> 
> 

## <a name="example"></a>Exemplo
O pipeline neste walkthrough executa o mapa de streaming Word Count/Reduzir no seu cluster Azure HDInsight. 

### <a name="linked-services"></a>Serviços ligados
#### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure
Em primeiro lugar, cria um serviço ligado para ligar o Azure Storage que é utilizado pelo cluster Azure HDInsight à fábrica de dados Azure. Se copiar/colar o seguinte código, não se esqueça de substituir o nome da conta e a chave de conta pelo nome e chave do seu Azure Storage. 

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

#### <a name="azure-hdinsight-linked-service"></a>Serviço ligado a Azure HDInsight
Em seguida, cria um serviço ligado para ligar o seu cluster Azure HDInsight à fábrica de dados Azure. Se copiar/colar o seguinte código, substitua o nome do cluster HDInsight pelo nome do seu cluster HDInsight e altere o nome de utilizador e os valores da palavra-passe. 

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
O gasoduto neste exemplo não tem entradas. Especifica um conjunto de dados de saída para a Atividade de Streaming HDInsight. Este conjunto de dados é apenas um conjunto de dados falso que é necessário para conduzir o calendário do pipeline. 

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
O gasoduto neste exemplo tem apenas uma atividade que é do tipo: **HDInsightStreaming**. 

O cluster HDInsight é automaticamente povoado com programas de exemplo (wc.exe e cat.exe) e dados (davinci.txt). Por predefinição, o nome do recipiente que é utilizado pelo cluster HDInsight é o nome do próprio cluster. Por exemplo, se o seu nome de cluster for myhdicluster, o nome do recipiente de bolhas associado seria myhdicluster.  

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
## <a name="see-also"></a>Consulte também
* [Atividade da Colmeia](data-factory-hive-activity.md)
* [Atividade do Porco](data-factory-pig-activity.md)
* [Atividade mapReduce](data-factory-map-reduce.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

