---
title: Criar/agendamento de Pipelines, encadear atividades no Data Factory | Documentos da Microsoft
description: Aprenda a criar um pipeline de dados no Azure Data Factory para mover e transformar dados. Crie um fluxo de trabalho orientados a dados para produzir pronto a utilizar as informações.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 13b137c7-1033-406f-aea7-b66f25b313c0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 65763eb57904210b202fb30b4394deea3a1f7baf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449568"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines e atividades no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-create-pipelines.md)
> * [Versão 2 (versão atual)](../concepts-pipelines-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [Pipelines no V2](../concepts-pipelines-activities.md).

Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e a utilizá-los para construir fluxos de dados completos e orientados por dados para os seus cenários de movimento de dados e processamento de dados.

> [!NOTE]
> Este artigo pressupõe que já leu [introdução ao Azure Data Factory](data-factory-introduction.md). Se não tiver práticos-práticos-experiência na criação de fábricas de dados, percorrer [tutorial de transformação de dados](data-factory-build-your-first-pipeline.md) e/ou [tutorial de movimento de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ajuda a compreender melhor este artigo.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. Os pipelines são agrupamentos lógicos de atividades que, em conjunto, realizam uma tarefa. As atividades num pipeline definem as ações a efetuar nos seus dados. Por exemplo, pode utilizar uma atividade de cópia para copiar dados de um SQL Server no local para um Armazenamento de Blobs do Azure. Em seguida, utilize uma atividade do Hive que execute um script do Hive num cluster do Azure HDInsight para processar/transformar os dados do armazenamento de blobs para produzir dados de saída. Por fim, utilize uma segunda atividade de cópia para copiar os dados de saída para um armazém do Azure SQL Data Warehouse sobre o qual são criadas soluções de relatórios de business intelligence (BI).

Uma atividade pode ter zero ou mais [conjuntos de dados](data-factory-create-datasets.md) de entrada e produzir um ou mais [conjuntos de dados](data-factory-create-datasets.md) de saída. O diagrama seguinte mostra a relação entre pipelines, atividades e conjuntos de dados no Data Factory:

![Relação entre o pipeline, a atividade e o conjunto de dados](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Um pipeline permite-lhe gerir atividades como um conjunto em vez de cada um deles individualmente. Por exemplo, pode implementar, agendar, suspender e retomar um pipeline, em vez de lidar com atividades no pipeline de forma independente.

O Data Factory suporta dois tipos de atividades -- atividades de movimento de dados e atividades de transformação de dados. Cada atividade pode ter zero ou mais entradas [conjuntos de dados](data-factory-create-datasets.md) e produzem um ou mais conjuntos de dados de saída.

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
Se precisar de mover o arquivo de dados num de dados que a atividade de cópia não suporte, ou transformar dados com a sua própria lógica, crie uma **atividade .NET personalizada**. Para obter detalhes sobre criar e utilizar uma atividade personalizada, veja [Use custom activities in an Azure Data Factory pipeline (Utilizar atividades personalizadas num pipeline do Azure Data Factory)](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Pipelines de agenda
Um pipeline está ativo apenas entre seus **começar** tempo e **final** tempo. Ele não é executado antes da hora de início ou após a hora de fim. Se o pipeline está em pausa, não são executada, independentemente de seu tempo de início e de fim. Para um execução do pipeline, ele deve não ser colocada em pausa. Ver [agendamento e execução](data-factory-scheduling-and-execution.md) para compreender como funciona o agendamento e execução no Azure Data Factory.

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
| nome |Nome do pipeline. Especifique um nome que represente a ação que o pipeline realiza. <br/><ul><li>Número máximo de carateres: 260</li><li>Tem de começar com um letra, um número ou um caráter de sublinhado (\_)</li><li>Seguintes carateres não são permitidos: ".", "+","?", "/", "<",">", "\*", "%", "&", ":","\\"</li></ul> |Sim |
| descrição | Especifique o texto que descreve para o que é utilizado o pipeline. |Sim |
| atividades | A secção **atividades** pode ter uma ou mais atividades definidas na mesma. Veja a secção seguinte para obter detalhes sobre o elemento JSON de atividades. | Sim |
| start | Data-hora de início para o pipeline. Tem de estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: `2016-10-14T16:32:41Z`. <br/><br/>É possível especificar uma hora local, por exemplo, um período de tempo EST. Eis um exemplo: `2016-02-27T06:00:00-05:00`", que é 6 AM estimativa<br/><br/>As propriedades de início e de fim especificam em conjunto o período ativo do pipeline. Apenas os setores de saída são produzidos neste período de Active Directory. |Não<br/><br/>Se especificar um valor para a propriedade final, tem de especificar o valor da propriedade de início.<br/><br/>As horas de início e de fim podem de estar vazias para criar um pipeline. Tem de especificar ambos os valores para definir um período de Active Directory para o execução do pipeline. Se não especificar horários de início e fim quando criar um pipeline, pode configurá-los usando o cmdlet Set-AzDataFactoryPipelineActivePeriod mais tarde. |
| end | Data / hora de fim do pipeline. Se for especificado tem de estar no formato ISO. Por exemplo: `2016-10-14T17:32:41Z` <br/><br/>É possível especificar uma hora local, por exemplo, um período de tempo EST. Eis um exemplo: `2016-02-27T06:00:00-05:00`, que é 6 AM estimativa<br/><br/>Para executar o pipeline de forma indefinida, especifique a 9999-09-09 como o valor para a propriedade final. <br/><br/> Um pipeline está ativo apenas entre a hora de início e hora de fim. Ele não é executado antes da hora de início ou após a hora de fim. Se o pipeline está em pausa, não são executada, independentemente de seu tempo de início e de fim. Para um execução do pipeline, ele deve não ser colocada em pausa. Ver [agendamento e execução](data-factory-scheduling-and-execution.md) para compreender como funciona o agendamento e execução no Azure Data Factory. |Não <br/><br/>Se especificar um valor para a propriedade de início, tem de especificar o valor da propriedade end.<br/><br/>Consulte as notas para o **iniciar** propriedade. |
| isPaused | Se definido como true, o pipeline não é executado. Ele está no Estado em pausa. Valor predefinido = false. Pode utilizar esta propriedade para ativar ou desativar um pipeline. |Não |
| pipelineMode | O método para agendar execuções do pipeline. Valores permitidos são: agendada (predefinição), onetime.<br/><br/>"Agendada" indica que o pipeline é executado num intervalo de tempo especificado, de acordo com o período ativo (hora de início e de fim). "Única" indica que o pipeline é executado apenas uma vez. Pipelines onetime, uma vez criadas não podem ser modificado/atualizado atualmente. Ver [Onetime pipeline](#onetime-pipeline) para obter detalhes sobre a definição de onetime. |Não |
| expirationTime | Duração de tempo após a criação para o qual o [pipeline única](#onetime-pipeline) é válido e deve permanecer aprovisionado. Se não tem qualquer ativo, falha, ou pendente é executado, o pipeline é automaticamente eliminado uma vez atinge o tempo de expiração. O valor predefinido: `"expirationTime": "3.00:00:00"`|Não |
| conjuntos de dados |Lista de conjuntos de dados para ser usado por atividades definidas no pipeline. Esta propriedade pode ser utilizada para definir conjuntos de dados que são específicas para este pipeline e não definido na fábrica de dados. Conjuntos de dados definidos dentro este pipeline só podem ser utilizados por este pipeline e não podem ser partilhados. Ver [conjuntos de dados de âmbito](data-factory-create-datasets.md#scoped-datasets) para obter detalhes. |Não |

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
| nome | Nome da atividade. Especifique um nome que represente a ação que a atividade realiza. <br/><ul><li>Número máximo de carateres: 260</li><li>Tem de começar com um letra, um número ou um caráter de sublinhado (\_)</li><li>Seguintes carateres não são permitidos: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Sim |
| descrição | Texto que descreve para o que é utilizada a atividade |Sim |
| tipo | Tipo de atividade. Consulte a [atividades de movimento de dados](#data-movement-activities) e [atividades de transformação de dados](#data-transformation-activities) secções para diferentes tipos de atividades. |Sim |
| entradas |Tabelas de entrada utilizadas pela atividade<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Sim |
| saídas |Tabelas de saída utilizadas pela atividade.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Sim |
| linkedServiceName |Nome do serviço ligado utilizado pela atividade. <br/><br/>Uma atividade pode exigir que especifique o serviço ligado que liga ao ambiente de computação necessário. |Sim para a atividade do HDInsight e Azure Machine Learning a atividade de classificação de Batch <br/><br/>Não para todas as outras. |
| typeProperties |Propriedades no **typeProperties** secção dependem do tipo da atividade. Para ver as propriedades do tipo de uma atividade, clique nas ligações para a atividade na secção anterior. | Não |
| política |Políticas que afetam o comportamento de runtime da atividade. Se não for especificado, são utilizadas políticas predefinidas. |Não |
| Scheduler | propriedade de "scheduler" é utilizada para definir um agendamento pretendida para a atividade. Seu subproperties são as mesmas que na [propriedade de disponibilidade num conjunto de dados](data-factory-create-datasets.md#dataset-availability). |Não |

### <a name="policies"></a>Políticas
As políticas afetam o comportamento de tempo de execução de uma atividade, especificamente quando o setor de uma tabela é processado. A tabela seguinte fornece os detalhes.

| Propriedade | Valores permitidos | Valor Predefinido | Descrição |
| --- | --- | --- | --- |
| simultaneidade |Número inteiro <br/><br/>Valor máximo: 10 |1 |Número de execuções simultâneas da atividade.<br/><br/>Determina o número de execuções de atividade parallel, que podem ser efetuadas em setores diferentes. Por exemplo, se precisar de uma atividade de passar por um grande conjunto de dados disponíveis, ter um valor maior da simultaneidade acelera o processamento de dados. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Determina a ordenação de setores de dados que estão a ser processados.<br/><br/>Por exemplo, se tiver 2 reparte (uma aconteça às 16:00 e outro às 17:00), e ambos são execução pendente. Se definir o executionPriorityOrder ser NewestFirst, o setor às 17:00 é processado pela primeira vez. Da mesma forma se definir o executionPriorityORder ser OldestFIrst, em seguida, o setor às 16:00 é processado. |
| retry |Número inteiro<br/><br/>Valor máximo pode ser 10 |0 |Número de tentativas antes do processamento de dados para o setor está marcado como falha. Execução da atividade de um setor de dados é repetida até o número especificado de tentativas. A nova tentativa é feita logo que possível após a falha. |
| tempo limite |TimeSpan |00:00:00 |Tempo limite para a atividade. Exemplo: 10: 00:00 (implica o tempo limite de 10 minutos)<br/><br/>Se um valor não for especificado ou for 0, o tempo limite é infinito.<br/><br/>Se o tempo de processamento de dados num setor excede o valor de tempo limite, ser cancelada e o sistema tenta repetir o processamento. O número de repetições depende da propriedade de repetição. Quando ocorre o tempo limite, o estado é definido como excedido. |
| atraso |TimeSpan |00:00:00 |Especifica o atraso antes de processamento de dados do setor a ser iniciada.<br/><br/>A execução da atividade de um setor de dados é iniciada depois do atraso é posterior à hora de execução esperada.<br/><br/>Exemplo: 10: 00:00 (implica o atraso de 10 minutos) |
| longRetry |Número inteiro<br/><br/>Valor máximo: 10 |1 |O número de tentativas de repetição longa antes da execução de setor falhou.<br/><br/>estão espaçadas a tentativas de longRetry, longRetryInterval. Por isso, se tiver de especificar uma hora entre tentativas de repetição, utilize o longRetry. Se forem especificados repetição e longRetry, cada tentativa de longRetry inclui tentativas de repetição e o número máximo de tentativas é tentar * longRetry.<br/><br/>Por exemplo, se tivermos as seguintes definições na política de atividade:<br/>Repetir: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Suponha que existe apenas um setor para executar (estado está a aguardar) e a execução da atividade falha sempre. Inicialmente, deveria haver 3 tentativas de execução consecutivos. Após cada tentativa, o estado do setor seria repetição. Depois de primeiros 3 tentativas ao longo, o estado do setor seria LongRetry.<br/><br/>Depois de uma hora (ou seja, o valor do longRetryInteval), deveria haver outro conjunto de 3 tentativas de execução consecutivos. Depois disso, seria possível executar o estado do setor e não existem mais tentativas devem ser tentadas. Assim geral 6 foram feitas tentativas.<br/><br/>Se qualquer execução for bem-sucedida, o estado do setor seria prontos e não existem mais tentativas são tentadas.<br/><br/>longRetry pode ser usado em situações em que a chegada de dados dependentes em momentos não determinística ou o ambiente geral é instável em que o processamento de dados ocorre. Nesses casos, as repetições não um após o outro podem ajudar ao fazê-lo e fazê-lo após um intervalo de tempo resulta na saída desejada.<br/><br/>Nota de advertência: não definir valores altos para longRetry ou longRetryInterval. Normalmente, os valores mais altos implicam outros problemas sistêmicos. |
| longRetryInterval |TimeSpan |00:00:00 |O atraso entre tentativas de repetição longa |

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
* Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink. Na [atividades de movimento de dados](#data-movement-activities) secção, clique o arquivo de dados que pretende utilizar como origem ou sink para saber mais sobre como mover dados de/para esse arquivo de dados.

Para obter instruções completas de criar este pipeline, veja [Tutorial: Copiar dados de armazenamento de BLOBs para base de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

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
* O `defines` seção é usada para especificar as definições de tempo de execução que são transmitidas ao script do hive como valores de configuração do Hive (por exemplo `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

A secção **typeProperties** é diferente para cada atividade de transformação. Para saber mais sobre as propriedades do tipo suportadas para uma atividade de transformação, clique na atividade de transformação no [atividades de transformação de dados](#data-transformation-activities) tabela.

Para obter instruções completas de criar este pipeline, veja [Tutorial: Crie seu primeiro pipeline para processar dados com o cluster do Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="multiple-activities-in-a-pipeline"></a>Múltiplas atividades num pipeline
Os dois pipelines de exemplo anteriores só contêm uma atividade. Pode ter mais de uma atividade num pipeline.

Se tiver múltiplas atividades num pipeline e a saída de uma atividade não é uma entrada de outra atividade, as atividades podem executar em paralelo, se os setores de dados de entrada para as atividades estão prontos.

Pode encadear duas atividades fazendo com que o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada de outra atividade. A segunda atividade é executado apenas quando primeiro for concluída com êxito.

![Encadeamento de atividades no mesmo pipeline](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Neste exemplo, o pipeline tem duas atividades: Activity1 e Activity2. O Activity1 usa Dataset1 como entrada e produz uma saída Dataset2. A atividade usa Dataset2 como entrada e produz uma saída Dataset3. Desde a saída de Activity1 (Dataset2) é a entrada de Activity2, as execuções de Activity2 apenas depois da atividade for concluída com êxito e produz o setor de Dataset2. Se o Activity1 falhar por algum motivo e não produz o setor de Dataset2, a atividade 2 não é executado para essas fatias (por exemplo: 9-me para o 10 AM).

Também pode encadear atividades que estão em diferentes pipelines.

![Encadeamento de atividades em dois pipelines](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Neste exemplo, Pipeline1 tem apenas uma atividade que assume Dataset1 como entrada e produz Dataset2 como saída. O Pipeline2 também tem apenas uma atividade que assume o Dataset2 como entrada e Dataset3 como saída.

Para obter mais informações, consulte [agendamento e execução](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).
## <a name="create-and-monitor-pipelines"></a>Criar e monitorizar pipelines
Pode criar pipelines ao utilizar uma destas ferramentas ou SDKs.

- Assistente para copiar.
- Portal do Azure
- Visual Studio
- Azure PowerShell
- Modelo Azure Resource Manager
- API REST
- API .NET

Veja os tutoriais seguintes para obter instruções passo a passo para criar pipelines ao utilizar uma destas ferramentas ou SDKs.

- [Criar um pipeline cum uma atividade de transformação de dados](data-factory-build-your-first-pipeline.md)
- [Criar um pipeline com uma atividade de movimento de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Depois de um pipeline é criado/implementado, pode gerir e monitorizar os seus pipelines com o painéis do portal do Azure ou o Monitor e a gerir a aplicação. Consulte os seguintes tópicos para obter instruções passo a passo.

- [Monitorizar e gerir pipelines com os painéis do portal do Azure](data-factory-monitor-manage-pipelines.md).
- [Monitorizar e gerir pipelines com a monitorizar e gerir aplicações](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Onetime pipeline
Pode criar e agendar o pipeline seja executado periodicamente (por exemplo: hora a hora ou diariamente) dentro as horas de início e de fim que especificar na definição do pipeline. Ver as atividades de agendamento para obter detalhes. Também pode criar um pipeline que é executado apenas uma vez. Para tal, defina o **pipelineMode** propriedade na definição de pipeline para **onetime** conforme mostrado no seguinte exemplo JSON. O valor predefinido para esta propriedade é **agendada**.

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

* **Inicie** e **end** vezes para o pipeline não forem especificadas.
* **Disponibilidade** de entrada e saída é especificado conjuntos de dados (**frequência** e **intervalo**), apesar do Data Factory não utiliza os valores.
* Vista de diagrama não mostra os pipelines de uso individual. Este comportamento é propositado.
* Não não possível atualizar os pipelines de uso individual. Pode clonar um único pipeline, mude o nome, atualizar as propriedades e implementá-la para criar outra.

## <a name="next-steps"></a>Passos Seguintes
- Para obter mais informações sobre conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md) artigo.
- Para obter mais informações sobre como os pipelines são agendados e executados, consulte [agendamento e execução no Azure Data Factory](data-factory-scheduling-and-execution.md) artigo.
