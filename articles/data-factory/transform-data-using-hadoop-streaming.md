---
title: Transformar dados utilizando a atividade de transmissão em fluxo do Hadoop no Azure Data Factory | Microsoft Docs
description: Explica como utilizar a atividade de transmissão em fluxo do Hadoop no Azure Data Factory para transformar dados através da execução de programas de transmissão em fluxo do Hadoop num cluster de Hadoop.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 4c2bf83fec3d8f961a84523365e4a98fe3bf7603
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052372"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformar dados utilizando a atividade de transmissão em fluxo do Hadoop no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Versão atual](transform-data-using-hadoop-streaming.md)

A atividade de transmissão em fluxo do HDInsight numa fábrica de dados [pipeline](concepts-pipelines-activities.md) executa programas de transmissão em fluxo do Hadoop no [os seus próprios](compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight. Este artigo baseia-se a [atividades de transformação de dados](transform-data.md) artigo, que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportados.

Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](introduction.md) e efetue a [Tutorial: transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

## <a name="json-sample"></a>Exemplo JSON
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

## <a name="syntax-details"></a>Detalhes de sintaxe

| Propriedade          | Descrição                              | Necessário |
| ----------------- | ---------------------------------------- | -------- |
| nome              | Nome da atividade                     | Sim      |
| descrição       | Texto que descreve o que é utilizada a atividade para | Não       |
| tipo              | Para a atividade de transmissão em fluxo do Hadoop, o tipo de atividade é HDInsightStreaming | Sim      |
| linkedServiceName | Referência para o cluster do HDInsight registado como um serviço ligado no Factory de dados. Para saber mais sobre este serviço ligado, consulte [serviços ligados de computação](compute-linked-services.md) artigo. | Sim      |
| Mapeador de            | Especifica o nome do mapeador executável | Sim      |
| reducer           | Especifica o nome do reducer executável | Sim      |
| combinação          | Especifica o nome da combinação executável | Não       |
| fileLinkedService | Referência a um serviço ligado do Storage do Azure utilizados para armazenar os programas mapeador, combinação e Reducer para ser executada. Se não especificar este serviço ligado, o serviço ligado de armazenamento de Azure definido no serviço ligado do HDInsight é utilizado. | Não       |
| filePath          | Forneça uma matriz de caminho para o mapeador, combinação, e programas Reducer armazenados no Storage do Azure referido pela fileLinkedService. O caminho é sensível a maiúsculas e minúsculas. | Sim      |
| entrada             | Especifica o caminho WASB para o ficheiro de entrada para o mapeador de pontos. | Sim      |
| saída            | Especifica o caminho WASB para o ficheiro de saída para o Reducer. | Sim      |
| getDebugInfo      | Especifica se os ficheiros de registo são copiados para o armazenamento do Azure utilizados pelo cluster do HDInsight (ou) especificado pelo scriptLinkedService. Valores permitidos: None, sempre ou falha. Valor predefinido: nenhuma. | Não       |
| argumentos         | Especifica uma matriz de argumentos para uma tarefa do Hadoop. Os argumentos são transmitidos como argumentos da linha de comandos para cada tarefa. | Não       |
| Define           | Especifique parâmetros como pares chave-valor para referenciar dentro do script de ramo de registo. | Não       | 

## <a name="next-steps"></a>Passos Seguintes
Consulte os artigos seguintes que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do ramo de registo](transform-data-using-hadoop-hive.md)
* [Atividade do PIg](transform-data-using-hadoop-pig.md)
* [Atividade de MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lote de aprendizagem máquina](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
