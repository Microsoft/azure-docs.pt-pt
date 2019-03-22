---
title: Transformar dados com atividade Pig no Azure Data Factory | Documentos da Microsoft
description: Saiba como pode utilizar a atividade Pig de uma fábrica de dados do Azure para executar Pig scripts num cluster do HDInsight no, a pedido/suas próprio.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 78ee2c1ce402a29f1a9dfdd29f31daef09134eba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57997014"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Transformar dados com atividade Pig no Azure Data Factory
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
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [transformar dados com atividade Pig no Data Factory](../transform-data-using-hadoop-pig.md).


A atividade Pig do HDInsight numa fábrica de dados [pipeline](data-factory-create-pipelines.md) executa consultas de Pig no [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight baseado em Windows/Linux. Este artigo baseia-se a [atividades de transformação de dados](data-factory-data-transformation-activities.md) artigo, que apresenta uma visão geral de transformação de dados e as atividades de transformação suportados.

> [!NOTE] 
> Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](data-factory-introduction.md) e fazer o tutorial: [Crie seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

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
| descrição |Texto que descreve o que a atividade é utilizada para |Não |
| tipo |HDinsightPig |Sim |
| entradas |Uma ou mais entradas consumidas pela atividade Pig |Não |
| saídas |Um ou mais saídas produzidas pela atividade Pig |Sim |
| linkedServiceName |Referência para o cluster de HDInsight registado como um serviço ligado no Data Factory |Sim |
| Script |Especifique o inline de script Pig |Não |
| caminho do script |Store o script Pig num armazenamento de Blobs do Azure e forneça o caminho para o ficheiro. Utilize a propriedade "script" ou 'scriptPath'. Não podem ser utilizados em conjunto. O nome de ficheiro diferencia maiúsculas de minúsculas. |Não |
| Define |Especifique parâmetros como pares chave/valor para fazer referência no Pig script |Não |

## <a name="example"></a>Exemplo
Vamos considerar um exemplo de registos de jogos analytics onde pretende identificar o tempo gasto por leitores de jogar jogos, iniciados pela sua empresa.

O registo de jogos de exemplo seguinte é um ficheiro de separada por vírgulas (,). Contém os seguintes campos – ProfileID, SessionStart, duração, SrcIPAddress e GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

O **Pig script** para processar estes dados:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Para executar este script Pig no pipeline do Data Factory, siga os passos abaixo:

1. Criar um serviço ligado para registrar [cluster de cálculo do seu próprio HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configure [cluster de cálculo do HDInsight a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos chamar este serviço ligado **HDInsightLinkedService**.
2. Criar uma [serviço ligado](data-factory-azure-blob-connector.md) para configurar a ligação para o armazenamento de Blobs do Azure que aloja os dados. Vamos chamar este serviço ligado **StorageLinkedService**.
3. Crie [conjuntos de dados](data-factory-create-datasets.md) apontando para a entrada e os dados de saída. Vamos chamar o conjunto de dados de entrada **PigSampleIn** e o conjunto de dados de saída **PigSampleOut**.
4. Copie a consulta de Pig num arquivo de armazenamento de Blobs do Azure configurada no passo 2 de #. Se o armazenamento do Azure que aloja os dados é diferente da que é que aloja o arquivo de consulta, crie um serviço ligado do armazenamento do Azure separado. Consulte o serviço ligado na configuração da atividade. Uso **scriptPath** para especificar o caminho para o ficheiro de script pig e **scriptLinkedService**. 
   
   > [!NOTE]
   > Também pode fornecer o inline de script Pig na definição da atividade, utilizando o **script** propriedade. No entanto, não recomendamos esta abordagem como todos os carateres especiais nas necessidades de script de caracteres de escape e pode causar problemas de depuração. A prática recomendada é seguir o passo 4 de #.
   >
   >
5. Crie o pipeline com a atividade de HDInsightPig. Esta atividade processa os dados de entrada ao executar o Pig script num cluster de HDInsight.

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
6. Implemente o pipeline. Ver [Criar pipelines](data-factory-create-pipelines.md) artigo para obter detalhes. 
7. Monitorize o pipeline com as vistas de monitorização e gestão da fábrica de dados. Ver [monitorização e gerir pipelines do Data Factory](data-factory-monitor-manage-pipelines.md) artigo para obter detalhes.

## <a name="specifying-parameters-for-a-pig-script"></a>Especificar parâmetros para um script Pig
Considere o seguinte exemplo: registos de jogos são ingeridos diariamente no armazenamento de Blobs do Azure e armazenados numa pasta data com base no particionada e a hora. Queira parametrizar o script Pig e passar a localização da pasta de entrada dinamicamente durante o tempo de execução e também produzir a saída particionada com a data e hora.

Para utilizar o script de Pig parametrizada, faça o seguinte:

* Definir os parâmetros **define**.

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
* No Pig Script, consulte os parâmetros com '**$parameterName**' conforme mostrado no exemplo a seguir:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Consultar Também
* [Atividade do Hive](data-factory-hive-activity.md)
* [Atividade MapReduce](data-factory-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
