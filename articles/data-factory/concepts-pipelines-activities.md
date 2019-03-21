---
title: Pipelines e atividades no Azure Data Factory | Microsoft Docs
description: Saiba mais sobre os pipelines e as atividades no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 845544a2062b43f0d9f883ddecbc2589b3357221
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997928"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines e atividades no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-create-pipelines.md)
> * [Versão atual](concepts-pipelines-activities.md)

Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e a utilizá-los para construir fluxos de dados completos e orientados por dados para os seus cenários de movimento de dados e processamento de dados.

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. Os pipelines são agrupamentos lógicos de atividades que, em conjunto, realizam uma tarefa. Por exemplo, um pipeline pode conter um conjunto de atividades que ingerem e limpam dados de registos e, depois, arrancam um trabalho do Spark num cluster do HDInsight para analisar esses dados de registo. O que isto tem de bom é que o pipeline lhe permite gerir as atividades como um conjunto, em vez de individualmente. Por exemplo, pode implementar e agendar o pipeline em vez das atividades individualmente.

As atividades num pipeline definem as ações a efetuar nos seus dados. Por exemplo, pode utilizar uma atividade de cópia para copiar dados de um SQL Server no local para um Armazenamento de Blobs do Azure. Em seguida, utilize uma atividade do Hive que execute um script do Hive num cluster do Azure HDInsight para processar/transformar os dados do armazenamento de blobs para produzir dados de saída. Por fim, utilize uma segunda atividade de cópia para copiar os dados de saída para um armazém do Azure SQL Data Warehouse sobre o qual são criadas soluções de relatórios de business intelligence (BI).

O Data Factory suporta três tipos de atividades: [atividades de movimento de dados](copy-activity-overview.md), [atividades de transformação de dados](transform-data.md) e [atividades de controlo](control-flow-web-activity.md). Uma atividade pode ter zero ou mais [conjuntos de dados](concepts-datasets-linked-services.md) de entrada e produzir um ou mais [conjuntos de dados](concepts-datasets-linked-services.md) de saída. O diagrama seguinte mostra a relação entre pipelines, atividades e conjuntos de dados no Data Factory:

![Relação entre conjuntos de dados, atividades e pipelines](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída da atividade. Os conjuntos de dados identificam dados dentro de diferentes arquivos de dados, como tabelas, ficheiros, pastas e documentos. Depois de criar um conjunto de dados, pode utilizá-lo com atividades num pipeline. Por exemplo, um conjunto de dados pode ser um conjunto de dados de entrada/saída de uma atividade Cópia ou de uma atividade HDInsightHive. Para obter mais informações sobre os conjuntos de dados, veja o artigo [Datasets in Azure Data Factory](concepts-datasets-linked-services.md) (Conjuntos de Dados no Azure Data Factory).

## <a name="data-movement-activities"></a>Atividades de movimento de dados
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados apresentados na tabela nesta secção. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Para obter mais informações, veja o artigo [Copy Activity - Overview](copy-activity-overview.md) (Atividade de Cópia - Descrição Geral).

## <a name="data-transformation-activities"></a>Atividades de transformação de dados
O Azure Data Factory suporta as seguintes atividades de transformação, que podem ser adicionadas a pipelines individualmente ou encadeadas com outra atividade.

Atividade de transformação de dados | Ambiente de computação
---------------------------- | -------------------
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Hadoop Streaming](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Atividades do Machine Learning: Execução de lotes e atualizar recurso](transform-data-using-machine-learning.md) | VM do Azure
[Procedimento Armazenado](transform-data-using-stored-procedure.md) | SQL Azure, Azure SQL Data Warehouse ou SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure Data Lake Analytics
[Código Personalizado](transform-data-using-dotnet-custom-activity.md) | Azure Batch
[Databricks Notebook](transform-data-databricks-notebook.md) | Azure Databricks

Para obter mais informações, veja o artigo [Data Transformation Activities](transform-data.md) (Atividades de Transformação de Dados).

## <a name="control-activities"></a>Atividades de controlo
São suportadas as atividades de fluxo de controlo abaixo:

Atividade de controlo | Descrição
---------------- | -----------
[Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md) | A atividade Executar Pipeline permite que um pipeline do Data Factory invoque outro pipeline.
[Atividade ForEach](control-flow-for-each-activity.md) | A atividade ForEach define um fluxo de controlo de repetição no seu pipeline. Esta atividade é utilizada para iterar uma coleção e executa atividades especificadas em ciclo. A implementação de ciclo desta atividade é semelhante à estrutura de ciclo Foreach nas linguagens de programação.
[Atividade Web](control-flow-web-activity.md) | A atividade Web pode ser utilizada para chamar um ponto final REST personalizado a partir de um pipeline do Data Factory. Pode transmitir conjuntos de dados e serviços ligados aos quais a atividade tem acesso e que pode consumir.
[Atividade de Pesquisa](control-flow-lookup-activity.md) | A atividade de Pesquisa pode ser utilizada para ler ou procurar registos/nomes de tabelas/valores em qualquer origem externa. Este resultado pode ser ainda referenciado por atividades subsequentes.
[Atividade Obter Metadados](control-flow-get-metadata-activity.md) | A atividade Obter Metadados pode ser utilizada para obter os metadados de quaisquer dados no Azure Data Factory.
[Atividade Until](control-flow-until-activity.md) | Implementa o ciclo Do-Until que é semelhante à estrutura de ciclo Do-Until nas linguagens de programação. Executa um conjunto de atividades num ciclo até que a condição associada às atividades seja avaliada como verdadeira. Pode especificar um valor de tempo limite para a atividade Until no Data Factory.
[Atividade Se Condição](control-flow-if-condition-activity.md) | Se Condição pode ser utilizada com base em ramos numa condição que é avaliada como verdadeira ou falsa. A atividade Se Condição disponibiliza a mesma funcionalidade que as instruções “se” fornecem nas linguagens de programação. Avalia um conjunto de atividades quando a condição é avaliada como `true` e outro conjunto de atividades quando é avaliada como `false`.
[Atividade Aguardar](control-flow-wait-activity.md) | Quando utiliza uma atividade Aguardar num pipeline, este aguarda o período de tempo especificado antes de continuar a execução das atividades subsequentes.

## <a name="pipeline-json"></a>JSON dos pipelines
Eis como os pipelines são definidos no formato JSON:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
         }
    }
}
```

Etiqueta | Descrição | Type | Necessário
--- | ----------- | ---- | --------
nome | Nome do pipeline. Especifique um nome que represente a ação que o pipeline realiza. <br/><ul><li>Número máximo de carateres: 140</li><li>Tem de começar com uma letra, número ou um caráter de sublinhado (\_)</li><li>Os carateres seguintes não são permitidos: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\”</li></ul> | String | Sim
descrição | Especifique o texto que descreve para o que é utilizado o pipeline. | String | Não
atividades | A secção **atividades** pode ter uma ou mais atividades definidas na mesma. Veja a secção [JSON da Atividade](#activity-json) para obter detalhes sobre o elemento JSON das atividades. | Array | Sim
parâmetros | A secção **parâmetros** pode ter um ou mais parâmetros definidos no pipeline, tornando-o flexível para reutilização. | Lista | Não

## <a name="activity-json"></a>JSON da Atividade
A secção **atividades** pode ter uma ou mais atividades definidas na mesma. Existem dois tipos principais de atividades: A execução e atividades de controlo.

### <a name="execution-activities"></a>Atividades de Execução
As atividades de execução incluem [atividades de movimento de dados](#data-movement-activities) e [de transformação de dados](#data-transformation-activities). Têm a estrutura de nível superior abaixo:

```json
{
    "name": "Execution Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

A tabela seguinte descreve as propriedades na definição JSON da atividade:

Etiqueta | Descrição | Necessário
--- | ----------- | ---------
nome | Nome da atividade. Especifique um nome que represente a ação que a atividade realiza. <br/><ul><li>Número máximo de carateres: 55</li><li>Tem de começar com um letra, um número ou um caráter de sublinhado (\_)</li><li>Os carateres seguintes não são permitidos: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\” | Sim</li></ul>
descrição | Texto que descreve para o que é utilizada a atividade | Sim
tipo | Tipo de atividade. Veja os diferentes tipos de atividades nas secções [Atividades de Movimento de Dados](#data-movement-activities), [Atividades de Transformação de Dados](#data-transformation-activities) e [Atividades de Controlo](#control-activities). | Sim
linkedServiceName | Nome do serviço ligado utilizado pela atividade.<br/><br/>Uma atividade pode exigir que especifique o serviço ligado que liga ao ambiente de computação necessário. | Sim, para Atividade do HDInsight, Atividade de Classificação do Machine Learning, Atividade de Procedimento Armazenado. <br/><br/>Não para todas as outras.
typeProperties | As propriedades na secção typeProperties dependem de cada tipo de atividade. Para ver as propriedades do tipo de uma atividade, clique nas ligações para a atividade na secção anterior. | Não
política | Políticas que afetam o comportamento de runtime da atividade. Esta propriedade inclui o comportamento de tempo limite e de repetição. Se não for especificada, são utilizados valores predefinidos. Para obter mais informações, veja a secção [Política das atividades](#activity-policy). | Não
dependsOn | Esta propriedade é utilizada para definir as dependências de atividade e de que forma as atividades subsequentes dependem de atividades anteriores. Para obter mais informações, veja [Dependência das atividades](#activity-dependency) | Não

### <a name="activity-policy"></a>Política das atividades
As políticas afetam o comportamento de runtime de uma atividade, proporcionando opções de configuração. As políticas das Atividades só estão disponíveis para as atividades de execução.

### <a name="activity-policy-json-definition"></a>Definição JSON da política de atividade

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity"
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60,
            "secureOutput": true
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```

Nome JSON | Descrição | Valores Permitidos | Necessário
--------- | ----------- | -------------- | --------
tempo limite | Especifica o tempo limite para a execução da atividade. | Timespan | Não. O tempo limite predefinido é de 7 dias.
retry | Número máximo de repetições | Número inteiro | Não. A predefinição é 0
retryIntervalInSeconds | O atraso entre as tentativas de repetição em segundos | Número inteiro | Não. A predefinição é 20 segundos
secureOutput | Quando definido como verdadeiro, o resultado da atividade é considerado seguro e não será registado na monitorização. | Booleano | Não. A predefinição é falso.

### <a name="control-activity"></a>Atividade de controlo
As atividades de controlo têm a estrutura de nível superior seguinte:

```json
{
    "name": "Control Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

Etiqueta | Descrição | Necessário
--- | ----------- | --------
nome | Nome da atividade. Especifique um nome que represente a ação que a atividade realiza.<br/><ul><li>Número máximo de carateres: 55</li><li>Tem de começar com um letra, um número ou um caráter de sublinhado (\_)</li><li>Os carateres seguintes não são permitidos: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\” | Sim</li><ul>
descrição | Texto que descreve para o que é utilizada a atividade | Sim
tipo | Tipo de atividade. Veja os diferentes tipos de atividades nas secções sobre as [atividades de movimento de dados](#data-movement-activities), as [atividades de transformação de dados](#data-transformation-activities) e as [atividades de controlo](#control-activities). | Sim
typeProperties | As propriedades na secção typeProperties dependem de cada tipo de atividade. Para ver as propriedades do tipo de uma atividade, clique nas ligações para a atividade na secção anterior. | Não
dependsOn | Esta propriedade é utilizada para definir a Dependência da Atividade e de que forma as atividades subsequentes dependem de atividades anteriores. Para obter mais informações, veja [Dependência das atividades](#activity-dependency). | Não

### <a name="activity-dependency"></a>Dependência das atividades
A Dependência das atividades define de que forma é que as atividades subsequentes dependem das atividades anteriores, determinando a condição de continuação da execução da tarefa seguinte. Uma atividade pode depender de uma ou várias atividades anteriores com condições de dependência diferentes.

As condições de dependência diferentes são: Foi efetuada com êxito, falha, ignorada, concluída.

Por exemplo, se um pipeline tiver a Atividade A -> Atividade B, os diferentes cenários que podem ocorrer são:

- A atividade B tem a condição de dependência de atividade **foi concluída com êxito**: A atividade B só é executada se o estado final de foi concluída com êxito
- A atividade B tem a condição de dependência de atividade **falha**: A atividade B só é executada se o estado final de falha
- A atividade B tem a condição de dependência de atividade **concluída**: Execuções de atividade B se o estado final de com êxito ou falhadas
- A atividade B tem a condição de dependência de atividade **ignorada**: Execuções de atividades B se o estado final de ignorada. “Ignorada” ocorre no cenário de Atividade X -> Atividade Y -> Atividade Z, em que cada atividade só é executada se a anterior for bem-sucedida. Se a Atividade X falhar, a Y fica com o estado “Ignorada”, porque nunca é executada. Do mesmo modo, o estado da Atividade Z também será “Ignorada”.

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>Exemplo: A atividade 2 depende da atividade 1 ser bem-sucedida

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

```

## <a name="sample-copy-pipeline"></a>Pipeline de cópia de exemplo
No pipeline de exemplo seguinte, existe uma atividade do tipo **Cópia** na secção **activities**. No exemplo, a [atividade de cópia](copy-activity-overview.md) copia os dados de um armazenamento de blobs do Azure para uma base de dados SQL do Azure.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Tenha em atenção os seguintes pontos:

- Na secção atividades, existe apenas uma atividade cujo **type** está definido como **Copy**.
- A entrada da atividade está definida como **InputDataset** e a saída como **OutputDataset**. Veja o artigo [Conjuntos de dados](concepts-datasets-linked-services.md) para saber como definir conjuntos de dados em JSON.
- Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink. Na secção [atividades de movimento de dados](#data-movement-activities), clique no arquivo de dados que pretende utilizar como origem ou sink para saber mais sobre como mover dados de/para esse arquivo.

Para obter instruções completas para criar este pipeline, veja [Início rápido: criar uma fábrica de dados](quickstart-create-data-factory-powershell.md).

## <a name="sample-transformation-pipeline"></a>Pipeline de transformação de exemplos
No pipeline de exemplo seguinte, existe uma atividade do tipo **HDInsightHive** na secção **activities**. Neste exemplo, a [atividade Hive do HDInsight](transform-data-using-hadoop-hive.md) transforma dados de um armazenamento de Blobs do Azure mediante a execução de um ficheiro de script de Hive num cluster do Hadoop para o Azure HDInsight.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
Tenha em atenção os seguintes pontos:

- Na secção “activities”, existe apenas uma atividade cujo **type** está definido como **HDInsightHive**.
- O ficheiro de script do Hive, **partitionweblogs.hql**, é armazenado na conta de armazenamento do Azure (especificada por scriptLinkedService, denominado AzureStorageLinkedService) e na pasta de script no contentor `adfgetstarted`.
- A secção `defines` é utilizada para especificar as definições de runtime que são transmitidas ao script do Hive como valores de configuração do Hive (por exemplo, $`{hiveconf:inputtable}` e `${hiveconf:partitionedtable}`).

A secção **typeProperties** é diferente para cada atividade de transformação. Para saber mais sobre as propriedades de tipos suportadas para atividades de transformação, clique na atividade de transformação em [Atividades de transformação de dados](#data-transformation-activities).

Para obter instruções completas para criar este pipeline, veja [Tutorial: transformar dados com o Spark](tutorial-transform-data-spark-powershell.md).

## <a name="multiple-activities-in-a-pipeline"></a>Múltiplas atividades num pipeline
Os dois pipelines de exemplo anteriores só contêm uma atividade. Pode ter mais de uma atividade num pipeline. Se tiver múltiplas atividades num pipeline e as atividades subsequentes não estiverem dependentes das atividades anteriores, as atividades podem ser executadas em paralelo.

Pode utilizar a [dependência das atividades](#activity-dependency) para encadear duas atividades; a dependência define de que forma é que as atividades subsequentes dependem das atividades anteriores, determinando a condição de continuação da execução da tarefa seguinte. Uma atividade pode depender de uma ou várias atividades anteriores com condições de dependência diferentes.

## <a name="scheduling-pipelines"></a>Agendamento de pipelines
Os pipelines são agendados por acionadores. Existem diferentes tipos de acionadores (acionador Scheduler, que permite acionar vários pipelines de acordo com uma agenda, bem como o acionador manual, que aciona os pipelines a pedido). Para obter mais informações sobre os acionadores, veja o artigo [Execuções de pipelines e acionadores](concepts-pipeline-execution-triggers.md).

Para que o seu acionador arranque uma execução de pipeline, tem de incluir uma referência ao pipeline desse pipeline na definição do acionador. Os pipelines e os acionadores têm uma relação “n-m” (muitos para muitos). Múltiplos acionadores podem arrancar um pipeline individual e o mesmo acionador pode arrancar vários pipelines. Quando o acionador estiver definido, tem de iniciá-lo para que o mesmo comece a acionar o pipeline. Para obter mais informações sobre os acionadores, veja o artigo [Pipeline execution and triggers](concepts-pipeline-execution-triggers.md) (Execução e acionadores de pipelines).

Por exemplo, imagine que tem um acionador Scheduler, o "Trigger A", que pretende que arranque o pipeline “MyCopyPipeline”. Defina o acionador, conforme mostrado no exemplo seguinte:

### <a name="trigger-a-definition"></a>Definição de Trigger A

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```



## <a name="next-steps"></a>Passos Seguintes
Veja os tutoriais seguintes para obter instruções passo a passo para criar pipelines com atividades:

- [Criar um pipeline com uma atividade de cópia](quickstart-create-data-factory-powershell.md)
- [Criar um pipeline cum uma atividade de transformação de dados](tutorial-transform-data-spark-powershell.md)
