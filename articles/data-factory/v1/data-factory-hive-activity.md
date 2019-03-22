---
title: Transformar dados com a atividade do Hive - Azure | Documentos da Microsoft
description: Saiba como pode utilizar a atividade de ramo de registo de uma fábrica de dados do Azure para executar consultas do Hive num cluster do HDInsight no, a pedido/suas próprio.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 4b622a5925aebd140fed2ac74eaf7cc186803b90
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113758"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Transformar dados com a atividade do Hive no Azure Data Factory 
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Atividade do Hive](data-factory-hive-activity.md) 
> * [Atividade PIg](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [transformar dados com a atividade do Hive no Data Factory](../transform-data-using-hadoop-hive.md).

A atividade Hive do HDInsight numa fábrica de dados [pipeline](data-factory-create-pipelines.md) executa consultas do Hive no [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight baseado em Windows/Linux. Este artigo baseia-se a [atividades de transformação de dados](data-factory-data-transformation-activities.md) artigo, que apresenta uma visão geral de transformação de dados e as atividades de transformação suportados.

> [!NOTE] 
> Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](data-factory-introduction.md) e fazer o tutorial: [Crie seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

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
| descrição |Texto que descreve o que a atividade é utilizada para |Não |
| tipo |HDinsightHive |Sim |
| entradas |Entradas consumidas pela atividade do Hive |Não |
| saídas |Saídas produzidas pela atividade do Hive |Sim |
| linkedServiceName |Referência para o cluster de HDInsight registado como um serviço ligado no Data Factory |Sim |
| Script |Especifique o inline de script do Hive |Não |
| caminho do script |Store o script do Hive num armazenamento de Blobs do Azure e forneça o caminho para o ficheiro. Utilize a propriedade "script" ou 'scriptPath'. Não podem ser utilizados em conjunto. O nome de ficheiro diferencia maiúsculas de minúsculas. |Não |
| Define |Especifique parâmetros como pares chave/valor para a referenciar dentro do script do Hive com o 'hiveconf' |Não |

## <a name="example"></a>Exemplo
Vamos considerar um exemplo de registos de jogos analytics onde pretende identificar o tempo gasto pelos utilizadores jogar jogos, iniciados pela sua empresa. 

O log seguinte é um registo de jogos de exemplo, que é a vírgula (`,`) separados e contém os seguintes campos – ProfileID, SessionStart, duração, SrcIPAddress e GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

O **script de ramo de registo** para processar estes dados:

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

Para executar este script do Hive no pipeline do Data Factory, tem de fazer o seguinte

1. Criar um serviço ligado para registrar [cluster de cálculo do seu próprio HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configure [cluster de cálculo do HDInsight a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos chamar este serviço ligado "HDInsightLinkedService".
2. Criar uma [serviço ligado](data-factory-azure-blob-connector.md) para configurar a ligação para o armazenamento de Blobs do Azure que aloja os dados. Vamos chamar este serviço ligado "StorageLinkedService"
3. Crie [conjuntos de dados](data-factory-create-datasets.md) apontando para a entrada e os dados de saída. Vamos chamar o conjunto de dados de entrada "HiveSampleIn" e o conjunto de dados de saída "HiveSampleOut"
4. A consulta do Hive como um ficheiro para o armazenamento de Blobs do Azure de cópia configurada no passo 2 de #. Se o armazenamento para alojar os dados é diferente daquele que aloja este ficheiro de consulta, crie um serviço ligado do armazenamento do Azure separado e fazer referência a ele na atividade. Uso **scriptPath** para especificar o caminho para o ficheiro de consulta de hive e **scriptLinkedService** para especificar o armazenamento do Azure que contém o ficheiro de script. 
   
   > [!NOTE]
   > Também pode fornecer o inline de script de ramo de registo na definição da atividade, utilizando o **script** propriedade. Não recomendamos esta abordagem como todos os carateres especiais no script dentro as necessidades de documentos JSON de caracteres de escape e pode causar problemas de depuração. A prática recomendada é seguir o passo 4 de #.
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
6. Implemente o pipeline. Ver [Criar pipelines](data-factory-create-pipelines.md) artigo para obter detalhes. 
7. Monitorize o pipeline com as vistas de monitorização e gestão da fábrica de dados. Ver [monitorização e gerir pipelines do Data Factory](data-factory-monitor-manage-pipelines.md) artigo para obter detalhes. 

## <a name="specifying-parameters-for-a-hive-script"></a>Especificar parâmetros para um script do Hive
Neste exemplo, registos de jogos são ingeridos diariamente no armazenamento de Blobs do Azure e são armazenados numa pasta particionada com a data e hora. Queira parametrizar o script do Hive e passar a localização da pasta de entrada dinamicamente durante o tempo de execução e também produzir a saída particionada com a data e hora.

Para utilizar o script de Hive parametrizado, faça o seguinte

* Definir os parâmetros **define**.

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
* No Script do Hive, consulte usando o parâmetro **${hiveconf:parameterName}**. 
  
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
  ## <a name="see-also"></a>Consultar Também
* [Atividade PIg](data-factory-pig-activity.md)
* [Atividade MapReduce](data-factory-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

