---
title: Invocar o Programa MapReduce da Azure Data Factory
description: Saiba como processar dados executando programas MapReduce num cluster Azure HDInsight a partir de uma fábrica de dados Azure.
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
ms.openlocfilehash: 089a2e6a0b90c1682e2ebdd146626c93cec35f77
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636855"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Invocar programas mapReduce da Fábrica de Dados
> [!div class="op_single_selector" title1="Atividades de Transformação"]
> * [Atividade da Colmeia](data-factory-hive-activity.md) 
> * [Atividade do Porco](data-factory-pig-activity.md)
> * [Atividade mapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade de Faísca](data-factory-spark.md)
> * [Atividade de Execução em Lotes do Azure Machine Learning Studio (clássico)](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização do Azure Machine Learning Studio (clássico)](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [.NET Atividade Personalizada](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte os dados de [transformação utilizando a atividade mapReduce na Data Factory.](../transform-data-using-hadoop-map-reduce.md)


A atividade HDInsight MapReduce num [oleoduto](data-factory-create-pipelines.md) data factory executa programas MapReduce por [conta própria](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou a pedido do grupo HDInsight baseado [em](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux. Este artigo baseia-se no artigo de atividades de [transformação](data-factory-data-transformation-activities.md) de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

> [!NOTE] 
> Se é novo na Azure Data Factory, leia através [da Introdução à Azure Data Factory](data-factory-introduction.md) e faça o tutorial: Construa o seu primeiro pipeline de [dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo.  

## <a name="introduction"></a>Introdução
Um oleoduto numa fábrica de dados da Azure processa dados em serviços de armazenamento ligados utilizando serviços de computação ligados. Contém uma sequência de atividades em que cada atividade realiza uma operação de processamento específica. Este artigo descreve a utilização da Atividade de Mílpe MapReduce HDInsight.

Consulte [o Pig](data-factory-pig-activity.md) and [Hive](data-factory-hive-activity.md) para obter detalhes sobre a execução de scripts de Pig/Hive num cluster HDInsight baseado no Windows/Linux a partir de um oleoduto utilizando atividades de Porco e Colmeia HDInsight. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON para atividade de mapreduce mapa hdinsight
Na definição JSON para a Atividade HDInsight: 

1. Defina o **tipo** de **atividade** para **HDInsight** .
2. Especifique o nome da classe para propriedade **classeName.**
3. Especifique o caminho para o ficheiro JAR, incluindo o nome do ficheiro para a propriedade **jarFilePath.**
4. Especifique o serviço ligado que se refere ao Azure Blob Storage que contém o ficheiro JAR para a propriedade **jarLinkedService.**   
5. Especifique quaisquer argumentos para o programa MapReduce na secção **de argumentos.** No tempo de execução, você vê alguns argumentos extra (por exemplo: mapreduce.job.tags) a partir do quadro MapReduce. Para diferenciar os seus argumentos com os argumentos MapReduce, considere usar a opção e o valor como argumentos como mostrados no exemplo seguinte (-s, --input, --output etc., são opções imediatamente seguidas pelos seus valores).

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
   Pode utilizar a Atividade de MapReduce HDInsight para executar qualquer ficheiro de frasco MapReduce num cluster HDInsight. Na seguinte amostra, a definição JSON de um oleoduto, a Atividade HDInsight está configurada para executar um ficheiro Mahout JAR.

## <a name="sample-on-github"></a>Amostra no GitHub
Você pode baixar uma amostra para usar a atividade de MapReduce HDInsight a partir de: [Data Factory Samples on GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Executar o programa Word Count
O pipeline neste exemplo executa o programa Word Count Map/Reduce no seu cluster Azure HDInsight.   

### <a name="linked-services"></a>Serviços Ligados
Em primeiro lugar, cria um serviço ligado para ligar o Azure Storage que é utilizado pelo cluster Azure HDInsight à fábrica de dados Azure. Se copiar/colar o seguinte código, não se esqueça de substituir o **nome** da conta e **a chave de conta** pelo nome e chave do seu Azure Storage. 

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

#### <a name="azure-hdinsight-linked-service"></a>Serviço ligado a Azure HDInsight
Em seguida, cria um serviço ligado para ligar o seu cluster Azure HDInsight à fábrica de dados Azure. Se copiar/colar o seguinte código, substitua o nome do **cluster HDInsight** pelo nome do seu cluster HDInsight e altere o nome de utilizador e os valores da palavra-passe.   

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
O gasoduto neste exemplo não tem entradas. Especifica um conjunto de dados de saída para a Atividade de Análise de Mapas hdinsight. Este conjunto de dados é apenas um conjunto de dados falso que é necessário para conduzir o calendário do pipeline.  

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
| tipo |O tipo deve ser definido para **HDInsightMapReduce** . |
| nome de classeName |O nome da classe é: **contador de palavras** |
| jarFilePath |Caminho para o ficheiro do frasco que contém a classe. Se copiar/colar o seguinte código, não se esqueça de alterar o nome do cluster. |
| jarLinkedService |Serviço ligado a Azure Storage que contém o ficheiro do frasco. Este serviço ligado refere-se ao armazenamento que está associado ao cluster HDInsight. |
| argumentos |O programa wordcount leva dois argumentos, uma entrada e uma saída. O ficheiro de entrada é o ficheiro davinci.txt. |
| frequência/intervalo |Os valores destas propriedades correspondem ao conjunto de dados de saída. |
| linkedServiceName |refere-se ao serviço ligado hdInsight que tinha criado anteriormente. |

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

## <a name="run-spark-programs"></a>Executar programas Spark
Pode utilizar a atividade MapReduce para executar programas do Spark no seu cluster do HDInsight Spark. Veja [Invoke Spark programs from Azure Data Factory (Invocar programas do Spark a partir do Azure Data Factory)](data-factory-spark.md) para obter detalhes  

[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: /previous-versions/azure/dn834987(v=azure.100)
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Consulte também
* [Atividade da Colmeia](data-factory-hive-activity.md)
* [Atividade do Porco](data-factory-pig-activity.md)
* [Atividade de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)