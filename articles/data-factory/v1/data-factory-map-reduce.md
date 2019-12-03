---
title: Invocar programa MapReduce da Azure Data Factory
description: Saiba como processar dados executando programas MapReduce em um cluster do Azure HDInsight de um data factory do Azure.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 598a16d25ba375b984a966cba190181edbda3d15
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74703143"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Invocar programas MapReduce de Data Factory
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
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [transformar dados usando a atividade MapReduce no data Factory](../transform-data-using-hadoop-map-reduce.md).


A atividade de MapReduce do HDInsight em um [pipeline](data-factory-create-pipelines.md) de data Factory executa programas MapReduce em [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) cluster do HDInsight baseado em Windows/Linux [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Este artigo se baseia no artigo [atividades de transformação de dados](data-factory-data-transformation-activities.md) , que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte.

> [!NOTE] 
> Se você for novo no Azure Data Factory, leia a [introdução ao Azure data Factory](data-factory-introduction.md) e faça o tutorial: [criar seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo.  

## <a name="introduction"></a>Introdução
Um pipeline em um data factory do Azure processa dados em serviços de armazenamento vinculados usando serviços de computação vinculados. Ele contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Este artigo descreve como usar a atividade de MapReduce do HDInsight.

Consulte [Pig](data-factory-pig-activity.md) e [Hive](data-factory-hive-activity.md) para obter detalhes sobre como executar scripts do Pig/Hive em um cluster hdinsight baseado em Windows/Linux de um pipeline usando as atividades Pig do hdinsight e Hive. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>Atividade do JSON para HDInsight MapReduce
Na definição de JSON para a atividade do HDInsight: 

1. Defina o **tipo** da **atividade** como **HDInsight**.
2. Especifique o nome da classe para a propriedade **ClassName** .
3. Especifique o caminho para o arquivo JAR, incluindo o nome do arquivo para a propriedade **jarFilePath** .
4. Especifique o serviço vinculado que se refere ao armazenamento de BLOBs do Azure que contém o arquivo JAR para a propriedade **jarLinkedService** .   
5. Especifique quaisquer argumentos para o programa MapReduce na seção **argumentos** . Em tempo de execução, você verá alguns argumentos extras (por exemplo: MapReduce. Job. Tags) da estrutura MapReduce. Para diferenciar seus argumentos com os argumentos MapReduce, considere o uso de Option e Value como argumentos, conforme mostrado no exemplo a seguir (-s,--Input,--output etc., são opções imediatamente seguidas por seus valores).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
   Você pode usar a atividade de MapReduce do HDInsight para executar qualquer arquivo JAR do MapReduce em um cluster HDInsight. Na definição de JSON de exemplo a seguir de um pipeline, a atividade do HDInsight é configurada para executar um arquivo JAR Mahout.

## <a name="sample-on-github"></a>Exemplo no GitHub
Você pode baixar um exemplo para usar a atividade de MapReduce do HDInsight em: [Data Factory exemplos no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Executando o programa de contagem de palavras
O pipeline neste exemplo executa o programa de mapeamento/redução de contagem de palavras em seu cluster do Azure HDInsight.   

### <a name="linked-services"></a>Serviços Ligados
Primeiro, você cria um serviço vinculado para vincular o armazenamento do Azure que é usado pelo cluster do Azure HDInsight para o data factory do Azure. Se você copiar/colar o código a seguir, não se esqueça de substituir o **nome da conta** e a **chave da conta** pelo nome e pela chave do armazenamento do Azure. 

#### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure

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
Em seguida, você cria um serviço vinculado para vincular seu cluster do Azure HDInsight ao data factory do Azure. Se você copiar/colar o código a seguir, substitua o **nome do cluster hdinsight** pelo nome do seu cluster hdinsight e altere os valores de nome de usuário e senha.   

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
O pipeline neste exemplo não pega nenhuma entrada. Especifique um conjunto de uma saída para a atividade de MapReduce do HDInsight. Esse conjunto de e é apenas um conjunto de uma fictício que é necessário para direcionar a agenda do pipeline.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
O pipeline neste exemplo tem apenas uma atividade que é do tipo: HDInsightMapReduce. Algumas das propriedades importantes no JSON são: 

| Propriedade | Notas |
|:--- |:--- |
| tipo |O tipo deve ser definido como **HDInsightMapReduce**. |
| className |O nome da classe é: **WordCount** |
| jarFilePath |Caminho para o arquivo JAR que contém a classe. Se você copiar/colar o código a seguir, não se esqueça de alterar o nome do cluster. |
| jarLinkedService |Serviço vinculado do armazenamento do Azure que contém o arquivo jar. Esse serviço vinculado refere-se ao armazenamento associado ao cluster HDInsight. |
| argumentos |O programa WordCount usa dois argumentos, uma entrada e uma saída. O arquivo de entrada é o arquivo DaVinci. txt. |
| frequência/intervalo |Os valores para essas propriedades correspondem ao conjunto de resultados de saída. |
| linkedServiceName |refere-se ao serviço vinculado do HDInsight que você criou anteriormente. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Executar programas do Spark
Pode utilizar a atividade MapReduce para executar programas do Spark no seu cluster do HDInsight Spark. Veja [Invoke Spark programs from Azure Data Factory (Invocar programas do Spark a partir do Azure Data Factory)](data-factory-spark.md) para obter detalhes  

[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Consulte também
* [Atividade do hive](data-factory-hive-activity.md)
* [Atividade Pig](data-factory-pig-activity.md)
* [Atividade de streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

