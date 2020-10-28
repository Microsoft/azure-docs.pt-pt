---
title: Transformar dados usando a atividade Hadoop MapReduce
description: Aprenda a processar dados executando os programas Hadoop MapReduce num cluster Azure HDInsight a partir de uma fábrica de dados Azure.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: 7685c2075b371ce22930b14187c27bcb0879c962
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632027"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transforme dados usando a atividade hadoop MapReduce na Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-map-reduce.md)
> * [Versão atual](transform-data-using-hadoop-map-reduce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade HDInsight MapReduce num [oleoduto](concepts-pipelines-activities.md) da Data Factory invoca o programa MapReduce no [seu próprio](compute-linked-services.md#azure-hdinsight-linked-service) ou a [pedido do](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster HDInsight. Este artigo baseia-se no artigo de atividades de [transformação](transform-data.md) de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

Se é novo na Azure Data Factory, leia através [da Introdução à Fábrica de Dados Azure](introduction.md) e faça o tutorial: [Tutorial: transforme dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo.

Consulte [o Pig](transform-data-using-hadoop-pig.md) and [Hive](transform-data-using-hadoop-hive.md) para obter detalhes sobre a execução de scripts de Porco/Colmeia num cluster HDInsight a partir de um oleoduto utilizando atividades de Porco e Colmeia HDInsight.

## <a name="syntax"></a>Syntax

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
| name              | Nome da atividade                     | Sim      |
| descrição       | Texto que descreve para que a atividade é usada | Não       |
| tipo              | Para a Atividade mapReduce, o tipo de atividade é HDinsightMapReduce | Sim      |
| linkedServiceName | Referência ao cluster HDInsight registado como um serviço ligado na Data Factory. Para saber mais sobre este serviço ligado, consulte o artigo [de serviços ligados a Compute.](compute-linked-services.md) | Sim      |
| nome de classeName         | Nome da Classe a executar         | Sim      |
| jarLinkedService  | Referência a um Serviço Ligado ao Armazenamento Azure utilizado para armazenar os ficheiros Jar. Apenas os serviços ligados a **[Azure Blob](./connector-azure-blob-storage.md)** e **[ADLS Gen2](./connector-azure-data-lake-storage.md)** são suportados aqui. Se não especificar este Serviço Linked, é utilizado o Serviço Ligado ao Armazenamento Azure definido no Serviço Linked HDInsight. | Não       |
| jarFilePath       | Forneça o caminho para os ficheiros Jar armazenados no Azure Storage referido pelo jarLinkedService. O nome do ficheiro é sensível a casos. | Sim      |
| jarlibs           | Array de corda do caminho para os ficheiros da biblioteca Jar referenciados pelo trabalho armazenado no Azure Storage definido no jarLinkedService. O nome do ficheiro é sensível a casos. | Não       |
| obterDebugInfo      | Especifica quando os ficheiros de registo são copiados para o Azure Storage utilizado pelo cluster HDInsight (ou) especificado pelo jarLinkedService. Valores permitidos: Nenhum, Sempre ou Fracasso. Valor predefinido: Nenhum. | Não       |
| argumentos         | Especifica uma série de argumentos para um trabalho de Hadoop. Os argumentos são passados como argumentos de linha de comando para cada tarefa. | Não       |
| define           | Especifique os parâmetros como pares chave/valor para referências dentro do script da Colmeia. | Não       |



## <a name="example"></a>Exemplo
Pode utilizar a Atividade de MapReduce HDInsight para executar qualquer ficheiro de frasco MapReduce num cluster HDInsight. Na seguinte amostra, a definição JSON de um oleoduto, a Atividade HDInsight está configurada para executar um ficheiro Mahout JAR.

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
Pode especificar quaisquer argumentos para o programa MapReduce na secção **de argumentos.** No tempo de execução, você vê alguns argumentos extra (por exemplo: mapreduce.job.tags) a partir do quadro MapReduce. Para diferenciar os seus argumentos com os argumentos MapReduce, considere usar a opção e o valor como argumentos como mostrados no exemplo seguinte (,--inputação,--output etc., são opções imediatamente seguidas pelos seus valores).

## <a name="next-steps"></a>Passos seguintes
Veja os seguintes artigos que explicam como transformar dados de outras formas:

* [Atividade u-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade do porco](transform-data-using-hadoop-pig.md)
* [Atividade de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (clássico) Atividade de execução de lote](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)