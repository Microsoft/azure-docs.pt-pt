---
title: Transforme dados usando a Atividade de Streaming hadoop - Azure
description: Saiba como pode utilizar a Atividade de Streaming Hadoop numa fábrica de dados Azure para transformar dados executando programas de Streaming Hadoop num cluster a pedido/seu próprio HDInsight.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74703371"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transforme dados usando a atividade de streaming de Hadoop na Fábrica de Dados Azure
> [!div class="op_single_selector" title1="Atividades de Transformação"]
> * [Atividade da Colmeia](data-factory-hive-activity.md) 
> * [Atividade do Porco](data-factory-pig-activity.md)
> * [MapReduce Atividade](data-factory-map-reduce.md)
> * [Atividade de streaming de hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade de Faísca](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [.NET Atividade Personalizada](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte a transformação de dados utilizando a atividade de [streaming Hadoop na Data Factory](../transform-data-using-hadoop-streaming.md).


Pode utilizar a Atividade de Atividade De Streaming HDInsightStreaming invocando um trabalho de Streaming hadoop a partir de um oleoduto Azure Data Factory. O seguinte snippet JSON mostra a sintaxe para a utilização da Atividade HDInsightStreaming num ficheiro JSON de pipeline. 

A Atividade de Streaming HDInsight num [pipeline](data-factory-create-pipelines.md) data Factory executa programas de Streaming Hadoop por [conta própria](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou a [pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) do cluster HDInsight baseado em HDInsight baseado em HDInsight. Este artigo baseia-se no artigo sobre atividades de transformação de [dados,](data-factory-data-transformation-activities.md) que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

> [!NOTE] 
> Se é novo na Azure Data Factory, leia através da [Introdução à Azure Data Factory](data-factory-introduction.md) e faça o tutorial: [Construa](data-factory-build-your-first-pipeline.md) o seu primeiro pipeline de dados antes de ler este artigo. 

## <a name="json-sample"></a>Amostra JSON
O cluster HDInsight é automaticamente povoado com programas de exemplo (wc.exe e cat.exe) e dados (davinci.txt). Por predefinição, o nome do recipiente utilizado pelo cluster HDInsight é o nome do próprio cluster. Por exemplo, se o seu nome de cluster for myhdicluster, o nome do recipiente blob associado seria myhdicluster. 

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

1. Defina o **LinkedServiceName** para o nome do serviço ligado que aponta para o seu cluster HDInsight no qual o trabalho de redução de mapas de streaming é executado.
2. Defina o tipo de atividade para **HDInsightStreaming**.
3. Para a propriedade do **mapper,** especifique o nome do mapper executável. No exemplo, cat.exe é o mapeexecutável.
4. Para a propriedade **redutor,** especifique o nome do redutor executável. No exemplo, wc.exe é o redutor executável.
5. Para a propriedade do tipo **de entrada,** especifique o ficheiro de entrada (incluindo a localização) para o mapper. No exemplo: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt`adfsample é o recipiente blob, exemplo/dados/Gutenberg é a pasta, e davinci.txt é a bolha.
6. Para a propriedade do tipo **de saída,** especifique o ficheiro de saída (incluindo a localização) para o redutor. A saída do trabalho de Streaming Hadoop está escrita para o local especificado para esta propriedade.
7. Na secção **filePaths,** especifique os caminhos para o mapper e executáveis redutores. No exemplo: "adfsample/example/apps/wc.exe", a adfsample é o recipiente blob, exemplo/apps é a pasta, e wc.exe é o executável.
8. Para a propriedade **fileLinkedService,** especifique o serviço ligado ao Armazenamento Azure que representa o armazenamento Azure que contém os ficheiros especificados na secção ficheiroS.
9. Para a propriedade de **argumentos,** especifique os argumentos para o trabalho de streaming.
10. A propriedade **getDebugInfo** é um elemento opcional. Quando está definido para falha, os registos são descarregados apenas por falha. Quando está definido para Sempre, os registos são sempre descarregados independentemente do estado de execução.

> [!NOTE]
> Como mostra o exemplo, especifica um conjunto de dados de saída para a Atividade de Streaming Hadoop para a propriedade de **saídas.** Este conjunto de dados é apenas um conjunto de dados de boneco que é necessário para impulsionar o calendário do gasoduto. Não necessita de especificar qualquer conjunto de dados de entrada para a atividade da propriedade de **inputs.**  
> 
> 

## <a name="example"></a>Exemplo
O pipeline neste walkthrough executa o programa de streaming Word Count Map/Reduce no seu cluster Azure HDInsight. 

### <a name="linked-services"></a>Serviços ligados
#### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure
Em primeiro lugar, cria-se um serviço ligado para ligar o Armazenamento Azure que é utilizado pelo cluster Azure HDInsight à fábrica de dados Azure. Se copiar/colar o seguinte código, não se esqueça de substituir o nome da conta e a chave da conta pelo nome e chave do seu Armazenamento Azure. 

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

#### <a name="azure-hdinsight-linked-service"></a>Serviço ligado azure HDInsight
Em seguida, cria um serviço ligado para ligar o seu cluster Azure HDInsight à fábrica de dados Azure. Se copiar/colar o seguinte código, substitua o nome do cluster HDInsight com o nome do seu cluster HDInsight e altere os valores de nome de utilizador e palavra-passe. 

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
O gasoduto neste exemplo não tem quaisquer inputs. Especifica um conjunto de dados de saída para a Atividade de Streaming HDInsight. Este conjunto de dados é apenas um conjunto de dados de boneco que é necessário para impulsionar o calendário do gasoduto. 

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
O oleoduto neste exemplo tem apenas uma atividade que é de tipo: **HDInsightStreaming**. 

O cluster HDInsight é automaticamente povoado com programas de exemplo (wc.exe e cat.exe) e dados (davinci.txt). Por predefinição, o nome do recipiente utilizado pelo cluster HDInsight é o nome do próprio cluster. Por exemplo, se o seu nome de cluster for myhdicluster, o nome do recipiente blob associado seria myhdicluster.  

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
* [Atividade da Colmeia](data-factory-hive-activity.md)
* [Atividade do Porco](data-factory-pig-activity.md)
* [MapReduce Atividade](data-factory-map-reduce.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

