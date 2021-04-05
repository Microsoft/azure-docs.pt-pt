---
title: Pipelines e atividades no Azure Data Factory
description: Saiba mais sobre os pipelines e as atividades no Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 870c812a68f765f987cfd3d1b953e0afeb3e9055
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100364533"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines e atividades no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-create-pipelines.md)
> * [Versão atual](concepts-pipelines-activities.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e a utilizá-los para construir fluxos de dados completos e orientados por dados para os seus cenários de movimento de dados e processamento de dados.

## <a name="overview"></a>Descrição Geral
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um agrupamento lógico de atividades que, em conjunto, executam uma tarefa. Por exemplo, um pipeline pode conter um conjunto de atividades que ingerem e limpam dados de registos e, em seguida, iniciam um fluxo de dados de mapeamento para analisar os dados de registo. O pipeline permite-lhe gerir as atividades como um conjunto, em vez de cada uma individualmente. Implementa e agenda o pipeline, em vez das atividades de forma independente.

As atividades num pipeline definem as ações a executar nos seus dados. Por exemplo, pode utilizar uma atividade de cópia para copiar dados do SQL Server para um Azure Blob Storage. Em seguida, utilize uma atividade de fluxo de dados ou uma atividade de Caderno databricks para processar e transformar dados do armazenamento de bolhas para um pool Azure Synapse Analytics em cima do qual são construídas soluções de relatório de inteligência empresarial.

A Data Factory tem três agrupamentos de atividades: atividades de movimento de [dados,](copy-activity-overview.md) [atividades de transformação de dados](transform-data.md)e [atividades de controlo.](#control-flow-activities) Uma atividade pode ter zero ou mais [conjuntos de dados](concepts-datasets-linked-services.md) de entrada e produzir um ou mais [conjuntos de dados](concepts-datasets-linked-services.md) de saída. O diagrama seguinte mostra a relação entre pipelines, atividades e conjuntos de dados no Data Factory:

![Relação entre conjuntos de dados, atividades e pipelines](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

Um conjunto de dados de entrada representa a entrada para uma atividade no pipeline, e um conjunto de dados de saída representa a saída para a atividade. Os conjuntos de dados identificam dados dentro de diferentes arquivos de dados, como tabelas, ficheiros, pastas e documentos. Depois de criar um conjunto de dados, pode utilizá-lo com atividades num pipeline. Por exemplo, um conjunto de dados pode ser um conjunto de dados de entrada/saída de uma atividade Cópia ou de uma atividade HDInsightHive. Para obter mais informações sobre os conjuntos de dados, veja o artigo [Datasets in Azure Data Factory](concepts-datasets-linked-services.md) (Conjuntos de Dados no Azure Data Factory).

## <a name="data-movement-activities"></a>Atividades de movimento de dados

A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados apresentados na tabela nesta secção. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Para obter mais informações, veja o artigo [Copy Activity - Overview](copy-activity-overview.md) (Atividade de Cópia - Descrição Geral).

## <a name="data-transformation-activities"></a>Atividades de transformação de dados
O Azure Data Factory suporta as seguintes atividades de transformação, que podem ser adicionadas a pipelines individualmente ou encadeadas com outra atividade.

Atividade de transformação de dados | Ambiente de computação
---------------------------- | -------------------
[Fluxo de Dados](control-flow-execute-data-flow-activity.md) | Azure Databricks gerido pela Azure Data Factory
[Função do Azure](control-flow-azure-function-activity.md) | Funções do Azure
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Hadoop Streaming](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Atividades do Azure Machine Learning Studio (clássico): Execução de Lote e Recurso de Atualização](transform-data-using-machine-learning.md) | VM do Azure
[Procedimento Armazenado](transform-data-using-stored-procedure.md) | Azure SQL, Azure Synapse Analytics ou SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure Data Lake Analytics
[Atividade Personalizada](transform-data-using-dotnet-custom-activity.md) | Azure Batch
[Databricks Notebook](transform-data-databricks-notebook.md) | Azure Databricks
[Atividade do frasco de dados](transform-data-databricks-jar.md) | Azure Databricks
[Atividade python de dados](transform-data-databricks-python.md) | Azure Databricks

Para obter mais informações, veja o artigo [Data Transformation Activities](transform-data.md) (Atividades de Transformação de Dados).

## <a name="control-flow-activities"></a>Atividades de fluxo de controlo
São suportadas as atividades de fluxo de controlo abaixo:

Atividade de controlo | Description
---------------- | -----------
[Variável do apêndice](control-flow-append-variable-activity.md) | Adicione um valor a uma variável de matriz existente.
[Executar Pipeline](control-flow-execute-pipeline-activity.md) | A atividade Executar Pipeline permite que um pipeline do Data Factory invoque outro pipeline.
[Filtro](control-flow-filter-activity.md) | Aplique uma expressão de filtro a uma matriz de entrada
[Para cada um](control-flow-for-each-activity.md) | A atividade ForEach define um fluxo de controlo de repetição no seu pipeline. Esta atividade é utilizada para iterar uma coleção e executa atividades especificadas em ciclo. A implementação de ciclo desta atividade é semelhante à estrutura de ciclo Foreach nas linguagens de programação.
[Obter Metadados](control-flow-get-metadata-activity.md) | A atividade Obter Metadados pode ser utilizada para obter os metadados de quaisquer dados no Azure Data Factory.
[Atividade Se Condição](control-flow-if-condition-activity.md) | Se Condição pode ser utilizada com base em ramos numa condição que é avaliada como verdadeira ou falsa. A atividade Se Condição disponibiliza a mesma funcionalidade que as instruções “se” fornecem nas linguagens de programação. Avalia um conjunto de atividades quando a condição avalia `true` e outro conjunto de atividades quando a condição avalia `false.`
[Atividade de Pesquisa](control-flow-lookup-activity.md) | A atividade de Pesquisa pode ser utilizada para ler ou procurar registos/nomes de tabelas/valores em qualquer origem externa. Este resultado pode ser ainda referenciado por atividades subsequentes.
[Definir Variável](control-flow-set-variable-activity.md) | Definir o valor de uma variável existente.
[Até a Atividade](control-flow-until-activity.md) | Implementa o ciclo Do-Until que é semelhante à estrutura de ciclo Do-Until nas linguagens de programação. Executa um conjunto de atividades num ciclo até que a condição associada às atividades seja avaliada como verdadeira. Pode especificar um valor de tempo limite para a atividade Until no Data Factory.
[Atividade de Validação](control-flow-validation-activity.md) | Certifique-se de que um gasoduto só continua a execução se existir um conjunto de dados de referência, cumprir um critério especificado ou se tiver sido atingido um tempo limite.
[Aguardar Atividade](control-flow-wait-activity.md) | Quando utiliza uma atividade de espera num oleoduto, o gasoduto aguarda o tempo especificado antes de continuar com a execução das atividades subsequentes.
[Atividade Web](control-flow-web-activity.md) | A atividade Web pode ser utilizada para chamar um ponto final REST personalizado a partir de um pipeline do Data Factory. Pode transmitir conjuntos de dados e serviços ligados aos quais a atividade tem acesso e que pode consumir.
[Atividade de Webhook](control-flow-webhook-activity.md) | Utilizando a atividade webhook, ligue para um ponto final e passe um URL de retorno de chamadas. O gasoduto aguarda que a chamada seja invocada antes de prosseguir para a próxima atividade.

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
        },
        "concurrency": <your max pipeline concurrency>,
        "annotations": [
        ]
    }
}
```

Etiqueta | Descrição | Tipo | Necessário
--- | ----------- | ---- | --------
name | Nome do pipeline. Especifique um nome que represente a ação que o pipeline realiza. <br/><ul><li>Número máximo de carateres: 140</li><li>Deve começar com uma letra, número ou um sublinhado ( \_ )</li><li>Não são permitidos personagens seguintes: ".", "+", """/", "<", ">","*", "%", "&",""" \" </li></ul> | String | Yes
descrição | Especifique o texto que descreve para o que é utilizado o pipeline. | String | No
atividades | A secção **atividades** pode ter uma ou mais atividades definidas na mesma. Veja a secção [JSON da Atividade](#activity-json) para obter detalhes sobre o elemento JSON das atividades. | Matriz | Yes
parâmetros | A secção **parâmetros** pode ter um ou mais parâmetros definidos no pipeline, tornando-o flexível para reutilização. | Lista | No
concurrency | O número máximo de execuções simultâneas que o gasoduto pode ter. Por padrão, não há máximo. Se o limite de concordância for atingido, os gasodutos adicionais são preenchidos até que os anteriores estejam completos | Número | No 
anotações | Uma lista de tags associadas ao oleoduto | Matriz | No

## <a name="activity-json"></a>JSON da Atividade
A secção **atividades** pode ter uma ou mais atividades definidas na mesma. Existem dois tipos principais de atividades: atividades de Execução e de Controlo.

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
name | Nome da atividade. Especifique um nome que represente a ação que a atividade realiza. <br/><ul><li>Número máximo de carateres: 55</li><li>Deve começar com um número de letra, ou um sublinhado \_ ()</li><li>Não são permitidos personagens seguintes: ".", "+", """/", "<", ">","*", "%", "&",""" \" | Yes</li></ul>
descrição | Texto que descreve para o que é utilizada a atividade | Yes
tipo | Tipo de atividade. Consulte as atividades de [movimento de dados,](#data-movement-activities) [atividades de transformação de dados](#data-transformation-activities)e [atividades de controlo](#control-flow-activities) para diferentes tipos de atividades. | Yes
linkedServiceName | Nome do serviço ligado utilizado pela atividade.<br/><br/>Uma atividade pode exigir que especifique o serviço ligado que liga ao ambiente de computação necessário. | Sim para atividade hdInsight, Azure Machine Learning Studio (clássico) Atividade de Pontuação de Lote, Atividade de Procedimento Armazenado. <br/><br/>Não para todas as outras.
typeProperties | As propriedades na secção typeProperties dependem de cada tipo de atividade. Para ver as propriedades do tipo de uma atividade, clique nas ligações para a atividade na secção anterior. | No
política | Políticas que afetam o comportamento de runtime da atividade. Esta propriedade inclui um tempo limite e um comportamento relempor. Se não for especificado, são utilizados valores predefinidos. Para obter mais informações, veja a secção [Política das atividades](#activity-policy). | No
dependsOn | Esta propriedade é utilizada para definir as dependências de atividade e de que forma as atividades subsequentes dependem de atividades anteriores. Para obter mais informações, veja [Dependência das atividades](#activity-dependency) | No

### <a name="activity-policy"></a>Política das atividades
As políticas afetam o comportamento de runtime de uma atividade, proporcionando opções de configuração. As políticas das Atividades só estão disponíveis para as atividades de execução.

### <a name="activity-policy-json-definition"></a>Definição JSON da política de atividade

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity",
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

Nome JSON | Description | Valores Permitidos | Necessário
--------- | ----------- | -------------- | --------
tempo limite | Especifica o tempo limite para a execução da atividade. | Timespan | N.º O tempo limite predefinido é de 7 dias.
retry | Número máximo de repetições | Número inteiro | N.º A predefinição é 0
retryIntervalInSeconds | O atraso entre as tentativas de repetição em segundos | Número inteiro | N.º O padrão é de 30 segundos
secureOutput | Quando definido como verdadeiro, a saída da atividade é considerada segura e não é registada para monitorização. | Booleano | N.º A predefinição é falso.

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
name | Nome da atividade. Especifique um nome que represente a ação que a atividade realiza.<br/><ul><li>Número máximo de carateres: 55</li><li>Deve começar com um número de letra, ou um sublinhado \_ ()</li><li>Não são permitidos personagens seguintes: ".", "+", """/", "<", ">","*", "%", "&",""" \" | Yes</li><ul>
descrição | Texto que descreve para o que é utilizada a atividade | Yes
tipo | Tipo de atividade. Veja os diferentes tipos de atividades nas secções sobre as [atividades de movimento de dados](#data-movement-activities), as [atividades de transformação de dados](#data-transformation-activities) e as [atividades de controlo](#control-flow-activities). | Yes
typeProperties | As propriedades na secção typeProperties dependem de cada tipo de atividade. Para ver as propriedades do tipo de uma atividade, clique nas ligações para a atividade na secção anterior. | No
dependsOn | Esta propriedade é utilizada para definir a Dependência da Atividade e de que forma as atividades subsequentes dependem de atividades anteriores. Para mais informações, consulte [a dependência da atividade.](#activity-dependency) | No

### <a name="activity-dependency"></a>Dependência das atividades
A Dependência da Atividade define como as atividades subsequentes dependem de atividades anteriores, determinando a condição de continuar a executar a próxima tarefa. Uma atividade pode depender de uma ou várias atividades anteriores com condições de dependência diferentes.

As condições de dependência diferentes são: Bem-sucedida, Falha, Ignorada, Concluída.

Por exemplo, se um pipeline tiver a Atividade A -> Atividade B, os diferentes cenários que podem ocorrer são:

- A Atividade B tem a condição de dependência de a Atividade A ser **bem-sucedida** - a Atividade B só é executada se o estado final de A for “bem-sucedida”
- A Atividade B tem a condição de dependência de a Atividade A **falhar** - a Atividade B só é executada se o estado final de A for “falha”
- A Atividade B tem a condição de dependência de a Atividade A ser **concluída** - a Atividade B só é executada se o estado final de A for “concluída”
- A atividade B tem uma condição de dependência da Atividade A com **ignorado**: A atividade B funciona se a atividade A tiver um estatuto final de ignorado. “Ignorada” ocorre no cenário de Atividade X -> Atividade Y -> Atividade Z, em que cada atividade só é executada se a anterior for bem-sucedida. Se a Atividade X falhar, então a Atividade Y tem um estatuto de "Skipped" porque nunca executa. Da mesma forma, a Atividade Z também tem um estatuto de "Skipped".

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>Exemplo: a Atividade 2 depende de a Atividade 1 ser bem-sucedida

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
No pipeline de exemplo seguinte, existe uma atividade do tipo **Cópia** na secção **activities**. Nesta amostra, a atividade de [cópias](copy-activity-overview.md) copia dados de um armazenamento Azure Blob para uma base de dados na Base de Dados Azure SQL.

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
- O ficheiro de scripts da Hive, **partitionweblogs.hql,** está armazenado na conta de Armazenamento Azure (especificada pelo scriptLinkedService, denominado AzureStorageLinkedService) e na pasta de scripts no recipiente `adfgetstarted` .
- A secção `defines` é utilizada para especificar as definições de runtime que são transmitidas ao script do Hive como valores de configuração do Hive (por exemplo, $`{hiveconf:inputtable}` e `${hiveconf:partitionedtable}`).

A secção **typeProperties** é diferente para cada atividade de transformação. Para saber mais sobre as propriedades de tipos suportadas para atividades de transformação, clique na atividade de transformação em [Atividades de transformação de dados](#data-transformation-activities).

Para obter instruções completas para criar este pipeline, veja [Tutorial: transformar dados com o Spark](tutorial-transform-data-spark-powershell.md).

## <a name="multiple-activities-in-a-pipeline"></a>Múltiplas atividades num pipeline
Os dois pipelines de exemplo anteriores só contêm uma atividade. Pode ter mais de uma atividade num pipeline. Se tiver múltiplas atividades num pipeline e as atividades subsequentes não estiverem dependentes das atividades anteriores, as atividades podem ser executadas em paralelo.

Pode utilizar a [dependência das atividades](#activity-dependency) para encadear duas atividades; a dependência define de que forma é que as atividades subsequentes dependem das atividades anteriores, determinando a condição de continuação da execução da tarefa seguinte. Uma atividade pode depender de uma ou várias atividades anteriores com condições de dependência diferentes.

## <a name="scheduling-pipelines"></a>Agendamento de pipelines
Os pipelines são agendados por acionadores. Existem diferentes tipos de gatilhos (gatilho do programador, que permite acionar os gasodutos num horário de relógio de parede, bem como o gatilho manual, que aciona os gasodutos a pedido). Para obter mais informações sobre os acionadores, veja o artigo [Pipeline execution and triggers](concepts-pipeline-execution-triggers.md) (Execução e acionadores de pipelines).

Para que o seu acionador arranque uma execução de pipeline, tem de incluir uma referência ao pipeline desse pipeline na definição do acionador. Os pipelines e os acionadores têm uma relação “n-m” (muitos para muitos). Vários gatilhos podem iniciar um único oleoduto, e o mesmo gatilho pode arrancar vários oleodutos. Quando o acionador estiver definido, tem de iniciá-lo para que o mesmo comece a acionar o pipeline. Para obter mais informações sobre os acionadores, veja o artigo [Pipeline execution and triggers](concepts-pipeline-execution-triggers.md) (Execução e acionadores de pipelines).

Por exemplo, digamos que tens um gatilho do Scheduler, "Trigger A", que eu quero dar o pontapé de saída do meu oleoduto, "MyCopyPipeline". Define o gatilho, como mostra o seguinte exemplo:

### <a name="trigger-a-definition"></a>Definição do Acionador A

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

## <a name="next-steps"></a>Passos seguintes
Veja os tutoriais seguintes para obter instruções passo a passo para criar pipelines com atividades:

- [Criar um pipeline com uma atividade de cópia](quickstart-create-data-factory-powershell.md)
- [Criar um pipeline cum uma atividade de transformação de dados](tutorial-transform-data-spark-powershell.md)
