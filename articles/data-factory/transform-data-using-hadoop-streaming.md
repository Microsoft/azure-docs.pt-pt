---
title: Transformar dados com a atividade de transmissão em fluxo do Hadoop no Azure Data Factory | Documentos da Microsoft
description: Explica como utilizar a atividade de transmissão em fluxo Hadoop no Azure Data Factory para transformar dados através da execução de programas de transmissão em fluxo do Hadoop num cluster do Hadoop.
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
ms.openlocfilehash: 0d8267f1cd65f78d5e98ae9d288d5fa5c4214420
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576434"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformar dados com a atividade de transmissão em fluxo do Hadoop no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Versão atual](transform-data-using-hadoop-streaming.md)

A atividade Streaming do HDInsight numa fábrica de dados [pipeline](concepts-pipelines-activities.md) executa programas de transmissão em fluxo do Hadoop no [seu próprio](compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight. Este artigo baseia-se a [atividades de transformação de dados](transform-data.md) artigo, que apresenta uma visão geral de transformação de dados e as atividades de transformação suportados.

Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](introduction.md) e faça a [Tutorial: transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

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

## <a name="syntax-details"></a>Detalhes da sintaxe

| Propriedade          | Descrição                              | Necessário |
| ----------------- | ---------------------------------------- | -------- |
| name              | Nome da atividade                     | Sim      |
| descrição       | Texto que descreve o que a atividade é utilizada para | Não       |
| tipo              | Para a atividade de transmissão em fluxo do Hadoop, o tipo de atividade é HDInsightStreaming | Sim      |
| linkedServiceName | Referência para o cluster de HDInsight registado como um serviço ligado no Data Factory. Para saber mais sobre este serviço ligado, veja [serviços ligados de computação](compute-linked-services.md) artigo. | Sim      |
| Mapeador de pontos            | Especifica o nome do executável o mapeador de pontos | Sim      |
| reducer           | Especifica o nome do reducer executável | Sim      |
| combinação          | Especifica o nome da combinação executável | Não       |
| fileLinkedService | Referência para um serviço ligado de armazenamento do Azure utilizada para armazenar os mapeador de combinação e Reducer a execução de programas. Se não especificar este serviço ligado, o serviço ligado de armazenamento de Azure definido no serviço ligado do HDInsight é utilizado. | Não       |
| filePath          | Forneça uma matriz de caminho para o mapeador de pontos, combinação, e programas Reducer armazenados no armazenamento do Azure referenciado pela fileLinkedService. O caminho é sensível a maiúsculas e minúsculas. | Sim      |
| entrada             | Especifica o caminho WASB para o ficheiro de entrada para o mapeador de pontos. | Sim      |
| saída            | Especifica o caminho WASB para o ficheiro de saída para o Reducer. | Sim      |
| getDebugInfo      | Especifica quando os arquivos de log são copiados para o armazenamento do Azure utilizado pelo cluster do HDInsight (ou) especificado pelo scriptLinkedService. Valores permitidos: Nenhum, sempre, ou a falha. Valor predefinido: Nenhum. | Não       |
| argumentos         | Especifica uma matriz de argumentos para uma tarefa do Hadoop. Os argumentos são transmitidos como argumentos da linha de comandos para cada tarefa. | Não       |
| Define           | Especifique parâmetros como pares chave/valor para referenciar a dentro do script do Hive. | Não       | 

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do Hive](transform-data-using-hadoop-hive.md)
* [Atividade PIg](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lotes de Aprendizado de máquina](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
