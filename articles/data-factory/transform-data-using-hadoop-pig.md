---
title: Transformar dados usando a atividade do Porco Hadoop
description: Saiba como pode usar a Atividade do Porco numa fábrica de dados Azure para executar scripts de porco num cluster hdinsight a pedido/seu próprio HDInsight.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: 8b6f75204f64f892f80abb4890796551bffd22a7
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368966"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Transformar dados usando a atividade do Hadoop Pig na Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-pig-activity.md)
> * [Versão atual](transform-data-using-hadoop-pig.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade do Porco HDInsight num [oleoduto](concepts-pipelines-activities.md) da Data Factory executa consultas de porco no [seu próprio](compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido do](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster HDInsight. Este artigo baseia-se no artigo de atividades de [transformação](transform-data.md) de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

Se é novo na Azure Data Factory, leia através [da Introdução à Fábrica de Dados Azure](introduction.md) e faça o [Tutorial: transforme dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

## <a name="syntax"></a>Syntax

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

| Propriedade            | Descrição                              | Obrigatório |
| ------------------- | ---------------------------------------- | -------- |
| name                | Nome da atividade                     | Yes      |
| descrição         | Texto que descreve para que a atividade é usada | No       |
| tipo                | Para a Atividade da Colmeia, o tipo de atividade é HDinsightPig | Yes      |
| linkedServiceName   | Referência ao cluster HDInsight registado como um serviço ligado na Data Factory. Para saber mais sobre este serviço ligado, consulte o artigo [de serviços ligados a Compute.](compute-linked-services.md) | Yes      |
| scriptLinkedService | Referência a um Serviço Ligado ao Armazenamento Azure usado para armazenar o script pig para ser executado. Apenas os serviços ligados a **[Azure Blob](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)** e **[ADLS Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)** são suportados aqui. Se não especificar este Serviço Linked, é utilizado o Serviço Ligado ao Armazenamento Azure definido no Serviço Linked HDInsight. | No       |
| scriptPath          | Forneça o caminho para o ficheiro de script armazenado no Azure Storage referido pelo scriptLinkedService. O nome do ficheiro é sensível a casos. | No       |
| obterDebugInfo        | Especifica quando os ficheiros de registo são copiados para o Azure Storage utilizado pelo cluster HDInsight (ou) especificado pelo scriptLinkedService. Valores permitidos: Nenhum, Sempre ou Fracasso. Valor predefinido: Nenhum. | No       |
| argumentos           | Especifica uma série de argumentos para um trabalho de Hadoop. Os argumentos são passados como argumentos de linha de comando para cada tarefa. | No       |
| define             | Especifique os parâmetros como pares chave/valor para referências dentro do script Pig. | Não       |

## <a name="next-steps"></a>Próximos passos
Veja os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade u-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade mapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (clássico) Atividade de execução de lote](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
