---
title: Transforme dados utilizando a atividade do Porco Hadoop
description: Saiba como pode usar a Atividade do Porco numa fábrica de dados Azure para executar scripts de porco num cluster HDInsight a pedido/seu próprio HDInsight.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 4064d62a6dc826b23ff1f51e9f61e48d362ae695
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912940"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Transforme dados utilizando a atividade do Porco Hadoop na Fábrica de Dados Azure

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-pig-activity.md)
> * [Versão atual](transform-data-using-hadoop-pig.md)

A atividade do Suíno HDInsight num [gasoduto](concepts-pipelines-activities.md) data factory executa consultas de porco por [si só](compute-linked-services.md#azure-hdinsight-linked-service) ou a [pedido](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) do cluster HDInsight. Este artigo baseia-se no artigo sobre atividades de transformação de [dados,](transform-data.md) que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

Se é novo na Azure Data Factory, leia através da [Introdução à Azure Data Factory](introduction.md) e faça o [Tutorial: transforme os dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
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

| Propriedade            | Descrição                              | Necessário |
| ------------------- | ---------------------------------------- | -------- |
| nome                | Nome da atividade                     | Sim      |
| descrição         | Texto descrevendo para que a atividade é usada | Não       |
| tipo                | Para a Atividade da Colmeia, o tipo de atividade é HDinsightPig | Sim      |
| linkedServiceName   | Referência ao cluster HDInsight registado como um serviço ligado na Data Factory. Para conhecer este serviço ligado, consulte o artigo de [serviços ligados à Compute.](compute-linked-services.md) | Sim      |
| scriptLinkedService | Referência a um Serviço Ligado ao Armazenamento Azure usado para armazenar o script Pig a ser executado. Se não especificar este Serviço Linked, o Serviço Ligado ao Armazenamento Azure definido no Serviço Ligado ao HDInsight é utilizado. | Não       |
| scriptPath          | Forneça o caminho para o ficheiro script armazenado no Armazenamento Azure referido pelo scriptLinkedService. O nome do ficheiro é sensível ao caso. | Não       |
| getDebugInfo        | Especifica quando os ficheiros de registo são copiados para o Armazenamento Azure utilizado pelo cluster HDInsight (ou) especificado pelo scriptLinkedService. Valores permitidos: Nenhum, sempre ou falha. Valor predefinido: Nenhum. | Não       |
| argumentos           | Especifica uma série de argumentos para um trabalho de Hadoop. Os argumentos são passados como argumentos de linha de comando para cada tarefa. | Não       |
| define             | Especifique os parâmetros como par de chaves/valor para referência dentro do script Pig. | Não       |

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [MapReduzir a atividade](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lote de aprendizagem automática](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
