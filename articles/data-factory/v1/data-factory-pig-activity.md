---
title: Transformar dados usando a atividade do porco na fábrica de dados Azure
description: Saiba como pode usar a Atividade do Porco na Azure Data Factory v1 para executar scripts de porco num cluster on-demand/seu próprio HDInsight.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 0ad84a0e848abda1b786958947b4081b11b139a7
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495350"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Transformar dados usando a atividade do porco na fábrica de dados Azure
> [!div class="op_single_selector" title1="Atividades de Transformação"]
> * [Atividade da Colmeia](data-factory-hive-activity.md) 
> * [Atividade do Porco](data-factory-pig-activity.md)
> * [Atividade mapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade de Faísca](data-factory-spark.md)
> * [Atividade de Execução em Lotes do Azure Machine Learning Studio (clássico)](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização do Azure Machine Learning Studio (clássico)](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [.NET Atividade Personalizada](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte os dados de [transformação utilizando a atividade do Porco na Data Factory.](../transform-data-using-hadoop-pig.md)


A atividade do Porco HDInsight num [oleoduto](data-factory-create-pipelines.md) da Data Factory executa consultas de porco no [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) conjunto de Windows/HdInsight baseado [em](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux. Este artigo baseia-se no artigo de atividades de [transformação](data-factory-data-transformation-activities.md) de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

> [!NOTE] 
> Se é novo na Azure Data Factory, leia através [da Introdução à Azure Data Factory](data-factory-introduction.md) e faça o tutorial: Construa o seu primeiro pipeline de [dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

## <a name="syntax"></a>Syntax

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

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| name |Nome da atividade |Sim |
| descrição |Texto que descreve para que a atividade é usada |Não |
| tipo |HDinsightPig |Sim |
| entradas |Uma ou mais entradas consumidas pela atividade do Porco |Não |
| saídas |Uma ou mais saídas produzidas pela atividade do Porco |Sim |
| linkedServiceName |Referência ao cluster HDInsight registado como um serviço ligado na Data Factory |Sim |
| script |Especificar o script do porco inline |Não |
| scriptPath |Guarde o script Pig num armazenamento de bolhas Azure e forneça o caminho para o arquivo. Use a propriedade 'script' ou 'scriptPath'. Ambos não podem ser usados juntos. O nome do ficheiro é sensível a casos. |Não |
| define |Especifique os parâmetros como pares chave/valor para referências dentro do script do Porco |Não |

## <a name="example"></a>Exemplo
Vamos considerar um exemplo de análise de registos de jogos onde pretendes identificar o tempo gasto pelos jogadores que jogam jogos lançados pela tua empresa.

O seguinte registo de jogo de amostra é um ficheiro separado de vírgula (,). Contém os seguintes campos - ProfileID, SessionStart, Duração, SrcIPAddress e GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

O **script do Porco** para processar estes dados:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Para executar este script pig num oleoduto da Data Factory, faça os seguintes passos:

1. Crie um serviço ligado para registar [o seu próprio cluster de computação HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar o cluster de [computação HDInsight a pedido.](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Vamos chamar este serviço ligado **HDInsightLinkedService**.
2. Crie um [serviço ligado](data-factory-azure-blob-connector.md) para configurar a ligação ao armazenamento Azure Blob que hospeda os dados. Vamos chamar este serviço ligado **StorageLinkedService**.
3. Crie [conjuntos de dados](data-factory-create-datasets.md) que apontam para a entrada e os dados de saída. Vamos chamar o conjunto de dados de entrada **PigSampleIn** e o conjunto de dados de saída **PigSampleOut**.
4. Copie a consulta do Porco num ficheiro que o Azure Blob Storage configura em passo #2. Se o armazenamento Azure que acolhe os dados for diferente daquele que acolhe o ficheiro de consulta, crie um serviço separado ligado ao Azure Storage. Consulte o serviço ligado na configuração da atividade. Utilize **o scriptPath** para especificar o caminho para o ficheiro de script de porco e **scriptLinkedService**. 
   
   > [!NOTE]
   > Você também pode fornecer o script pig inline na definição de atividade usando a propriedade **script.** No entanto, não recomendamos esta abordagem, uma vez que todos os personagens especiais do script precisam de ser escapados e podem causar problemas de depurações. A melhor prática é seguir o passo #4.
   >
   >
5. Crie o oleoduto com a atividade HDInsightPig. Esta atividade processa os dados de entrada executando o script do porco no cluster HDInsight.

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
6. Desdobre o oleoduto. Consulte [a criação de artigos](data-factory-create-pipelines.md) de pipelines para obter mais detalhes. 
7. Monitorize o gasoduto utilizando os pontos de vista de monitorização e gestão da fábrica de dados. Consulte o artigo [de monitorização e gestão](data-factory-monitor-manage-pipelines.md) dos gasodutos data factory para obter mais detalhes.

## <a name="specifying-parameters-for-a-pig-script"></a>Especificando parâmetros para um script de porco
Considere o seguinte exemplo: os registos de jogos são ingeridos diariamente no Azure Blob Storage e armazenados numa pasta dividida com base na data e hora. Pretende parametrizar o script do Porco e passar a localização da pasta de entrada dinamicamente durante o tempo de funcionamento e também produzir a saída dividida com data e hora.

Para utilizar o script de porco parametrizado, faça o seguinte:

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
* No Pig Script, consulte os parâmetros que utilizam o '**$parameterName**' como mostrado no seguinte exemplo:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Consulte também
* [Atividade da Colmeia](data-factory-hive-activity.md)
* [Atividade mapReduce](data-factory-map-reduce.md)
* [Atividade de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
