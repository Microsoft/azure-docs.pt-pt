---
title: Transformar dados usando a atividade MapReduce do Hadoop
description: Saiba como processar dados executando programas de MapReduce do Hadoop em um cluster do Azure HDInsight de um data factory do Azure.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 5d38e3126442bcf34c96cead2b2ea59507b50b8c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912869"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transformar dados usando a atividade MapReduce do Hadoop no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-map-reduce.md)
> * [Versão atual](transform-data-using-hadoop-map-reduce.md)

A atividade de MapReduce do HDInsight em um [pipeline](concepts-pipelines-activities.md) data Factory invoca o programa MapReduce em [seu próprio](compute-linked-services.md#azure-hdinsight-linked-service) cluster HDInsight ou [sob demanda](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Este artigo se baseia no artigo [atividades de transformação de dados](transform-data.md) , que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte.

Se você for novo no Azure Data Factory, leia a [introdução ao Azure data Factory](introduction.md) e faça o tutorial: [tutorial: transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo.

Consulte [Pig](transform-data-using-hadoop-pig.md) e [Hive](transform-data-using-hadoop-hive.md) para obter detalhes sobre como executar scripts do Pig/Hive em um cluster HDInsight de um pipeline usando as atividades Pig do hdinsight e Hive.

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Detalhes da sintaxe

| Propriedade          | Descrição                              | Obrigatório |
| ----------------- | ---------------------------------------- | -------- |
| nome              | Nome da atividade                     | Sim      |
| descrição       | Texto que descreve para que a atividade é usada | Não       |
| tipo              | Para a atividade MapReduce, o tipo de atividade é HDinsightMapReduce | Sim      |
| linkedServiceName | Referência ao cluster HDInsight registrado como um serviço vinculado no Data Factory. Para saber mais sobre esse serviço vinculado, consulte o artigo [Serviços vinculados de computação](compute-linked-services.md) . | Sim      |
| className         | Nome da classe a ser executada         | Sim      |
| jarLinkedService  | Referência a um serviço vinculado do armazenamento do Azure usado para armazenar os arquivos jar. Se você não especificar esse serviço vinculado, o serviço vinculado do armazenamento do Azure definido no serviço vinculado do HDInsight será usado. | Não       |
| jarFilePath       | Forneça o caminho para os arquivos jar armazenados no armazenamento do Azure referenciado por jarLinkedService. O nome do arquivo diferencia maiúsculas de minúsculas. | Sim      |
| jarlibs           | Matriz de cadeia de caracteres do caminho para os arquivos de biblioteca jar referenciados pelo trabalho armazenado no armazenamento do Azure definido em jarLinkedService. O nome do arquivo diferencia maiúsculas de minúsculas. | Não       |
| getDebugInfo      | Especifica quando os arquivos de log são copiados para o armazenamento do Azure usado pelo cluster HDInsight (ou) especificado por jarLinkedService. Valores permitidos: nenhum, sempre ou falha. Valor padrão: nenhum. | Não       |
| arguments         | Especifica uma matriz de argumentos para um trabalho do Hadoop. Os argumentos são passados como argumentos de linha de comando para cada tarefa. | Não       |
| defines           | Especifique parâmetros como pares de chave/valor para referência no script do hive. | Não       |



## <a name="example"></a>Exemplo
Você pode usar a atividade de MapReduce do HDInsight para executar qualquer arquivo JAR do MapReduce em um cluster HDInsight. Na definição de JSON de exemplo a seguir de um pipeline, a atividade do HDInsight é configurada para executar um arquivo JAR Mahout.

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
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
    }
}
```
Você pode especificar quaisquer argumentos para o programa MapReduce na seção **argumentos** . Em tempo de execução, você verá alguns argumentos extras (por exemplo: MapReduce. Job. Tags) da estrutura MapReduce. Para diferenciar seus argumentos com os argumentos MapReduce, considere o uso de Option e Value como argumentos, conforme mostrado no exemplo a seguir (-s,--Input,--output etc., são opções imediatamente seguidas por seus valores).

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras:

* [Atividade de U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do hive](transform-data-using-hadoop-hive.md)
* [Atividade Pig](transform-data-using-hadoop-pig.md)
* [Atividade de streaming do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de Machine Learning lote](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
