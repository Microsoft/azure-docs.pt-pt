---
title: Invocar mapReduce programa da Fábrica de Dados Azure
description: Saiba como processar dados executando programas MapReduce num cluster Azure HDInsight de uma fábrica de dados Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74703143"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Invocar mapReduce programas da fábrica de dados
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
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [a transformação de dados utilizando](../transform-data-using-hadoop-map-reduce.md)a atividade MapReduce na Fábrica de Dados .


O HDInsight MapReduce atividade num [pipeline](data-factory-create-pipelines.md) data factory executa programas MapReduce por [si só](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou a [pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) do cluster HDInsight baseado em HDInsight. Este artigo baseia-se no artigo sobre atividades de transformação de [dados,](data-factory-data-transformation-activities.md) que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

> [!NOTE] 
> Se é novo na Azure Data Factory, leia através da [Introdução à Azure Data Factory](data-factory-introduction.md) e faça o tutorial: [Construa](data-factory-build-your-first-pipeline.md) o seu primeiro pipeline de dados antes de ler este artigo.  

## <a name="introduction"></a>Introdução
Um oleoduto numa fábrica de dados azure processa dados em serviços de armazenamento ligados utilizando serviços de cálculo ligados. Contém uma sequência de atividades em que cada atividade realiza uma operação de processamento específica. Este artigo descreve a utilização do Mapa HDInsight Reduzir a Atividade.

Consulte [o Pig](data-factory-pig-activity.md) and [Hive](data-factory-hive-activity.md) para obter detalhes sobre a execução de scripts de Pig/Hive num cluster HDInsight baseado no Windows/Linux a partir de um pipeline utilizando atividades de Porco e Colmeia HDInsight. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON para HDInsight MapReduce Activity
Na definição JSON para a Atividade HDInsight: 

1. Defina o **tipo** de **atividade** para **HDInsight**.
2. Especifique o nome da classe para a propriedade **className.**
3. Especifique o caminho para o ficheiro JAR, incluindo o nome de ficheiro da propriedade **jarFilePath.**
4. Especifique o serviço ligado que se refere ao Armazenamento De Blob Azure que contém o ficheiro JAR para a propriedade **jarLinkedService.**   
5. Especifique quaisquer argumentos para o programa MapReduce na secção **de argumentos.** No tempo de execução, você vê alguns argumentos extra (por exemplo: mapreduce.job.tags) do quadro MapReduce. Para diferenciar os seus argumentos com os argumentos MapReduce, considere usar tanto a opção como o valor como argumentos como mostrado no exemplo seguinte (-s, --entrada, -- saída, etc., são opções imediatamente seguidas pelos seus valores).

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
   Pode utilizar o HDInsight MapReduce Activity para executar qualquer ficheiro mapReduce num cluster HDInsight. Na seguinte amostra, a definição JSON de um pipeline, a Atividade HDInsight é configurada para executar um ficheiro Mahout JAR.

## <a name="sample-on-github"></a>Amostra no GitHub
Pode descarregar uma amostra para utilizar o HDInsight MapReduce Activity a partir de: [Data Factory Samples on GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Executar o programa Word Count
O pipeline neste exemplo executa o programa Word Count Map/Reduce no seu cluster Azure HDInsight.   

### <a name="linked-services"></a>Serviços Ligados
Em primeiro lugar, cria-se um serviço ligado para ligar o Armazenamento Azure que é utilizado pelo cluster Azure HDInsight à fábrica de dados Azure. Se copiar/colar o seguinte código, não se esqueça de substituir o nome da **conta** e a chave da **conta** pelo nome e chave do seu Armazenamento Azure. 

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

#### <a name="azure-hdinsight-linked-service"></a>Serviço ligado azure HDInsight
Em seguida, cria um serviço ligado para ligar o seu cluster Azure HDInsight à fábrica de dados Azure. Se copiar/colar o seguinte código, substitua o nome do **cluster HDInsight** com o nome do seu cluster HDInsight e altere os valores de nome de utilizador e palavra-passe.   

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
O gasoduto neste exemplo não tem quaisquer inputs. Especifica um conjunto de dados de saída para o HDInsight MapReduce Activity. Este conjunto de dados é apenas um conjunto de dados de boneco que é necessário para impulsionar o calendário do gasoduto.  

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
O oleoduto neste exemplo tem apenas uma atividade que é de tipo: HDInsightMapReduce. Algumas das propriedades importantes no JSON são: 

| Propriedade | Notas |
|:--- |:--- |
| tipo |O tipo deve ser definido para **HDInsightMapReduce**. |
| className |O nome da classe é: **wordcount** |
| jarFilePath |Caminho para o arquivo do frasco que contém a classe. Se copiar/colar o seguinte código, não se esqueça de alterar o nome do cluster. |
| jarLinkedService |Serviço ligado ao Armazenamento Azure que contém o ficheiro do frasco. Este serviço ligado refere-se ao armazenamento que está associado ao cluster HDInsight. |
| argumentos |O programa wordcount requer dois argumentos, uma entrada e uma saída. O ficheiro de entrada é o ficheiro davinci.txt. |
| frequência/intervalo |Os valores destas propriedades correspondem ao conjunto de dados de saída. |
| linkedServiceName |refere-se ao serviço ligado ao HDInsight que tinha criado anteriormente. |

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

[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Veja também
* [Atividade da Colmeia](data-factory-hive-activity.md)
* [Atividade do Porco](data-factory-pig-activity.md)
* [Atividade de streaming de hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

