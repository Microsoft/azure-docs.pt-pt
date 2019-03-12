---
title: Transformar dados com a atividade de Hive do Hadoop no Azure Data Factory | Documentos da Microsoft
description: Saiba como pode utilizar a atividade de ramo de registo de uma fábrica de dados do Azure para executar consultas do Hive num cluster do HDInsight no, a pedido/suas próprio.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 3852b2d18b48be63cbc612159facb6273f23dc2b
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575584"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Transformar dados com a atividade de Hive do Hadoop no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-hive-activity.md)
> * [Versão atual](transform-data-using-hadoop-hive.md)

A atividade Hive do HDInsight numa fábrica de dados [pipeline](concepts-pipelines-activities.md) executa consultas do Hive no [seu próprio](compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight. Este artigo baseia-se a [atividades de transformação de dados](transform-data.md) artigo, que apresenta uma visão geral de transformação de dados e as atividades de transformação suportados.

Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](introduction.md) e faça a [Tutorial: transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

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
| Propriedade            | Descrição                                                  | Necessário |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | Nome da atividade                                         | Sim      |
| descrição         | Texto que descreve o que a atividade é utilizada para                | Não       |
| tipo                | Para a atividade do Hive, o tipo de atividade é HDinsightHive        | Sim      |
| linkedServiceName   | Referência para o cluster de HDInsight registado como um serviço ligado no Data Factory. Para saber mais sobre este serviço ligado, veja [serviços ligados de computação](compute-linked-services.md) artigo. | Sim      |
| scriptLinkedService | Referência para um serviço ligado de armazenamento do Azure utilizada para armazenar o script de ramo de registo a ser executado. Se não especificar este serviço ligado, o serviço ligado de armazenamento de Azure definido no serviço ligado do HDInsight é utilizado. | Não       |
| scriptPath          | Forneça o caminho para o ficheiro de script armazenado no armazenamento do Azure mencionados pelo scriptLinkedService. O nome de ficheiro diferencia maiúsculas de minúsculas. | Sim      |
| getDebugInfo        | Especifica quando os arquivos de log são copiados para o armazenamento do Azure utilizado pelo cluster do HDInsight (ou) especificado pelo scriptLinkedService. Valores permitidos: Nenhum, sempre, ou a falha. Valor predefinido: Nenhum. | Não       |
| argumentos           | Especifica uma matriz de argumentos para uma tarefa do Hadoop. Os argumentos são transmitidos como argumentos da linha de comandos para cada tarefa. | Não       |
| Define             | Especifique parâmetros como pares chave/valor para referenciar a dentro do script do Hive. | Não       |
| queryTimeout        | Consultar o valor de tempo limite (em minutos). Aplicável quando o cluster do HDInsight é com o Enterprise Security Package ativada. | Não       |

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade PIg](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lotes de Aprendizado de máquina](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
