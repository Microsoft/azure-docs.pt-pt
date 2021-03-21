---
title: Transformar dados usando a atividade de Streaming Hadoop
description: Explica como usar a Atividade de Streaming Hadoop na Azure Data Factory para transformar dados executando programas de streaming Hadoop num cluster Hadoop.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: e2a9bc9d664ba15da3cdefa5cf28519ab703d6ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361440"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformar dados usando a atividade de Streaming Hadoop na Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Versão atual](transform-data-using-hadoop-streaming.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Atividade de Streaming HDInsight num [oleoduto](concepts-pipelines-activities.md) de Data Factory executa programas de streaming Hadoop no [seu próprio](compute-linked-services.md#azure-hdinsight-linked-service) ou cluster HDInsight a [pedido.](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Este artigo baseia-se no artigo de atividades de [transformação](transform-data.md) de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

Se é novo na Azure Data Factory, leia através [da Introdução à Fábrica de Dados Azure](introduction.md) e faça o [Tutorial: transforme dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

## <a name="json-sample"></a>Amostra JSON
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
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
| descrição       | Texto que descreve para que a atividade é usada | No       |
| tipo              | Para a Atividade de Streaming Hadoop, o tipo de atividade é HDInsightStreaming | Yes      |
| linkedServiceName | Referência ao cluster HDInsight registado como um serviço ligado na Data Factory. Para saber mais sobre este serviço ligado, consulte o artigo [de serviços ligados a Compute.](compute-linked-services.md) | Yes      |
| mapper            | Especifica o nome do mapper executável | Yes      |
| redutor           | Especifica o nome do redutor executável | Yes      |
| combiner          | Especifica o nome do combinador executável | No       |
| fileLinkedService | Referência a um Serviço Ligado ao Armazenamento Azure usado para armazenar os programas Mapper, Combiner e Redutor a serem executados. Apenas os serviços ligados a **[Azure Blob](./connector-azure-blob-storage.md)** e **[ADLS Gen2](./connector-azure-data-lake-storage.md)** são suportados aqui. Se não especificar este Serviço Linked, é utilizado o Serviço Ligado ao Armazenamento Azure definido no Serviço Linked HDInsight. | No       |
| filePath          | Forneça uma variedade de caminhos para os programas Mapper, Combiner e Redutor armazenados no Armazenamento Azure referido por fileLinkedService. O caminho é sensível a maiúsculas e minúsculas. | Yes      |
| entrada             | Especifica o caminho WASB para o ficheiro de entrada para o Mapper. | Yes      |
| saída            | Especifica o caminho WASB para o ficheiro de saída para o Redutor. | Yes      |
| obterDebugInfo      | Especifica quando os ficheiros de registo são copiados para o Azure Storage utilizado pelo cluster HDInsight (ou) especificado pelo scriptLinkedService. Valores permitidos: Nenhum, Sempre ou Fracasso. Valor predefinido: Nenhum. | No       |
| argumentos         | Especifica uma série de argumentos para um trabalho de Hadoop. Os argumentos são passados como argumentos de linha de comando para cada tarefa. | No       |
| define           | Especifique os parâmetros como pares chave/valor para referências dentro do script da Colmeia. | Não       | 

## <a name="next-steps"></a>Próximos passos
Veja os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade u-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade do porco](transform-data-using-hadoop-pig.md)
* [Atividade mapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade do Apache Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (clássico) Atividade de execução de lote](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)