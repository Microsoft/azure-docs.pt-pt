---
title: Transformar dados usando a atividade Pig no Azure Data Factory
description: Saiba como você pode usar a atividade Pig em uma data factory do Azure para executar scripts do Pig em um cluster do HDInsight sob demanda/seu próprio.
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
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74703198"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Transformar dados usando a atividade Pig no Azure Data Factory
> [!div class="op_single_selector" title1="Atividades de transformação"]
> * [Atividade do hive](data-factory-hive-activity.md) 
> * [Atividade Pig](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [transformar dados usando a atividade Pig em data Factory](../transform-data-using-hadoop-pig.md).


A atividade Pig do HDInsight em um [pipeline](data-factory-create-pipelines.md) data Factory executa consultas Pig em [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) cluster do HDInsight baseado em Windows/Linux [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Este artigo se baseia no artigo [atividades de transformação de dados](data-factory-data-transformation-activities.md) , que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte.

> [!NOTE] 
> Se você for novo no Azure Data Factory, leia a [introdução ao Azure data Factory](data-factory-introduction.md) e faça o tutorial: [criar seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

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

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| nome |Nome da atividade |Sim |
| descrição |Texto que descreve para que a atividade é usada |Não |
| tipo |HDinsightPig |Sim |
| informações |Uma ou mais entradas consumidas pela atividade Pig |Não |
| produz |Uma ou mais saídas produzidas pela atividade Pig |Sim |
| linkedServiceName |Referência ao cluster HDInsight registrado como um serviço vinculado no Data Factory |Sim |
| . |Especificar o script Pig embutido |Não |
| scriptPath |Armazene o script Pig em um armazenamento de BLOBs do Azure e forneça o caminho para o arquivo. Use a propriedade ' script ' ou ' scriptPath '. Ambos não podem ser usados juntos. O nome do arquivo diferencia maiúsculas de minúsculas. |Não |
| autor |Especificar parâmetros como pares de chave/valor para referência no script Pig |Não |

## <a name="example"></a>Exemplo
Vamos considerar um exemplo de análise de logs de jogos em que você deseja identificar o tempo gasto pelos jogadores que desempenham jogos lançados por sua empresa.

O log de jogos de exemplo a seguir é um arquivo separado por vírgula (,). Ele contém os campos a seguir – ProfileId, SessionStart, Duration, SrcIPAddress e gametype.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

O **script Pig** para processar esses dados:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Para executar esse script Pig em um pipeline Data Factory, execute as seguintes etapas:

1. Crie um serviço vinculado para registrar [seu próprio cluster de computação hdinsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar o [cluster de computação hdinsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos chamar esse serviço vinculado **HDInsightLinkedService**.
2. Crie um [serviço vinculado](data-factory-azure-blob-connector.md) para configurar a conexão com o armazenamento de BLOBs do Azure que hospeda os dados. Vamos chamar esse serviço vinculado **StorageLinkedService**.
3. Crie [conjuntos](data-factory-create-datasets.md) de dados que apontam para a entrada e os de saída. Vamos chamar o DataSet de entrada **PigSampleIn** e o conjunto de dados de saída **PigSampleOut**.
4. Copie a consulta Pig em um arquivo que o armazenamento de BLOBs do Azure configurada na etapa #2. Se o armazenamento do Azure que hospeda os dados for diferente daquele que hospeda o arquivo de consulta, crie um serviço vinculado do armazenamento do Azure separado. Consulte o serviço vinculado na configuração da atividade. Use **ScriptPath** para especificar o caminho para o arquivo de script Pig e **scriptLinkedService**. 
   
   > [!NOTE]
   > Você também pode fornecer o script Pig embutido na definição da atividade usando a propriedade **script** . No entanto, não recomendamos essa abordagem, pois todos os caracteres especiais no script precisam ser ignorados e podem causar problemas de depuração. A prática recomendada é seguir a etapa #4.
   >
   >
5. Crie o pipeline com a atividade HDInsightPig. Essa atividade processa os dados de entrada executando o script Pig no cluster HDInsight.

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
6. Implante o pipeline. Consulte o artigo [criando pipelines](data-factory-create-pipelines.md) para obter detalhes. 
7. Monitore o pipeline usando as exibições de monitoramento e gerenciamento de data factory. Consulte o artigo [monitoramento e gerenciar pipelines de data Factory](data-factory-monitor-manage-pipelines.md) para obter detalhes.

## <a name="specifying-parameters-for-a-pig-script"></a>Especificando parâmetros para um script Pig
Considere o exemplo a seguir: os logs de jogos são ingeridos diariamente no armazenamento de BLOBs do Azure e armazenados em uma pasta particionada com base em data e hora. Você deseja parametrizar o script Pig e passar o local da pasta de entrada dinamicamente durante o tempo de execução e também produzir a saída particionada com data e hora.

Para usar o script Pig com parâmetros, faça o seguinte:

* Defina os parâmetros em **define**.

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
* No script Pig, consulte os parâmetros usando ' **$ParameterName**', conforme mostrado no exemplo a seguir:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Consulte também
* [Atividade do hive](data-factory-hive-activity.md)
* [Atividade MapReduce](data-factory-map-reduce.md)
* [Atividade de streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
