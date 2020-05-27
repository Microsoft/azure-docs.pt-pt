---
title: Transforme dados usando a atividade da Colmeia Hadoop
description: Saiba como pode usar a Atividade da Colmeia numa fábrica de dados Azure para executar consultas da Hive num cluster HDInsight a pedido/seu próprio.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 05/08/2019
ms.openlocfilehash: 877c1719a76f23f8446164b641dc2dac84261e0e
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849285"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Transforme dados utilizando a atividade da Urticária Hadoop na Fábrica de Dados Azure

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-hive-activity.md)
> * [Versão atual](transform-data-using-hadoop-hive.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade da Hive HDInsight num [pipeline](concepts-pipelines-activities.md) data factory executa consultas de Hive [por conta própria](compute-linked-services.md#azure-hdinsight-linked-service) ou por um cluster HDInsight a [pedido.](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Este artigo baseia-se no artigo sobre atividades de transformação de [dados,](transform-data.md) que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

Se é novo na Azure Data Factory, leia através da [Introdução à Azure Data Factory](introduction.md) e faça o [Tutorial: transforme os dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

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
| name                | Nome da atividade                                         | Yes      |
| descrição         | Texto descrevendo para que a atividade é usada                | No       |
| tipo                | Para a Atividade da Colmeia, o tipo de atividade é HDinsightHive        | Yes      |
| linkedServiceName   | Referência ao cluster HDInsight registado como um serviço ligado na Data Factory. Para conhecer este serviço ligado, consulte o artigo de [serviços ligados à Compute.](compute-linked-services.md) | Yes      |
| scriptLinkedService | Referência a um Serviço Ligado ao Armazenamento Azure usado para armazenar o script da Colmeia a ser executado. Apenas os serviços ligados ao **[Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)** e **[ADLS Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)** são suportados aqui. Se não especificar este Serviço Linked, o Serviço Ligado ao Armazenamento Azure definido no Serviço Ligado ao HDInsight é utilizado.  | No       |
| scriptPath          | Forneça o caminho para o ficheiro script armazenado no Armazenamento Azure referido pelo scriptLinkedService. O nome do ficheiro é sensível ao caso. | Yes      |
| getDebugInfo        | Especifica quando os ficheiros de registo são copiados para o Armazenamento Azure utilizado pelo cluster HDInsight (ou) especificado pelo scriptLinkedService. Valores permitidos: Nenhum, sempre ou falha. Valor predefinido: Nenhum. | No       |
| argumentos           | Especifica uma série de argumentos para um trabalho de Hadoop. Os argumentos são passados como argumentos de linha de comando para cada tarefa. | No       |
| define             | Especifique os parâmetros como par de chaves/valor para referência dentro do script da Colmeia. | No       |
| consultaTimeout        | Valor de tempo de consulta (em minutos). Aplicável quando o cluster HDInsight está com o Pacote de Segurança empresarial ativado. | Não       |

## <a name="next-steps"></a>Próximos passos
Consulte os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade de porco](transform-data-using-hadoop-pig.md)
* [MapReduzir a atividade](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lote de aprendizagem automática](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
