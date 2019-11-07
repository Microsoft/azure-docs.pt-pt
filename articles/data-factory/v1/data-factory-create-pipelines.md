---
title: Criar/agendar pipelines, atividades de encadeamento no Data Factory
description: Aprenda a criar um pipeline de dados no Azure Data Factory para mover e transformar dados. Crie um fluxo de trabalho controlado por dados para produzir informações prontas para uso.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f93bea240ee3f139c9be84199d116f9f3f231261
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682732"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines e atividades no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-create-pipelines.md)
> * [Versão 2 (versão atual)](../concepts-pipelines-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [pipelines na v2](../concepts-pipelines-activities.md).

Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e a utilizá-los para construir fluxos de dados completos e orientados por dados para os seus cenários de movimento de dados e processamento de dados.

> [!NOTE]
> Este artigo pressupõe que você tenha passado pela [introdução ao Azure data Factory](data-factory-introduction.md). Se você não tiver experiência prática com a criação de fábricas de dados, passar pelo tutorial de [transformação de dados](data-factory-build-your-first-pipeline.md) e/ou no tutorial de movimentação de [dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ajudará você a entender melhor este artigo.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. Os pipelines são agrupamentos lógicos de atividades que, em conjunto, realizam uma tarefa. As atividades num pipeline definem as ações a efetuar nos seus dados. Por exemplo, pode utilizar uma atividade de cópia para copiar dados de um SQL Server no local para um Armazenamento de Blobs do Azure. Em seguida, utilize uma atividade do Hive que execute um script do Hive num cluster do Azure HDInsight para processar/transformar os dados do armazenamento de blobs para produzir dados de saída. Por fim, utilize uma segunda atividade de cópia para copiar os dados de saída para um armazém do Azure SQL Data Warehouse sobre o qual são criadas soluções de relatórios de business intelligence (BI).

Uma atividade pode ter zero ou mais [conjuntos de dados](data-factory-create-datasets.md) de entrada e produzir um ou mais [conjuntos de dados](data-factory-create-datasets.md) de saída. O diagrama seguinte mostra a relação entre pipelines, atividades e conjuntos de dados no Data Factory:

![Relação entre pipeline, atividade e conjunto de conjuntos](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Um pipeline permite que você gerencie atividades como um conjunto, em vez de cada uma individualmente. Por exemplo, você pode implantar, agendar, suspender e retomar um pipeline, em vez de lidar com atividades no pipeline de forma independente.

O Data Factory suporta dois tipos de atividades -- atividades de movimento de dados e atividades de transformação de dados. Cada atividade pode ter zero ou mais [conjuntos](data-factory-create-datasets.md) de dados de entrada e produzir um ou mais conjuntos de dados de saída.

Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída da atividade. Os conjuntos de dados identificam dados dentro de diferentes arquivos de dados, como tabelas, ficheiros, pastas e documentos. Depois de criar um conjunto de dados, pode utilizá-lo com atividades num pipeline. Por exemplo, um conjunto de dados pode ser um conjunto de dados de entrada/saída de uma atividade Cópia ou de uma atividade HDInsightHive. Para obter mais informações sobre os conjuntos de dados, veja o artigo [Datasets in Azure Data Factory](data-factory-create-datasets.md) (Conjuntos de Dados no Azure Data Factory).

### <a name="data-movement-activities"></a>Atividades de movimento de dados
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados seguintes. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os arquivos de dados com * podem estar no local ou no Azure IaaS e requerem a instalação do [Data Management Gateway](data-factory-data-management-gateway.md) num computador no local/Azure IaaS.

Para obter mais informações, veja o artigo [Data Movement Activities (Atividades de Movimento de Dados)](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Atividades de transformação de dados
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Para obter mais informações, veja o artigo [Data Transformation Activities (Atividades de Transformação de Dados)](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Atividades .NET personalizadas
Se você precisar mover dados de/para um armazenamento de dados para o qual a atividade de cópia não dá suporte ou transformar dados usando sua própria lógica, crie uma **atividade personalizada do .net**. Para obter detalhes sobre criar e utilizar uma atividade personalizada, veja [Use custom activities in an Azure Data Factory pipeline (Utilizar atividades personalizadas num pipeline do Azure Data Factory)](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Agendar pipelines
Um pipeline está ativo somente entre a hora de **início** e a hora de **término** . Ele não é executado antes da hora de início ou após a hora de término. Se o pipeline estiver em pausa, ele não será executado independentemente de sua hora de início e de término. Para que um pipeline seja executado, ele não deve ser pausado. Consulte [agendamento e execução](data-factory-scheduling-and-execution.md) para entender como funciona o agendamento e a execução no Azure data Factory.

## <a name="pipeline-json"></a>JSON dos pipelines
Vamos ver mais de perto a definição dos pipelines no formato JSON. A estrutura genérica de um pipeline tem o aspeto seguinte:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets":
        [
        ]
    }
}
```

| Etiqueta | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome do pipeline. Especifique um nome que represente a ação que o pipeline realiza. <br/><ul><li>Número máximo de carateres: 260</li><li>Deve começar com um número de letra ou um sublinhado (\_)</li><li>Os seguintes caracteres não são permitidos: ".", "+", "?", "/", "<", ">", "\*", "%", "&", ":", "\\"</li></ul> |Sim |
| descrição | Especifique o texto que descreve para o que é utilizado o pipeline. |Sim |
| atividades | A secção **atividades** pode ter uma ou mais atividades definidas na mesma. Consulte a próxima seção para obter detalhes sobre o elemento JSON de atividades. | Sim |
| start | Data/hora de início do pipeline. Deve estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: `2016-10-14T16:32:41Z`. <br/><br/>É possível especificar uma hora local, por exemplo, uma hora ESTIMAda. Aqui está um exemplo: `2016-02-27T06:00:00-05:00`", que é a 06 EST.<br/><br/>As propriedades Start e End juntas especificam o período ativo para o pipeline. As fatias de saída são produzidas somente com neste período ativo. |Não<br/><br/>Se você especificar um valor para a propriedade end, deverá especificar o valor da Propriedade Start.<br/><br/>As horas de início e de término podem estar vazias para criar um pipeline. Você deve especificar os dois valores para definir um período ativo para que o pipeline seja executado. Se você não especificar os horários de início e término ao criar um pipeline, poderá defini-los usando o cmdlet Set-AzDataFactoryPipelineActivePeriod mais tarde. |
| completo | Data/hora de término do pipeline. Se especificado deve estar no formato ISO. Por exemplo: `2016-10-14T17:32:41Z` <br/><br/>É possível especificar uma hora local, por exemplo, uma hora ESTIMAda. Aqui está um exemplo: `2016-02-27T06:00:00-05:00`, que é a 06 EST.<br/><br/>Para executar o pipeline indefinidamente, especifique 9999-09-09 como o valor para a propriedade end. <br/><br/> Um pipeline está ativo somente entre a hora de início e a hora de término. Ele não é executado antes da hora de início ou após a hora de término. Se o pipeline estiver em pausa, ele não será executado independentemente de sua hora de início e de término. Para que um pipeline seja executado, ele não deve ser pausado. Consulte [agendamento e execução](data-factory-scheduling-and-execution.md) para entender como funciona o agendamento e a execução no Azure data Factory. |Não <br/><br/>Se você especificar um valor para a propriedade Start, deverá especificar o valor da Propriedade End.<br/><br/>Consulte Observações para a propriedade **Iniciar** . |
| isPaused | Se definido como true, o pipeline não será executado. Ele está no estado em pausa. Valor padrão = false. Você pode usar essa propriedade para habilitar ou desabilitar um pipeline. |Não |
| pipelineMode | O método para o agendamento de execuções para o pipeline. Os valores permitidos são: Scheduled (padrão), OneTime.<br/><br/>' Scheduled ' indica que o pipeline é executado em um intervalo de tempo especificado de acordo com seu período ativo (hora de início e término). ' OneTime ' indica que o pipeline é executado apenas uma vez. Pipelines de OneTime depois de criados não podem ser modificados/atualizados no momento. Consulte [pipeline de OneTime](#onetime-pipeline) para obter detalhes sobre a configuração de OneTime. |Não |
| expirationTime | Duração do tempo após a criação para a qual o [pipeline de uso único](#onetime-pipeline) é válido e deve permanecer provisionado. Se não houver execuções ativas, com falha ou pendentes, o pipeline será excluído automaticamente quando atingir o tempo de expiração. O valor padrão: `"expirationTime": "3.00:00:00"`|Não |
| Conjuntos |Lista de conjuntos de valores a serem usados por atividades definidas no pipeline. Essa propriedade pode ser usada para definir conjuntos de valores específicos para esse pipeline e não definidos dentro do data factory. Os conjuntos de itens definidos nesse pipeline só podem ser usados por esse pipeline e não podem ser compartilhados. Consulte [conjuntos de valores com escopo](data-factory-create-datasets.md#scoped-datasets) para obter detalhes. |Não |

## <a name="activity-json"></a>JSON da Atividade
A secção **atividades** pode ter uma ou mais atividades definidas na mesma. Cada atividade tem a seguinte estrutura de nível superior:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs": "[]",
    "outputs": "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

A tabela seguinte descreve as propriedades na definição JSON da atividade:

| Etiqueta | Descrição | Necessário |
| --- | --- | --- |
| nome | Nome da atividade. Especifique um nome que represente a ação que a atividade realiza. <br/><ul><li>Número máximo de carateres: 260</li><li>Deve começar com um número de letra ou um sublinhado (\_)</li><li>Os seguintes caracteres não são permitidos: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li></ul> |Sim |
| descrição | Texto que descreve para o que é utilizada a atividade |Sim |
| tipo | Tipo de atividade. Confira as seções atividades de [movimentação de dados](#data-movement-activities) e atividades de [transformação de dados](#data-transformation-activities) para diferentes tipos de atividades. |Sim |
| informações |Tabelas de entrada usadas pela atividade<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Sim |
| produz |Tabelas de saída usadas pela atividade.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Sim |
| linkedServiceName |Nome do serviço ligado utilizado pela atividade. <br/><br/>Uma atividade pode exigir que especifique o serviço ligado que liga ao ambiente de computação necessário. |Sim para a atividade do HDInsight e Azure Machine Learning atividade de Pontuação de lote <br/><br/>Não para todas as outras. |
| typeProperties |As propriedades na seção **typeproperties** dependem do tipo da atividade. Para ver as propriedades do tipo de uma atividade, clique nas ligações para a atividade na secção anterior. | Não |
| política |Políticas que afetam o comportamento de runtime da atividade. Se não for especificado, as políticas padrão serão usadas. |Não |
| Agendador | a propriedade "scheduler" é usada para definir o agendamento desejado para a atividade. Suas subpropriedades são as mesmas que as da [propriedade de disponibilidade em um conjunto de um](data-factory-create-datasets.md#dataset-availability). |Não |

### <a name="policies"></a>Políticas
As políticas afetam o comportamento de tempo de execução de uma atividade, especificamente quando a fatia de uma tabela é processada. A tabela a seguir fornece os detalhes.

| Propriedade | Valores permitidos | Valor padrão | Descrição |
| --- | --- | --- | --- |
| corrente |Número inteiro <br/><br/>Valor máximo: 10 |1 |Número de execuções simultâneas da atividade.<br/><br/>Ele determina o número de execuções de atividade paralelas que podem ocorrer em diferentes fatias. Por exemplo, se uma atividade precisar passar por um grande conjunto de dados disponíveis, ter um valor de simultaneidade maior acelera o processamento de dados. |
| Executionpriorityorder como |NewestFirst<br/><br/>OldestFirst |OldestFirst |Determina a ordenação de fatias de dados que estão sendo processadas.<br/><br/>Por exemplo, se você tiver duas fatias (uma acontecendo em 16:00 e outra às 17:00), e ambas estiverem com execução pendente. Se você definir Executionpriorityorder como como NewestFirst, a fatia em 5 PM será processada primeiro. Da mesma forma, se você definir Executionpriorityorder como como OldestFIrst, a fatia às 4 PM será processada. |
| retry |Número inteiro<br/><br/>O valor máximo pode ser 10 |0 |Número de repetições antes de o processamento de dados para a fatia ser marcado como falha. A execução da atividade para uma fatia de dados é repetida até a contagem de repetições especificada. A repetição é feita assim que possível após a falha. |
| tempo limite |Período |00:00:00 |Tempo limite para a atividade. Exemplo: 00:10:00 (implica o tempo limite de 10 minutos)<br/><br/>Se um valor não for especificado ou for 0, o tempo limite será infinito.<br/><br/>Se o tempo de processamento de dados em uma fatia exceder o valor de tempo limite, ele será cancelado e o sistema tentará repetir o processamento. O número de repetições depende da Propriedade Retry. Quando o tempo limite ocorre, o status é definido como TimedOut. |
| retardo |Período |00:00:00 |Especifique o atraso antes que o processamento de dados da fatia seja iniciado.<br/><br/>A execução da atividade para uma fatia de dados é iniciada após o atraso ultrapassar o tempo de execução esperado.<br/><br/>Exemplo: 00:10:00 (implica atraso de 10 minutos) |
| longRetry |Número inteiro<br/><br/>Valor máximo: 10 |1 |O número de tentativas de repetição longas antes da execução da fatia falhar.<br/><br/>as tentativas de longRetry são espaçadas por longRetryInterval. Portanto, se você precisar especificar um tempo entre as tentativas de repetição, use longRetry. Se ambas as opções Retry e longRetry forem especificadas, cada tentativa de longRetry incluirá tentativas de repetição e o número máximo de tentativas será Retry * longRetry.<br/><br/>Por exemplo, se tivermos as seguintes configurações na política de atividade:<br/>Tentar novamente: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Suponha que haja apenas uma fatia a ser executada (o status está aguardando) e a execução da atividade falha a cada vez. Inicialmente, haveria três tentativas consecutivas de execução. Após cada tentativa, o status da fatia seria tentar novamente. Após o fim das três primeiras tentativas, o status da fatia seria LongRetry.<br/><br/>Após uma hora (ou seja, o valor de longRetryInteval), haveria outro conjunto de três tentativas de execução consecutivas. Depois disso, o status da fatia seria falhado e não haverá mais tentativas de repetição. Portanto, as 6 tentativas gerais foram feitas.<br/><br/>Se qualquer execução for realizada com sucesso, o status da fatia estará pronto e não haverá mais novas tentativas.<br/><br/>o longRetry pode ser usado em situações em que os dados dependentes chegam em momentos não determinísticos ou o ambiente geral é instável sob o que ocorre o processamento de dados. Nesses casos, fazer novas tentativas uma após a outra pode não ajudar e fazer isso após um intervalo de tempo resulta na saída desejada.<br/><br/>Palavra de cuidado: não defina valores altos para longRetry ou longRetryInterval. Normalmente, valores mais altos implicam outros problemas do sistema. |
| longRetryInterval |Período |00:00:00 |O atraso entre as tentativas de repetição longas |

## <a name="sample-copy-pipeline"></a>Pipeline de cópia de exemplo
No pipeline de exemplo seguinte, existe uma atividade do tipo **Cópia** na secção **activities**. No exemplo, a [atividade de cópia](data-factory-data-movement-activities.md) copia os dados de um armazenamento de blobs do Azure para uma base de dados SQL do Azure.

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
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```

Tenha em atenção os seguintes pontos:

* Na secção atividades, existe apenas uma atividade cujo **type** está definido como **Copy**.
* A entrada da atividade está definida como **InputDataset** e a saída como **OutputDataset**. Veja o artigo [Conjuntos de dados](data-factory-create-datasets.md) para saber como definir conjuntos de dados em JSON.
* Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink. Na seção [atividades de movimentação de dados](#data-movement-activities) , clique no armazenamento de dados que você deseja usar como fonte ou coletor para saber mais sobre como mover dados para/desse armazenamento de dados.

Para obter uma explicação completa da criação desse pipeline, consulte [tutorial: copiar dados do armazenamento de BLOBs para o banco de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="sample-transformation-pipeline"></a>Pipeline de transformação de exemplos
No pipeline de exemplo seguinte, existe uma atividade do tipo **HDInsightHive** na secção **activities**. Neste exemplo, a [atividade Hive do HDInsight](data-factory-hive-activity.md) transforma dados de um armazenamento de Blobs do Azure mediante a execução de um ficheiro de script de Hive num cluster do Hadoop para o Azure HDInsight.

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
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Tenha em atenção os seguintes pontos:

* Na secção “activities”, existe apenas uma atividade cujo **type** está definido como **HDInsightHive**.
* O ficheiro do Script de ramo de registo **partitionweblogs.hql** é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService, denominado **AzureStorageLinkedService**) e na pasta **script** no contentor **adfgetstarted**.
* A seção `defines` é usada para especificar as configurações de tempo de execução que são passadas para o script do hive como valores de configuração do hive (por exemplo, `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

A secção **typeProperties** é diferente para cada atividade de transformação. Para saber mais sobre as propriedades de tipo com suporte para uma atividade de transformação, clique na atividade de transformação na tabela [atividades de transformação de dados](#data-transformation-activities) .

Para obter uma explicação completa da criação desse pipeline, consulte [tutorial: criar seu primeiro pipeline para processar dados usando o cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="multiple-activities-in-a-pipeline"></a>Múltiplas atividades num pipeline
Os dois pipelines de exemplo anteriores só contêm uma atividade. Pode ter mais de uma atividade num pipeline.

Se você tiver várias atividades em um pipeline e a saída de uma atividade não for uma entrada de outra atividade, as atividades poderão ser executadas em paralelo se as fatias de dados de entrada para as atividades estiverem prontas.

Você pode encadear duas atividades fazendo com que o conjunto de dados de saída de uma atividade seja o de entrada da outra atividade. A segunda atividade é executada somente quando a primeira é concluída com êxito.

![Encadeando atividades no mesmo pipeline](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Neste exemplo, o pipeline tem duas atividades: Atividade1 e da atividade2. O Atividade1 usa dataSet1 como uma entrada e produz um Dataset2 de saída. A atividade usa Dataset2 como uma entrada e produz um Dataset3 de saída. Como a saída de Atividade1 (Dataset2) é a entrada de da atividade2, o da atividade2 é executado somente depois que a atividade é concluída com êxito e produz a fatia Dataset2. Se o Atividade1 falhar por algum motivo e não produzir a fatia Dataset2, a atividade 2 não será executada para essa fatia (por exemplo: 9h às 0,10).

Você também pode encadear atividades que estão em pipelines diferentes.

![Encadeando atividades em dois pipelines](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Neste exemplo, Pipeline1 tem apenas uma atividade que usa dataSet1 como uma entrada e produz Dataset2 como uma saída. O Pipeline2 também tem apenas uma atividade que usa Dataset2 como entrada e Dataset3 como uma saída.

Para obter mais informações, consulte [agendamento e execução](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).
## <a name="create-and-monitor-pipelines"></a>Criar e monitorar pipelines
Você pode criar pipelines usando uma dessas ferramentas ou SDKs.

- Assistente de Cópia
- Visual Studio
- Azure PowerShell
- Modelo Azure Resource Manager
- API REST
- API .NET

Consulte os tutoriais a seguir para obter instruções passo a passo para criar pipelines usando uma dessas ferramentas ou SDKs.

- [Criar um pipeline cum uma atividade de transformação de dados](data-factory-build-your-first-pipeline.md)
- [Criar um pipeline com uma atividade de movimentação de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Depois que um pipeline é criado/implantado, você pode gerenciar e monitorar seus pipelines usando as portal do Azure Blades ou monitorar e gerenciar o aplicativo. Consulte os tópicos a seguir para obter instruções passo a passo.

- [Monitore e gerencie pipelines usando as folhas de portal do Azure](data-factory-monitor-manage-pipelines.md).
- [Monitorar e gerenciar pipelines usando o aplicativo monitorar e gerenciar](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Pipeline de OneTime
Você pode criar e agendar um pipeline para ser executado periodicamente (por exemplo: por hora ou diariamente) dentro das horas de início e de término especificadas na definição do pipeline. Consulte Agendando atividades para obter detalhes. Você também pode criar um pipeline que é executado apenas uma vez. Para fazer isso, defina a propriedade **pipelinemode** na definição de pipeline como **OneTime** , conforme mostrado no exemplo de JSON a seguir. O valor padrão dessa propriedade é **agendado**.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
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
                "name": "CopyActivity-0"
            }
        ],
        "pipelineMode": "OneTime"
    }
}
```

Tenha em atenção o seguinte:

* Os horários de **início** e **término** do pipeline não são especificados.
* A **disponibilidade** dos conjuntos de dados de entrada e saída é especificada (**frequência** e **intervalo**), mesmo que data Factory não use os valores.
* A exibição de diagrama não mostra pipelines de uso único. Este comportamento é propositado.
* Pipelines de uso único não podem ser atualizados. Você pode clonar um pipeline de uso único, renomeá-lo, atualizar as propriedades e implantá-lo para criar outro.

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre conjuntos de dados, consulte o artigo [criar conjuntos](data-factory-create-datasets.md) de dados.
- Para obter mais informações sobre como os pipelines são agendados e executados, consulte [agendamento e execução no artigo Azure data Factory](data-factory-scheduling-and-execution.md) .
