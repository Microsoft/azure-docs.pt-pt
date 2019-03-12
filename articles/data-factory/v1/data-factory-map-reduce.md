---
title: Invocar programas MapReduce de fábrica de dados do Azure
description: Saiba como processar dados executando programas MapReduce num cluster HDInsight do Azure a partir de uma fábrica de dados do Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: ccf67aab2d393d4ee820c19e839ff3076712843d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550076"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Invocar programas MapReduce a partir do Data Factory
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
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [transformar dados com a atividade de MapReduce no Data Factory](../transform-data-using-hadoop-map-reduce.md).


A atividade de MapReduce do HDInsight numa fábrica de dados [pipeline](data-factory-create-pipelines.md) executa programas MapReduce no [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight baseado em Windows/Linux. Este artigo baseia-se a [atividades de transformação de dados](data-factory-data-transformation-activities.md) artigo, que apresenta uma visão geral de transformação de dados e as atividades de transformação suportados.

> [!NOTE] 
> Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](data-factory-introduction.md) e fazer o tutorial: [Crie seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo.  

## <a name="introduction"></a>Introdução
Um pipeline de uma fábrica de dados do Azure processa os dados nos serviços de armazenamento ligada com os serviços ligados de computação. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específico. Este artigo descreve usando a atividade de MapReduce do HDInsight.

Ver [Pig](data-factory-pig-activity.md) e [Hive](data-factory-hive-activity.md) para obter detalhes sobre o Pig/Hive a executar scripts num HDInsight baseado em Windows/Linux cluster partir de um pipeline com o HDInsight Pig e Hive atividades. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON para a atividade MapReduce do HDInsight
Na definição de JSON para a atividade do HDInsight: 

1. Definir o **tipo** da **atividade** para **HDInsight**.
2. Especifique o nome da classe para **className** propriedade.
3. Especifique o caminho para o ficheiro JAR, incluindo o nome de ficheiro para **jarFilePath** propriedade.
4. Especifique o serviço ligado que referencia o armazenamento de Blobs do Azure que contém o ficheiro JAR para **jarLinkedService** propriedade.   
5. Especifique os argumentos para o programa de MapReduce na **argumentos** secção. No tempo de execução, verá alguns argumentos adicionais (por exemplo: mapreduce.job.tags) do MapReduce framework. Para diferenciar os argumentos com os argumentos do MapReduce, considere utilizar a opção e o valor como argumentos, conforme mostrado no exemplo a seguir (- s, - entrada, – resultado etc., são as opções seguidas imediatamente pelos respetivos valores).

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
Pode usar a atividade de MapReduce do HDInsight para executar qualquer ficheiro. jar do MapReduce num cluster do HDInsight. A seguinte definição de JSON exemplo de um pipeline, a atividade do HDInsight está configurada para executar um ficheiro Mahout JAR.

## <a name="sample-on-github"></a>Exemplo no GitHub
Pode baixar um exemplo para utilizar a atividade MapReduce do HDInsight: [Amostras do Data Factory no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Executar o programa de contagem de palavras
O pipeline neste exemplo é executado o programa de mapa/redução de contagem de palavras no seu cluster Azure HDInsight.   

### <a name="linked-services"></a>Serviços Ligados
Em primeiro lugar, criar um serviço ligado para ligar o armazenamento do Azure que é utilizado pelo cluster do HDInsight do Azure à fábrica de dados do Azure. Se copiar/colar o código a seguir, não se esqueça de substituir **nome da conta** e **chave de conta** com o nome e chave de armazenamento do Azure. 

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

#### <a name="azure-hdinsight-linked-service"></a>Serviço ligado de HDInsight do Azure
Em seguida, criar um serviço ligado para ligar o seu cluster do HDInsight do Azure à fábrica de dados do Azure. Se copiar/colar o código a seguir, substitua **nome do cluster de HDInsight** com o nome do cluster do HDInsight e a alteração de valores de nome e palavra-passe de utilizador.   

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
O pipeline neste exemplo não tem quaisquer entradas. Especifique um conjunto de dados de saída para a atividade de MapReduce do HDInsight. Este conjunto de dados é apenas um dataset fictício que é necessário para orientar a agenda do pipeline.  

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
| className |Nome da classe é: **wordcount** |
| jarFilePath |Caminho para o ficheiro jar que contém a classe. Se copiar/colar o código a seguir, não se esqueça de alterar o nome do cluster. |
| jarLinkedService |Serviço ligado do armazenamento do Azure que contém o ficheiro jar. Este serviço ligado refere-se para o armazenamento que está associado ao HDInsight cluster. |
| argumentos |O programa de wordcount leva dois argumentos, entrada e saída. O ficheiro de entrada é o arquivo de davinci.txt. |
| frequência/intervalo |Os valores para estas propriedades corresponder o conjunto de dados de saída. |
| linkedServiceName |refere-se ao serviço ligado de HDInsight tivesse criado anteriormente. |

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

## <a name="see-also"></a>Consultar Também
* [Atividade do Hive](data-factory-hive-activity.md)
* [Atividade PIg](data-factory-pig-activity.md)
* [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

