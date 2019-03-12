---
title: Transformar dados com a atividade MapReduce do Hadoop no Azure Data Factory | Documentos da Microsoft
description: Saiba como processar os dados ao executar programas de MapReduce do Hadoop num cluster HDInsight do Azure a partir de uma fábrica de dados do Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: ccc194dd4120762a30da3ad28cdabed6faf53ba2
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576400"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transformar dados com a atividade MapReduce do Hadoop no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-map-reduce.md)
> * [Versão atual](transform-data-using-hadoop-map-reduce.md)

A atividade de MapReduce do HDInsight numa fábrica de dados [pipeline](concepts-pipelines-activities.md) invoca o programa MapReduce no [seu próprio](compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight. Este artigo baseia-se a [atividades de transformação de dados](transform-data.md) artigo, que apresenta uma visão geral de transformação de dados e as atividades de transformação suportados.

Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](introduction.md) e fazer o tutorial: [Tutorial: transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo.

Ver [Pig](transform-data-using-hadoop-pig.md) e [Hive](transform-data-using-hadoop-hive.md) para obter detalhes sobre o Pig/Hive a executar scripts num HDInsight cluster partir de um pipeline com o HDInsight Pig e Hive atividades.

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
| name              | Nome da atividade                     | Sim      |
| descrição       | Texto que descreve o que a atividade é utilizada para | Não       |
| tipo              | Para a atividade de MapReduce, o tipo de atividade é HDinsightMapReduce | Sim      |
| linkedServiceName | Referência para o cluster de HDInsight registado como um serviço ligado no Data Factory. Para saber mais sobre este serviço ligado, veja [serviços ligados de computação](compute-linked-services.md) artigo. | Sim      |
| className         | Nome da classe a ser executado         | Sim      |
| jarLinkedService  | Referência para um serviço ligado de armazenamento do Azure utilizada para armazenar os ficheiros Jar. Se não especificar este serviço ligado, o serviço ligado de armazenamento de Azure definido no serviço ligado do HDInsight é utilizado. | Não       |
| jarFilePath       | Forneça o caminho para os ficheiros Jar armazenados no armazenamento do Azure referenciado pela jarLinkedService. O nome de ficheiro diferencia maiúsculas de minúsculas. | Sim      |
| jarlibs           | Matriz do caminho para os ficheiros da biblioteca Jar referenciado pela tarefa armazenada no armazenamento do Azure, definido no jarLinkedService de cadeias de caracteres. O nome de ficheiro diferencia maiúsculas de minúsculas. | Não       |
| getDebugInfo      | Especifica quando os arquivos de log são copiados para o armazenamento do Azure utilizado pelo cluster do HDInsight (ou) especificado pelo jarLinkedService. Valores permitidos: Nenhum, sempre, ou a falha. Valor predefinido: Nenhum. | Não       |
| argumentos         | Especifica uma matriz de argumentos para uma tarefa do Hadoop. Os argumentos são transmitidos como argumentos da linha de comandos para cada tarefa. | Não       |
| Define           | Especifique parâmetros como pares chave/valor para referenciar a dentro do script do Hive. | Não       |



## <a name="example"></a>Exemplo
Pode usar a atividade de MapReduce do HDInsight para executar qualquer ficheiro. jar do MapReduce num cluster do HDInsight. A seguinte definição de JSON exemplo de um pipeline, a atividade do HDInsight está configurada para executar um ficheiro Mahout JAR.

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
Pode especificar qualquer argumentos para o programa de MapReduce na **argumentos** secção. No tempo de execução, verá alguns argumentos adicionais (por exemplo: mapreduce.job.tags) do MapReduce framework. Para diferenciar os argumentos com os argumentos do MapReduce, considere utilizar a opção e o valor como argumentos, conforme mostrado no exemplo a seguir (- s, - entrada, – resultado etc., são as opções seguidas imediatamente pelos respetivos valores).

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas:

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do Hive](transform-data-using-hadoop-hive.md)
* [Atividade PIg](transform-data-using-hadoop-pig.md)
* [Atividade de transmissão em fluxo do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lotes de Aprendizado de máquina](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
