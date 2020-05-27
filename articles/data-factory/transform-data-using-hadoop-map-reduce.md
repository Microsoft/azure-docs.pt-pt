---
title: Transformar dados usando mapas hadoopReduzir a atividade
description: Saiba como processar dados executando programas Hadoop MapReduce num cluster Azure HDInsight de uma fábrica de dados Azure.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: 48afff71d4b5241ede1783a270658e56e4b8c242
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849251"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transforme dados usando mapas hadoopReduzir a atividade na Fábrica de Dados Azure

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-map-reduce.md)
> * [Versão atual](transform-data-using-hadoop-map-reduce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O HDInsight MapReduce atividade num [pipeline](concepts-pipelines-activities.md) data factory invoca o programa MapReduce por [conta própria](compute-linked-services.md#azure-hdinsight-linked-service) ou a [pedido](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) do cluster HDInsight. Este artigo baseia-se no artigo sobre atividades de transformação de [dados,](transform-data.md) que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

Se é novo na Azure Data Factory, leia através da [Introdução à Azure Data Factory](introduction.md) e faça o tutorial: [Tutorial: transforme dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo.

Consulte [o Pig](transform-data-using-hadoop-pig.md) and [Hive](transform-data-using-hadoop-hive.md) para obter detalhes sobre a execução de scripts de porco/colmeia num cluster HDInsight a partir de um oleoduto utilizando atividades de Porco e Colmeia HDInsight.

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

| Propriedade          | Descrição                              | Necessário |
| ----------------- | ---------------------------------------- | -------- |
| name              | Nome da atividade                     | Yes      |
| descrição       | Texto descrevendo para que a atividade é usada | No       |
| tipo              | Para mapReduce Activity, o tipo de atividade é HDinsightMapReduce | Yes      |
| linkedServiceName | Referência ao cluster HDInsight registado como um serviço ligado na Data Factory. Para conhecer este serviço ligado, consulte o artigo de [serviços ligados à Compute.](compute-linked-services.md) | Yes      |
| className         | Nome da Classe a ser executado         | Yes      |
| jarLinkedService  | Referência a um Serviço Ligado ao Armazenamento Azure usado para armazenar os ficheiros Jar. Apenas os serviços ligados ao **[Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)** e **[ADLS Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)** são suportados aqui. Se não especificar este Serviço Linked, o Serviço Ligado ao Armazenamento Azure definido no Serviço Ligado ao HDInsight é utilizado. | No       |
| jarFilePath       | Forneça o caminho para os ficheiros Jar armazenados no Armazenamento Azure referidos pelo jarLinkedService. O nome do ficheiro é sensível ao caso. | Yes      |
| jarlibs           | Conjunto de cordas do caminho para os ficheiros da biblioteca Jar referenciados pelo trabalho armazenado no Armazenamento Azure definido no jarLinkedService. O nome do ficheiro é sensível ao caso. | No       |
| getDebugInfo      | Especifica quando os ficheiros de registo são copiados para o Armazenamento Azure utilizado pelo cluster HDInsight (ou) especificado pelo jarLinkedService. Valores permitidos: Nenhum, sempre ou falha. Valor predefinido: Nenhum. | No       |
| argumentos         | Especifica uma série de argumentos para um trabalho de Hadoop. Os argumentos são passados como argumentos de linha de comando para cada tarefa. | No       |
| define           | Especifique os parâmetros como par de chaves/valor para referência dentro do script da Colmeia. | No       |



## <a name="example"></a>Exemplo
Pode utilizar o HDInsight MapReduce Activity para executar qualquer ficheiro mapReduce num cluster HDInsight. Na seguinte amostra, a definição JSON de um pipeline, a Atividade HDInsight é configurada para executar um ficheiro Mahout JAR.

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
Pode especificar quaisquer argumentos para o programa MapReduce na secção **de argumentos.** No tempo de execução, você vê alguns argumentos extra (por exemplo: mapreduce.job.tags) do quadro MapReduce. Para diferenciar os seus argumentos com os argumentos MapReduce, considere usar tanto a opção como o valor como argumentos como mostrado no exemplo seguinte (-s,--inprodução,--produção etc., são opções imediatamente seguidas pelos seus valores).

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas:

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade de porco](transform-data-using-hadoop-pig.md)
* [Atividade de streaming de hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lote de aprendizagem automática](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
