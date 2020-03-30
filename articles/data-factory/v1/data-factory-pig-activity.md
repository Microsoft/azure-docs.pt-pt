---
title: Transforme dados utilizando a atividade do porco na Fábrica de Dados Azure
description: Saiba como pode usar a Atividade do Porco numa fábrica de dados Azure para executar scripts de porco num cluster HDInsight a pedido/seu próprio HDInsight.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 5b8e7201a6239ef1fe83fb89d4b361995e305bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703198"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Transforme dados utilizando a atividade do porco na Fábrica de Dados Azure
> [!div class="op_single_selector" title1="Atividades de Transformação"]
> * [Atividade da Colmeia](data-factory-hive-activity.md) 
> * [Atividade do Porco](data-factory-pig-activity.md)
> * [MapReduce Atividade](data-factory-map-reduce.md)
> * [Atividade de streaming de hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade de Faísca](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [.NET Atividade Personalizada](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [a transformação de dados utilizando](../transform-data-using-hadoop-pig.md)a atividade do Porco na Fábrica de Dados .


A atividade do Suíno HDInsight num [gasoduto](data-factory-create-pipelines.md) data Factory executa consultas de porco [por si só](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou a [pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) do cluster HDInsight baseado em Linux. Este artigo baseia-se no artigo sobre atividades de transformação de [dados,](data-factory-data-transformation-activities.md) que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

> [!NOTE] 
> Se é novo na Azure Data Factory, leia através da [Introdução à Azure Data Factory](data-factory-introduction.md) e faça o tutorial: [Construa](data-factory-build-your-first-pipeline.md) o seu primeiro pipeline de dados antes de ler este artigo. 

## <a name="syntax"></a>Sintaxe

```JSON
{
  "name": "HiveActivitySamplePipeline",
  "properties": {
    "activities": [
      {
        "name": "Pig Activity",
        "description": "description",
        "type": "HDInsightPig",
        "inputs": [
          {
            "name": "input tables"
          }
        ],
        "outputs": [
          {
            "name": "output tables"
          }
        ],
        "linkedServiceName": "MyHDInsightLinkedService",
        "typeProperties": {
          "script": "Pig script",
          "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
      }
    ]
  }
}
```

## <a name="syntax-details"></a>Detalhes da sintaxe

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da atividade |Sim |
| descrição |Texto descrevendo para que a atividade é usada |Não |
| tipo |HDinsightPig |Sim |
| inputs |Uma ou mais inputs consumidas pela atividade do Porco |Não |
| saídas |Uma ou mais saídas produzidas pela atividade do Porco |Sim |
| linkedServiceName |Referência ao cluster HDInsight registado como um serviço ligado na Data Factory |Sim |
| . |Especifique a linha inline do roteiro do porco |Não |
| scriptPath |Guarde o guião pig num armazenamento de blob Azure e forneça o caminho para o ficheiro. Utilize a propriedade 'script' ou 'scriptPath'. Ambos não podem ser usados juntos. O nome do ficheiro é sensível ao caso. |Não |
| define |Especificar parâmetros como par de chaves/valor para referência dentro do roteiro do Porco |Não |

## <a name="example"></a>Exemplo
Vamos considerar um exemplo de análise de registos de jogos onde quer identificar o tempo gasto pelos jogadores que jogam jogos lançados pela sua empresa.

O seguinte registo de jogo de amostra é um ficheiro separado vírina (,) separado. Contém os seguintes campos – ProfileID, SessionStart, Duração, SRCIPAddress e GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

O **roteiro do Porco** para processar estes dados:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Para executar este guião pig num oleoduto data factory, faça os seguintes passos:

1. Crie um serviço ligado para registar o seu próprio cluster de [computação HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar o [cluster de computação HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)a pedido . Vamos chamar este serviço ligado **HDInsightLinkedService**.
2. Crie um [serviço ligado](data-factory-azure-blob-connector.md) para configurar a ligação ao armazenamento Azure Blob que acolhe os dados. Vamos chamar a este serviço ligado **StorageLinkedService**.
3. Crie [conjuntos](data-factory-create-datasets.md) de dados que apontem para a entrada e os dados de saída. Vamos ligar para o conjunto de dados de entrada **PigSampleIn** e o conjunto de dados de saída **PigSampleOut**.
4. Copie a consulta do Porco num ficheiro o Armazenamento De Blob Azure configurado em passo #2. Se o armazenamento Azure que acolhe os dados for diferente daquele que acolhe o ficheiro de consulta, crie um serviço independente de armazenamento azure. Consulte o serviço ligado na configuração da atividade. Utilize **scriptPath** para especificar o caminho para o ficheiro script de porco e **scriptLinkedService**. 
   
   > [!NOTE]
   > Você também pode fornecer o script Pig inline na definição de atividade usando **a** propriedade script. No entanto, não recomendamos esta abordagem, uma vez que todos os personagens especiais do guião precisam de ser escapados e podem causar problemas de depuração. A melhor prática é seguir o passo #4.
   >
   >
5. Crie o pipeline com a atividade HDInsightPig. Esta atividade processa os dados de entrada executando o script pig no cluster HDInsight.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
6. Desloque o oleoduto. Consulte o artigo [de Criação de pipelines](data-factory-create-pipelines.md) para mais detalhes. 
7. Monitorize o gasoduto utilizando as vistas de monitorização e gestão da fábrica de dados. Consulte o artigo de [Monitorização e Gestão](data-factory-monitor-manage-pipelines.md) de Pipelines da Fábrica de Dados para mais detalhes.

## <a name="specifying-parameters-for-a-pig-script"></a>Especificando parâmetros para um roteiro de porco
Considere o seguinte exemplo: os registos de jogos são ingeridos diariamente no Armazenamento De Blob Azure e armazenados numa pasta dividida com base na data e hora. Pretende parametrizar o script pig e passar a localização da pasta de entrada dinamicamente durante o tempo de execução e também produzir a saída dividida com data e hora.

Para utilizar o roteiro de porco parametrizado, faça o seguinte:

* Defina os parâmetros em **definições.**

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
* No Roteiro do Porco, consulte os parâmetros utilizando '**$parameterName**' como mostrado no seguinte exemplo:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Veja também
* [Atividade da Colmeia](data-factory-hive-activity.md)
* [MapReduce Atividade](data-factory-map-reduce.md)
* [Atividade de streaming de hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
