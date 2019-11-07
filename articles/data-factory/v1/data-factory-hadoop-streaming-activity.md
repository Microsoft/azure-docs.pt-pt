---
title: Transformar dados usando a atividade de streaming do Hadoop – Azure
description: Saiba como você pode usar a atividade de streaming do Hadoop em uma data factory do Azure para transformar dados executando programas de streaming do Hadoop em um cluster sob demanda/seu próprio HDInsight.
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
ms.openlocfilehash: c56961b28750f3ba6450c26c897c1ef6c54b3ed8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73667508"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformar dados usando a atividade de streaming do Hadoop no Azure Data Factory
> [!div class="op_single_selector" title1="Atividades de transformação"]
> * [Atividade do hive](data-factory-hive-activity.md) 
> * [Atividade Pig](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [transformar dados usando a atividade de streaming do Hadoop no data Factory](../transform-data-using-hadoop-streaming.md).


Você pode usar a atividade HDInsightStreamingActivity para invocar um trabalho de streaming do Hadoop de um pipeline Azure Data Factory. O trecho JSON a seguir mostra a sintaxe para usar o HDInsightStreamingActivity em um arquivo JSON de pipeline. 

A atividade de streaming do HDInsight em um [pipeline](data-factory-create-pipelines.md) data Factory executa programas de streaming do Hadoop em [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) cluster do HDInsight baseado em Windows/Linux [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Este artigo se baseia no artigo [atividades de transformação de dados](data-factory-data-transformation-activities.md) , que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte.

> [!NOTE] 
> Se você for novo no Azure Data Factory, leia a [introdução ao Azure data Factory](data-factory-introduction.md) e faça o tutorial: [criar seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

## <a name="json-sample"></a>Exemplo de JSON
O cluster HDInsight é preenchido automaticamente com programas de exemplo (WC. exe e Cat. exe) e dados (DaVinci. txt). Por padrão, o nome do contêiner que é usado pelo cluster HDInsight é o nome do próprio cluster. Por exemplo, se o nome do cluster for myhdicluster, o nome do contêiner de blob associado seria myhdicluster. 

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

1. Defina **linkedServiceName** como o nome do serviço vinculado que aponta para o cluster HDInsight no qual o trabalho de streaming MapReduce é executado.
2. Defina o tipo da atividade como **HDInsightStreaming**.
3. Para a propriedade **Mapper** , especifique o nome do executável do mapeador. No exemplo, Cat. exe é o executável do mapeador.
4. Para a propriedade **redutor** , especifique o nome do executável redutor. No exemplo, WC. exe é o executável do redutor.
5. Para a propriedade tipo de **entrada** , especifique o arquivo de entrada (incluindo o local) para o mapeador. No exemplo: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt`: adfsample é o contêiner de BLOB, example/data/Gutenberg é a pasta e DaVinci. txt é o blob.
6. Para a propriedade tipo de **saída** , especifique o arquivo de saída (incluindo o local) para o redutor. A saída do trabalho de streaming do Hadoop é gravada no local especificado para essa propriedade.
7. Na seção arquivos de **caminho** , especifique os caminhos para os executáveis do mapeador e do redutor. No exemplo: "adfsample/example/apps/WC. exe", adfsample é o contêiner de BLOB, example/apps é a pasta e WC. exe é o executável.
8. Para a propriedade **fileLinkedService** , especifique o serviço vinculado do armazenamento do Azure que representa o armazenamento do Azure que contém os arquivos especificados na seção filePaths.
9. Para a propriedade **arguments** , especifique os argumentos para o trabalho de streaming.
10. A propriedade **getDebugInfo** é um elemento opcional. Quando definido como falha, os logs são baixados apenas em caso de falha. Quando definido como sempre, os logs são sempre baixados, independentemente do status de execução.

> [!NOTE]
> Conforme mostrado no exemplo, você especifica um conjunto de uma saída para a atividade de streaming do Hadoop para a propriedade **Outputs** . Esse conjunto de e é apenas um conjunto de uma fictício que é necessário para direcionar a agenda do pipeline. Você não precisa especificar nenhum conjunto de dados de entrada para a atividade da propriedade **entradas** .  
> 
> 

## <a name="example"></a>Exemplo
O pipeline neste tutorial executa o programa de mapa/redução de streaming de contagem de palavras em seu cluster do Azure HDInsight. 

### <a name="linked-services"></a>Serviços ligados
#### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure
Primeiro, você cria um serviço vinculado para vincular o armazenamento do Azure que é usado pelo cluster do Azure HDInsight para o data factory do Azure. Se você copiar/colar o código a seguir, não se esqueça de substituir o nome da conta e a chave da conta pelo nome e pela chave do armazenamento do Azure. 

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

#### <a name="azure-hdinsight-linked-service"></a>Serviço vinculado do Azure HDInsight
Em seguida, você cria um serviço vinculado para vincular seu cluster do Azure HDInsight ao data factory do Azure. Se você copiar/colar o código a seguir, substitua o nome do cluster HDInsight pelo nome do seu cluster HDInsight e altere os valores de nome de usuário e senha. 

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
#### <a name="output-dataset"></a>Conjunto de saída
O pipeline neste exemplo não pega nenhuma entrada. Você especifica um conjunto de uma saída para a atividade de streaming do HDInsight. Esse conjunto de e é apenas um conjunto de uma fictício que é necessário para direcionar a agenda do pipeline. 

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

O cluster HDInsight é preenchido automaticamente com programas de exemplo (WC. exe e Cat. exe) e dados (DaVinci. txt). Por padrão, o nome do contêiner que é usado pelo cluster HDInsight é o nome do próprio cluster. Por exemplo, se o nome do cluster for myhdicluster, o nome do contêiner de blob associado seria myhdicluster.  

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
## <a name="see-also"></a>Veja também
* [Atividade do hive](data-factory-hive-activity.md)
* [Atividade Pig](data-factory-pig-activity.md)
* [Atividade MapReduce](data-factory-map-reduce.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

