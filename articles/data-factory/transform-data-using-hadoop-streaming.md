---
title: Transforme dados utilizando a atividade de Streaming Hadoop
description: Explica como usar a Atividade de Streaming Hadoop na Azure Data Factory para transformar dados executando programas de streaming hadoop num cluster Hadoop.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: c1bba6903fe1cb8cc5bae9a12153553594180b43
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418886"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transforme dados utilizando a atividade de streaming de Hadoop na Fábrica de Dados Azure
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Versão atual](transform-data-using-hadoop-streaming.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Atividade de Streaming HDInsight num [pipeline](concepts-pipelines-activities.md) data factory executa programas de streaming hadoop por [conta própria](compute-linked-services.md#azure-hdinsight-linked-service) ou a [pedido](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) do cluster HDInsight. Este artigo baseia-se no artigo sobre atividades de transformação de [dados,](transform-data.md) que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

Se é novo na Azure Data Factory, leia através da [Introdução à Azure Data Factory](introduction.md) e faça o [Tutorial: transforme os dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

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
| nome              | Nome da atividade                     | Sim      |
| descrição       | Texto descrevendo para que a atividade é usada | Não       |
| tipo              | Para a Atividade de Streaming de Hadoop, o tipo de atividade é HDInsightStreaming | Sim      |
| linkedServiceName | Referência ao cluster HDInsight registado como um serviço ligado na Data Factory. Para conhecer este serviço ligado, consulte o artigo de [serviços ligados à Compute.](compute-linked-services.md) | Sim      |
| mapper            | Especifica o nome do mapeador executável | Sim      |
| redutor           | Especifica o nome do redutor executável | Sim      |
| combinador          | Especifica o nome do combinador executável | Não       |
| ficheiroSLinkedService | Referência a um Serviço Ligado ao Armazenamento Azure usado para armazenar os programas Mapper, Combiner e Reducer a serem executados. Se não especificar este Serviço Linked, o Serviço Ligado ao Armazenamento Azure definido no Serviço Ligado ao HDInsight é utilizado. | Não       |
| filePath          | Forneça uma variedade de caminhos para os programas Mapper, Combiner e Reducer armazenados no Armazenamento Azure referido saqueado por fileLinkedService. O caminho é sensível a maiúsculas e minúsculas. | Sim      |
| entrada             | Especifica o caminho WASB para o ficheiro de entrada do Mapper. | Sim      |
| saída            | Especifica o caminho WASB para o ficheiro de saída do Redutor. | Sim      |
| getDebugInfo      | Especifica quando os ficheiros de registo são copiados para o Armazenamento Azure utilizado pelo cluster HDInsight (ou) especificado pelo scriptLinkedService. Valores permitidos: Nenhum, sempre ou falha. Valor predefinido: Nenhum. | Não       |
| argumentos         | Especifica uma série de argumentos para um trabalho de Hadoop. Os argumentos são passados como argumentos de linha de comando para cada tarefa. | Não       |
| define           | Especifique os parâmetros como par de chaves/valor para referência dentro do script da Colmeia. | Não       | 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade de porco](transform-data-using-hadoop-pig.md)
* [MapReduzir a atividade](transform-data-using-hadoop-map-reduce.md)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lote de aprendizagem automática](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
