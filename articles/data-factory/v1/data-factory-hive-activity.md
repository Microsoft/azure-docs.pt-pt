---
title: Transformar dados usando a Atividade da Colmeia - Azure
description: Saiba como pode utilizar a Atividade da Colmeia na Azure Data Factory v1 para executar consultas de Hive num cluster a pedido/seu próprio HDInsight.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 63e726c98922b789977a884bf747f12186707d57
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782712"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Transformar dados usando a atividade da Colmeia na Fábrica de Dados Azure 
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
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte os dados de [transformação utilizando a atividade da Hive na Data Factory.](../transform-data-using-hadoop-hive.md)

A atividade de Hive HDInsight num [oleoduto](data-factory-create-pipelines.md) da Data Factory executa consultas de Hive no [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) do grupo HDInsight baseado em Windows/Linux. Este artigo baseia-se no artigo de atividades de [transformação](data-factory-data-transformation-activities.md) de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.

> [!NOTE] 
> Se é novo na Azure Data Factory, leia através [da Introdução à Azure Data Factory](data-factory-introduction.md) e faça o tutorial: Construa o seu primeiro pipeline de [dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

## <a name="syntax"></a>Syntax

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
| name |Nome da atividade |Yes |
| descrição |Texto que descreve para que a atividade é usada |No |
| tipo |HDinsightHive |Yes |
| entradas |Entradas consumidas pela atividade da Colmeia |No |
| saídas |Saídas produzidas pela atividade da Colmeia |Yes |
| linkedServiceName |Referência ao cluster HDInsight registado como um serviço ligado na Data Factory |Yes |
| script |Especificar o script da Colmeia inline |No |
| scriptPath |Guarde o script hive num armazenamento de bolhas Azure e forneça o caminho para o arquivo. Use a propriedade 'script' ou 'scriptPath'. Ambos não podem ser usados juntos. O nome do ficheiro é sensível a casos. |No |
| define |Especifique os parâmetros como pares chave/valor para referência dentro do script da Colmeia usando 'hiveconf' |No |

## <a name="example"></a>Exemplo
Vamos considerar um exemplo de análise de registos de jogos onde pretendes identificar o tempo gasto pelos utilizadores a jogar jogos lançados pela tua empresa. 

O seguinte registo é um registo de jogo de amostra, que é `,` vírgula ( ) separado e contém os seguintes campos - ProfileID, SessionStart, Duração, SrcIPAddress e GameType.

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

Para executar este script da Colmeia num oleoduto da Data Factory, é necessário fazer o seguinte

1. Crie um serviço ligado para registar [o seu próprio cluster de computação HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar o cluster de [computação HDInsight a pedido.](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Vamos chamar este serviço ligado "HDInsightLinkedService".
2. Crie um [serviço ligado](data-factory-azure-blob-connector.md) para configurar a ligação ao armazenamento Azure Blob que hospeda os dados. Vamos chamar este serviço ligado "StorageLinkedService"
3. Crie [conjuntos de dados](data-factory-create-datasets.md) que apontam para a entrada e os dados de saída. Vamos chamar o conjunto de dados de entrada "HiveSampleIn" e o conjunto de dados de saída "HiveSampleOut"
4. Copie a consulta da Colmeia como um ficheiro para o Azure Blob Storage configurado em #2 passo. se o armazenamento para hospedar os dados for diferente daquele que hospeda este ficheiro de consulta, crie um serviço separado de armazenamento Azure Storage e consulte-os na atividade. Utilize **o scriptPath** para especificar o caminho para o ficheiro de consulta de colmeia e **scriptLinkedService** para especificar o armazenamento do Azure que contém o ficheiro de script. 
   
   > [!NOTE]
   > Também pode fornecer o script da Colmeia na definição de atividade utilizando **a** propriedade script. Não recomendamos esta abordagem, uma vez que todos os caracteres especiais do guião no documento JSON precisam de ser escapados e podem causar problemas de depurações. A melhor prática é seguir o passo #4.
   > 
   > 
5. Crie um oleoduto com a atividade HDInsightHive. A atividade processa/transforma os dados.

  ```json
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

6. Desdobre o oleoduto. Consulte [a criação de artigos](data-factory-create-pipelines.md) de pipelines para obter mais detalhes. 
7. Monitorize o gasoduto utilizando os pontos de vista de monitorização e gestão da fábrica de dados. Consulte o artigo [de monitorização e gestão](data-factory-monitor-manage-pipelines.md) dos gasodutos data factory para obter mais detalhes. 

## <a name="specifying-parameters-for-a-hive-script"></a>Especificando parâmetros para um script de Colmeia
Neste exemplo, os registos de jogos são ingeridos diariamente no Azure Blob Storage e são armazenados numa pasta dividida com data e hora. Pretende parametrizar o script da Colmeia e passar a localização da pasta de entrada dinamicamente durante o tempo de funcionamento e também produzir a saída dividida com data e hora.

Para utilizar o roteiro da Colmeia parametrizada, faça o seguinte

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
* No Script hive, consulte o parâmetro usando **${hiveconf:parameterName}**. 
  
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
  ## <a name="see-also"></a>Consulte também
* [Atividade do Porco](data-factory-pig-activity.md)
* [Atividade mapReduce](data-factory-map-reduce.md)
* [Atividade de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

