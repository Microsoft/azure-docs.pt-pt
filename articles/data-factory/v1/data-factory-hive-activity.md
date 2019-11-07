---
title: Transformar dados usando a atividade do hive – Azure
description: Saiba como você pode usar a atividade do hive em uma data factory do Azure para executar consultas do hive em um cluster do HDInsight sob demanda/.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f159f672c999d7877ef89cd78d23c4a608ccf1ab
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666998"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Transformar dados usando a atividade do hive no Azure Data Factory 
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
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [transformar dados usando a atividade do hive no data Factory](../transform-data-using-hadoop-hive.md).

A atividade do hive do HDInsight em um [pipeline](data-factory-create-pipelines.md) data Factory executa consultas de Hive em [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) cluster do HDInsight baseado em Windows/Linux [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Este artigo se baseia no artigo [atividades de transformação de dados](data-factory-data-transformation-activities.md) , que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte.

> [!NOTE] 
> Se você for novo no Azure Data Factory, leia a [introdução ao Azure data Factory](data-factory-introduction.md) e faça o tutorial: [criar seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

## <a name="syntax"></a>Sintaxe

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
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
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Detalhes da sintaxe
| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da atividade |Sim |
| descrição |Texto que descreve para que a atividade é usada |Não |
| tipo |HDinsightHive |Sim |
| informações |Entradas consumidas pela atividade de Hive |Não |
| produz |Saídas produzidas pela atividade de Hive |Sim |
| linkedServiceName |Referência ao cluster HDInsight registrado como um serviço vinculado no Data Factory |Sim |
| . |Especificar o script do hive embutido |Não |
| scriptPath |Armazene o script do hive em um armazenamento de BLOBs do Azure e forneça o caminho para o arquivo. Use a propriedade ' script ' ou ' scriptPath '. Ambos não podem ser usados juntos. O nome do arquivo diferencia maiúsculas de minúsculas. |Não |
| autor |Especifique parâmetros como pares de chave/valor para referência no script do hive usando ' hiveconf ' |Não |

## <a name="example"></a>Exemplo
Vamos considerar um exemplo de análise de logs de jogos em que você deseja identificar o tempo gasto pelos usuários que desempenham jogos lançados por sua empresa. 

O log a seguir é um log de jogo de exemplo, que é separado por vírgula (`,`) e contém os campos a seguir – ProfileId, SessionStart, Duration, SrcIPAddress e gametype.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

O **script do hive** para processar esses dados:

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

Para executar esse script do hive em um pipeline Data Factory, você precisa fazer o seguinte

1. Crie um serviço vinculado para registrar [seu próprio cluster de computação hdinsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar o [cluster de computação hdinsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos chamar esse serviço vinculado "HDInsightLinkedService".
2. Crie um [serviço vinculado](data-factory-azure-blob-connector.md) para configurar a conexão com o armazenamento de BLOBs do Azure que hospeda os dados. Vamos chamar esse serviço vinculado "StorageLinkedService"
3. Crie [conjuntos](data-factory-create-datasets.md) de dados que apontam para a entrada e os de saída. Vamos chamar o DataSet de entrada "HiveSampleIn" e o conjunto de dados de saída "HiveSampleOut"
4. Copie a consulta do hive como um arquivo para o armazenamento de BLOBs do Azure configurado na etapa #2. Se o armazenamento para hospedar os dados for diferente daquele que hospeda esse arquivo de consulta, crie um serviço vinculado do armazenamento do Azure separado e consulte-o na atividade. Use **ScriptPath** para especificar o caminho para o arquivo de consulta do hive e **scriptLinkedService** para especificar o armazenamento do Azure que contém o arquivo de script. 
   
   > [!NOTE]
   > Você também pode fornecer o script do hive embutido na definição da atividade usando a propriedade **script** . Não recomendamos essa abordagem, pois todos os caracteres especiais no script dentro do documento JSON precisam ser ignorados e podem causar problemas de depuração. A prática recomendada é seguir a etapa #4.
   > 
   > 
5. Crie um pipeline com a atividade HDInsightHive. A atividade processa/transforma os dados.

    ```JSON   
    {   
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                {
                    "name": "HiveSampleIn"
                }
                ],
                "outputs": [
                {
                    "name": "HiveSampleOut"
                }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
            ]
        }
    }
    ```
6. Implante o pipeline. Consulte o artigo [criando pipelines](data-factory-create-pipelines.md) para obter detalhes. 
7. Monitore o pipeline usando as exibições de monitoramento e gerenciamento de data factory. Consulte o artigo [monitoramento e gerenciar pipelines de data Factory](data-factory-monitor-manage-pipelines.md) para obter detalhes. 

## <a name="specifying-parameters-for-a-hive-script"></a>Especificando parâmetros para um script do hive
Neste exemplo, os logs de jogos são ingeridos diariamente no armazenamento de BLOBs do Azure e são armazenados em uma pasta particionada com data e hora. Você deseja parametrizar o script do hive e passar o local da pasta de entrada dinamicamente durante o tempo de execução e também produzir a saída particionada com data e hora.

Para usar o script Hive com parâmetros, faça o seguinte

* Defina os parâmetros em **define**.

    ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* No script do hive, consulte o parâmetro usando **$ {hiveconf: ParameterName}** . 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
  ## <a name="see-also"></a>Veja também
* [Atividade Pig](data-factory-pig-activity.md)
* [Atividade MapReduce](data-factory-map-reduce.md)
* [Atividade de streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

