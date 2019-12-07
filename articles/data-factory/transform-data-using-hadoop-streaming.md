---
title: Transformar dados usando a atividade de streaming do Hadoop
description: Explica como usar a atividade de streaming do Hadoop no Azure Data Factory para transformar dados executando programas de streaming do Hadoop em um cluster Hadoop.
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
ms.openlocfilehash: 01237218e30859015b1c8f0a5adeebd8b9bdf7eb
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893795"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformar dados usando a atividade de streaming do Hadoop no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Versão atual](transform-data-using-hadoop-streaming.md)

A atividade de streaming do HDInsight em um [pipeline](concepts-pipelines-activities.md) data Factory executa programas de streaming do Hadoop em [seu próprio](compute-linked-services.md#azure-hdinsight-linked-service) cluster HDInsight ou [sob demanda](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Este artigo se baseia no artigo [atividades de transformação de dados](transform-data.md) , que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte.

Se você for novo no Azure Data Factory, leia a [introdução ao Azure data Factory](introduction.md) e faça o [tutorial: transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

## <a name="json-sample"></a>Exemplo de JSON
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

| Propriedade          | Descrição                              | Obrigatório |
| ----------------- | ---------------------------------------- | -------- |
| nome              | Nome da atividade                     | Sim      |
| descrição       | Texto que descreve para que a atividade é usada | Não       |
| tipo              | Para a atividade de streaming do Hadoop, o tipo de atividade é HDInsightStreaming | Sim      |
| linkedServiceName | Referência ao cluster HDInsight registrado como um serviço vinculado no Data Factory. Para saber mais sobre esse serviço vinculado, consulte o artigo [Serviços vinculados de computação](compute-linked-services.md) . | Sim      |
| mapea            | Especifica o nome do executável do mapeador | Sim      |
| redutor           | Especifica o nome do executável redutor | Sim      |
| combinador          | Especifica o nome do executável do combinador | Não       |
| fileLinkedService | Referência a um serviço vinculado do armazenamento do Azure usado para armazenar os programas mapeador, combinador e redutor a serem executados. Se você não especificar esse serviço vinculado, o serviço vinculado do armazenamento do Azure definido no serviço vinculado do HDInsight será usado. | Não       |
| filePath          | Forneça uma matriz de caminho para os programas mapeador, combinador e redutor armazenados no armazenamento do Azure referenciado por fileLinkedService. O caminho é sensível a maiúsculas e minúsculas. | Sim      |
| entrada             | Especifica o caminho WASB para o arquivo de entrada para o mapeador. | Sim      |
| saída            | Especifica o caminho WASB para o arquivo de saída para o redutor. | Sim      |
| getDebugInfo      | Especifica quando os arquivos de log são copiados para o armazenamento do Azure usado pelo cluster HDInsight (ou) especificado por scriptLinkedService. Valores permitidos: nenhum, sempre ou falha. Valor padrão: nenhum. | Não       |
| arguments         | Especifica uma matriz de argumentos para um trabalho do Hadoop. Os argumentos são passados como argumentos de linha de comando para cada tarefa. | Não       |
| defines           | Especifique parâmetros como pares de chave/valor para referência no script do hive. | Não       | 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras: 

* [Atividade de U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do hive](transform-data-using-hadoop-hive.md)
* [Atividade Pig](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de Machine Learning lote](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
