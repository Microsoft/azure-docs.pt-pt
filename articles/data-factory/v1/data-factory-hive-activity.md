---
title: Transforme dados usando a Atividade da Colmeia - Azure
description: Saiba como pode usar a Atividade da Colmeia numa fábrica de dados Azure para executar consultas da Hive num cluster HDInsight a pedido/seu próprio.
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
ms.openlocfilehash: d153f8c316cbb76e063f07f7f823c8d9c4a21f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703360"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Transforme dados usando a atividade da Colmeia na Fábrica de Dados Azure 
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
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [a transformação de dados utilizando a atividade](../transform-data-using-hadoop-hive.md)da Colmeia na Fábrica de Dados .

A atividade da Hive HDInsight num [pipeline](data-factory-create-pipelines.md) data factory executa consultas de Hive [por si só](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou a [pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) do cluster HDInsight baseado em HDInsight. Este artigo baseia-se no artigo sobre atividades de transformação de [dados,](data-factory-data-transformation-activities.md) que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

> [!NOTE] 
> Se é novo na Azure Data Factory, leia através da [Introdução à Azure Data Factory](data-factory-introduction.md) e faça o tutorial: [Construa](data-factory-build-your-first-pipeline.md) o seu primeiro pipeline de dados antes de ler este artigo. 

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
| descrição |Texto descrevendo para que a atividade é usada |Não |
| tipo |HDinsightHive |Sim |
| inputs |Inputs consumidos pela atividade da Colmeia |Não |
| saídas |Saídas produzidas pela atividade da Colmeia |Sim |
| linkedServiceName |Referência ao cluster HDInsight registado como um serviço ligado na Data Factory |Sim |
| . |Especifique a linha inline do script da Colmeia |Não |
| scriptPath |Guarde o script da Colmeia num armazenamento de blob Azure e forneça o caminho para o ficheiro. Utilize a propriedade 'script' ou 'scriptPath'. Ambos não podem ser usados juntos. O nome do ficheiro é sensível ao caso. |Não |
| define |Especificar parâmetros como par de chaves/valor para referência dentro do script da Colmeia usando 'hiveconf' |Não |

## <a name="example"></a>Exemplo
Vamos considerar um exemplo de análise de registos de jogos onde pretende identificar o tempo gasto pelos utilizadores que jogam jogos lançados pela sua empresa. 

O seguinte registo é um registo de jogo`,`de amostra, que é víra () separado e contém os seguintes campos – ProfileID, SessionStart, Duração, SrcIPAddress e GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

O **script da Colmeia** para processar estes dados:

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

Para executar este script da Colmeia num oleoduto data factory, você precisa fazer o seguinte

1. Crie um serviço ligado para registar o seu próprio cluster de [computação HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar o [cluster de computação HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)a pedido . Vamos chamar a este serviço ligado "HDInsightLinkedService".
2. Crie um [serviço ligado](data-factory-azure-blob-connector.md) para configurar a ligação ao armazenamento Azure Blob que acolhe os dados. Vamos chamar a este serviço ligado "StorageLinkedService"
3. Crie [conjuntos](data-factory-create-datasets.md) de dados que apontem para a entrada e os dados de saída. Vamos ligar para o conjunto de dados de entrada "HiveSampleIn" e o conjunto de dados de saída "HiveSampleOut"
4. Copie a consulta da Hive como um ficheiro para o Armazenamento De Blob Azure configurado em passo #2. se o armazenamento para hospedar os dados for diferente daquele que hospeda este ficheiro de consulta, crie um serviço ligado ao Armazenamento Azure separado e consulte-o na atividade. Utilize o **scriptPath** para especificar o caminho para o ficheiro de consulta de colmeia e **scriptLinkedService** para especificar o armazenamento Azure que contém o ficheiro script. 
   
   > [!NOTE]
   > Você também pode fornecer o script Hive inline na definição de atividade usando **a** propriedade script. Não recomendamos esta abordagem, uma vez que todos os caracteres especiais do guião dentro do documento JSON precisam de ser escapados e podem causar problemas de depuração. A melhor prática é seguir o passo #4.
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
6. Desloque o oleoduto. Consulte o artigo [de Criação de pipelines](data-factory-create-pipelines.md) para mais detalhes. 
7. Monitorize o gasoduto utilizando as vistas de monitorização e gestão da fábrica de dados. Consulte o artigo de [Monitorização e Gestão](data-factory-monitor-manage-pipelines.md) de Pipelines da Fábrica de Dados para mais detalhes. 

## <a name="specifying-parameters-for-a-hive-script"></a>Especificando parâmetros para um roteiro da Colmeia
Neste exemplo, os registos de jogos são ingeridos diariamente no Armazenamento De Blob Azure e são armazenados numa pasta dividida com data e hora. Pretende parametrizar o script da Colmeia e passar a localização da pasta de entrada dinamicamente durante o tempo de execução e também produzir a saída dividida com data e hora.

Para utilizar o script da Colmeia parametrizada, faça o seguinte

* Defina os parâmetros em **definições.**

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
* No Script Hive, consulte o parâmetro usando **${hiveconf:parâmetroName}**. 
  
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
* [Atividade do Porco](data-factory-pig-activity.md)
* [MapReduce Atividade](data-factory-map-reduce.md)
* [Atividade de streaming de hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

