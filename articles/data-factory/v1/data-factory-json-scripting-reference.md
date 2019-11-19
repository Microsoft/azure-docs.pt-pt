---
title: Referência de script Azure Data Factory-JSON
description: Fornece esquemas JSON para entidades de Data Factory.
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
ms.openlocfilehash: b72be7026b0b8077cf5bf9f775d10fd03edd9118
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73815628"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Referência de script Azure Data Factory-JSON
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory.


Este artigo fornece esquemas JSON e exemplos para definir entidades de Azure Data Factory (pipeline, atividade, conjunto de serviços e serviço vinculado).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Pipeline
A estrutura de alto nível para uma definição de pipeline é a seguinte:

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

A tabela a seguir descreve as propriedades dentro da definição de JSON do pipeline:

| Propriedade | Descrição | Necessário
-------- | ----------- | --------
| nome | Nome do pipeline. Especifique um nome que represente a ação que a atividade ou o pipeline está configurado para fazer<br/><ul><li>Número máximo de carateres: 260</li><li>Deve começar com um número de letra ou um sublinhado (\_)</li><li>Os seguintes caracteres não são permitidos: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li></ul> |Sim |
| descrição |Texto que descreve a atividade ou o pipeline usado para | Não |
| atividades | Contém uma lista de atividades. | Sim |
| start |Data/hora de início do pipeline. Deve estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41. <br/><br/>É possível especificar uma hora local, por exemplo, uma hora ESTIMAda. Aqui está um exemplo: `2016-02-27T06:00:00**-05:00`, que é a 06 EST.<br/><br/>As propriedades Start e End juntas especificam o período ativo para o pipeline. As fatias de saída são produzidas somente com neste período ativo. |Não<br/><br/>Se você especificar um valor para a propriedade end, deverá especificar o valor da Propriedade Start.<br/><br/>As horas de início e de término podem estar vazias para criar um pipeline. Você deve especificar os dois valores para definir um período ativo para que o pipeline seja executado. Se você não especificar os horários de início e término ao criar um pipeline, poderá defini-los usando o cmdlet Set-AzDataFactoryPipelineActivePeriod mais tarde. |
| completo |Data/hora de término do pipeline. Se especificado deve estar no formato ISO. Por exemplo: 2014-10-14T17:32:41 <br/><br/>É possível especificar uma hora local, por exemplo, uma hora ESTIMAda. Aqui está um exemplo: `2016-02-27T06:00:00**-05:00`, que é a 06 EST.<br/><br/>Para executar o pipeline indefinidamente, especifique 9999-09-09 como o valor para a propriedade end. |Não <br/><br/>Se você especificar um valor para a propriedade Start, deverá especificar o valor da Propriedade End.<br/><br/>Consulte Observações para a propriedade **Iniciar** . |
| isPaused |Se definido como true, o pipeline não é executado. Valor padrão = false. Você pode usar essa propriedade para habilitar ou desabilitar. |Não |
| pipelineMode |O método para o agendamento de execuções para o pipeline. Os valores permitidos são: Scheduled (padrão), OneTime.<br/><br/>' Scheduled ' indica que o pipeline é executado em um intervalo de tempo especificado de acordo com seu período ativo (hora de início e término). ' OneTime ' indica que o pipeline é executado apenas uma vez. Pipelines de OneTime depois de criados não podem ser modificados/atualizados no momento. Consulte [pipeline de OneTime](data-factory-create-pipelines.md#onetime-pipeline) para obter detalhes sobre a configuração de OneTime. |Não |
| expirationTime |Duração do tempo após a criação para a qual o pipeline é válido e deve permanecer provisionado. Se não houver execuções ativas, com falha ou pendentes, o pipeline será excluído automaticamente assim que atingir o tempo de expiração. |Não |


## <a name="activity"></a>Atividade
A estrutura de alto nível para uma atividade dentro de uma definição de pipeline (elemento Activities) é a seguinte:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
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

A tabela a seguir descreve as propriedades na definição de JSON da atividade:

| Etiqueta | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da atividade. Especifique um nome que represente a ação que a atividade está configurada para fazer<br/><ul><li>Número máximo de carateres: 260</li><li>Deve começar com um número de letra ou um sublinhado (\_)</li><li>Os seguintes caracteres não são permitidos: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li></ul> |Sim |
| descrição |Texto que descreve para que a atividade é usada. |Não |
| tipo |Especifica o tipo da atividade. Consulte as seções [armazenamento de dados](#data-stores) e atividades de transformação de [dados](#data-transformation-activities) para diferentes tipos de atividades. |Sim |
| informações |Tabelas de entrada usadas pela atividade<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Não para atividades HDInsightStreaming e SqlServerStoredProcedure <br/> <br/> Sim para todos os outros |
| produz |Tabelas de saída usadas pela atividade.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Sim |
| linkedServiceName |Nome do serviço ligado utilizado pela atividade. <br/><br/>Uma atividade pode exigir que especifique o serviço ligado que liga ao ambiente de computação necessário. |Sim para atividades do HDInsight, atividades de Azure Machine Learning e atividade de procedimento armazenado. <br/><br/>Não para todas as outras. |
| typeProperties |As propriedades na seção typeproperties dependem do tipo da atividade. |Não |
| política |Políticas que afetam o comportamento de runtime da atividade. Se não for especificado, as políticas padrão serão usadas. |Não |
| Agendador |a propriedade "scheduler" é usada para definir o agendamento desejado para a atividade. Suas subpropriedades são as mesmas que as da [propriedade de disponibilidade em um conjunto de um](data-factory-create-datasets.md#dataset-availability). |Não |

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

### <a name="typeproperties-section"></a>seção typeproperties
A seção typeproperties é diferente para cada atividade. As atividades de transformação têm apenas as propriedades de tipo. Consulte a seção [atividades de transformação de dados](#data-transformation-activities) neste artigo para obter exemplos de JSON que definem atividades de transformação em um pipeline.

A **atividade de cópia** tem duas subseções na seção typeproperties: **origem** e **coletor**. Consulte a seção [armazenamentos de dados](#data-stores) neste artigo para obter exemplos de JSON que mostram como usar um armazenamento de dados como fonte e/ou coletor.

### <a name="sample-copy-pipeline"></a>Pipeline de cópia de exemplo
No pipeline de exemplo seguinte, existe uma atividade do tipo **Cópia** na secção **activities**. Neste exemplo, a [atividade de cópia](data-factory-data-movement-activities.md) copia dados de um armazenamento de BLOBs do Azure para um banco de dados SQL do Azure.

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
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Tenha em atenção os seguintes pontos:

* Na secção atividades, existe apenas uma atividade cujo **type** está definido como **Copy**.
* A entrada da atividade está definida como **InputDataset** e a saída como **OutputDataset**.
* Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink.

Consulte a seção [armazenamentos de dados](#data-stores) neste artigo para obter exemplos de JSON que mostram como usar um armazenamento de dados como fonte e/ou coletor.

Para obter uma explicação completa da criação desse pipeline, consulte [tutorial: copiar dados do armazenamento de BLOBs para o banco de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="sample-transformation-pipeline"></a>Pipeline de transformação de exemplos
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
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Tenha em atenção os seguintes pontos:

* Na secção “activities”, existe apenas uma atividade cujo **type** está definido como **HDInsightHive**.
* O ficheiro do Script de ramo de registo **partitionweblogs.hql** é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService, denominado **AzureStorageLinkedService**) e na pasta **script** no contentor **adfgetstarted**.
* A seção **define** é usada para especificar as configurações de tempo de execução que são passadas para o script do hive como valores de configuração do hive (por exemplo, `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Consulte a seção [atividades de transformação de dados](#data-transformation-activities) neste artigo para obter exemplos de JSON que definem atividades de transformação em um pipeline.

Para obter uma explicação completa da criação desse pipeline, consulte [tutorial: criar seu primeiro pipeline para processar dados usando o cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="linked-service"></a>Serviço ligado
A estrutura de alto nível para uma definição de serviço vinculada é a seguinte:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

A tabela a seguir descreve as propriedades na definição de JSON da atividade:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| nome | Nome do serviço vinculado. | Sim |
| Propriedades-tipo | Tipo do serviço vinculado. Por exemplo: armazenamento do Azure, banco de dados SQL do Azure. |
| typeProperties | A seção typeproperties tem elementos que são diferentes para cada armazenamento de dados ou ambiente de computação. Consulte a seção armazenamentos de dados para todos os serviços vinculados do repositório de dados e [ambientes de computação](#compute-environments) para todos os serviços vinculados de computação |

## <a name="dataset"></a>Conjunto de dados
Um conjunto de Azure Data Factory é definido da seguinte maneira:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

A tabela a seguir descreve as propriedades no JSON acima:

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| nome | Nome do conjunto de uma. Consulte [regras de nomenclatura de Azure data Factory](data-factory-naming-rules.md) para regras de nomenclatura. |Sim |ND |
| tipo | Tipo do conjunto de um. Especifique um dos tipos com suporte pelo Azure Data Factory (por exemplo: AzureBlob, AzureSqlTable). Consulte a seção [armazenamentos de dados](#data-stores) para todos os repositórios de dados e tipos de conjunto de dado com suporte pelo data Factory. |
| estruturá | Esquema do conjunto de um. Ele contém colunas, seus tipos, etc. | Não |ND |
| typeProperties | Propriedades correspondentes ao tipo selecionado. Consulte a seção [armazenamentos de dados](#data-stores) para obter os tipos com suporte e suas propriedades. |Sim |ND |
| externo | Sinalizador booliano para especificar se um conjunto de um DataSet é gerado explicitamente por um pipeline data factory ou não. |Não |false |
| availability | Define a janela de processamento ou o modelo de divisão para a produção do conjunto de os. Para obter detalhes sobre o modelo de divisão de conjunto de informações, consulte o artigo [agendamento e execução](data-factory-scheduling-and-execution.md) . |Sim |ND |
| política |Define os critérios ou a condição que as fatias de DataSet devem atender. <br/><br/>Para obter detalhes, consulte a seção política de conjunto de informações. |Não |ND |

Cada coluna na seção **estrutura** contém as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da coluna. |Sim |
| tipo |Tipo de dados da coluna.  |Não |
| UICulture |Cultura baseada em .NET a ser usada quando o tipo é especificado e é o tipo .NET `Datetime` ou `Datetimeoffset`. A predefinição é `en-us`. |Não |
| ao |Cadeia de caracteres de formato a ser usada quando o tipo é especificado e é do tipo .NET `Datetime` ou `Datetimeoffset`. |Não |

No exemplo a seguir, o conjunto de caracteres tem três colunas `slicetimestamp`, `projectname`e `pageviews` e são do tipo: cadeia de caracteres, Cadeia de caracteres e decimal, respectivamente.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

A tabela a seguir descreve as propriedades que podem ser usadas na seção **disponibilidade** :

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| frequência |Especifica a unidade de tempo para produção da fatia do conjunto de um.<br/><br/><b>Frequência com suporte</b>: minuto, hora, dia, semana, mês |Sim |ND |
| intervalo |Especifica um multiplicador para a frequência<br/><br/>"Intervalo de frequência x" determina a frequência com que a fatia é produzida.<br/><br/>Se você precisar que o conjunto de um seja dividido por hora, defina a <b>frequência</b> como <b>hora</b>e o <b>intervalo</b> como <b>1</b>.<br/><br/><b>Observação</b>: se você especificar a frequência como minuto, recomendamos que defina o intervalo como não menor que 15 |Sim |ND |
| estilo |Especifica se a fatia deve ser produzida no início/fim do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se Frequency for definido como Month e Style for definido como EndOfInterval, a fatia será produzida no último dia do mês. Se o estilo for definido como StartOfInterval, a fatia será produzida no primeiro dia do mês.<br/><br/>Se Frequency for definido como Day e Style for definido como EndOfInterval, a fatia será produzida na última hora do dia.<br/><br/>Se Frequency for definida como hour e Style for definido como EndOfInterval, a fatia será produzida no final da hora. Por exemplo, para uma fatia do período de 1 PM – 2 PM, a fatia é produzida às 2 PM. |Não |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo usada pelo Agendador para computar os limites de fatia do conjunto de cálculo. <br/><br/><b>Observação</b>: se o AnchorDateTime tiver partes de data mais granulares do que a frequência, as partes mais granulares serão ignoradas. <br/><br/>Por exemplo, se o <b>intervalo</b> for por <b>hora</b> (frequência: hora e intervalo: 1) e o <b>AnchorDateTime</b> contiver <b>minutos e segundos</b> , as partes de <b>minutos e segundos</b> do AnchorDateTime serão ignoradas. |Não |01/01/0001 |
| desvio |Período de tempo pelo qual o início e o término de todas as fatias do conjunto de todos são deslocados. <br/><br/><b>Observação</b>: se anchorDateTime e offset forem especificados, o resultado será o deslocamento combinado. |Não |ND |

A seguinte seção de disponibilidade especifica que o conjunto de dados de saída é produzido por hora (ou) o conjunto de informações de entrada está disponível por hora:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

A seção de **política** na definição do conjunto de conjuntos define os critérios ou a condição que as fatias do conjunto de os deve atender.

| Nome da Política | Descrição | Aplicado a | Necessário | Predefinição |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Valida que os dados em um **blob do Azure** atendem aos requisitos mínimos de tamanho (em megabytes). |Blob do Azure |Não |ND |
| minimumRows |Valida que os dados em um banco de dados **SQL do Azure** ou uma **tabela do Azure** contêm o número mínimo de linhas. |<ul><li>Base de Dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |ND |

**Exemplo:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

A menos que um conjunto de um DataSet esteja sendo produzido por Azure Data Factory, ele deve ser marcado como **externo**. Essa configuração geralmente se aplica às entradas da primeira atividade em um pipeline, a menos que a atividade ou o encadeamento de pipeline esteja sendo usado.

| Nome | Descrição | Necessário | Valor padrão |
| --- | --- | --- | --- |
| dataDelay |Tempo para atrasar a verificação na disponibilidade dos dados externos para a fatia determinada. Por exemplo, se os dados estiverem disponíveis por hora, a verificação para ver os dados externos está disponível e a fatia correspondente está pronta pode ser atrasada com o uso de datadelay.<br/><br/>Aplica-se somente ao momento atual.  Por exemplo, se for 1:00 PM no momento e esse valor for de 10 minutos, a validação começará às 1:10 PM.<br/><br/>Essa configuração não afeta fatias no passado (fatias com hora de término da fatia + data Delay < agora) são processadas sem nenhum atraso.<br/><br/>É necessário especificar uma hora maior que 23:59 horas usando o formato `day.hours:minutes:seconds`. Por exemplo, para especificar 24 horas, não use 24:00:00; em vez disso, use 1,00:00:00. Se você usar o 24:00:00, ele será tratado como 24 dias (24.00:00:00). Por 1 dia e 4 horas, especifique 1:04:00:00. |Não |0 |
| retryInterval |O tempo de espera entre uma falha e a próxima tentativa de repetição. Se uma tentativa falhar, a próxima tentativa será após retryInterval. <br/><br/>Se for 1:00 PM no momento, começaremos a primeira tentativa. Se a duração para concluir a primeira verificação de validação for de 1 minuto e a operação falhar, a próxima tentativa será de 1:00 + 1 min (duração) + 1 min (intervalo de repetição) = 1:02 PM. <br/><br/>Para fatias no passado, não há nenhum atraso. A repetição ocorre imediatamente. |Não |00:01:00 (1 minuto) |
| retryTimeout |O tempo limite para cada tentativa de repetição.<br/><br/>Se essa propriedade for definida como 10 minutos, a validação precisará ser concluída dentro de 10 minutos. Se demorar mais de 10 minutos para executar a validação, a nova tentativa atingirá o tempo limite.<br/><br/>Se todas as tentativas para a validação atingirem o tempo limite, a fatia será marcada como TimedOut. |Não |00:10:00 (10 minutos) |
| maximumRetry |Número de vezes para verificar a disponibilidade dos dados externos. O valor máximo permitido é 10. |Não |3 |


## <a name="data-stores"></a>ARMAZENAMENTOS DE DADOS
A seção [serviço vinculado](#linked-service) forneceu descrições para elementos JSON que são comuns a todos os tipos de serviços vinculados. Esta seção fornece detalhes sobre os elementos JSON que são específicos para cada armazenamento de dados.

A seção [DataSet](#dataset) forneceu descrições para elementos JSON que são comuns a todos os tipos de conjuntos de valores. Esta seção fornece detalhes sobre os elementos JSON que são específicos para cada armazenamento de dados.

A seção de [atividade](#activity) forneceu descrições para elementos JSON que são comuns a todos os tipos de atividades. Esta seção fornece detalhes sobre os elementos JSON que são específicos para cada armazenamento de dados quando ele é usado como origem/coletor em uma atividade de cópia.

Clique no link da loja em que você está interessado para ver os esquemas JSON para o serviço vinculado, o conjunto de chaves e a origem/coletor da atividade de cópia.

| Categoria | Arquivo de dados
|:--- |:--- |
| **Azure** |[Armazenamento de Blobs do Azure](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Base de Dados SQL do Azure](#azure-sql-database) |
| &nbsp; |[Azure SQL Data Warehouse](#azure-sql-data-warehouse) |
| &nbsp; |[Pesquisa Cognitiva do Azure](#azure-cognitive-search) |
| &nbsp; |[Armazenamento de tabelas do Azure](#azure-table-storage) |
| **Bases de dados** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **Ficheiro** |[Amazon S3](#amazon-s3) |
| &nbsp; |[Sistema de Ficheiros](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Outros** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |Tabela Web |

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

### <a name="linked-service"></a>Serviço ligado
Há dois tipos de serviços vinculados: serviço vinculado do armazenamento do Azure e serviço vinculado de SAS do armazenamento do Azure.

#### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
Para vincular sua conta de armazenamento do Azure a um data factory usando a **chave de conta**, crie um serviço vinculado do armazenamento do Azure. Para definir um serviço vinculado do armazenamento do Azure, defina o **tipo** do serviço vinculado para **AzureStorage**. Em seguida, você pode especificar as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| connectionString |Especifique as informações necessárias para se conectar ao armazenamento do Azure para a propriedade connectionString. |Sim |

##### <a name="example"></a>Exemplo

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Serviço vinculado de SAS do armazenamento do Azure
O serviço vinculado de SAS do armazenamento do Azure permite vincular uma conta de armazenamento do Azure a uma data factory do Azure usando uma SAS (assinatura de acesso compartilhado). Ele fornece o data factory com acesso restrito/com limite de tempo a todos/recursos específicos (blob/contêiner) no armazenamento. Para vincular sua conta de armazenamento do Azure a um data factory usando a assinatura de acesso compartilhado, crie um serviço vinculado de SAS do armazenamento do Azure. Para definir um serviço vinculado de SAS do armazenamento do Azure, defina o **tipo** do serviço vinculado para **AzureStorageSas**. Em seguida, você pode especificar as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| sasUri |Especifique o URI da assinatura de acesso compartilhado para os recursos de armazenamento do Azure, como BLOB, contêiner ou tabela. |Sim |

##### <a name="example"></a>Exemplo

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Para obter mais informações sobre esses serviços vinculados, consulte o artigo [conector do armazenamento de BLOBs do Azure](data-factory-azure-blob-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de uma série de BLOBs do Azure, defina o **tipo** do conjunto de um como **AzureBlob**. Em seguida, especifique as seguintes propriedades específicas do blob do Azure na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para o contêiner e a pasta no armazenamento de BLOBs. Exemplo: myblobcontainer\myblobfolder\ |Sim |
| fileName |Nome do blob. o nome do arquivo é opcional e diferencia maiúsculas de minúsculas.<br/><br/>Se você especificar um nome de arquivo, a atividade (incluindo a cópia) funcionará no blob específico.<br/><br/>Quando fileName não for especificado, Copy incluirá todos os BLOBs no folderPath para o conjunto de dados de entrada.<br/><br/>Quando fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado estaria no seguinte formato: `Data.<Guid>.txt` (por exemplo: Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt) |Não |
| partitionedBy |partitionedBy é uma propriedade opcional. Você pode usá-lo para especificar um folderPath e um nome de arquivo dinâmico para dados de série temporal. Por exemplo, folderPath pode ser parametrizado para cada hora dos dados. |Não |
| ao | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **Type** em Format para um desses valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e seções de [formato parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção formato nas definições do conjunto de dados de entrada e saída. |Não |
| çã | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **gzip**, **deflate**, **bzip2**e **ZipDeflate**. Os níveis com suporte são: **ideal** e **mais rápido**. Para obter mais informações, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


Para obter mais informações, consulte o artigo [conector de blob do Azure](data-factory-azure-blob-connector.md#dataset-properties) .

### <a name="blobsource-in-copy-activity"></a>Blobname na atividade de cópia
Se você estiver copiando dados de um armazenamento de BLOBs do Azure, defina o **tipo de origem** da atividade de cópia para **blobname**e especifique as propriedades a seguir na seção de **origem** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. |True (valor padrão), false |Não |

#### <a name="example-blobsource"></a>Exemplo: **blobname**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>BlobSink na atividade de cópia
Se você estiver copiando dados para um armazenamento de BLOBs do Azure, defina o **tipo de coletor** da atividade de cópia como **BlobSink**e especifique as propriedades a seguir na seção **Sink** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BLOB ou FileSystem. |<b>PreserveHierarchy</b>: preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy</b>: todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm o nome gerado automaticamente. <br/><br/><b>MergeFiles (padrão):</b> mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/blob for especificado, o nome do arquivo mesclado será o nome especificado; caso contrário, seria um nome de arquivo gerado automaticamente. |Não |

#### <a name="example-blobsink"></a>Exemplo: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [conector de blob do Azure](data-factory-azure-blob-connector.md#copy-activity-properties) .

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Serviço ligado
Para definir um Azure Data Lake Store serviço vinculado, defina o tipo do serviço vinculado para **AzureDataLakeStore**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **AzureDataLakeStore** | Sim |
| dataLakeStoreUri | Especifique as informações sobre a conta de Azure Data Lake Store. Ele está no seguinte formato: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| subscriptionId | ID de assinatura do Azure à qual Data Lake Store pertence. | Necessário para o coletor |
| resourceGroupName | Nome do grupo de recursos do Azure ao qual Data Lake Store pertence. | Necessário para o coletor |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim (para autenticação de entidade de serviço) |
| servicePrincipalKey | Especifique a chave do aplicativo. | Sim (para autenticação de entidade de serviço) |
| vários | Especifique as informações do locatário (nome de domínio ou ID do locatário) em que seu aplicativo reside. Você pode recuperá-lo passando o mouse sobre o canto superior direito da portal do Azure. | Sim (para autenticação de entidade de serviço) |
| nesse | Clique no botão **autorizar** no **Editor de data Factory** e insira sua credencial que atribui a URL de autorização gerada automaticamente a essa propriedade. | Sim (para autenticação de credenciais de usuário)|
| sessionId | ID da sessão OAuth da sessão de autorização do OAuth. Cada ID de sessão é exclusiva e só pode ser usada uma vez. Essa configuração é gerada automaticamente quando você usa o editor de Data Factory. | Sim (para autenticação de credenciais de usuário) |

#### <a name="example-using-service-principal-authentication"></a>Exemplo: usando a autenticação de entidade de serviço
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Exemplo: usando a autenticação de credenciais de usuário
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector Azure data Lake Store](data-factory-azure-datalake-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de Azure Data Lake Store, defina o **tipo** do conjunto de um como **AzureDataLakeStore**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| folderPath |Caminho para o contêiner e a pasta no repositório de Azure Data Lake. |Sim |
| fileName |Nome do arquivo no repositório de Azure Data Lake. o nome do arquivo é opcional e diferencia maiúsculas de minúsculas. <br/><br/>Se você especificar um nome de arquivo, a atividade (incluindo a cópia) funcionará no arquivo específico.<br/><br/>Quando fileName não for especificado, Copy inclui todos os arquivos em folderPath para o conjunto de dados de entrada.<br/><br/>Quando fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado estaria no seguinte formato: `Data.<Guid>.txt` (por exemplo: Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt) |Não |
| partitionedBy |partitionedBy é uma propriedade opcional. Você pode usá-lo para especificar um folderPath e um nome de arquivo dinâmico para dados de série temporal. Por exemplo, folderPath pode ser parametrizado para cada hora dos dados. |Não |
| ao | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **Type** em Format para um desses valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e seções de [formato parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção formato nas definições do conjunto de dados de entrada e saída. |Não |
| çã | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **gzip**, **deflate**, **bzip2**e **ZipDeflate**. Os níveis com suporte são: **ideal** e **mais rápido**. Para obter mais informações, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

#### <a name="example"></a>Exemplo
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector Azure data Lake Store](data-factory-azure-datalake-connector.md#dataset-properties) .

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Fonte de Azure Data Lake Store na atividade de cópia
Se você estiver copiando dados de um Azure Data Lake Store, defina o **tipo de origem** da atividade de cópia para **AzureDataLakeStoreSource**e especifique as propriedades a seguir na seção de **origem** :

**AzureDataLakeStoreSource** dá suporte às seguintes propriedades **typeproperties** da seção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. |True (valor padrão), false |Não |

#### <a name="example-azuredatalakestoresource"></a>Exemplo: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [conector Azure data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties) .

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Azure Data Lake Store coletor na atividade de cópia
Se você estiver copiando dados para um Azure Data Lake Store, defina o **tipo de coletor** da atividade de cópia como **AzureDataLakeStoreSink**e especifique as propriedades a seguir na seção **Sink** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyBehavior |Especifica o comportamento de cópia. |<b>PreserveHierarchy</b>: preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy</b>: todos os arquivos da pasta de origem são criados no primeiro nível da pasta de destino. Os arquivos de destino são criados com o nome gerado automaticamente.<br/><br/><b>MergeFiles</b>: mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/blob for especificado, o nome do arquivo mesclado será o nome especificado; caso contrário, seria um nome de arquivo gerado automaticamente. |Não |

#### <a name="example-azuredatalakestoresink"></a>Exemplo: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [conector Azure data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties) .

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Serviço ligado
Para definir um Azure Cosmos DB serviço vinculado, defina o **tipo** do serviço vinculado para o **DocumentDb**e especifique as propriedades a seguir na seção **typeproperties** :

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| connectionString |Especifique as informações necessárias para se conectar ao banco de dados Azure Cosmos DB. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
Para obter mais informações, consulte o artigo [conector Azure Cosmos DB](data-factory-azure-documentdb-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de Azure Cosmos DB, defina o **tipo** do conjunto de um como **DocumentDbCollection**e especifique as propriedades a seguir na seção **typeproperties** :

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| collectionName |Nome da coleção de Azure Cosmos DB. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
Para obter mais informações, consulte o artigo [conector Azure Cosmos DB](data-factory-azure-documentdb-connector.md#dataset-properties) .

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Azure Cosmos DB a origem da coleção na atividade de cópia
Se você estiver copiando dados de um Azure Cosmos DB, defina o **tipo de origem** da atividade de cópia para **DocumentDbCollectionSource**e especifique as propriedades a seguir na seção de **origem** :


| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| consulta |Especifique a consulta para ler os dados. |Cadeia de caracteres de consulta com suporte pelo Azure Cosmos DB. <br/><br/>Exemplo: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificado, a instrução SQL executada: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Caractere especial para indicar que o documento está aninhado |Qualquer caractere. <br/><br/>Azure Cosmos DB é um repositório NoSQL para documentos JSON, em que estruturas aninhadas são permitidas. Azure Data Factory permite que o usuário denotasse hierarquia via nestingSeparator, que é "." nos exemplos acima. Com o separador, a atividade de cópia gerará o objeto "nome" com três elementos filhos primeiro, meio e último, de acordo com "nome. primeiro", "nome. meio" e "nome. último" na definição da tabela. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Azure Cosmos DB coletor de coleta na atividade de cópia
Se você estiver copiando dados para Azure Cosmos DB, defina o **tipo de coletor** da atividade de cópia como **DocumentDbCollectionSink**e especifique as propriedades a seguir na seção **Sink** :

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| nestingSeparator |Um caractere especial no nome da coluna de origem para indicar que o documento aninhado é necessário. <br/><br/>Por exemplo acima: `Name.First` na tabela de saída produz a seguinte estrutura JSON no documento Cosmos DB:<br/><br/>"Name": {<br/>    "Primeiro": "João"<br/>}, |Caráter utilizado para separar níveis de aninhamento.<br/><br/>O valor padrão é `.` (ponto). |Caráter utilizado para separar níveis de aninhamento. <br/><br/>O valor padrão é `.` (ponto). |
| WriteBatchSize |Número de solicitações paralelas para Azure Cosmos DB serviço para criar documentos.<br/><br/>Você pode ajustar o desempenho ao copiar dados de/para Azure Cosmos DB usando essa propriedade. Você pode esperar um melhor desempenho ao aumentar o writeBatchSize porque mais solicitações paralelas para Azure Cosmos DB são enviadas. No entanto, você precisará evitar a limitação que pode gerar a mensagem de erro: "a taxa de solicitação é grande".<br/><br/>A limitação é decidida por vários fatores, incluindo o tamanho dos documentos, o número de termos em documentos, a política de indexação da coleção de destino, etc. Para operações de cópia, você pode usar uma coleção melhor (por exemplo, S3) para ter a maior taxa de transferência disponível (2.500 unidades de solicitação/segundo). |Número inteiro |Não (padrão: 5) |
| writeBatchTimeout |Tempo de espera para a operação ser concluída antes de atingir o tempo limite. |período<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [conector Azure Cosmos DB](data-factory-azure-documentdb-connector.md#copy-activity-properties) .

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do banco de dados SQL do Azure, defina o **tipo** do serviço vinculado para **AzureSqlDatabase**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| connectionString |Especifique as informações necessárias para se conectar à instância do banco de dados SQL do Azure para a propriedade connectionString. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector do SQL do Azure](data-factory-azure-sql-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Azure SQL Database, defina o **Type** do conjunto como **AzureSqlTable**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou exibição na instância do banco de dados SQL do Azure à qual o serviço vinculado se refere. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Para obter mais informações, consulte o artigo [conector do SQL do Azure](data-factory-azure-sql-connector.md#dataset-properties) .

### <a name="sql-source-in-copy-activity"></a>Origem do SQL na atividade de cópia
Se você estiver copiando dados de um banco de dado SQL do Azure, defina o **tipo de origem** da atividade de cópia para **sqlsource**e especifique as propriedades a seguir na seção **Source** :


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Os nomes e maiúsculas e minúsculas dos parâmetros devem corresponder aos nomes e maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Para obter mais informações, consulte o artigo [conector do SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties) .

### <a name="sql-sink-in-copy-activity"></a>Coletor SQL na atividade de cópia
Se você estiver copiando os dados para o Azure SQL Database, defina o **tipo de coletor** da atividade de cópia para **sqlsink**e especifique as propriedades a seguir na seção **Sink** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo de espera para que a operação de inserção em lote seja concluída antes de atingir o tempo limite. |período<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |
| WriteBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanupScript |Especifique uma consulta para a atividade de cópia a ser executada, de modo que os dados de uma fatia específica sejam limpos. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique um nome de coluna para a atividade de cópia preencher com o identificador de fatia gerado automaticamente, que é usado para limpar os dados de uma fatia específica quando executado novamente. |Nome da coluna de uma coluna com tipo de dados binary (32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que upserts (atualiza/insere) dados na tabela de destino. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Os nomes e maiúsculas e minúsculas dos parâmetros devem corresponder aos nomes e maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |
| sqlWriterTableType |Especifique um nome de tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia torna os dados sendo movidos disponíveis em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados que estão sendo copiados com os dados existentes. |Um nome de tipo de tabela. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [conector do SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties) .

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do Azure SQL Data Warehouse, defina o **tipo** do serviço vinculado para **AzureSqlDW**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| connectionString |Especifique as informações necessárias para se conectar à instância de SQL Data Warehouse do Azure para a propriedade connectionString. |Sim |



#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector de SQL data warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de SQL Data Warehouse do Azure, defina o **tipo** do conjunto de um como **AzureSqlDWTable**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou exibição no banco de dados SQL Data Warehouse do Azure ao qual o serviço vinculado se refere. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector de SQL data warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) .

### <a name="sql-dw-source-in-copy-activity"></a>Origem do SQL DW na atividade de cópia
Se você estiver copiando dados do Azure SQL Data Warehouse, defina o **tipo de origem** da atividade de cópia para **SqlDWSource**e especifique as propriedades a seguir na seção de **origem** :


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Os nomes e maiúsculas e minúsculas dos parâmetros devem corresponder aos nomes e maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [conector de SQL data warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) .

### <a name="sql-dw-sink-in-copy-activity"></a>Coletor de DW do SQL na atividade de cópia
Se você estiver copiando dados para o Azure SQL Data Warehouse, defina o **tipo de coletor** da atividade de cópia como **SqlDWSink**e especifique as propriedades a seguir na seção **Sink** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Especifique uma consulta para a atividade de cópia a ser executada, de modo que os dados de uma fatia específica sejam limpos. |Uma instrução de consulta. |Não |
| allowPolyBase |Indica se o polybase (quando aplicável) deve ser usado em vez do mecanismo de BULKINSERT. <br/><br/> **Usar o polybase é a maneira recomendada para carregar dados em SQL Data Warehouse.** |Verdadeiro <br/>False (padrão) |Não |
| polyBaseSettings |Um grupo de propriedades que pode ser especificado quando a propriedade **allowPolybase** é definida como **true**. |&nbsp; |Não |
| rejeitarvalue |Especifica o número ou o percentual de linhas que podem ser rejeitadas antes da falha da consulta. <br/><br/>Saiba mais sobre as opções de rejeição do polybase na seção **argumentos** do tópico [criar tabela externa (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) . |0 (padrão), 1, 2,... |Não |
| rejectType |Especifica se a opção rejeiçãovalue é especificada como um valor literal ou uma porcentagem. |Valor (padrão), percentual |Não |
| rejectSampleValue |Determina o número de linhas a serem recuperadas antes que o polybase recalcule a porcentagem de linhas rejeitadas. |1, 2,... |Sim, se **rejeitátype** for **percentual** |
| useTypeDefault |Especifica como tratar valores ausentes em arquivos de texto delimitados quando o polybase recupera dados do arquivo de texto.<br/><br/>Saiba mais sobre essa propriedade na seção argumentos em [criar formato de arquivo externo (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |True, false (padrão) |Não |
| WriteBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize |Inteiro (número de linhas) |Não (padrão: 10000) |
| writeBatchTimeout |Tempo de espera para que a operação de inserção em lote seja concluída antes de atingir o tempo limite. |período<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [conector de SQL data warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) .

## <a name="azure-cognitive-search"></a>Pesquisa Cognitiva do Azure

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do Azure Pesquisa Cognitiva, defina o **tipo** do serviço vinculado para **AzureSearch**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| url | URL para o serviço de pesquisa. | Sim |
| key | Chave de administração para o serviço de pesquisa. | Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector de pesquisa cognitiva do Azure](data-factory-azure-search-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de Pesquisa Cognitiva do Azure, defina o **tipo** do conjunto de um como **AzureSearchIndex**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| tipo | A propriedade Type deve ser definida como **AzureSearchIndex**.| Sim |
| indexName | Nome do índice de pesquisa. Data Factory não cria o índice. O índice deve existir no Pesquisa Cognitiva do Azure. | Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector de pesquisa cognitiva do Azure](data-factory-azure-search-connector.md#dataset-properties) .

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Coletor de índice de Pesquisa Cognitiva do Azure na atividade de cópia
Se você estiver copiando dados para um índice de pesquisa, defina o **tipo de coletor** da atividade de cópia como **AzureSearchIndexSink**e especifique as propriedades a seguir na seção **Sink** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| writeBehavior | Especifica se é para mesclar ou substituir quando um documento já existe no índice. | Mesclar (padrão)<br/>Carregar| Não |
| WriteBatchSize | Carrega dados no índice de pesquisa quando o tamanho do buffer atinge writeBatchSize. | 1 a 1.000. O valor padrão é 1000. | Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [conector de pesquisa cognitiva do Azure](data-factory-azure-search-connector.md#copy-activity-properties) .

## <a name="azure-table-storage"></a>Table Storage do Azure

### <a name="linked-service"></a>Serviço ligado
Há dois tipos de serviços vinculados: serviço vinculado do armazenamento do Azure e serviço vinculado de SAS do armazenamento do Azure.

#### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
Para vincular sua conta de armazenamento do Azure a um data factory usando a **chave de conta**, crie um serviço vinculado do armazenamento do Azure. Para definir um serviço vinculado do armazenamento do Azure, defina o **tipo** do serviço vinculado para **AzureStorage**. Em seguida, você pode especificar as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade Type deve ser definida como: **AzureStorage** |Sim |
| connectionString |Especifique as informações necessárias para se conectar ao armazenamento do Azure para a propriedade connectionString. |Sim |

**Exemplo:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Serviço vinculado de SAS do armazenamento do Azure
O serviço vinculado de SAS do armazenamento do Azure permite vincular uma conta de armazenamento do Azure a uma data factory do Azure usando uma SAS (assinatura de acesso compartilhado). Ele fornece o data factory com acesso restrito/com limite de tempo a todos/recursos específicos (blob/contêiner) no armazenamento. Para vincular sua conta de armazenamento do Azure a um data factory usando a assinatura de acesso compartilhado, crie um serviço vinculado de SAS do armazenamento do Azure. Para definir um serviço vinculado de SAS do armazenamento do Azure, defina o **tipo** do serviço vinculado para **AzureStorageSas**. Em seguida, você pode especificar as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade Type deve ser definida como: **AzureStorageSas** |Sim |
| sasUri |Especifique o URI da assinatura de acesso compartilhado para os recursos de armazenamento do Azure, como BLOB, contêiner ou tabela. |Sim |

**Exemplo:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Para obter mais informações sobre esses serviços vinculados, consulte o artigo [conector de armazenamento de tabela do Azure](data-factory-azure-table-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de uma tabela do Azure, defina o **tipo** do conjunto de um como **azuretable**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância do banco de dados de tabela do Azure à qual o serviço vinculado se refere. |Sim. Quando um TableName é especificado sem um azureTableSourceQuery, todos os registros da tabela são copiados para o destino. Se um azureTableSourceQuery também for especificado, os registros da tabela que satisfazem a consulta serão copiados para o destino. |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações sobre esses serviços vinculados, consulte o artigo [conector de armazenamento de tabela do Azure](data-factory-azure-table-connector.md#dataset-properties) .

### <a name="azure-table-source-in-copy-activity"></a>Origem da tabela do Azure na atividade de cópia
Se você estiver copiando dados do armazenamento de tabelas do Azure, defina o **tipo de origem** da atividade de cópia para **AzureTableSource**e especifique as propriedades a seguir na seção de **origem** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableSourceQuery |Use a consulta personalizada para ler os dados. |Cadeia de consulta de tabela do Azure. Consulte os exemplos na próxima seção. |Não. Quando um TableName é especificado sem um azureTableSourceQuery, todos os registros da tabela são copiados para o destino. Se um azureTableSourceQuery também for especificado, os registros da tabela que satisfazem a consulta serão copiados para o destino. |
| azureTableSourceIgnoreTableNotFound |Indica se a assimilação da exceção da tabela não existe. |TRUE<br/>FOR |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações sobre esses serviços vinculados, consulte o artigo [conector de armazenamento de tabela do Azure](data-factory-azure-table-connector.md#copy-activity-properties) .

### <a name="azure-table-sink-in-copy-activity"></a>Coletor de tabela do Azure na atividade de cópia
Se você estiver copiando dados para o armazenamento de tabelas do Azure, defina o **tipo de coletor** da atividade de cópia como **AzureTableSink**e especifique as propriedades a seguir na seção **Sink** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Valor de chave de partição padrão que pode ser usado pelo coletor. |Um valor de cadeia de caracteres. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são usados como chaves de partição. Se não for especificado, AzureTableDefaultPartitionKeyValue será usado como a chave de partição. |Um nome de coluna. |Não |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores de coluna são usados como chave de linha. Se não for especificado, use um GUID para cada linha. |Um nome de coluna. |Não |
| azureTableInsertType |O modo para inserir dados na tabela do Azure.<br/><br/>Essa propriedade controla se as linhas existentes na tabela de saída com as chaves de partição e de linha correspondentes têm seus valores substituídos ou mesclados. <br/><br/>Para saber mais sobre como essas configurações (Mesclar e substituir) funcionam, confira os tópicos [Inserir ou mesclar entidade](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [Inserir ou substituir entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx) . <br/><br> Essa configuração se aplica ao nível de linha, não ao nível de tabela e nenhuma das opções exclui as linhas na tabela de saída que não existem na entrada. |Mesclar (padrão)<br/>Substitua |Não |
| WriteBatchSize |Insere dados na tabela do Azure quando writeBatchSize ou writeBatchTimeout é atingido. |Inteiro (número de linhas) |Não (padrão: 10000) |
| writeBatchTimeout |Insere dados na tabela do Azure quando writeBatchSize ou writeBatchTimeout é atingido |período<br/><br/>Exemplo: "00:20:00" (20 minutos) |Não (padrão para o valor de tempo limite padrão do cliente de armazenamento 90 s) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Para obter mais informações sobre esses serviços vinculados, consulte o artigo [conector de armazenamento de tabela do Azure](data-factory-azure-table-connector.md#copy-activity-properties) .

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do Amazon redshift, defina o **tipo** do serviço vinculado para **AmazonRedshift**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Endereço IP ou nome do host do servidor Amazon redshift. |Sim |
| porta |O número da porta TCP que o servidor Amazon redshift usa para escutar conexões de cliente. |Não, valor padrão: 5439 |
| base de dados |Nome do banco de dados do Amazon redshift. |Sim |
| o nome de utilizador |Nome do usuário que tem acesso ao banco de dados. |Sim |
| palavra-passe |Senha da conta de usuário. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

Para obter mais informações, consulte o artigo conector do Amazon redshift.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de redshift do Amazon, defina o **tipo** do conjunto de um como **RelationalTable**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela no banco de dados do Amazon redshift ao qual o serviço vinculado se refere. |Não (se a **consulta** de **RelationalSource** for especificada) |


#### <a name="example"></a>Exemplo

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Para obter mais informações, consulte o artigo conector do Amazon redshift.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se você estiver copiando dados do Amazon redshift, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique as propriedades a seguir na seção de **origem** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **TableName** de **DataSet** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Para obter mais informações, consulte o artigo conector do Amazon redshift.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do IBM DB2, defina o **tipo** do serviço vinculado para **OnPremisesDB2**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor DB2. |Sim |
| base de dados |Nome do banco de dados DB2. |Sim |
| schema |Nome do esquema no banco de dados. O nome do esquema diferencia maiúsculas de minúsculas. |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados DB2. Os valores possíveis são: Anonymous, Basic e Windows. |Sim |
| o nome de utilizador |Especifique o nome de usuário se você estiver usando a autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a senha para a conta de usuário especificada para o nome do usuário. |Não |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao banco de dados DB2 local. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Para obter mais informações, consulte o artigo conector IBM DB2.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de uma série DB2, defina o **Type** do conjunto de um como **RelationalTable**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância do banco de dados DB2 à qual o serviço vinculado se refere. O TableName diferencia maiúsculas de minúsculas. |Não (se a **consulta** de **RelationalSource** for especificada)

#### <a name="example"></a>Exemplo
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo conector IBM DB2.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se você estiver copiando dados do IBM DB2, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique as propriedades a seguir na seção de **origem** :


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""`. |Não (se **TableName** de **DataSet** for especificado) |

#### <a name="example"></a>Exemplo
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Para obter mais informações, consulte o artigo conector IBM DB2.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do MySQL, defina o **tipo** do serviço vinculado para **OnPremisesMySql**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor MySQL. |Sim |
| base de dados |Nome do banco de dados MySQL. |Sim |
| schema |Nome do esquema no banco de dados. |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados MySQL. Os valores possíveis são: `Basic`. |Sim |
| Usu |Especifique o nome de usuário para se conectar ao banco de dados MySQL. |Sim |
| palavra-passe |Especifique a senha da conta de usuário que você especificou. |Sim |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao banco de dados MySQL local. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector do MySQL](data-factory-onprem-mysql-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de um banco de um MySQL, defina o **tipo** do conjunto de um como **RelationalTable**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância do banco de dados MySQL à qual o serviço vinculado se refere. |Não (se a **consulta** de **RelationalSource** for especificada) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Para obter mais informações, consulte o artigo [conector do MySQL](data-factory-onprem-mysql-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se você estiver copiando dados de um banco de dado MySQL, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique as propriedades a seguir na seção **Source** :


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **TableName** de **DataSet** for especificado) |


#### <a name="example"></a>Exemplo
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [conector do MySQL](data-factory-onprem-mysql-connector.md#copy-activity-properties) .

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do Oracle, defina o **tipo** do serviço vinculado para **OnPremisesOracle**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Driver | Especifique qual driver usar para copiar dados de/para Oracle Database. Os valores permitidos são **Microsoft** ou **odp** (padrão). Consulte a seção versão e instalação com suporte em detalhes do driver. | Não |
| connectionString | Especifique as informações necessárias para se conectar à instância de Oracle Database para a propriedade connectionString. | Sim |
| gatewayName | Nome do gateway usado para se conectar ao servidor Oracle local |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector Oracle](data-factory-onprem-oracle-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de um Oracle, defina o **tipo** do conjunto de um como **oracletable**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela no Oracle Database ao qual o serviço vinculado se refere. |Não (se **oracleReaderQuery** de **oracleprovider** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Para obter mais informações, consulte o artigo [conector Oracle](data-factory-onprem-oracle-connector.md#dataset-properties) .

### <a name="oracle-source-in-copy-activity"></a>Origem do Oracle na atividade de cópia
Se você estiver copiando dados de um Oracle Database, defina o **tipo de origem** da atividade de cópia para **Oracle**, e especifique as propriedades a seguir na seção de **origem** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| oracleReaderQuery |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable` <br/><br/>Se não for especificado, a instrução SQL executada: `select * from MyTable` |Não (se **TableName** de **DataSet** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [conector Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) .

### <a name="oracle-sink-in-copy-activity"></a>Coletor Oracle na atividade de cópia
Se você estiver copiando dados para o Oracle Database, defina o **tipo de coletor** da atividade de cópia para **OracleSink**e especifique as propriedades a seguir na seção **Sink** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo de espera para que a operação de inserção em lote seja concluída antes de atingir o tempo limite. |período<br/><br/> Exemplo: 00:30:00 (30 minutos). |Não |
| WriteBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 100) |
| sqlWriterCleanupScript |Especifique uma consulta para a atividade de cópia a ser executada, de modo que os dados de uma fatia específica sejam limpos. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a atividade de cópia a ser preenchida com o identificador de fatia gerado automaticamente, que é usado para limpar os dados de uma fatia específica quando executado novamente. |Nome da coluna de uma coluna com tipo de dados binary (32). |Não |

#### <a name="example"></a>Exemplo
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Para obter mais informações, consulte o artigo [conector Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) .

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do PostgreSQL, defina o **tipo** do serviço vinculado para **OnPremisesPostgreSql**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor PostgreSQL. |Sim |
| base de dados |Nome do banco de dados PostgreSQL. |Sim |
| schema |Nome do esquema no banco de dados. O nome do esquema diferencia maiúsculas de minúsculas. |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados PostgreSQL. Os valores possíveis são: Anonymous, Basic e Windows. |Sim |
| o nome de utilizador |Especifique o nome de usuário se você estiver usando a autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a senha para a conta de usuário especificada para o nome do usuário. |Não |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao banco de dados PostgreSQL local. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Para obter mais informações, consulte o artigo [conector do PostgreSQL](data-factory-onprem-postgresql-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de um banco de um PostgreSQL, defina o **tipo** do conjunto de um como **RelationalTable**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância do banco de dados PostgreSQL à qual o serviço vinculado se refere. O TableName diferencia maiúsculas de minúsculas. |Não (se a **consulta** de **RelationalSource** for especificada) |

#### <a name="example"></a>Exemplo
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Para obter mais informações, consulte o artigo [conector do PostgreSQL](data-factory-onprem-postgresql-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se você estiver copiando dados de um banco de dado PostgreSQL, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique as propriedades a seguir na seção de **origem** :


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: "consulta": "selecionar * de \"MySchema\".\"MyTable\"". |Não (se **TableName** de **DataSet** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [conector do PostgreSQL](data-factory-onprem-postgresql-connector.md#copy-activity-properties) .

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do SAP Business Warehouse (BW), defina o **tipo** do serviço vinculado para **SapBw**e especifique as propriedades a seguir na seção **typeproperties** :

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
servidor | Nome do servidor no qual reside a instância de SAP BW. | string | Sim
Número | Número de sistema do sistema de SAP BW. | Número decimal de dois dígitos representado como uma cadeia de caracteres. | Sim
clientId | ID do cliente do cliente no sistema SAP W. | Número decimal de três dígitos representado como uma cadeia de caracteres. | Sim
o nome de utilizador | Nome do usuário que tem acesso ao servidor SAP | string | Sim
palavra-passe | A palavra-passe do utilizador. | string | Sim
gatewayName | Nome do gateway que o serviço de Data Factory deve usar para se conectar à instância de SAP BW local. | string | Sim
encryptedCredential | A cadeia de caracteres de credencial criptografada. | string | Não

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector do SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de SAP BW, defina o **tipo** do conjunto de um como **RelationalTable**. Não há propriedades específicas do tipo com suporte para o conjunto de SAP BW do tipo **RelationalTable**.

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Para obter mais informações, consulte o artigo [conector do SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se você estiver copiando dados do SAP Business Warehouse, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique as propriedades a seguir na seção de **origem** :


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta | Especifica a consulta MDX para ler dados da instância de SAP BW. | Consulta MDX. | Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [conector do SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) .

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Serviço ligado
Para definir um SAP HANA serviço vinculado, defina o **tipo** do serviço vinculado para **SapHana**e especifique as propriedades a seguir na seção **typeproperties** :

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
servidor | Nome do servidor no qual reside a instância de SAP HANA. Se o servidor estiver usando uma porta personalizada, especifique `server:port`. | string | Sim
authenticationType | Tipo de autenticação. | Strings. "Básico" ou "Windows" | Sim
o nome de utilizador | Nome do usuário que tem acesso ao servidor SAP | string | Sim
palavra-passe | A palavra-passe do utilizador. | string | Sim
gatewayName | Nome do gateway que o serviço de Data Factory deve usar para se conectar à instância de SAP HANA local. | string | Sim
encryptedCredential | A cadeia de caracteres de credencial criptografada. | string | Não

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
Para obter mais informações, consulte o artigo [conector SAP Hana](data-factory-sap-hana-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de SAP HANA, defina o **tipo** do conjunto de um como **RelationalTable**. Não há propriedades específicas do tipo com suporte para o conjunto de SAP HANA do tipo **RelationalTable**.

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Para obter mais informações, consulte o artigo [conector SAP Hana](data-factory-sap-hana-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se você estiver copiando dados de um armazenamento de dados SAP HANA, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique as propriedades a seguir na seção de **origem** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta | Especifica a consulta SQL para ler dados da instância de SAP HANA. | Consulta SQL. | Sim |


#### <a name="example"></a>Exemplo


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [conector SAP Hana](data-factory-sap-hana-connector.md#copy-activity-properties) .


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Serviço ligado
Você cria um serviço vinculado do tipo **OnPremisesSqlServer** para vincular um banco de dados SQL Server local a um data Factory. A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do SQL Server local.

A tabela a seguir fornece a descrição para elementos JSON específicos para SQL Server serviço vinculado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **OnPremisesSqlServer**. |Sim |
| connectionString |Especifique as informações de connectionString necessárias para se conectar ao banco de dados local SQL Server usando a autenticação SQL ou a autenticação do Windows. |Sim |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao banco de dados SQL Server local. |Sim |
| o nome de utilizador |Especifique o nome de usuário se você estiver usando a autenticação do Windows. Exemplo: **domainname\\username**. |Não |
| palavra-passe |Especifique a senha para a conta de usuário especificada para o nome do usuário. |Não |

Você pode criptografar credenciais usando o cmdlet **New-AzDataFactoryEncryptValue** e usá-las na cadeia de conexão, conforme mostrado no exemplo a seguir (propriedade**EncryptedCredential** ):

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Exemplo: JSON para usar a autenticação do SQL

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Exemplo: JSON para usar a autenticação do Windows

Se o nome de usuário e a senha forem especificados, o gateway os usará para representar a conta de usuário especificada para se conectar ao banco de dados SQL Server local. Caso contrário, o gateway se conecta ao SQL Server diretamente com o contexto de segurança do gateway (sua conta de inicialização).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de SQL Server, defina o **tipo** do conjunto de um como **sqlservertable**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou exibição na instância de banco de dados SQL Server à qual o serviço vinculado se refere. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector SQL Server](data-factory-sqlserver-connector.md#dataset-properties) .

### <a name="sql-source-in-copy-activity"></a>Origem do SQL na atividade de cópia
Se você estiver copiando dados de um banco de SQL Server, defina o **tipo de origem** da atividade de cópia para **sqlsource**e especifique as propriedades a seguir na seção **Source** :


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable`. Pode fazer referência a várias tabelas do banco de dados referenciado pelo DataSet de entrada. Se não for especificado, a instrução SQL executada: selecione from MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Os nomes e maiúsculas e minúsculas dos parâmetros devem corresponder aos nomes e maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

Se o **sqlReaderQuery** for especificado para sqlsource, a atividade de cópia executará essa consulta em relação à origem do banco de dados SQL Server para obter os dados.

Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e **storedprocedureparameters** (se o procedimento armazenado usar parâmetros).

Se você não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura serão usadas para criar uma consulta Select a ser executada no banco de dados SQL Server. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas serão selecionadas da tabela.

> [!NOTE]
> Ao usar o **sqlReaderStoredProcedureName**, você ainda precisa especificar um valor para a propriedade **TableName** no JSON do conjunto de valores. No entanto, não há validações executadas nessa tabela.


#### <a name="example"></a>Exemplo
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Neste exemplo, **sqlReaderQuery** é especificado para sqlsource. A atividade de cópia executa essa consulta em relação à origem do banco de dados SQL Server para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e **storedprocedureparameters** (se o procedimento armazenado usar parâmetros). O sqlReaderQuery pode fazer referência a várias tabelas dentro do banco de dados referenciado pelo DataSet de entrada. Ele não está limitado apenas ao conjunto de tabelas como TableName TYPEPROPERTY do DataSet.

Se você não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura serão usadas para criar uma consulta Select a ser executada no banco de dados SQL Server. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas serão selecionadas da tabela.

Para obter mais informações, consulte o artigo [conector SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) .

### <a name="sql-sink-in-copy-activity"></a>Coletor SQL na atividade de cópia
Se você estiver copiando dados para um banco de SQL Server, defina o **tipo de coletor** da atividade de cópia para **sqlsink**e especifique as propriedades a seguir na seção **Sink** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo de espera para que a operação de inserção em lote seja concluída antes de atingir o tempo limite. |período<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |
| WriteBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanupScript |Especifique a consulta para a atividade de cópia a ser executada, de modo que os dados de uma fatia específica sejam limpos. Para obter mais informações, consulte a seção de repetição. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a atividade de cópia a ser preenchida com o identificador de fatia gerado automaticamente, que é usado para limpar os dados de uma fatia específica quando executado novamente. Para obter mais informações, consulte a seção de repetição. |Nome da coluna de uma coluna com tipo de dados binary (32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que upserts (atualiza/insere) dados na tabela de destino. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Os nomes e maiúsculas e minúsculas dos parâmetros devem corresponder aos nomes e maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |
| sqlWriterTableType |Especifique o nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia torna os dados sendo movidos disponíveis em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados que estão sendo copiados com os dados existentes. |Um nome de tipo de tabela. |Não |

#### <a name="example"></a>Exemplo
O pipeline contém uma atividade de cópia que é configurada para usar esses conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** está definido como **blobname** e o tipo de **coletor** está definido como **sqlsink**.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [conector SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) .

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do Sybase, defina o **tipo** do serviço vinculado para **OnPremisesSybase**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor Sybase. |Sim |
| base de dados |Nome do banco de dados Sybase. |Sim |
| schema |Nome do esquema no banco de dados. |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados Sybase. Os valores possíveis são: Anonymous, Basic e Windows. |Sim |
| o nome de utilizador |Especifique o nome de usuário se você estiver usando a autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a senha para a conta de usuário especificada para o nome do usuário. |Não |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao banco de dados Sybase local. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector do Sybase](data-factory-onprem-sybase-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de uma do Sybase, defina o **tipo** do conjunto de um como **RelationalTable**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância do banco de dados Sybase à qual o serviço vinculado se refere. |Não (se a **consulta** de **RelationalSource** for especificada) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector do Sybase](data-factory-onprem-sybase-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se você estiver copiando dados de um banco de dado Sybase, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique as propriedades a seguir na seção **origem** :


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **TableName** de **DataSet** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [conector do Sybase](data-factory-onprem-sybase-connector.md#copy-activity-properties) .

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do Teradata, defina o **tipo** do serviço vinculado para **OnPremisesTeradata**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor Teradata. |Sim |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados Teradata. Os valores possíveis são: Anonymous, Basic e Windows. |Sim |
| o nome de utilizador |Especifique o nome de usuário se você estiver usando a autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a senha para a conta de usuário especificada para o nome do usuário. |Não |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao banco de dados Teradata local. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector do Teradata](data-factory-onprem-teradata-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de um DataSet de banco de Teradata, defina o **tipo** do conjunto de um como **RelationalTable**. No momento, não há nenhuma propriedade de tipo com suporte para o conjunto de texto Teradata.

#### <a name="example"></a>Exemplo
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector do Teradata](data-factory-onprem-teradata-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se você estiver copiando dados de um banco de dados Teradata, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique as propriedades a seguir na seção **origem** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable`. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

Para obter mais informações, consulte o artigo [conector do Teradata](data-factory-onprem-teradata-connector.md#copy-activity-properties) .

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do Cassandra, defina o **tipo** do serviço vinculado para **OnPremisesCassandra**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| hospedeira |Um ou mais endereços IP ou nomes de host de servidores Cassandra.<br/><br/>Especifique uma lista separada por vírgulas de endereços IP ou nomes de host para se conectar a todos os servidores simultaneamente. |Sim |
| porta |A porta TCP que o servidor Cassandra usa para escutar conexões de cliente. |Não, valor padrão: 9042 |
| authenticationType |Básico ou anônimo |Sim |
| o nome de utilizador |Especifique o nome de usuário para a conta de usuário. |Sim, se authenticationType estiver definido como básico. |
| palavra-passe |Especifique a senha para a conta de usuário. |Sim, se authenticationType estiver definido como básico. |
| gatewayName |O nome do gateway que é usado para se conectar ao banco de dados Cassandra local. |Sim |
| encryptedCredential |Credencial criptografada pelo Gateway. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector do Cassandra](data-factory-onprem-cassandra-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de Cassandra, defina o **tipo** do conjunto de um como **CassandraTable**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| keyspace |Nome do keyspace ou esquema no banco de dados Cassandra. |Sim (se a **consulta** para **CassandraSource** não estiver definida). |
| tableName |Nome da tabela no banco de dados Cassandra. |Sim (se a **consulta** para **CassandraSource** não estiver definida). |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector do Cassandra](data-factory-onprem-cassandra-connector.md#dataset-properties) .

### <a name="cassandra-source-in-copy-activity"></a>Origem de Cassandra na atividade de cópia
Se você estiver copiando dados do Cassandra, defina o **tipo de origem** da atividade de cópia para **CassandraSource**e especifique as propriedades a seguir na seção de **origem** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Consulta SQL-92 ou consulta CQL. Consulte [referência de CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao usar a consulta SQL, especifique o **nome do keyspace. nome da tabela** para representar a tabela que você deseja consultar. |Não (se TableName e keyspace no conjunto de conjuntos forem definidos). |
| consistencyLevel |O nível de consistência especifica quantas réplicas devem responder a uma solicitação de leitura antes de retornar dados para o aplicativo cliente. Cassandra verifica o número especificado de réplicas em busca de dados para atender à solicitação de leitura. |UM, DOIS, TRÊS, QUORUM, TODOS, LOCAL_QUORUM, EACH_QUORUM LOCAL_ONE. Consulte [Configurando a consistência de dados](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) para obter detalhes. |Não. O valor padrão é um. |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [conector do Cassandra](data-factory-onprem-cassandra-connector.md#copy-activity-properties) .

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do MongoDB, defina o **tipo** do serviço vinculado para **OnPremisesMongoDB**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Endereço IP ou nome do host do servidor MongoDB. |Sim |
| porta |Porta TCP que o servidor MongoDB usa para escutar conexões de cliente. |Opcional, valor padrão: 27017 |
| authenticationType |Básico ou anônimo. |Sim |
| o nome de utilizador |Conta de usuário para acessar o MongoDB. |Sim (se a autenticação básica for usada). |
| palavra-passe |A palavra-passe do utilizador. |Sim (se a autenticação básica for usada). |
| AuthName |Nome do banco de dados MongoDB que você deseja usar para verificar suas credenciais para autenticação. |Opcional (se a autenticação básica for usada). padrão: usa a conta de administrador e o banco de dados especificado usando a propriedade databaseName. |
| NomeDoBancoDeDados |Nome do banco de dados MongoDB que você deseja acessar. |Sim |
| gatewayName |Nome do gateway que acessa o armazenamento de dados. |Sim |
| encryptedCredential |Credencial criptografada pelo Gateway. |Opcional |

#### <a name="example"></a>Exemplo

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte o [artigo conector do MongoDB](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de conjuntos do MongoDB, defina o **tipo** do conjunto de um como **mongodbcollection**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| collectionName |Nome da coleção no banco de dados MongoDB. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

Para obter mais informações, consulte o [artigo conector do MongoDB](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>Origem do MongoDB na atividade de cópia
Se você estiver copiando dados do MongoDB, defina o **tipo de origem** da atividade de cópia para **MongoDB**e especifique as propriedades a seguir na seção de **origem** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL-92. Por exemplo: `select * from MyTable`. |Não (se **CollectionName** de **DataSet** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o [artigo conector do MongoDB](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do Amazon S3, defina o **tipo** do serviço vinculado para **AwsAccessKey**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| accessKeyid |ID da chave de acesso secreta. |string |Sim |
| secretAccessKey |A própria chave de acesso de segredo. |Cadeia de caracteres secreta criptografada |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

Para obter mais informações, consulte o [artigo conector do Amazon S3](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de um do Amazon S3, defina o **tipo** do conjunto de um como **AmazonS3**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| bucketName |O nome do Bucket S3. |String |Sim |
| key |A chave do objeto S3. |String |Não |
| prefixo |Prefixo da chave do objeto S3. Os objetos cujas chaves começam com esse prefixo são selecionados. Aplica-se somente quando a chave está vazia. |String |Não |
| versão |A versão do objeto S3 se o controle de versão S3 estiver habilitado. |String |Não |
| ao | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **Type** em Format para um desses valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e seções de [formato parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção formato nas definições do conjunto de dados de entrada e saída. |Não | |
| çã | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **gzip**, **deflate**, **bzip2**e **ZipDeflate**. Os níveis com suporte são: **ideal** e **mais rápido**. Para obter mais informações, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não | |


> [!NOTE]
> bucketname + Key especifica o local do objeto S3 em que Bucket é o contêiner raiz para objetos S3 e a chave é o caminho completo para o objeto S3.

#### <a name="example-sample-dataset-with-prefix"></a>Exemplo: conjunto de exemplo com prefixo

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Exemplo: conjunto de dados de exemplo (com versão)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Exemplo: caminhos dinâmicos para S3
No exemplo, usamos valores fixos para as propriedades Key e bucketname no conjunto de um do Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Você pode ter Data Factory calcular a chave e o bucketname dinamicamente no tempo de execução usando variáveis de sistema como SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Você pode fazer o mesmo para a propriedade prefix de um conjunto de um Amazon S3. Consulte [funções de data Factory e variáveis de sistema](data-factory-functions-variables.md) para obter uma lista de funções e variáveis com suporte.

Para obter mais informações, consulte o [artigo conector do Amazon S3](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Origem do sistema de arquivos na atividade de cópia
Se você estiver copiando dados do Amazon S3, defina o **tipo de origem** da atividade de cópia como **FileSystemName**e especifique as propriedades a seguir na seção **Source** :


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Especifica se os objetos S3 devem ser recursivamente listados no diretório. |verdadeiro/falso |Não |


#### <a name="example"></a>Exemplo


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

Para obter mais informações, consulte o [artigo conector do Amazon S3](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Sistema de Ficheiros


### <a name="linked-service"></a>Serviço ligado
Você pode vincular um sistema de arquivos local a um data factory do Azure com o serviço vinculado do **servidor de arquivos local** . A tabela a seguir fornece descrições para elementos JSON que são específicos para o serviço vinculado do servidor de arquivos local.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |Verifique se a propriedade Type está definida como **OnPremisesFileServer**. |Sim |
| hospedeira |Especifica o caminho raiz da pasta que você deseja copiar. Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Consulte amostra de serviço vinculado e definições de conjunto de exemplos. |Sim |
| ID |Especifique a ID do usuário que tem acesso ao servidor. |Não (se você escolher encryptedCredential) |
| palavra-passe |Especifique a senha para o usuário (UserID). |Não (se você escolher encryptedCredential |
| encryptedCredential |Especifique as credenciais criptografadas que você pode obter executando o cmdlet New-AzDataFactoryEncryptValue. |Não (se você optar por especificar userid e password em texto sem formatação) |
| gatewayName |Especifica o nome do gateway que Data Factory deve usar para se conectar ao servidor de arquivos local. |Sim |

#### <a name="sample-folder-path-definitions"></a>Definições de caminho de pasta de exemplo

| Cenário | Host na definição de serviço vinculado | folderPath na definição de DataSet |
| --- | --- | --- |
| Pasta local no computador do gateway de Gerenciamento de Dados: <br/><br/>Exemplos: D:\\\* ou D:\folder\subfolder\\\* |D:\\\\ (para Gerenciamento de Dados gateway 2,0 e versões posteriores) <br/><br/> localhost (para versões anteriores do que Gerenciamento de Dados gateway 2,0) |.\\\\ ou pasta\\\\subpasta (para Gerenciamento de Dados gateway 2,0 e versões posteriores) <br/><br/>D:\\\\ ou D:\\pasta \\\\subpasta \\(para a versão do gateway abaixo de 2,0) |
| Pasta compartilhada remota: <br/><br/>Exemplos: \\\\meuservidor\\compartilhamento\\\* ou \\\\MyServer\\compartilhamento\\pasta\\\\subpasta \* |\\\\\\\\meuservidor\\compartilhamento de \\ |.\\\\ ou pasta\\subpasta \\ |


#### <a name="example-using-username-and-password-in-plain-text"></a>Exemplo: usando nome de usuário e senha em texto sem formatação

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Exemplo: usando encryptedcredential

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte o [artigo conector do sistema de arquivos](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de um sistema de arquivos, defina o **tipo** do conjunto de um como **FileShare**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Especifica o subcaminho para a pasta. Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Consulte amostra de serviço vinculado e definições de conjunto de exemplos.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pasta com base em data/hora de início/término da fatia. |Sim |
| fileName |Especifique o nome do arquivo no **FolderPath** se você quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar nenhum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando fileName não for especificado para um conjunto de resultados de saída, o nome do arquivo gerado estará no seguinte formato: <br/><br/>`Data.<Guid>.txt` (exemplo: Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt) |Não |
| fileFilter |Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no folderPath em vez de todos os arquivos. <br/><br/>Os valores permitidos são: `*` (vários caracteres) e `?` (caractere único).<br/><br/>Exemplo 1: "FileFilter": "*. log"<br/>Exemplo 2: "FileFilter": 2016-1-?. localizado<br/><br/>Observe que FileFilter é aplicável a um conjunto de dados de FileShare de entrada. |Não |
| partitionedBy |Você pode usar partitionedBy para especificar um folderPath/fileName dinâmico para dados de série temporal. Um exemplo é folderPath parametrizado para cada hora dos dados. |Não |
| ao | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **Type** em Format para um desses valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e seções de [formato parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção formato nas definições do conjunto de dados de entrada e saída. |Não |
| çã | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **gzip**, **deflate**, **bzip2**e **ZipDeflate**; e os níveis com suporte são: **ideal** e **mais rápido**. consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> Você não pode usar fileName e FileFilter simultaneamente.

#### <a name="example"></a>Exemplo

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o [artigo conector do sistema de arquivos](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Origem do sistema de arquivos na atividade de cópia
Se você estiver copiando dados do sistema de arquivos, defina o **tipo de origem** da atividade de cópia como **FileSystemName**e especifique as propriedades a seguir na seção **Source** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. |True, false (padrão) |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Para obter mais informações, consulte o [artigo conector do sistema de arquivos](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Coletor do sistema de arquivos na atividade de cópia
Se você estiver copiando dados para o sistema de arquivos, defina o **tipo de coletor** da atividade de cópia para **FileSystemSink**e especifique as propriedades a seguir na seção **Sink** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BLOB ou FileSystem. |**PreserveHierarchy:** Preserva a hierarquia de arquivos na pasta de destino. Ou seja, o caminho relativo do arquivo de origem para a pasta de origem é o mesmo que o caminho relativo do arquivo de destino para a pasta de destino.<br/><br/>**FlattenHierarchy:** Todos os arquivos da pasta de origem são criados no primeiro nível da pasta de destino. Os arquivos de destino são criados com um nome gerado automaticamente.<br/><br/>**MergeFiles:** Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/nome do blob for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, será um nome de arquivo gerado automaticamente. |Não |

detectar

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o [artigo conector do sistema de arquivos](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado de FTP, defina o **tipo** do serviço vinculado para **ftpserver**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| hospedeira |Nome ou endereço IP do servidor FTP |Sim |&nbsp; |
| authenticationType |Especificar o tipo de autenticação |Sim |Básico, anônimo |
| o nome de utilizador |Usuário que tem acesso ao servidor FTP |Não |&nbsp; |
| palavra-passe |Senha para o usuário (username) |Não |&nbsp; |
| encryptedCredential |Credencial criptografada para acessar o servidor FTP |Não |&nbsp; |
| gatewayName |Nome do gateway de Gerenciamento de Dados para se conectar a um servidor FTP local |Não |&nbsp; |
| porta |Porta na qual o servidor FTP está ouvindo |Não |21 |
| enableSsl |Especifique se deseja usar o canal FTP sobre SSL/TLS |Não |true |
| enableServerCertificateValidation |Especifique se deseja habilitar a validação de certificado SSL do servidor ao usar o canal FTP sobre SSL/TLS |Não |true |

#### <a name="example-using-anonymous-authentication"></a>Exemplo: usando a autenticação anônima

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Exemplo: usando nome de usuário e senha em texto sem formatação para autenticação básica

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Exemplo: usando Port, enableSsl, enableServerCertificateValidation

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Exemplo: usando encryptedCredential para autenticação e gateway

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

Para obter mais informações, consulte o artigo [conector de FTP](data-factory-ftp-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de texto de FTP, defina o **tipo** do conjunto de um como **FileShare**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Sub-caminho para a pasta. Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Consulte amostra de serviço vinculado e definições de conjunto de exemplos.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pasta com base em data/hora de início/término da fatia. |Sim
| fileName |Especifique o nome do arquivo no **FolderPath** se você quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar nenhum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando fileName não for especificado para um conjunto de resultados de saída, o nome do arquivo gerado estaria no seguinte formato: <br/><br/>`Data.<Guid>.txt` (exemplo: Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt) |Não |
| fileFilter |Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no folderPath em vez de todos os arquivos.<br/><br/>Os valores permitidos são: `*` (vários caracteres) e `?` (caractere único).<br/><br/>Exemplos 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> FileFilter é aplicável a um conjunto de dados de FileShare de entrada. Não há suporte para essa propriedade com HDFS. |Não |
| partitionedBy |partitionedBy pode ser usado para especificar um nome de arquivo folderPath dinâmico para dados de série temporal. Por exemplo, folderPath parametrizado para cada hora dos dados. |Não |
| ao | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **Type** em Format para um desses valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e seções de [formato parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção formato nas definições do conjunto de dados de entrada e saída. |Não |
| çã | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **gzip**, **deflate**, **bzip2**e **ZipDeflate**; e os níveis com suporte são: **ideal** e **mais rápido**. Para obter mais informações, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se o modo de transferência binária deve ser usado. True para modo binário e falso ASCII. Valor padrão: true. Essa propriedade só pode ser usada quando o tipo de serviço vinculado associado for do tipo: FtpServer. |Não |

> [!NOTE]
> filename e FileFilter não podem ser usados simultaneamente.

#### <a name="example"></a>Exemplo

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector de FTP](data-factory-ftp-connector.md#dataset-properties) .

### <a name="file-system-source-in-copy-activity"></a>Origem do sistema de arquivos na atividade de cópia
Se você estiver copiando dados de um servidor FTP, defina o **tipo de origem** da atividade de cópia para o **FileSystemName**e especifique as propriedades a seguir na seção **Source** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. |True, false (padrão) |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [conector de FTP](data-factory-ftp-connector.md#copy-activity-properties) .


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do HDFS, defina o **tipo** do serviço vinculado para **HDFS**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **HDFS** |Sim |
| Url |URL para o HDFS |Sim |
| authenticationType |Anônimo ou Windows. <br><br> Para usar a **autenticação Kerberos** para o conector HDFS, consulte esta seção para configurar seu ambiente local de acordo. |Sim |
| Usu |Nome de usuário para autenticação do Windows. |Sim (para autenticação do Windows) |
| palavra-passe |Senha para autenticação do Windows. |Sim (para autenticação do Windows) |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao HDFS. |Sim |
| encryptedCredential |Saída [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) da credencial de acesso. |Não |

#### <a name="example-using-anonymous-authentication"></a>Exemplo: usando a autenticação anônima

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Exemplo: usando a autenticação do Windows

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte o artigo conector do HDFS.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de conjuntos de um HDFS, defina o **tipo** do conjunto de um como **FileShare**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para a pasta. Exemplo: `myfolder`<br/><br/>Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Por exemplo: para pasta\subpasta, especifique a pasta\\\\subpasta e para d:\samplefolder, especifique d:\\\\pastadeexemplo.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pasta com base em data/hora de início/término da fatia. |Sim |
| fileName |Especifique o nome do arquivo no **FolderPath** se você quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar nenhum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando fileName não for especificado para um conjunto de resultados de saída, o nome do arquivo gerado estaria no seguinte formato: <br/><br/>`Data.<Guid>.txt` (por exemplo: Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt |Não |
| partitionedBy |partitionedBy pode ser usado para especificar um nome de arquivo folderPath dinâmico para dados de série temporal. Exemplo: folderPath parametrizado para cada hora dos dados. |Não |
| ao | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **Type** em Format para um desses valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e seções de [formato parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção formato nas definições do conjunto de dados de entrada e saída. |Não |
| çã | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **gzip**, **deflate**, **bzip2**e **ZipDeflate**. Os níveis com suporte são: **ideal** e **mais rápido**. Para obter mais informações, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> filename e FileFilter não podem ser usados simultaneamente.

#### <a name="example"></a>Exemplo

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte o artigo conector do HDFS.

### <a name="file-system-source-in-copy-activity"></a>Origem do sistema de arquivos na atividade de cópia
Se você estiver copiando dados do HDFS, defina o **tipo de origem** da atividade de cópia como **FileSystemName**e especifique as propriedades a seguir na seção **Source** :

O **FileSystemName** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. |True, false (padrão) |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo conector do HDFS.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do SFTP, defina o **tipo** do serviço vinculado para **SFTP**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| hospedeira | Nome ou endereço IP do servidor SFTP. |Sim |
| porta |Porta na qual o servidor SFTP está escutando. O valor padrão é: 21 |Não |
| authenticationType |Especifique o tipo de autenticação. Valores permitidos: **Basic**, **SshPublicKey**. <br><br> Consulte usando a autenticação básica e [usando seções de autenticação de chave pública SSH](#using-ssh-public-key-authentication) em mais propriedades e exemplos JSON, respectivamente. |Sim |
| skipHostKeyValidation | Especifique se deseja ignorar a validação da chave do host. | Não. O valor padrão: false |
| hostKeyFingerprint | Especifique a impressão digital da chave do host. | Sim se o `skipHostKeyValidation` for definido como false.  |
| gatewayName |Nome do gateway de Gerenciamento de Dados para se conectar a um servidor SFTP local. | Sim, se estiver copiando dados de um servidor SFTP local. |
| encryptedCredential | Credencial criptografada para acessar o servidor SFTP. Gerado automaticamente quando você especifica autenticação básica (nome de usuário + senha) ou autenticação SshPublicKey (nome de usuário + caminho ou conteúdo da chave privada) no assistente de cópia ou na caixa de diálogo pop-up do ClickOnce. | Não. Aplica-se somente ao copiar dados de um servidor SFTP local. |

#### <a name="example-using-basic-authentication"></a>Exemplo: usando a autenticação básica

Para usar a autenticação básica, defina `authenticationType` como `Basic`e especifique as propriedades a seguir, além das genéricas do conector SFTP introduzidas na última seção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| o nome de utilizador | Usuário que tem acesso ao servidor SFTP. |Sim |
| palavra-passe | Senha do usuário (username). | Sim |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exemplo: **autenticação básica com credencial criptografada**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**Usando a autenticação de chave pública SSH:**

Para usar a autenticação básica, defina `authenticationType` como `SshPublicKey`e especifique as propriedades a seguir, além das genéricas do conector SFTP introduzidas na última seção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| o nome de utilizador |Usuário que tem acesso ao servidor SFTP |Sim |
| privateKeyPath | Especifique o caminho absoluto para o arquivo de chave privada que o gateway pode acessar. | Especifique o `privateKeyPath` ou `privateKeyContent`. <br><br> Aplica-se somente ao copiar dados de um servidor SFTP local. |
| privateKeyContent | Uma cadeia de caracteres serializada do conteúdo da chave privada. O assistente de cópia pode ler o arquivo de chave privada e extrair automaticamente o conteúdo da chave privada. Se você estiver usando qualquer outra ferramenta/SDK, use a propriedade privateKeyPath em vez disso. | Especifique o `privateKeyPath` ou `privateKeyContent`. |
| passPhrase | Especifique a frase secreta/senha para descriptografar a chave privada se o arquivo de chave estiver protegido por uma frase secreta. | Sim se o arquivo de chave privada for protegido por uma frase secreta. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exemplo: **autenticação SshPublicKey usando conteúdo de chave privada**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector do SFTP](data-factory-sftp-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de conjuntos de um SFTP, defina o **tipo** do conjunto de um como **FileShare**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Sub-caminho para a pasta. Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Consulte amostra de serviço vinculado e definições de conjunto de exemplos.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pasta com base em data/hora de início/término da fatia. |Sim |
| fileName |Especifique o nome do arquivo no **FolderPath** se você quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar nenhum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando fileName não for especificado para um conjunto de resultados de saída, o nome do arquivo gerado estaria no seguinte formato: <br/><br/>`Data.<Guid>.txt` (exemplo: Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt) |Não |
| fileFilter |Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no folderPath em vez de todos os arquivos.<br/><br/>Os valores permitidos são: `*` (vários caracteres) e `?` (caractere único).<br/><br/>Exemplos 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> FileFilter é aplicável a um conjunto de dados de FileShare de entrada. Não há suporte para essa propriedade com HDFS. |Não |
| partitionedBy |partitionedBy pode ser usado para especificar um nome de arquivo folderPath dinâmico para dados de série temporal. Por exemplo, folderPath parametrizado para cada hora dos dados. |Não |
| ao | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **Type** em Format para um desses valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e seções de [formato parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção formato nas definições do conjunto de dados de entrada e saída. |Não |
| çã | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **gzip**, **deflate**, **bzip2**e **ZipDeflate**. Os níveis com suporte são: **ideal** e **mais rápido**. Para obter mais informações, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se o modo de transferência binária deve ser usado. True para modo binário e falso ASCII. Valor padrão: true. Essa propriedade só pode ser usada quando o tipo de serviço vinculado associado for do tipo: FtpServer. |Não |

> [!NOTE]
> filename e FileFilter não podem ser usados simultaneamente.

#### <a name="example"></a>Exemplo

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector do SFTP](data-factory-sftp-connector.md#dataset-properties) .

### <a name="file-system-source-in-copy-activity"></a>Origem do sistema de arquivos na atividade de cópia
Se você estiver copiando dados de uma fonte de SFTP, defina o **tipo de origem** da atividade de cópia para o **FileSystemName**e especifique as propriedades a seguir na seção **Source** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. |True, false (padrão) |Não |



#### <a name="example"></a>Exemplo

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [conector do SFTP](data-factory-sftp-connector.md#copy-activity-properties) .


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado de HTTP, defina o **tipo** do serviço vinculado para **http**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| url | URL base para o servidor Web | Sim |
| authenticationType | Especifica o tipo de autenticação. Os valores permitidos são: **Anonymous**, **Basic**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Consulte as seções abaixo desta tabela em mais propriedades e exemplos JSON para esses tipos de autenticação, respectivamente. | Sim |
| enableServerCertificateValidation | Especifique se deseja habilitar a validação de certificado SSL do servidor se a origem for um servidor Web HTTPS | Não, o padrão é true |
| gatewayName | Nome do gateway de Gerenciamento de Dados para se conectar a uma origem HTTP local. | Sim, se estiver copiando dados de uma origem HTTP local. |
| encryptedCredential | Credencial criptografada para acessar o ponto de extremidade HTTP. Gerado automaticamente quando você configura as informações de autenticação no assistente de cópia ou na caixa de diálogo pop-up do ClickOnce. | Não. Aplique somente ao copiar dados de um servidor HTTP local. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Exemplo: usando a autenticação básica, Digest ou Windows
Defina `authenticationType` como `Basic`, `Digest`ou `Windows`e especifique as propriedades a seguir, além das genéricas do conector HTTP introduzidas acima:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| o nome de utilizador | Nome de usuário para acessar o ponto de extremidade HTTP. | Sim |
| palavra-passe | Senha do usuário (username). | Sim |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Exemplo: usando a autenticação ClientCertificate

Para usar a autenticação básica, defina `authenticationType` como `ClientCertificate`e especifique as propriedades a seguir, além das genéricas do conector HTTP introduzidas acima:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| embeddedCertData | O conteúdo codificado na Base64 de dados binários do arquivo de troca de informações pessoais (PFX). | Especifique o `embeddedCertData` ou `certThumbprint`. |
| certThumbprint | A impressão digital do certificado que foi instalado no repositório de certificados do computador do gateway. Aplicar somente ao copiar dados de uma origem HTTP local. | Especifique o `embeddedCertData` ou `certThumbprint`. |
| palavra-passe | Senha associada ao certificado. | Não |

Se você usar `certThumbprint` para autenticação e o certificado estiver instalado no repositório pessoal do computador local, será necessário conceder a permissão de leitura para o serviço do gateway:

1. Inicie o MMC (console de gerenciamento Microsoft). Adicione o snap-in de **certificados** que tem como destino o **computador local**.
2. Expanda **certificados**, **pessoal**e clique em **certificados**.
3. Clique com o botão direito do mouse no certificado do repositório pessoal e selecione **todas as tarefas**->**gerenciar chaves privadas...**
3. Na guia **segurança** , adicione a conta de usuário sob a qual gerenciamento de dados serviço host de gateway está sendo executado com o acesso de leitura para o certificado.

**Exemplo: usando o certificado do cliente:** Esse serviço vinculado vincula seu data factory a um servidor Web HTTP local. Ele usa um certificado de cliente instalado no computador com o gateway de Gerenciamento de Dados instalado.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Exemplo: usando o certificado de cliente em um arquivo
Esse serviço vinculado vincula seu data factory a um servidor Web HTTP local. Ele usa um arquivo de certificado do cliente no computador com Gerenciamento de Dados gateway instalado.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector http](data-factory-http-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de um DataSet, defina o **tipo** do conjunto de um como **http**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| relativeUrl | Uma URL relativa para o recurso que contém os dados. Quando o caminho não for especificado, somente a URL especificada na definição de serviço vinculado será usada. <br><br> Para construir a URL dinâmica, você pode usar [Data Factory funções e variáveis do sistema](data-factory-functions-variables.md), por exemplo: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Não |
| requestMethod | Método http. Os valores permitidos são **Get** ou **post**. | Não. A predefinição é `GET`. |
| additionalHeaders | Cabeçalhos de solicitação HTTP adicionais. | Não |
| requestBody | Corpo da solicitação HTTP. | Não |
| ao | Se você quiser simplesmente **recuperar os dados do ponto de extremidade http como estão** sem analisá-los, ignore estas configurações de formato. <br><br> Se você deseja analisar o conteúdo da resposta HTTP durante a cópia, há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e seções de [formato parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . |Não |
| çã | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **gzip**, **deflate**, **bzip2**e **ZipDeflate**. Os níveis com suporte são: **ideal** e **mais rápido**. Para obter mais informações, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

#### <a name="example-using-the-get-default-method"></a>Exemplo: usando o método GET (padrão)

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Exemplo: usando o método POST

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
Para obter mais informações, consulte o artigo [conector http](data-factory-http-connector.md#dataset-properties) .

### <a name="http-source-in-copy-activity"></a>Origem HTTP na atividade de cópia
Se você estiver copiando dados de uma origem HTTP, defina o **tipo de origem** da atividade de cópia para **httpname**e especifique as propriedades a seguir na seção de **origem** :

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| httpRequestTimeout | O tempo limite (TimeSpan) para a solicitação HTTP obter uma resposta. É o tempo limite para obter uma resposta, não o tempo limite para ler dados de resposta. | Não. Valor padrão: 00:01:40 |


#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [conector http](data-factory-http-connector.md#copy-activity-properties) .

## <a name="odata"></a>OData

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do OData, defina o **tipo** do serviço vinculado para **OData**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| url |URL do serviço OData. |Sim |
| authenticationType |Tipo de autenticação usado para se conectar à fonte OData. <br/><br/> Para o Cloud OData, os valores possíveis são Anonymous, Basic e OAuth (Observe Azure Data Factory atualmente só dão suporte ao OAuth com base em Azure Active Directory). <br/><br/> Para o OData local, os valores possíveis são Anonymous, Basic e Windows. |Sim |
| o nome de utilizador |Especifique o nome de usuário se você estiver usando a autenticação básica. |Sim (somente se você estiver usando a autenticação básica) |
| palavra-passe |Especifique a senha para a conta de usuário especificada para o nome do usuário. |Sim (somente se você estiver usando a autenticação básica) |
| authorizedCredential |Se você estiver usando o OAuth, clique no botão **autorizar** no assistente de data Factory de cópia ou no editor e insira sua credencial, então o valor dessa propriedade será gerado automaticamente. |Sim (somente se você estiver usando a autenticação OAuth) |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao serviço OData local. Especifique somente se você estiver copiando dados da fonte OData local. |Não |

#### <a name="example---using-basic-authentication"></a>Exemplo-usando a autenticação básica
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Exemplo – usando a autenticação anônima

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Exemplo-usando a autenticação do Windows para acessar a fonte OData local

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Exemplo-usando a autenticação OAuth acessando a fonte OData de nuvem
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector OData](data-factory-odata-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de um banco de um, defina o **tipo** do conjunto de um como **ODataResource**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Multi-Path |Caminho para o recurso OData |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector OData](data-factory-odata-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se você estiver copiando dados de uma fonte OData, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique as propriedades a seguir na seção **Source** :

| Propriedade | Descrição | Exemplo | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |"? $select = name, Description & $top = 5" |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [conector OData](data-factory-odata-connector.md#copy-activity-properties) .


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do ODBC, defina o **tipo** do serviço vinculado para **OnPremisesOdbc**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| connectionString |A parte de credencial de não acesso da cadeia de conexão e uma credencial criptografada opcional. Consulte os exemplos nas seções a seguir. |Sim |
| credencial |A parte de credencial de acesso da cadeia de conexão especificada no formato de valor de propriedade específico do driver. Exemplo: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao armazenamento de dados ODBC. Os valores possíveis são: Anonymous e Basic. |Sim |
| o nome de utilizador |Especifique o nome de usuário se você estiver usando a autenticação básica. |Não |
| palavra-passe |Especifique a senha para a conta de usuário especificada para o nome do usuário. |Não |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao armazenamento de dados ODBC. |Sim |

#### <a name="example---using-basic-authentication"></a>Exemplo-usando a autenticação básica

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Exemplo-usando a autenticação básica com credenciais criptografadas
Você pode criptografar as credenciais usando o cmdlet [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) .

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Exemplo: usando a autenticação anônima

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector ODBC](data-factory-odbc-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de um ODBC, defina o **tipo** do conjunto de um como **RelationalTable**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela no repositório de dados ODBC. |Sim |


#### <a name="example"></a>Exemplo

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector ODBC](data-factory-odbc-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se você estiver copiando dados de um armazenamento de dados ODBC, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique as propriedades a seguir na seção **origem** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable`. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [conector ODBC](data-factory-odbc-connector.md#copy-activity-properties) .

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado do Salesforce, defina o **tipo** do serviço vinculado para **Salesforce**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| environmentUrl | Especifique a URL da instância do Salesforce. <br><br> -O padrão é "https:\//login.salesforce.com". <br> -Para copiar dados da área restrita, especifique "https://test.salesforce.com". <br> -Para copiar dados do domínio personalizado, especifique, por exemplo, "https://[domínio]. My. Salesforce. com". |Não |
| o nome de utilizador |Especifique um nome de usuário para a conta de usuário. |Sim |
| palavra-passe |Especifique uma senha para a conta de usuário. |Sim |
| securityToken |Especifique um token de segurança para a conta de usuário. Consulte [obter token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para obter instruções sobre como redefinir/obter um token de segurança. Para saber mais sobre os tokens de segurança em geral, consulte [segurança e a API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector do Salesforce](data-factory-salesforce-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de texto do Salesforce, defina o **tipo** do conjunto de um como **RelationalTable**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela no Salesforce. |Não (se uma **consulta** de **RelationalSource** for especificada) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector do Salesforce](data-factory-salesforce-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se você estiver copiando dados do Salesforce, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique as propriedades a seguir na seção de **origem** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Uma consulta SQL-92 ou consulta [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) . Por exemplo: `select * from MyTable__c`. |Não (se **TableName** do **DataSet** for especificado) |

#### <a name="example"></a>Exemplo



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> A parte "__c" do nome da API é necessária para qualquer objeto personalizado.

Para obter mais informações, consulte o artigo [conector do Salesforce](data-factory-salesforce-connector.md#copy-activity-properties) .

## <a name="web-data"></a>Dados da Web

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço vinculado da Web, defina o **tipo** do serviço vinculado para **Web**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Url |URL para a origem da Web |Sim |
| authenticationType |Modo. |Sim |


#### <a name="example"></a>Exemplo


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector de tabela da Web](data-factory-web-table-connector.md#linked-service-properties) .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de uma Web, defina o **tipo** do conjunto de um como **webtable**e especifique as propriedades a seguir na seção **typeproperties** :

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |Tipo do conjunto de um. deve ser definido como **Webtable** |Sim |
| Multi-Path |Uma URL relativa para o recurso que contém a tabela. |Não. Quando o caminho não for especificado, somente a URL especificada na definição de serviço vinculado será usada. |
| index |O índice da tabela no recurso. Consulte a seção obter índice de uma tabela em uma página HTML para obter as etapas para obter o índice de uma tabela em uma página HTML. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector de tabela da Web](data-factory-web-table-connector.md#dataset-properties) .

### <a name="web-source-in-copy-activity"></a>Origem da Web na atividade de cópia
Se você estiver copiando dados de uma tabela da Web, defina o **tipo de origem** da atividade de cópia para **websource**. Atualmente, quando a origem na atividade de cópia é do tipo **websource**, não há suporte para nenhuma propriedade adicional.

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [conector de tabela da Web](data-factory-web-table-connector.md#copy-activity-properties) .

## <a name="compute-environments"></a>AMBIENTES DE COMPUTAÇÃO
A tabela a seguir lista os ambientes de computação com suporte pelo Data Factory e as atividades de transformação que podem ser executados neles. Clique no link para a computação em que você está interessado para ver os esquemas JSON para o serviço vinculado vinculá-lo a um data factory.

| Ambiente de computação | Atividades |
| --- | --- |
| [Cluster Hdinsight sob demanda](#on-demand-azure-hdinsight-cluster) ou [seu próprio cluster hdinsight](#existing-azure-hdinsight-cluster) |[Atividade personalizada do .net](#net-custom-activity), [atividade do hive](#hdinsight-hive-activity), [atividade Pig](#hdinsight-pig-activity), [atividade MapReduce](#hdinsight-mapreduce-activity), atividade de streaming do Hadoop, [atividade do Spark](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[Atividade personalizada do .NET](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Machine Learning atividade de execução em lote](#machine-learning-batch-execution-activity), [Machine Learning atividade atualizar recurso](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Banco de dados SQL do Azure](#azure-sql-database), [SQL data warehouse do Azure](#azure-sql-data-warehouse), [SQL Server](#sql-server-stored-procedure) |[Procedimento Armazenado](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Cluster HDInsight do Azure sob demanda
O serviço de Azure Data Factory pode criar automaticamente um cluster HDInsight sob demanda baseado em Windows/Linux para processar dados. O cluster é criado na mesma região que a conta de armazenamento (Propriedade linkedServiceName no JSON) associada ao cluster. Você pode executar as seguintes atividades de transformação neste serviço vinculado: [atividade personalizada do .net](#net-custom-activity), atividade do [Hive](#hdinsight-hive-activity), [atividade de Pig](#hdinsight-pig-activity), [atividade de MapReduce](#hdinsight-mapreduce-activity), atividade de streaming do Hadoop, atividade do [Spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Serviço ligado
A tabela a seguir fornece descrições para as propriedades usadas na definição de JSON do Azure de um serviço vinculado do HDInsight sob demanda.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como **HDInsightOnDemand**. |Sim |
| clusterSize |Número de nós de trabalho/dados no cluster. O cluster HDInsight é criado com 2 nós de cabeçalho junto com o número de nós de trabalho que você especifica para essa propriedade. Os nós são de tamanho Standard_D3 com 4 núcleos, portanto, um cluster de quatro nós de trabalho usa 24 núcleos (4\*4 = 16 núcleos para nós de trabalho, mais 2\*4 = 8 núcleos para nós de cabeçalho). Consulte [Criar clusters Hadoop baseados em Linux no HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) para obter detalhes sobre a camada de Standard_D3. |Sim |
| TimeToLive |O tempo ocioso permitido para o cluster HDInsight sob demanda. Especifica por quanto tempo o cluster HDInsight sob demanda permanece ativo após a conclusão de uma atividade de execução se não houver nenhum outro trabalho ativo no cluster.<br/><br/>Por exemplo, se uma execução de atividade demorar 6 minutos e TimeToLive for definido como 5 minutos, o cluster permanecerá ativo por 5 minutos após os 6 minutos de processamento da execução da atividade. Se outra execução de atividade for executada com a janela de 6 minutos, ela será processada pelo mesmo cluster.<br/><br/>A criação de um cluster HDInsight sob demanda é uma operação cara (pode levar algum tempo), portanto, use essa configuração conforme necessário para melhorar o desempenho de um data factory reutilizando um cluster HDInsight sob demanda.<br/><br/>Se você definir o valor TimeToLive como 0, o cluster será excluído assim que a atividade for executada no processo. Por outro lado, se você definir um valor alto, o cluster poderá permanecer ocioso desnecessariamente, resultando em altos custos. Portanto, é importante que você defina o valor apropriado com base em suas necessidades.<br/><br/>Vários pipelines podem compartilhar a mesma instância do cluster HDInsight sob demanda se o valor da Propriedade TimeToLive estiver definido adequadamente |Sim |
| versão |Versão do cluster HDInsight. Para obter detalhes, consulte [versões do HDInsight com suporte no Azure data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Não |
| linkedServiceName |O serviço vinculado do armazenamento do Azure a ser usado pelo cluster sob demanda para armazenar e processar dados. <p>No momento, você não pode criar um cluster HDInsight sob demanda que usa um Azure Data Lake Store como armazenamento. Se você quiser armazenar os dados de resultado do processamento do HDInsight em um Azure Data Lake Store, use uma atividade de cópia para copiar os dados do armazenamento de BLOBs do Azure para o Azure Data Lake Store.</p>  | Sim |
| additionalLinkedServiceNames |Especifica contas de armazenamento adicionais para o serviço vinculado do HDInsight para que o serviço de Data Factory possa registrá-las em seu nome. |Não |
| osType |Tipo de sistema operacional. Os valores permitidos são: Windows (padrão) e Linux |Não |
| hcatalogLinkedServiceName |O nome do serviço vinculado do SQL do Azure que aponta para o banco de dados HCatalog. O cluster HDInsight sob demanda é criado usando o banco de dados SQL do Azure como o metastore. |Não |

### <a name="json-example"></a>Exemplo de JSON
O JSON a seguir define um serviço vinculado do HDInsight sob demanda baseado em Linux. O serviço de Data Factory cria automaticamente um cluster HDInsight **baseado em Linux** ao processar uma fatia de dados.

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Serviços vinculados de computação](data-factory-compute-linked-services.md) .

## <a name="existing-azure-hdinsight-cluster"></a>Cluster do Azure HDInsight existente
Você pode criar um serviço vinculado do Azure HDInsight para registrar seu próprio cluster HDInsight com Data Factory. Você pode executar as seguintes atividades de transformação de dados neste serviço vinculado: [atividade personalizada do .net](#net-custom-activity), atividade do [Hive](#hdinsight-hive-activity), [atividade de Pig](#hdinsight-pig-activity), atividade de [MapReduce](#hdinsight-mapreduce-activity), atividade de streaming do Hadoop, [atividade do Spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Serviço ligado
A tabela a seguir fornece descrições para as propriedades usadas na definição de JSON do Azure de um serviço vinculado do Azure HDInsight.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como **HDInsight**. |Sim |
| clusterUri |O URI do cluster HDInsight. |Sim |
| o nome de utilizador |Especifique o nome do usuário a ser usado para se conectar a um cluster HDInsight existente. |Sim |
| palavra-passe |Especifique a senha para a conta de usuário. |Sim |
| linkedServiceName | Nome do serviço vinculado do armazenamento do Azure que se refere ao armazenamento de BLOBs do Azure usado pelo cluster HDInsight. <p>No momento, não é possível especificar um serviço vinculado Azure Data Lake Store para essa propriedade. Você pode acessar dados na Azure Data Lake Store de scripts Hive/Pig se o cluster HDInsight tiver acesso ao Data Lake Store. </p>  |Sim |

Para obter as versões de clusters HDInsight com suporte, consulte [versões do hdinsight com suporte](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

#### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
Você pode criar um serviço vinculado do lote do Azure para registrar um pool do lote de VMs (máquinas virtuais) com um data factory. Você pode executar atividades personalizadas do .NET usando o lote do Azure ou o Azure HDInsight. Você pode executar uma [atividade personalizada do .net](#net-custom-activity) nesse serviço vinculado.

### <a name="linked-service"></a>Serviço ligado
A tabela a seguir fornece descrições para as propriedades usadas na definição de JSON do Azure de um serviço vinculado do lote do Azure.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como **AzureBatch**. |Sim |
| accountName |Nome da conta do lote do Azure. |Sim |
| accessKey |Chave de acesso para a conta do lote do Azure. |Sim |
| poolName |Nome do pool de máquinas virtuais. |Sim |
| linkedServiceName |Nome do serviço vinculado do armazenamento do Azure associado a este serviço vinculado do lote do Azure. Esse serviço vinculado é usado para arquivos de preparação necessários para executar a atividade e armazenar os logs de execução da atividade. |Sim |


#### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning"></a>Azure Machine Learning
Você cria um serviço vinculado Azure Machine Learning para registrar um ponto de extremidade de Pontuação Machine Learning lote com um data factory. Duas atividades de transformação de dados que podem ser executadas nesse serviço vinculado: [Machine Learning atividade de execução em lote](#machine-learning-batch-execution-activity), [Machine Learning atividade atualizar recurso](#machine-learning-update-resource-activity).

### <a name="linked-service"></a>Serviço ligado
A tabela a seguir fornece descrições para as propriedades usadas na definição de JSON do Azure de um serviço vinculado Azure Machine Learning.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Tipo |A propriedade Type deve ser definida como: **AzureML**. |Sim |
| mlEndpoint |A URL de Pontuação do lote. |Sim |
| apiKey |A API do modelo do espaço de trabalho publicado. |Sim |

#### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Você cria um serviço vinculado **Azure data Lake Analytics** para vincular um serviço de computação Azure data Lake Analytics a um data Factory do Azure antes de usar a [atividade data Lake Analytics U-SQL](data-factory-usql-activity.md) em um pipeline.

### <a name="linked-service"></a>Serviço ligado

A tabela a seguir fornece descrições para as propriedades usadas na definição de JSON de um Azure Data Lake Analytics serviço vinculado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Tipo |A propriedade Type deve ser definida como: **AzureDataLakeAnalytics**. |Sim |
| accountName |Nome da conta de Azure Data Lake Analytics. |Sim |
| dataLakeAnalyticsUri |URI de Azure Data Lake Analytics. |Não |
| nesse |O código de autorização é recuperado automaticamente depois de clicar no botão **autorizar** no Editor de data Factory e concluir o logon do OAuth. |Sim |
| subscriptionId |ID da assinatura do Azure |Não (se não for especificado, a assinatura do data factory será usada). |
| resourceGroupName |Nome do grupo de recursos do Azure |Não (se não for especificado, o grupo de recursos do data factory será usado). |
| sessionId |ID da sessão da sessão de autorização do OAuth. Cada ID de sessão é exclusiva e só pode ser usada uma vez. Quando você usa o editor de Data Factory, essa ID é gerada automaticamente. |Sim |


#### <a name="json-example"></a>Exemplo de JSON
O exemplo a seguir fornece a definição de JSON para um Azure Data Lake Analytics serviço vinculado.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="sql-server-stored-procedure"></a>SQL Server procedimento armazenado

Você cria um SQL Server serviço vinculado e o usa com a [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline de data Factory.

### <a name="linked-service"></a>Serviço ligado
Você cria um serviço vinculado do tipo **OnPremisesSqlServer** para vincular um banco de dados SQL Server local a um data Factory. A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do SQL Server local.

A tabela a seguir fornece a descrição para elementos JSON específicos para SQL Server serviço vinculado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **OnPremisesSqlServer**. |Sim |
| connectionString |Especifique as informações de connectionString necessárias para se conectar ao banco de dados local SQL Server usando a autenticação SQL ou a autenticação do Windows. |Sim |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao banco de dados SQL Server local. |Sim |
| o nome de utilizador |Especifique o nome de usuário se você estiver usando a autenticação do Windows. Exemplo: **domainname\\username**. |Não |
| palavra-passe |Especifique a senha para a conta de usuário especificada para o nome do usuário. |Não |

Você pode criptografar credenciais usando o cmdlet **New-AzDataFactoryEncryptValue** e usá-las na cadeia de conexão, conforme mostrado no exemplo a seguir (propriedade**EncryptedCredential** ):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Exemplo: JSON para usar a autenticação do SQL

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Exemplo: JSON para usar a autenticação do Windows

Se o nome de usuário e a senha forem especificados, o gateway os usará para representar a conta de usuário especificada para se conectar ao banco de dados SQL Server local. Caso contrário, o gateway se conecta ao SQL Server diretamente com o contexto de segurança do gateway (sua conta de inicialização).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [conector SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) .

## <a name="data-transformation-activities"></a>ATIVIDADES DE TRANSFORMAÇÃO DE DADOS

Atividade | Descrição
-------- | -----------
[Atividade do hive do HDInsight](#hdinsight-hive-activity) | A atividade do hive do HDInsight em um pipeline Data Factory executa consultas de Hive em seu próprio cluster do HDInsight baseado em Windows/Linux sob demanda.
[Atividade de Pig do HDInsight](#hdinsight-pig-activity) | A atividade Pig do HDInsight em um pipeline Data Factory executa consultas Pig em seu próprio cluster do HDInsight baseado em Windows/Linux sob demanda.
[Atividade MapReduce do HDInsight](#hdinsight-mapreduce-activity) | A atividade de MapReduce do HDInsight em um pipeline de Data Factory executa programas MapReduce em seu próprio cluster do HDInsight baseado em Windows/Linux sob demanda.
[Atividade Streaming do HDInsight](#hdinsight-streaming-activity) | A atividade de streaming do HDInsight em um pipeline Data Factory executa programas de streaming do Hadoop em seu próprio cluster do HDInsight baseado em Windows/Linux sob demanda.
[Atividade do HDInsight Spark](#hdinsight-spark-activity) | A atividade do HDInsight Spark em um pipeline Data Factory executa programas do Spark em seu próprio cluster HDInsight.
[Atividade de Execução em Lote do Machine Learning](#machine-learning-batch-execution-activity) | Azure Data Factory permite que você crie facilmente pipelines que usam um serviço Web publicado Azure Machine Learning para análise preditiva. Usando a atividade de execução em lote em um pipeline Azure Data Factory, você pode invocar um serviço Web Machine Learning para fazer previsões sobre os dados no lote.
[Atividade de Recursos de Atualização de Machine Learning](#machine-learning-update-resource-activity) | Com o passar do tempo, os modelos de previsão no Machine Learning a Pontuação dos experimentos precisam ser retreinados usando novos conjuntos de dados de entrada. Depois de concluir o retreinamento, você deseja atualizar o serviço Web de pontuação com o modelo de Machine Learning retreinado. Você pode usar a atividade atualizar recurso para atualizar o serviço Web com o modelo treinado recentemente.
[Atividade de Procedimento Armazenado](#stored-procedure-activity) | Você pode usar a atividade de procedimento armazenado em um pipeline Data Factory para invocar um procedimento armazenado em um dos seguintes repositórios de dados: banco de dados SQL do Azure, Azure SQL Data Warehouse, SQL Server banco de dado em sua empresa ou em uma VM do Azure.
[Data Lake Analytics atividade U-SQL](#data-lake-analytics-u-sql-activity) | Data Lake Analytics atividade U-SQL executa um script U-SQL em um cluster Azure Data Lake Analytics.
[Atividade personalizada do .NET](#net-custom-activity) | Se você precisar transformar dados de uma maneira que não seja suportada pelo Data Factory, poderá criar uma atividade personalizada com sua própria lógica de processamento de dados e usar a atividade no pipeline. Você pode configurar a atividade personalizada do .NET para ser executada usando um serviço de lote do Azure ou um cluster do Azure HDInsight.


## <a name="hdinsight-hive-activity"></a>Atividade Hive do HDInsight
Você pode especificar as propriedades a seguir em uma definição de JSON de atividade de Hive. A propriedade Type para a atividade deve ser: **HDInsightHive**. Você deve criar um serviço vinculado do HDInsight primeiro e especificar o nome dele como um valor para a propriedade **linkedServiceName** . As propriedades a seguir têm suporte na seção **typeproperties** quando você define o tipo de atividade para HDInsightHive:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| . |Especificar o script do hive embutido |Não |
| caminho do script |Armazene o script do hive em um armazenamento de BLOBs do Azure e forneça o caminho para o arquivo. Use a propriedade ' script ' ou ' scriptPath '. Ambos não podem ser usados juntos. O nome do arquivo diferencia maiúsculas de minúsculas. |Não |
| autor |Especifique parâmetros como pares de chave/valor para referência no script do hive usando ' hiveconf ' |Não |

Essas propriedades de tipo são específicas para a atividade do hive. Outras propriedades (fora da seção typeproperties) têm suporte para todas as atividades.

### <a name="json-example"></a>Exemplo de JSON
O JSON a seguir define uma atividade de hive do HDInsight em um pipeline.

```json
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

Para obter mais informações, consulte o artigo [atividade do hive](data-factory-hive-activity.md) .

## <a name="hdinsight-pig-activity"></a>Atividade Pig do HDInsight
Você pode especificar as propriedades a seguir em uma definição de JSON de atividade Pig. A propriedade Type para a atividade deve ser: **HDInsightPig**. Você deve criar um serviço vinculado do HDInsight primeiro e especificar o nome dele como um valor para a propriedade **linkedServiceName** . As propriedades a seguir têm suporte na seção **typeproperties** quando você define o tipo de atividade para HDInsightPig:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| . |Especificar o script Pig embutido |Não |
| caminho do script |Armazene o script Pig em um armazenamento de BLOBs do Azure e forneça o caminho para o arquivo. Use a propriedade ' script ' ou ' scriptPath '. Ambos não podem ser usados juntos. O nome do arquivo diferencia maiúsculas de minúsculas. |Não |
| autor |Especificar parâmetros como pares de chave/valor para referência no script Pig |Não |

Essas propriedades de tipo são específicas para a atividade Pig. Outras propriedades (fora da seção typeproperties) têm suporte para todas as atividades.

### <a name="json-example"></a>Exemplo de JSON

```json
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

Para obter mais informações, consulte o artigo atividade de Pig.

## <a name="hdinsight-mapreduce-activity"></a>Atividade MapReduce do HDInsight
Você pode especificar as propriedades a seguir em uma definição de JSON da atividade MapReduce. A propriedade Type para a atividade deve ser: **HDInsightMapReduce**. Você deve criar um serviço vinculado do HDInsight primeiro e especificar o nome dele como um valor para a propriedade **linkedServiceName** . As propriedades a seguir têm suporte na seção **typeproperties** quando você define o tipo de atividade para HDInsightMapReduce:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| jarLinkedService | Nome do serviço vinculado para o armazenamento do Azure que contém o arquivo JAR. | Sim |
| jarFilePath | Caminho para o arquivo JAR no armazenamento do Azure. | Sim |
| className | Nome da classe principal no arquivo JAR. | Sim |
| argumentos | Uma lista de argumentos separados por vírgulas para o programa MapReduce. Em tempo de execução, você verá alguns argumentos extras (por exemplo: MapReduce. Job. Tags) da estrutura MapReduce. Para diferenciar seus argumentos com os argumentos MapReduce, considere o uso de Option e Value como argumentos, conforme mostrado no exemplo a seguir (-s,--Input,--output etc., são opções imediatamente seguidas por seus valores) | Não |

### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [atividade do MapReduce](data-factory-map-reduce.md) .

## <a name="hdinsight-streaming-activity"></a>Atividade Streaming do HDInsight
Você pode especificar as propriedades a seguir em uma definição de JSON de atividade de streaming do Hadoop. A propriedade Type para a atividade deve ser: **HDInsightStreaming**. Você deve criar um serviço vinculado do HDInsight primeiro e especificar o nome dele como um valor para a propriedade **linkedServiceName** . As propriedades a seguir têm suporte na seção **typeproperties** quando você define o tipo de atividade para HDInsightStreaming:

| Propriedade | Descrição |
| --- | --- |
| mapea | Nome do executável do mapeador. No exemplo, Cat. exe é o executável do mapeador.|
| redutor | Nome do executável redutor. No exemplo, WC. exe é o executável do redutor. |
| entrada | Arquivo de entrada (incluindo o local) para o mapeador. No exemplo: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"`: adfsample é o contêiner de BLOB, example/data/Gutenberg é a pasta e DaVinci. txt é o blob. |
| saída | Arquivo de saída (incluindo o local) para o redutor. A saída do trabalho de streaming do Hadoop é gravada no local especificado para essa propriedade. |
| filePaths | Caminhos para os executáveis do mapeador e do redutor. No exemplo: "adfsample/example/apps/WC. exe", adfsample é o contêiner de BLOB, example/apps é a pasta e WC. exe é o executável. |
| fileLinkedService | Serviço vinculado do armazenamento do Azure que representa o armazenamento do Azure que contém os arquivos especificados na seção filePaths. |
| argumentos | Uma lista de argumentos separados por vírgulas para o programa MapReduce. Em tempo de execução, você verá alguns argumentos extras (por exemplo: MapReduce. Job. Tags) da estrutura MapReduce. Para diferenciar seus argumentos com os argumentos MapReduce, considere o uso de Option e Value como argumentos, conforme mostrado no exemplo a seguir (-s,--Input,--output etc., são opções imediatamente seguidas por seus valores) |
| getDebugInfo | Um elemento opcional. Quando definido como falha, os logs são baixados apenas em caso de falha. Quando ele é definido como todos, os logs são sempre baixados, independentemente do status de execução. |

> [!NOTE]
> Você deve especificar um conjunto de uma saída para a atividade de streaming do Hadoop para a propriedade **Outputs** . Esse conjunto de e pode ser apenas um conjunto de uma fictício que é necessário para direcionar a agenda do pipeline (por hora, diariamente, etc.). Se a atividade não receber uma entrada, você poderá ignorar a especificação de um conjunto de dados de entrada para a atividade da propriedade **entradas** .

## <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [atividade de streaming do Hadoop](data-factory-hadoop-streaming-activity.md) .

## <a name="hdinsight-spark-activity"></a>Atividade do HDInsight Spark
Você pode especificar as propriedades a seguir em uma definição de JSON da atividade do Spark. A propriedade Type para a atividade deve ser: **HDInsightSpark**. Você deve criar um serviço vinculado do HDInsight primeiro e especificar o nome dele como um valor para a propriedade **linkedServiceName** . As propriedades a seguir têm suporte na seção **typeproperties** quando você define o tipo de atividade para HDInsightSpark:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| rootPath | O contêiner de blob do Azure e a pasta que contém o arquivo Spark. O nome do arquivo diferencia maiúsculas de minúsculas. | Sim |
| entryFilePath | Caminho relativo para a pasta raiz do código/pacote do Spark. | Sim |
| className | Classe principal Java/Spark do aplicativo | Não |
| argumentos | Uma lista de argumentos de linha de comando para o programa Spark. | Não |
| proxyUser | A conta de usuário a ser representada para executar o programa Spark | Não |
| sparkConfig | Propriedades de configuração do Spark. | Não |
| getDebugInfo | Especifica quando os arquivos de log do Spark são copiados para o armazenamento do Azure usado pelo cluster HDInsight (ou) especificado por sparkJobLinkedService. Valores permitidos: nenhum, sempre ou falha. Valor padrão: nenhum. | Não |
| sparkJobLinkedService | O serviço vinculado do armazenamento do Azure que contém o arquivo de trabalho do Spark, as dependências e os logs.  Se você não especificar um valor para essa propriedade, o armazenamento associado ao cluster HDInsight será usado. | Não |

### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Tenha em atenção os seguintes pontos:

- A propriedade **Type** é definida como **HDInsightSpark**.
- O **rootPath** é definido como **adfspark\\pyFiles** , em que adfspark é o contêiner de blob do Azure e pyFiles é a pasta Ok nesse contêiner. Neste exemplo, o armazenamento de BLOBs do Azure é aquele associado ao cluster do Spark. Você pode carregar o arquivo para um armazenamento do Azure diferente. Se você fizer isso, crie um serviço vinculado do armazenamento do Azure para vincular essa conta de armazenamento ao data factory. Em seguida, especifique o nome do serviço vinculado como um valor para a propriedade **sparkJobLinkedService** . Consulte Propriedades da atividade do Spark para obter detalhes sobre essa propriedade e outras propriedades com suporte na atividade do Spark.
- O **entryFilePath** é definido como **Test.py**, que é o arquivo Python.
- A propriedade **getDebugInfo** é definida como **Always**, o que significa que os arquivos de log sempre são gerados (êxito ou falha).

    > [!IMPORTANT]
    > É recomendável que você não defina essa propriedade como sempre em um ambiente de produção, a menos que esteja solucionando um problema.
- A seção de **saídas** tem um conjunto de uma saída. Você deve especificar um conjunto de uma saída mesmo se o programa Spark não produzir nenhuma saída. O conjunto de resultados de saída orienta o agendamento para o pipeline (por hora, diariamente, etc.).

Para obter mais informações sobre a atividade, consulte o artigo [atividade do Spark](data-factory-spark.md) .

## <a name="machine-learning-batch-execution-activity"></a>Atividade de Execução em Lote do Machine Learning
Você pode especificar as propriedades a seguir em uma definição de JSON de atividade de execução de lote do Azure Machine Learning Studio. A propriedade Type para a atividade deve ser: **AzureMLBatchExecution**. Você deve criar um Azure Machine Learning serviço vinculado primeiro e especificar o nome dele como um valor para a propriedade **linkedServiceName** . As propriedades a seguir têm suporte na seção **typeproperties** quando você define o tipo de atividade para AzureMLBatchExecution:

Propriedade | Descrição | Necessário
-------- | ----------- | --------
webServiceInput | O conjunto de dados a ser passado como uma entrada para o serviço Web Azure Machine Learning Studio. Esse conjunto de informações também deve ser incluído nas entradas para a atividade. |Use webServiceInput ou webServiceInputs. |
webServiceInputs | Especifique os conjuntos de valores a serem passados como entradas para o serviço Web Azure Machine Learning Studio. Se o serviço Web usa várias entradas, use a propriedade webServiceInputs em vez de usar a propriedade webServiceInput. Os conjuntos de valores que são referenciados pelo **webServiceInputs** também devem ser incluídos nas **entradas**da atividade. | Use webServiceInput ou webServiceInputs. |
webServiceOutputs | Os conjuntos de resultados atribuídos como saídas para o serviço Web do Azure Machine Learning Studio. O serviço Web retorna dados de saída neste DataSet. | Sim |
globalParameters | Especifique valores para os parâmetros de serviço Web nesta seção. | Não |

### <a name="json-example"></a>Exemplo de JSON
Neste exemplo, a atividade tem o conjunto de dados **MLSqlInput** como entrada e **MLSqlOutput** como a saída. O **MLSqlInput** é passado como uma entrada para o serviço Web usando a propriedade JSON **webServiceInput** . O **MLSqlOutput** é passado como uma saída para o serviço Web usando a propriedade JSON **webServiceOutputs** .

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

No exemplo de JSON, o serviço Web Azure Machine Learning implantado usa um módulo leitor e gravador para ler/gravar dados de/para um banco de dado SQL do Azure. Esse serviço Web expõe os quatro parâmetros a seguir: nome do servidor de banco de dados, nome do banco de dados, nome da conta de usuário do servidor e senha da conta de usuário do servidor.

> [!NOTE]
> Somente as entradas e saídas da atividade AzureMLBatchExecution podem ser passadas como parâmetros para o serviço Web. Por exemplo, no trecho JSON acima, MLSqlInput é uma entrada para a atividade AzureMLBatchExecution, que é passada como uma entrada para o serviço Web por meio do parâmetro webServiceInput.

## <a name="machine-learning-update-resource-activity"></a>Atividade de Recursos de Atualização de Machine Learning
Você pode especificar as propriedades a seguir em uma definição de JSON de atividade de atualização do Azure Machine Learning Studio. A propriedade Type para a atividade deve ser: **AzureMLUpdateResource**. Você deve criar um Azure Machine Learning serviço vinculado primeiro e especificar o nome dele como um valor para a propriedade **linkedServiceName** . As propriedades a seguir têm suporte na seção **typeproperties** quando você define o tipo de atividade para AzureMLUpdateResource:

Propriedade | Descrição | Necessário
-------- | ----------- | --------
trainedModelName | Nome do modelo retreinado. | Sim |
trainedModelDatasetName | DataSet apontando para o arquivo iLearner retornado pela operação de novo treinamento. | Sim |

### <a name="json-example"></a>Exemplo de JSON
O pipeline tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução em lote do Azure Machine Learning Studio usa os dados de treinamento como entrada e produz um arquivo iLearner como uma saída. A atividade invoca o serviço Web de treinamento (teste de treinamento exposto como um serviço Web) com os dados de treinamento de entrada e recebe o arquivo ilearner do WebService. O placeholderBlob é apenas um conjunto de resultados de saída fictício exigido pelo serviço de Azure Data Factory para executar o pipeline.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U-SQL do Data Lake Analytics
Você pode especificar as propriedades a seguir em uma definição de JSON de atividade do U-SQL. A propriedade Type para a atividade deve ser: **DataLakeAnalyticsU-SQL**. Você deve criar um Azure Data Lake Analytics serviço vinculado e especificar o nome dele como um valor para a propriedade **linkedServiceName** . As propriedades a seguir têm suporte na seção **typeproperties** quando você define o tipo de atividade para DATALAKEANALYTICSU-SQL:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| scriptPath |Caminho para a pasta que contém o script U-SQL. O nome do arquivo diferencia maiúsculas de minúsculas. |Não (se você usar o script) |
| scriptLinkedService |Serviço vinculado que vincula o armazenamento que contém o script ao data factory |Não (se você usar o script) |
| . |Especifique o script embutido em vez de especificar scriptPath e scriptLinkedService. Por exemplo: "script": "criar teste de banco de dados". |Não (se você usar scriptPath e scriptLinkedService) |
| degreeOfParallelism |O número máximo de nós usados simultaneamente para executar o trabalho. |Não |
| prioridade |Determina quais trabalhos de todos os que estão na fila devem ser selecionados para serem executados primeiro. Quanto menor o número, maior a prioridade. |Não |
| parâmetros |Parâmetros para o script U-SQL |Não |

### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities":
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs":
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

Para obter mais informações, consulte [Data Lake Analytics atividade U-SQL](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Atividade de Procedimento Armazenado
Você pode especificar as propriedades a seguir em uma definição de JSON de atividade de procedimento armazenado. A propriedade Type para a atividade deve ser: **SqlServerStoredProcedure**. Você deve criar um dos seguintes serviços vinculados e especificar o nome do serviço vinculado como um valor para a propriedade **linkedServiceName** :

- SQL Server
- Base de Dados SQL do Azure
- Azure SQL Data Warehouse

As propriedades a seguir têm suporte na seção **typeproperties** quando você define o tipo de atividade para SqlServerStoredProcedure:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| storedProcedureName |Especifique o nome do procedimento armazenado no banco de dados SQL do Azure ou SQL Data Warehouse do Azure que é representado pelo serviço vinculado usado pela tabela de saída. |Sim |
| storedProcedureParameters |Especifique valores para parâmetros de procedimento armazenado. Se você precisar passar NULL para um parâmetro, use a sintaxe: "param1": NULL (todas as letras minúsculas). Consulte o exemplo a seguir para saber mais sobre como usar essa propriedade. |Não |

Se você especificar um conjunto de dados de entrada, ele deverá estar disponível (no status ' pronto ') para que a atividade de procedimento armazenado seja executada. O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como um parâmetro. Ele é usado apenas para verificar a dependência antes de iniciar a atividade de procedimento armazenado. Você deve especificar um conjunto de uma saída para uma atividade de procedimento armazenado.

O conjunto de resultados de saída especifica o **agendamento** para a atividade de procedimento armazenado (por hora, semanalmente, mensalmente, etc.). O conjunto de dados de saída deve usar um **serviço vinculado** que se refere a um banco de dados SQL do Azure ou a um SQL data warehouse do Azure ou a um banco de dados SQL Server no qual você deseja que o procedimento armazenado seja executado. O conjunto de resultados de saída pode servir como uma maneira de passar o resultado do procedimento armazenado para processamento subsequente por outra atividade ([atividades de encadeamento](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) no pipeline. No entanto, Data Factory não grava automaticamente a saída de um procedimento armazenado para esse conjunto de um. É o procedimento armazenado que grava em uma tabela SQL para a qual o conjunto de resultados de saída aponta. Em alguns casos, o conjunto de resultados de saída pode ser um conjunto de uma **fictício**, que é usado apenas para especificar o agendamento para executar a atividade de procedimento armazenado.

### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

Para obter mais informações, consulte o artigo [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) .

## <a name="net-custom-activity"></a>Atividade personalizada do .NET
Você pode especificar as propriedades a seguir em uma definição de JSON de atividade personalizada do .NET. A propriedade Type para a atividade deve ser: **DotNetActivity**. Você deve criar um serviço vinculado do Azure HDInsight ou um serviço vinculado do lote do Azure e especificar o nome do serviço vinculado como um valor para a propriedade **linkedServiceName** . As propriedades a seguir têm suporte na seção **typeproperties** quando você define o tipo de atividade para DotNetActivity:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| AssemblyName | Nome do assembly. No exemplo, é: **MyDotnetActivity. dll**. | Sim |
| Ponto |Nome da classe que implementa a interface IDotNetActivity. No exemplo, é: **MyDotNetActivityNS. MyDotNetActivity** , em que MyDotNetActivityNS é o namespace e MyDotNetActivity é a classe.  | Sim |
| PackageLinkedService | Nome do serviço vinculado do armazenamento do Azure que aponta para o armazenamento de BLOBs que contém o arquivo zip da atividade personalizada. No exemplo, é: **AzureStorageLinkedService**.| Sim |
| PackageFile | Nome do arquivo zip. No exemplo, é: **customactivitycontainer/MyDotNetActivity. zip**. | Sim |
| extendedProperties | Propriedades estendidas que você pode definir e passar para o código .NET. Neste exemplo, a variável **SliceStart** é definida como um valor com base na variável de sistema SliceStart. | Não |

### <a name="json-example"></a>Exemplo de JSON

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Para obter informações detalhadas, consulte [usar atividades personalizadas no artigo data Factory](data-factory-use-custom-activities.md) .

## <a name="next-steps"></a>Próximos Passos
Veja os tutoriais seguintes:

- [Tutorial: criar um pipeline com uma atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Tutorial: criar um pipeline com uma atividade de Hive](data-factory-build-your-first-pipeline.md)
