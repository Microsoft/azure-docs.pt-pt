---
title: Transformar dados usando a atividade hive do Hadoop
description: Saiba como você pode usar a atividade do hive em uma data factory do Azure para executar consultas do hive em um cluster do HDInsight sob demanda/.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/15/2019
ms.openlocfilehash: b4af3f897a12c71d73962735d3fe68d95f138cef
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912916"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Transformar dados usando a atividade hive do Hadoop no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-hive-activity.md)
> * [Versão atual](transform-data-using-hadoop-hive.md)

A atividade do hive do HDInsight em um [pipeline](concepts-pipelines-activities.md) data Factory executa consultas de Hive em [seu próprio](compute-linked-services.md#azure-hdinsight-linked-service) cluster HDInsight ou [sob demanda](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Este artigo se baseia no artigo [atividades de transformação de dados](transform-data.md) , que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte.

Se você for novo no Azure Data Factory, leia a [introdução ao Azure data Factory](introduction.md) e faça o [tutorial: transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
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
| Propriedade            | Descrição                                                  | Obrigatório |
| ------------------- | ------------------------------------------------------------ | -------- |
| nome                | Nome da atividade                                         | Sim      |
| descrição         | Texto que descreve para que a atividade é usada                | Não       |
| tipo                | Para a atividade Hive, o tipo de atividade é HDinsightHive        | Sim      |
| linkedServiceName   | Referência ao cluster HDInsight registrado como um serviço vinculado no Data Factory. Para saber mais sobre esse serviço vinculado, consulte o artigo [Serviços vinculados de computação](compute-linked-services.md) . | Sim      |
| scriptLinkedService | Referência a um serviço vinculado do armazenamento do Azure usado para armazenar o script do hive a ser executado. Se você não especificar esse serviço vinculado, o serviço vinculado do armazenamento do Azure definido no serviço vinculado do HDInsight será usado. | Não       |
| scriptPath          | Forneça o caminho para o arquivo de script armazenado no armazenamento do Azure referenciado por scriptLinkedService. O nome do arquivo diferencia maiúsculas de minúsculas. | Sim      |
| getDebugInfo        | Especifica quando os arquivos de log são copiados para o armazenamento do Azure usado pelo cluster HDInsight (ou) especificado por scriptLinkedService. Valores permitidos: nenhum, sempre ou falha. Valor padrão: nenhum. | Não       |
| arguments           | Especifica uma matriz de argumentos para um trabalho do Hadoop. Os argumentos são passados como argumentos de linha de comando para cada tarefa. | Não       |
| defines             | Especifique parâmetros como pares de chave/valor para referência no script do hive. | Não       |
| queryTimeout        | Valor de tempo limite da consulta (em minutos). Aplicável quando o cluster HDInsight está com Enterprise Security Package habilitado. | Não       |

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras: 

* [Atividade de U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade Pig](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de Machine Learning lote](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
