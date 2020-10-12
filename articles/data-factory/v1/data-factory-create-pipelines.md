---
title: Criar/Agendar Oleodutos, Atividades em Cadeia na Fábrica de Dados
description: Aprenda a criar um pipeline de dados na Azure Data Factory para mover e transformar dados. Crie um fluxo de trabalho orientado por dados para produzir informação pronta a usar.
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
ms.openlocfilehash: 2d7701e215011165ffef33353de7f9372b1142cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440750"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Oleodutos e Atividades na Fábrica de Dados Azure
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-create-pipelines.md)
> * [Versão 2 (versão atual)](../concepts-pipelines-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [Pipelines em V2](../concepts-pipelines-activities.md).

Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e a utilizá-los para construir fluxos de dados completos e orientados por dados para os seus cenários de movimento de dados e processamento de dados.

> [!NOTE]
> Este artigo pressupõe que passou pela [Introdução à Fábrica de Dados Azure.](data-factory-introduction.md) Se você não tiver prática na criação de fábricas de dados, passar por [tutorial de transformação](data-factory-build-your-first-pipeline.md) de dados e/ou [tutorial de movimento de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) iria ajudá-lo a entender melhor este artigo.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um agrupamento lógico de atividades que, em conjunto, executam uma tarefa. As atividades num pipeline definem as ações a executar nos seus dados. Por exemplo, pode utilizar uma atividade de cópia para copiar dados de uma base de dados do SQL Server para um Azure Blob Storage. Em seguida, utilize uma atividade do Hive que execute um script do Hive num cluster do Azure HDInsight para processar/transformar os dados do armazenamento de blobs para produzir dados de saída. Finalmente, utilize uma segunda atividade de cópia para copiar os dados de saída para um Azure Synapse Analytics (ex-SQL Data Warehouse) em cima do qual são construídas soluções de reporte de informação de inteligência empresarial (BI).

Uma atividade pode ter zero ou mais [conjuntos de dados](data-factory-create-datasets.md) de entrada e produzir um ou mais [conjuntos de dados](data-factory-create-datasets.md) de saída. O diagrama seguinte mostra a relação entre pipelines, atividades e conjuntos de dados no Data Factory:

![Relação entre o pipeline, a atividade e o conjunto de dados](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Um oleoduto permite-lhe gerir as atividades como um conjunto em vez de cada um individualmente. Por exemplo, pode implantar, programar, suspender e retomar um oleoduto, em vez de lidar com atividades no oleoduto de forma independente.

O Data Factory suporta dois tipos de atividades -- atividades de movimento de dados e atividades de transformação de dados. Cada atividade pode ter [conjuntos de dados de](data-factory-create-datasets.md) entrada zero ou mais e produzir um ou mais conjuntos de dados de saída.

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
Se necessitar de mover dados de/para uma loja de dados que a Atividade de Cópia não suporta, ou transformar dados usando a sua própria lógica, crie uma **atividade personalizada .NET**. Para obter detalhes sobre criar e utilizar uma atividade personalizada, veja [Use custom activities in an Azure Data Factory pipeline (Utilizar atividades personalizadas num pipeline do Azure Data Factory)](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Agendar oleodutos
Um gasoduto só está ativo entre a hora **de início** e **o fim.** Não é executado antes da hora de início ou depois do fim do tempo. Se o gasoduto for interrompido, não será executado independentemente do seu início e fim. Para que um oleoduto corra, não deve ser interrompido. Consulte [Agendamento e Execução](data-factory-scheduling-and-execution.md) para entender como funciona o agendamento e execução na Fábrica de Dados Azure.

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

| Etiqueta | Descrição | Obrigatório |
| --- | --- | --- |
| name |Nome do pipeline. Especifique um nome que represente a ação que o pipeline realiza. <br/><ul><li>Número máximo de carateres: 260</li><li>Deve começar com um número de letra, ou um sublinhado \_ ()</li><li>Não são permitidos personagens seguintes: ".", "+", "" "/", "<", ">", \* "%", "&", \\ ""</li></ul> |Sim |
| descrição | Especifique o texto que descreve para o que é utilizado o pipeline. |Sim |
| atividades | A secção **atividades** pode ter uma ou mais atividades definidas na mesma. Consulte a secção seguinte para mais detalhes sobre o elemento JSON das atividades. | Sim |
| iniciar | Hora de início para o oleoduto. Deve estar no [formato ISO.](https://en.wikipedia.org/wiki/ISO_8601) Por exemplo: `2016-10-14T16:32:41Z`. <br/><br/>É possível especificar uma hora local, por exemplo, uma hora EST. Aqui está um exemplo: `2016-02-27T06:00:00-05:00` " que é 6 AM EST.<br/><br/>As propriedades de início e de fim em conjunto especificam o período ativo para o gasoduto. As fatias de saída só são produzidas neste período ativo. |Não<br/><br/>Se especificar um valor para a propriedade final, deve especificar valor para a propriedade inicial.<br/><br/>Os tempos de início e fim podem estar vazios para criar um oleoduto. Tem de especificar ambos os valores para definir um período ativo para o gasoduto funcionar. Se não especificar os tempos de início e de fim ao criar um oleoduto, pode defini-los utilizando o Set-AzDataFactoryPipelineActivePeriod cmdlet mais tarde. |
| fim | Fim da data para o oleoduto. Se especificado deve estar no formato ISO. Por exemplo: `2016-10-14T17:32:41Z` <br/><br/>É possível especificar uma hora local, por exemplo, uma hora EST. Aqui está um exemplo: `2016-02-27T06:00:00-05:00` , que é 6 AM EST.<br/><br/>Para executar o pipeline de forma indefinida, especifique 9999-09-09 como o valor da propriedade end. <br/><br/> Um gasoduto só está ativo entre a hora de início e o fim. Não é executado antes da hora de início ou depois do fim do tempo. Se o gasoduto for interrompido, não será executado independentemente do seu início e fim. Para que um oleoduto corra, não deve ser interrompido. Consulte [Agendamento e Execução](data-factory-scheduling-and-execution.md) para entender como funciona o agendamento e execução na Fábrica de Dados Azure. |Não <br/><br/>Se especificar um valor para a propriedade inicial, deve especificar valor para a propriedade final.<br/><br/>Consulte as notas para a propriedade **inicial.** |
| isPaused | Se for verdadeiro, o gasoduto não funciona. Está no estado de pausa. Valor predefinido = falso. Pode utilizar esta propriedade para ativar ou desativar um oleoduto. |Não |
| pipelineMode | O método de agendamento vai para o oleoduto. Os valores permitidos são: programado (predefinição), uma vez.<br/><br/>O «Programado» indica que o gasoduto funciona com um intervalo de tempo especificado de acordo com o período ativo (tempo de início e fim). 'Onetime' indica que o gasoduto funciona apenas uma vez. Os gasodutos únicos criados não podem ser modificados/atualizados atualmente. Consulte [o pipeline OneTime](#onetime-pipeline) para obter detalhes sobre a definição única. |Não |
| tempo de expiração | Duração do tempo após a criação para a qual o [gasoduto único](#onetime-pipeline) é válido e deve permanecer a provisionado. Se não tiver qualquer execução ativa, falhada ou pendente, o gasoduto é automaticamente eliminado assim que atingir o prazo de validade. O valor predefinido: `"expirationTime": "3.00:00:00"`|Não |
| conjuntos de dados |Lista de conjuntos de dados a utilizar por atividades definidas no pipeline. Esta propriedade pode ser usada para definir conjuntos de dados específicos a este oleoduto e não definidos dentro da fábrica de dados. Os conjuntos de dados definidos neste pipeline só podem ser utilizados por este oleoduto e não podem ser partilhados. Consulte [os conjuntos de dados scoped](data-factory-create-datasets.md#scoped-datasets) para obter mais detalhes. |Não |

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

| Etiqueta | Descrição | Obrigatório |
| --- | --- | --- |
| name | Nome da atividade. Especifique um nome que represente a ação que a atividade realiza. <br/><ul><li>Número máximo de carateres: 260</li><li>Deve começar com um número de letra, ou um sublinhado \_ ()</li><li>Não são permitidos personagens seguintes: ".", "+", "" "/", "<", ">","*","%", "&", \\ ""</li></ul> |Sim |
| descrição | Texto que descreve para o que é utilizada a atividade |Sim |
| tipo | Tipo de atividade. Consulte as [secções de Atividades](#data-movement-activities) de Movimento de Dados e [Atividades de Transformação](#data-transformation-activities) de Dados para diferentes tipos de atividades. |Sim |
| entradas |Tabelas de entrada utilizadas pela atividade<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Sim |
| saídas |Tabelas de saída usadas pela atividade.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Sim |
| linkedServiceName |Nome do serviço ligado utilizado pela atividade. <br/><br/>Uma atividade pode exigir que especifique o serviço ligado que liga ao ambiente de computação necessário. |Sim para atividade de hdinsight e atividade de pontuação de lote de machine learning Azure <br/><br/>Não para todas as outras. |
| typeProperties |As propriedades na secção **de tipoProperties** dependem do tipo de atividade. Para ver as propriedades do tipo de uma atividade, clique nas ligações para a atividade na secção anterior. | Não |
| política |Políticas que afetam o comportamento de runtime da atividade. Se não for especificado, são utilizadas políticas predefinidas. |Não |
| scheduler | A propriedade "scheduler" é usada para definir o agendamento desejado para a atividade. As suas subpropriedades são as mesmas que estão na [propriedade disponível num conjunto de dados.](data-factory-create-datasets.md#dataset-availability) |Não |

### <a name="policies"></a>Políticas
As políticas afetam o comportamento em tempo de execução de uma atividade, especificamente quando a fatia de uma mesa é processada. A tabela seguinte fornece os detalhes.

| Propriedade | Valores permitidos | Valor Predefinido | Descrição |
| --- | --- | --- | --- |
| concurrency |Número inteiro <br/><br/>Valor máximo: 10 |1 |Número de execuções simultâneas da atividade.<br/><br/>Determina o número de execuções paralelas de atividade que podem acontecer em diferentes fatias. Por exemplo, se uma atividade precisar passar por um grande conjunto de dados disponíveis, ter um maior valor de concordância acelera o processamento de dados. |
| executaçãoPriorityOrder |Mais recenteMente Primeiro<br/><br/>O Mais Antigo Primeiro |O Mais Antigo Primeiro |Determina a encomenda de fatias de dados que estão a ser processadas.<br/><br/>Por exemplo, se tiver duas fatias (uma acontecendo às 16h, e outra às 17h), e ambas estiverem pendentes de execução. Se definir a execuçãoPriorityOrder para newestFirst, a fatia às 17:00 é processada primeiro. Da mesma forma, se definir a execuçãoPriorityORder como OFIrst mais antigo, então a fatia às 16:00 é processada. |
| retry |Número inteiro<br/><br/>O valor máximo pode ser 10 |0 |O número de retrações antes do tratamento de dados para a fatia é marcado como Falha. A execução da atividade de uma fatia de dados é novamente julgada até à contagem de repetição especificada. A repetição é feita o mais rápido possível após o fracasso. |
| tempo limite |TimeSpan |00:00:00 |Tempo limite para a atividade. Exemplo: 00:10:00 (implica intervalo de tempo de 10 minutos)<br/><br/>Se um valor não for especificado ou for 0, o tempo limite é infinito.<br/><br/>Se o tempo de processamento de dados numa fatia exceder o valor de tempo limite, é cancelado e o sistema tenta voltar a tentar o processamento. O número de retrações depende da propriedade de retíria. Quando ocorre o tempo limite, o estado é definido para TimedOut. |
| atraso |TimeSpan |00:00:00 |Especifique o atraso antes do processamento de dados da fatia começar.<br/><br/>A execução da atividade para uma fatia de dados é iniciada após o Atraso ter passado o tempo de execução esperado.<br/><br/>Exemplo: 00:10:00 (implica atraso de 10 minutos) |
| LongRetry |Número inteiro<br/><br/>Valor máximo: 10 |1 |O número de tentativas de repetição de longas tentativas antes da execução da fatia é falhado.<br/><br/>as tentativas de longRetry são espaçadas por LongRetryInterval. Por isso, se precisar de especificar um tempo entre tentativas de re-tentativas, use longRetry. Se tanto a Retry como a LongRetry forem especificadas, cada tentativa de longa Volta inclui tentativas de Retry e o número máximo de tentativas é Retry * longRetry.<br/><br/>Por exemplo, se tivermos as seguintes definições na política de atividade:<br/>Redação: 3<br/>longRetry: 2<br/>LongRetryInterval: 01:00:00<br/><br/>Assuma que só há uma fatia para executar (o estado está à espera) e a execução da atividade falha sempre. Inicialmente, haveria 3 tentativas de execução consecutivas. Após cada tentativa, o estado da fatia seria Retry. Após as primeiras 3 tentativas, o estado da fatia seria LongRetry.<br/><br/>Após uma hora (isto é, o valor de LongRetryInteval), haveria outro conjunto de 3 tentativas de execução consecutivas. Depois disso, o estado da fatia seria falhado e não se tentariam mais recauchutados. No total, foram feitas 6 tentativas.<br/><br/>Se alguma execução for bem sucedida, o estado da fatia será pronto e não se tentam mais recauchutagens.<br/><br/>LongRetry pode ser usado em situações em que os dados dependentes chegam em tempos não determinísticos ou o ambiente geral é escamoso sob o qual o processamento de dados ocorre. Nesses casos, fazer retrógios um após o outro pode não ajudar e fazê-lo após um intervalo de tempo resulta na saída desejada.<br/><br/>Palavra de precaução: não desafie valores elevados para longRetry ou longRetryInterval. Tipicamente, valores mais elevados implicam outras questões sistémicas. |
| LongRetryInterval |TimeSpan |00:00:00 |O atraso entre longas tentativas de relemissão |

## <a name="sample-copy-pipeline"></a>Pipeline de cópia de exemplo
No pipeline de exemplo seguinte, existe uma atividade do tipo **Cópia** na secção **activities**. Nesta amostra, a atividade de [cópias](data-factory-data-movement-activities.md) copia dados de um armazenamento Azure Blob para Azure SQL Database.

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
* Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink. Na secção de atividades de [movimento de Dados,](#data-movement-activities) clique na loja de dados que pretende utilizar como fonte ou pia para saber mais sobre a movimentação de dados para/a partir dessa loja de dados.

Para obter uma passagem completa da criação deste pipeline, consulte [Tutorial: Copiar dados do Blob Storage para a SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

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
* A `defines` secção é utilizada para especificar as definições de tempo de execução que são passadas para o script da colmeia como valores de configuração da Colmeia (por `${hiveconf:inputtable}` exemplo, `${hiveconf:partitionedtable}` ).

A secção **typeProperties** é diferente para cada atividade de transformação. Para conhecer as propriedades tipo suportadas para uma atividade de transformação, clique na atividade de transformação na tabela [de atividades de transformação de Dados.](#data-transformation-activities)

Para uma caminhada completa da criação deste oleoduto, consulte [Tutorial: Construa o seu primeiro oleoduto para processar dados utilizando o cluster Hadoop.](data-factory-build-your-first-pipeline.md)

## <a name="multiple-activities-in-a-pipeline"></a>Múltiplas atividades num pipeline
Os dois pipelines de exemplo anteriores só contêm uma atividade. Pode ter mais de uma atividade num pipeline.

Se tiver múltiplas atividades num oleoduto e a produção de uma atividade não for uma entrada de outra atividade, as atividades podem ser executadas em paralelo se as fatias de dados de entrada para as atividades estiverem prontas.

Pode acorrentar duas atividades tendo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. A segunda atividade só executa quando a primeira termina com sucesso.

![Atividades de acorrentamento no mesmo oleoduto](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Nesta amostra, o gasoduto tem duas atividades: Atividade1 e Atividade2. A Activity1 toma o Dataset1 como uma entrada e produz um Dataset2 de saída. A Atividade toma o Dataset2 como uma entrada e produz um Dataset3 de saída. Uma vez que a produção da Atividade1 (Dataset2) é a entrada da Atividade2, a Atividade2 funciona apenas após a Conclusão da Atividade concluída com sucesso e produzir a fatia dataset2. Se a Atividade1 falhar por algum motivo e não produzir a fatia dataset2, a Atividade 2 não funciona para essa fatia (por exemplo: 9:00 às 10:00).

Também pode cadeiar atividades que estão em diferentes oleodutos.

![Atividades de acorrentamento em dois oleodutos](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Nesta amostra, o Pipeline1 tem apenas uma atividade que toma o Dataset1 como entrada e produz o Dataset2 como uma saída. O Pipeline2 também tem apenas uma atividade que toma o Dataset2 como uma entrada e o Dataset3 como uma saída.

Para mais informações, consulte [o agendamento e a execução.](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)
## <a name="create-and-monitor-pipelines"></a>Criar e monitorizar gasodutos
Pode criar oleodutos utilizando uma destas ferramentas ou SDKs.

- Assistente de Cópia
- Visual Studio
- Azure PowerShell
- Modelo Azure Resource Manager
- API REST
- API .NET

Consulte os seguintes tutoriais para obter instruções passo a passo para a criação de gasodutos utilizando uma destas ferramentas ou SDKs.

- [Criar um pipeline cum uma atividade de transformação de dados](data-factory-build-your-first-pipeline.md)
- [Construir um oleoduto com uma atividade de movimento de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Uma vez criado/implantado um gasoduto, pode gerir e monitorizar os seus oleodutos utilizando as lâminas do portal Azure ou monitore e gere a App. Consulte os seguintes tópicos para obter instruções passo a passo.

- [Monitorize e gere os gasodutos utilizando lâminas do portal Azure](data-factory-monitor-manage-pipelines.md).
- [Monitorize e gere os oleodutos utilizando a App Monitor e Manage](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Gasoduto único
Pode criar e agendar um gasoduto para funcionar periodicamente (por exemplo, de hora a hora ou diariamente) dentro dos tempos de início e de fim especificados na definição do gasoduto. Consulte as atividades de Agendamento para mais detalhes. Também pode criar um oleoduto que funciona apenas uma vez. Para tal, define a propriedade **pipelineMode** na definição do gasoduto para **uma única vez,** como mostrado na amostra JSON seguinte. O valor predefinido para esta propriedade está **agendado.**

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

* **Os** tempos **de início** e fim da tubagem não são especificados.
* **A disponibilidade** de conjuntos de dados de entrada e saída é especificada **(frequência** e **intervalo),** mesmo que a Data Factory não utilize os valores.
* A vista do diagrama não mostra gasodutos únicos. Este comportamento é propositado.
* Os gasodutos únicos não podem ser atualizados. Pode clonar um oleoduto único, renomeá-lo, atualizar propriedades e implantá-lo para criar outro.

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre conjuntos de dados, consulte Criar artigo [conjuntos de dados.](data-factory-create-datasets.md)
- Para obter mais informações sobre como os oleodutos são programados e executados, consulte [Agendamento e execução no artigo da Azure Data Factory.](data-factory-scheduling-and-execution.md)
