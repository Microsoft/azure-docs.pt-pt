---
title: Azure Data Factory - Referência de Scripting JSON
description: Fornece schemas JSON para entidades da Fábrica de Dados.
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
ms.openlocfilehash: 3492f917be8116d0eed0c7ec03ed8aa9ff506520
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80346585"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory - Referência de Scripting JSON
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory.


Este artigo fornece schemas jSON e exemplos para a definição de entidades da Fábrica de Dados Azure (pipeline, atividade, dataset e serviço ligado).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Pipeline
A estrutura de alto nível para uma definição de gasoduto é a seguinte:

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

A tabela seguinte descreve as propriedades dentro da definição JSON do gasoduto:

| Propriedade | Descrição | Necessário
-------- | ----------- | --------
| nome | Nome do pipeline. Especificar um nome que represente a ação que a atividade ou o gasoduto está configurado para fazer<br/><ul><li>Número máximo de carateres: 260</li><li>Deve começar com um número de\_letra, ou um sublinhado ( )</li><li>Seguir personagens não são permitidos: ".", "+", "?",, "/", "<", ">","%", "&","""""""""""\\</li></ul> |Sim |
| descrição |Texto descrevendo para que é utilizada a atividade ou o gasoduto | Não |
| atividades | Contém uma lista de atividades. | Sim |
| start |Inicie a data-data para o oleoduto. Deve estar no [formato ISO.](https://en.wikipedia.org/wiki/ISO_8601) Por exemplo: 2014-10-14T16:32:41. <br/><br/>É possível especificar uma hora local, por exemplo, uma hora EST. Aqui está um `2016-02-27T06:00:00**-05:00`exemplo: , que é 6 AM EST.<br/><br/>As propriedades de início e de extremidade em conjunto especificam o período ativo para o gasoduto. As fatias de saída só são produzidas neste período ativo. |Não<br/><br/>Se especificar um valor para a propriedade final, deve especificar valor para a propriedade inicial.<br/><br/>Os tempos de início e de fim podem ser vazios para criar um oleoduto. Deve especificar ambos os valores para definir um período ativo para o gasoduto funcionar. Se não especificar os tempos de início e de fim ao criar um pipeline, pode defini-los utilizando o cmdlet Set-AzDataFactoryPipelineActivePeriod mais tarde. |
| fim |Fim da data para o oleoduto. Se especificado deve estar no formato ISO. Por exemplo: 2014-10-14T17:32:41 <br/><br/>É possível especificar uma hora local, por exemplo, uma hora EST. Aqui está um `2016-02-27T06:00:00**-05:00`exemplo: , que é 6 AM EST.<br/><br/>Para executar o pipeline de forma indefinida, especifique 9999-09-09 como o valor da propriedade end. |Não <br/><br/>Se especificar um valor para a propriedade inicial, deve especificar valor para a propriedade final.<br/><br/>Consulte as notas para a propriedade **inicial.** |
| isPaused |Se for definido como verdadeiro, o gasoduto não funciona. Valor predefinido = falso. Pode utilizar esta propriedade para ativar ou desativar. |Não |
| pipelineMode |O método de agendamento corre para o oleoduto. Os valores permitidos são: agendados (padrão), uma vez.<br/><br/>«Programado» indica que o gasoduto funciona num determinado intervalo de tempo de acordo com o seu período ativo (início e fim). 'One time' indica que o gasoduto funciona apenas uma vez. Os gasodutos únicos uma vez criados não podem ser modificados/atualizados atualmente. Consulte o [oleoduto Onetime](data-factory-create-pipelines.md#onetime-pipeline) para obter mais informações sobre a definição de uma vez. |Não |
| expiração Tempo |Duração do tempo após a criação para a qual o gasoduto é válido e deve permanecer provisionado. Se não tiver quaisquer funcionações ativas, falhadas ou pendentes, o gasoduto é eliminado automaticamente assim que atingir o prazo de validade. |Não |


## <a name="activity"></a>Atividade
A estrutura de alto nível para uma atividade dentro de uma definição de gasoduto (elemento de atividade) é a seguinte:

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

A tabela seguinte descreve as propriedades dentro da definição JSON de atividade:

| Etiqueta | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da atividade. Especifique um nome que represente a ação que a atividade está configurada para fazer<br/><ul><li>Número máximo de carateres: 260</li><li>Deve começar com um número de\_letra, ou um sublinhado ( )</li><li>Seguir personagens não são permitidos: ".", "+", "?",, "/", "<", ">","%", "&","""""""""""\\</li></ul> |Sim |
| descrição |Texto descrevendo para que a atividade é usada. |Não |
| tipo |Especifica o tipo de atividade. Consulte as secções de DATA [STORES](#data-stores) e [DATA TRANSFORMATION ACTIVITIES](#data-transformation-activities) para diferentes tipos de atividades. |Sim |
| inputs |Tabelas de entrada utilizadas pela atividade<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Não para as atividades hDInsightStreaming e SqlServerStoredProcedure <br/> <br/> Sim, para todos os outros. |
| saídas |Tabelas de saída utilizadas pela atividade.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Sim |
| linkedServiceName |Nome do serviço ligado utilizado pela atividade. <br/><br/>Uma atividade pode exigir que especifique o serviço ligado que liga ao ambiente de computação necessário. |Sim para atividades HDInsight, atividades de Machine Learning Azure e Atividade de Procedimento Armazenado. <br/><br/>Não para todas as outras. |
| typeProperties |As propriedades na secção tipoPropriedades dependem do tipo da atividade. |Não |
| política |Políticas que afetam o comportamento de runtime da atividade. Se não for especificado, são utilizadas políticas predefinidas. |Não |
| scheduler |A propriedade "scheduler" é utilizada para definir o agendamento desejado para a atividade. As suas subpropriedades são as mesmas que as da propriedade de disponibilidade num conjunto de [dados.](data-factory-create-datasets.md#dataset-availability) |Não |

### <a name="policies"></a>Políticas
As políticas afetam o comportamento de tempo de execução de uma atividade, especificamente quando a fatia de uma mesa é processada. A tabela seguinte fornece os detalhes.

| Propriedade | Valores permitidos | Valor Predefinido | Descrição |
| --- | --- | --- | --- |
| conmoeda |Número inteiro <br/><br/>Valor máximo: 10 |1 |Número de execuções simultâneas da atividade.<br/><br/>Determina o número de execuções paralelas de atividade que podem acontecer em diferentes fatias. Por exemplo, se uma atividade precisa passar por um grande conjunto de dados disponíveis, ter um valor de condivisa maior acelera o processamento de dados. |
| execuçãoPriorityOrder |Mais recente<br/><br/>O primeiro mais velho |O primeiro mais velho |Determina a encomenda de fatias de dados que estão a ser processadas.<br/><br/>Por exemplo, se tiver 2 fatias (uma a acontecer às 16h, e outra às 17h), e ambas estão pendentes de execução. Se definir a execuçãoPriorityOrder para ser NewestFirst, a fatia às 17:00 é processada primeiro. Da mesma forma, se definir a execuçãoPriorityORder como O FIrst mais antigo, então a fatia às 16:00 é processada. |
| retry |Número inteiro<br/><br/>O valor máximo pode ser 10 |0 |O número de repetições antes do processamento de dados para a fatia é marcado como Falha. A execução da atividade para uma fatia de dados é novamente experimentada até à contagem de retry especificada. A reprovação é feita o mais rápido possível após o fracasso. |
| tempo limite |TimeSpan |00:00:00 |Intervalo para a atividade. Exemplo: 00:10:00 (implica tempo de 10 minutos)<br/><br/>Se um valor não for especificado ou for 0, o tempo limite é infinito.<br/><br/>Se o tempo de processamento de dados numa fatia exceder o valor de tempo limite, é cancelado e o sistema tenta voltar a tentar o processamento. O número de tentativas depende da propriedade de retry. Quando o tempo de tempo ocorre, o estado é definido para TimedOut. |
| atraso |TimeSpan |00:00:00 |Especifique o atraso antes do processamento de dados da fatia.<br/><br/>A execução da atividade para uma fatia de dados é iniciada após o atraso ter passado o tempo de execução esperado.<br/><br/>Exemplo: 00:10:00 (implica atraso de 10 minutos) |
| longRetry |Número inteiro<br/><br/>Valor máximo: 10 |1 |O número de tentativas de retry longas antes da execução da fatia é falhado.<br/><br/>as tentativas de longRetry são espaçadas por longRetryInterval. Por isso, se precisar especificar um tempo entre tentativas de retry, use longRetry. Se forespecificado tanto o Retry como o longRetry, cada tentativa de longa-retry inclui tentativas de retry e o número máximo de tentativas é Retry * longRetry.<br/><br/>Por exemplo, se tivermos as seguintes definições na política de atividade:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Assuma que só há uma fatia para executar (o estado é espera) e a execução da atividade falha sempre. Inicialmente haveria 3 tentativas de execução consecutivas. Após cada tentativa, o estado da fatia seria Retry. Depois de terminarem as primeiras 3 tentativas, o estado da fatia seria LongRetry.<br/><br/>Após uma hora (isto é, valor de LongRetryInteval), haveria outro conjunto de 3 tentativas de execução consecutivas. Depois disso, o estado da fatia seria falhado e não se tentariam mais tentativas de tentativa. Daí que no total foram feitas 6 tentativas.<br/><br/>Se qualquer execução for bem sucedida, o estado da fatia estará pronto e não se tentarem mais tentativas de tentativas.<br/><br/>O longRetry pode ser utilizado em situações em que os dados dependentes chegam a tempos não determinísticos ou o ambiente global é excêntrico sob o qual ocorre o tratamento de dados. Nesses casos, fazer repetições um após o outro pode não ajudar e fazê-lo após um intervalo de tempo resulta na saída desejada.<br/><br/>Palavra de precaução: não detete valores elevados para longRetry ou longRetryInterval. Tipicamente, valores mais elevados implicam outras questões sistémicas. |
| longRetryInterval |TimeSpan |00:00:00 |O atraso entre longas tentativas de retry |

### <a name="typeproperties-section"></a>secção typeProperties
A secção typeProperties é diferente para cada atividade. As atividades de transformação têm propriedades do tipo. Consulte a secção [DE ATIVIDADES](#data-transformation-activities) DE TRANSFORMAÇÃO DE DADOS neste artigo para amostras JSON que definem as atividades de transformação num oleoduto.

**A atividade de cópia** tem duas subsecções na secção typeProperties: **origem** e **pia**. Consulte a secção [DATA STORES](#data-stores) neste artigo para amostras JSON que mostrem como usar um armazenamento de dados como fonte e/ou afundar.

### <a name="sample-copy-pipeline"></a>Pipeline de cópia de exemplo
No pipeline de exemplo seguinte, existe uma atividade do tipo **Cópia** na secção **activities**. Nesta amostra, a [atividade copy](data-factory-data-movement-activities.md) copia dados de um armazenamento Azure Blob para uma base de dados Azure SQL.

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

Consulte a secção [DATA STORES](#data-stores) neste artigo para amostras JSON que mostrem como usar um armazenamento de dados como fonte e/ou afundar.

Para uma passagem completa pela criação deste pipeline, consulte [Tutorial: Copiar dados do Armazenamento Blob para a Base de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

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
* A secção **de definição** é usada para especificar as definições de tempo de execução `${hiveconf:partitionedtable}`que são passadas para o script da colmeia como valores de configuração da Colmeia (por exemplo, `${hiveconf:inputtable}`).

Consulte a secção [DE ATIVIDADES](#data-transformation-activities) DE TRANSFORMAÇÃO DE DADOS neste artigo para amostras JSON que definem as atividades de transformação num oleoduto.

Para uma completa passagem pela criação deste pipeline, consulte [Tutorial: Construa o seu primeiro pipeline para processar dados utilizando o cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="linked-service"></a>Serviço ligado
A estrutura de alto nível para uma definição de serviço ligada é a seguinte:

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

A tabela seguinte descreve as propriedades dentro da definição JSON de atividade:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| nome | Nome do serviço ligado. | Sim |
| propriedades - tipo | Tipo de serviço ligado. Por exemplo: Armazenamento Azure, Base de Dados Azure SQL. |
| typeProperties | A secção typeProperties tem elementos diferentes para cada armazenamento de dados ou ambiente de computação. Consulte a secção de lojas de dados para todos os serviços ligados à loja de dados e ambientes de [computação](#compute-environments) para todos os serviços ligados à computação |

## <a name="dataset"></a>Conjunto de dados
Um conjunto de dados na Azure Data Factory é definido da seguinte forma:

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

A tabela seguinte descreve propriedades no JSON acima:

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| nome | Nome do conjunto de dados. Ver [Azure Data Factory - Regras de nomeação](data-factory-naming-rules.md) para regras de nomeação. |Sim |ND |
| tipo | Tipo de conjunto de dados. Especifique um dos tipos suportados pela Azure Data Factory (por exemplo: AzureBlob, AzureSqlTable). Consulte a secção [DATA STORES](#data-stores) para todas as lojas de dados e tipos de conjuntos de dados suportados pela Data Factory. |
| estrutura | Esquema do conjunto de dados. Contém colunas, os seus tipos, etc. | Não |ND |
| typeProperties | Propriedades correspondentes ao tipo selecionado. Consulte a secção [DATA STORES](#data-stores) para obter tipos suportados e suas propriedades. |Sim |ND |
| externo | Bandeira booleana para especificar se um conjunto de dados é explicitamente produzido por um gasoduto de fábrica de dados ou não. |Não |false |
| disponibilidade | Define a janela de processamento ou o modelo de corte para a produção de conjuntode dados. Para mais detalhes sobre o modelo de corte de conjunto de dados, consulte o artigo [agendamento e execução.](data-factory-scheduling-and-execution.md) |Sim |ND |
| política |Define os critérios ou a condição que as fatias de conjunto de dados devem cumprir. <br/><br/>Para mais detalhes, consulte a secção Política de Dataset. |Não |ND |

Cada coluna na secção **estrutura** contém as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da coluna. |Sim |
| tipo |Tipo de dados da coluna.  |Não |
| cultura |Cultura baseada em .NET a ser utilizada quando `Datetime` o `Datetimeoffset`tipo é especificado e é do tipo .NET ou . A predefinição é `en-us`. |Não |
| formato |Cadeia de formato a utilizar quando o `Datetime` tipo `Datetimeoffset`é especificado e é do tipo .NET ou . |Não |

No exemplo seguinte, o conjunto de `slicetimestamp` `projectname`dados `pageviews` tem três colunas, e são de tipo: String, String e Decimal respectivamente.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

A tabela seguinte descreve propriedades que pode utilizar na secção **de disponibilidade:**

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| frequência |Especifica a unidade de tempo para a produção de fatias de conjunto de dados.<br/><br/><b>Frequência suportada</b>: Minuto, Hora, Dia, Semana, Mês |Sim |ND |
| intervalo |Especifica um multiplicador para a frequência<br/><br/>O "intervalo de frequência x" determina a frequência com que a fatia é produzida.<br/><br/>Se precisar que o conjunto de dados seja cortado de hora em hora, coloque <b>a Frequência</b> para <b>hora</b> <b>e</b> intervalo para <b>1</b>.<br/><br/><b>Nota:</b>Se especificar frequência como Minuto, recomendamos que detetete o intervalo para pelo menos 15 |Sim |ND |
| estilo |Especifica se a fatia deve ser produzida no início/fim do intervalo.<ul><li>Início do intervalo</li><li>Fim do intervalo</li></ul><br/><br/>Se a Frequência estiver definida para mês e o estilo estiver definido para EndOfInterval, a fatia é produzida no último dia do mês. Se o estilo estiver definido para o StartOfInterval, a fatia é produzida no primeiro dia do mês.<br/><br/>Se a frequência estiver definida para o Dia e o estilo estiver definido para EndOfInterval, a fatia é produzida na última hora do dia.<br/><br/>Se a frequência estiver definida para hora e o estilo estiver definido para EndOfInterval, a fatia é produzida no final da hora. Por exemplo, para uma fatia para o período das 13:00 às 14:00 horas, a fatia é produzida às 14:00. |Não |Fim do intervalo |
| âncoraDateTime |Define a posição absoluta no tempo utilizado pelo programador para calcular os limites da fatia de conjunto de dados. <br/><br/><b>Nota:</b>Se o AnchorDateTime tiver peças de data mais granulares do que a frequência, as peças mais granulares são ignoradas. <br/><br/>Por exemplo, se o <b>intervalo</b> for <b>de hora em hora</b> (frequência: hora e intervalo: 1) e o <b>AnchorDateTime</b> contiver <b>minutos e segundos,</b> então as partes dos <b>minutos e segundos</b> do AnchorDateTime são ignoradas. |Não |01/01/0001 |
| offset |Timepan pelo qual o início e o fim de todas as fatias de conjunto de dados são deslocados. <br/><br/><b>Nota:</b>Se forem especificados o âncoraDateTime e offset, o resultado é o turno combinado. |Não |ND |

A seguinte secção de disponibilidade especifica que o conjunto de dados de saída é produzido de hora em hora (ou) o conjunto de dados de entrada está disponível de hora em hora:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

A secção **de política** na definição de conjunto de dados define os critérios ou a condição que as fatias de conjunto de dados devem cumprir.

| Nome da Política | Descrição | Aplicado a | Necessário | Predefinição |
| --- | --- | --- | --- | --- |
| mínimoTamanhoMB |Valida que os dados de uma **bolha Azure** satisfazem os requisitos mínimos de tamanho (em megabytes). |Blob do Azure |Não |ND |
| mínimoS filas |Valida que os dados numa base de **dados Azure SQL** ou numa **tabela Azure** contenham o número mínimo de linhas. |<ul><li>Base de Dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |ND |

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

A menos que um conjunto de dados esteja a ser produzido pela Azure Data Factory, deve ser marcado como **externo**. Esta definição aplica-se geralmente às inputs da primeira atividade num gasoduto, a menos que estejam a ser utilizadas atividades ou correntes de gasodutos.

| Nome | Descrição | Necessário | Valor Predefinido |
| --- | --- | --- | --- |
| dadosAtraso |Hora de atrasar a verificação da disponibilidade dos dados externos para a fatia dada. Por exemplo, se os dados estiverem disponíveis de hora em hora, a verificação para ver os dados externos está disponível e a fatia correspondente está pronta pode ser adiada utilizando dadosAtraso.<br/><br/>Só se aplica ao presente.  Por exemplo, se for 1:00 PM agora e este valor for de 10 minutos, a validação começa às 13:10.<br/><br/>Esta definição não afeta fatias no passado (fatias com Slice End Time + dataDelay < Now) são processadas sem qualquer atraso.<br/><br/>O tempo superior às 23:59 horas `day.hours:minutes:seconds` precisa de ser especificado utilizando o formato. Por exemplo, para especificar 24 horas, não use 24:00:00; em vez disso, use 1.00:00:00. Se utilizar 24:00:00, é tratado como 24 dias (24.00:00:00). Durante 1 dia e 4 horas, especifique 1:04:00:00. |Não |0 |
| retryInterval |O tempo de espera entre um fracasso e a próxima tentativa de novo. Se uma tentativa falhar, a próxima tentativa é depois do intervalo de novo. <br/><br/>Se for 13:00 agora, começamos a primeira tentativa. Se a duração para completar a primeira verificação de validação for de 1 minuto e a operação falhar, a próxima tentativa é de 1:00 + 1 min (duração) + 1 min (intervalo de repetição) = 1:02 PM. <br/><br/>Para fatias no passado, não há atraso. A retentativa acontece imediatamente. |Não |00:01:00 (1 minuto) |
| retryTimeout |O tempo de paragem para cada tentativa de reprovação.<br/><br/>Se esta propriedade estiver definida para 10 minutos, a validação tem de ser concluída dentro de 10 minutos. Se demorar mais de 10 minutos a efetuar a validação, o tempo de retry é de saque.<br/><br/>Se todas as tentativas de validação forem, a fatia é marcada como TimedOut. |Não |00:10:00 (10 minutos) |
| máximoRetry |Número de vezes para verificar a disponibilidade dos dados externos. O valor máximo permitido é de 10. |Não |3 |


## <a name="data-stores"></a>LOJAS DE DADOS
A secção de [serviço seleções ligadas](#linked-service) forneceu descrições para elementos JSON que são comuns a todos os tipos de serviços ligados. Esta secção fornece detalhes sobre elementos JSON específicos para cada loja de dados.

A secção [Dataset](#dataset) forneceu descrições para elementos JSON que são comuns a todos os tipos de conjuntos de dados. Esta secção fornece detalhes sobre elementos JSON específicos para cada loja de dados.

A secção [de Atividade](#activity) forneceu descrições para elementos JSON que são comuns a todos os tipos de atividades. Esta secção fornece detalhes sobre elementos JSON específicos de cada loja de dados quando é usado como fonte/pia numa atividade de cópia.

Clique no link para a loja em que está interessado em ver os schemas JSON para serviço ligado, conjunto de dados e a fonte/pia para a atividade de cópia.

| Categoria | Arquivo de dados
|:--- |:--- |
| **Azure** |[Armazenamento de Blobs do Azure](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Base de Dados SQL do Azure](#azure-sql-database) |
| &nbsp; |[Azure SQL Data Warehouse](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Cognitive Search](#azure-cognitive-search) |
| &nbsp; |[Armazenamento de mesa azure](#azure-table-storage) |
| **Bases de Dados** |[Amazon Redshift](#amazon-redshift) |
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
Existem dois tipos de serviços ligados: serviço ligado ao Armazenamento Azure e serviço ligado ao Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
Para ligar a sua conta de armazenamento Azure a uma fábrica de dados utilizando a **chave da conta,** crie um serviço ligado ao Armazenamento Azure. Para definir um serviço ligado ao Armazenamento Azure, defina o **tipo** de serviço ligado ao **AzureStorage**. Em seguida, pode especificar as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| conexãoString |Especifique as informações necessárias para ligar ao armazenamento Azure para a propriedade connectionString. |Sim |

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

#### <a name="azure-storage-sas-linked-service"></a>Serviço Ligado ao Armazenamento Azure SAS
O serviço ligado ao Azure Storage SAS permite-lhe ligar uma Conta de Armazenamento Azure a uma fábrica de dados Azure utilizando uma Assinatura de Acesso Partilhado (SAS). Fornece à fábrica de dados um acesso restrito/limite de tempo a todos/recursos específicos (blob/container) no armazenamento. Para ligar a sua conta de armazenamento Azure a uma fábrica de dados utilizando a Signature de Acesso Partilhado, crie um serviço ligado ao Azure Storage SAS. Para definir um serviço ligado ao Azure Storage SAS, defina o **tipo** de serviço ligado ao **AzureStorageSas**. Em seguida, pode especificar as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| sasUri |Especifique a assinatura de acesso partilhado URI aos recursos de armazenamento do Azure, tais como blob, contentor ou mesa. |Sim |

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

Para obter mais informações sobre estes serviços ligados, consulte o artigo do [conector Azure Blob Storage.](data-factory-azure-blob-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Azure Blob, defina o **tipo** de conjunto de dados para **AzureBlob**. Em seguida, especifique as seguintes propriedades específicas de Azure Blob na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para o recipiente e pasta no armazenamento de bolhas. Exemplo: myblobcontainer\myblobfolder\ |Sim |
| fileName |Nome da bolha. fileName é opcional e sensível a casos.<br/><br/>Se especificar um nome de ficheiro, a atividade (incluindo cópia) funciona no Blob específico.<br/><br/>Quando o nome do ficheiro não é especificado, a Cópia inclui todos os Blobs na pastaPath para o conjunto de dados de entrada.<br/><br/>Quando o nome do ficheiro não é especificado para um conjunto de dados de `Data.<Guid>.txt` saída, o nome do ficheiro gerado estaria no seguinte formato: (por exemplo: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| divididoBy |partitionedBy é uma propriedade opcional. Pode usá-lo para especificar uma pasta dinâmicaPath e nome de ficheiro para dados da série de tempo. Por exemplo, a pastaO caminho pode ser parametrizado para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [Formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**. Os níveis suportados são: **Optimal** e **Fastest**. Para mais informações, consulte [formatos de ficheiroe de compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

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


Para mais informações, consulte o artigo do [conector Azure Blob.](data-factory-azure-blob-connector.md#dataset-properties)

### <a name="blobsource-in-copy-activity"></a>BlobSource na atividade de cópia
Se estiver a copiar dados de um Armazenamento De Blob Azure, delineie o **tipo** de origem da atividade de cópia para **o BlobSource**e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. |Verdadeiro (valor predefinido), Falso |Não |

#### <a name="example-blobsource"></a>Exemplo: **BlobSource**
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
Se estiver a copiar dados para um Armazenamento De Blob Azure, detete o tipo de **sumidouro** da atividade de cópia para **BlobSink,** e especifique as seguintes propriedades na secção **do lavatório:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copiarComportamento |Define o comportamento da cópia quando a fonte é BlobSource ou FileSystem. |<b>PreserveHierarchy</b>: preserva a hierarquia dos ficheiros na pasta-alvo. O caminho relativo do ficheiro fonte para a pasta fonte é idêntico ao caminho relativo do ficheiro alvo para a pasta-alvo.<br/><br/><b>Hierarquia Plana</b>: todos os ficheiros da pasta fonte estão no primeiro nível da pasta-alvo. Os ficheiros-alvo têm um nome gerado automaticamente. <br/><br/><b>MergeFiles (predefinição):</b> funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome Ficheiro/Bolha for especificado, o nome do ficheiro fundido será o nome especificado; caso contrário, seria o nome de ficheiro gerado automaticamente. |Não |

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

Para mais informações, consulte o artigo do [conector Azure Blob.](data-factory-azure-blob-connector.md#copy-activity-properties)

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Azure Data Lake Store, defina o tipo de serviço ligado à **AzureDataLakeStore**e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **AzureDataLakeStore** | Sim |
| dataLakeStoreUri | Especifique informações sobre a conta Azure Data Lake Store. Está no seguinte formato: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| subscriptionId | Id de subscrição Azure a que pertence a Data Lake Store. | Obrigatório para afundar |
| resourceGroupName | Nome de grupo de recursos Azure a que pertence a Data Lake Store. | Obrigatório para afundar |
| serviçoPrincipalId | Especifique a identificação do cliente do pedido. | Sim (para autenticação principal de serviço) |
| serviçoPrincipalKey | Especifique a chave da aplicação. | Sim (para autenticação principal de serviço) |
| inquilino | Especifique as informações do arrendatário (nome de domínio ou ID do inquilino) sob a qual reside a sua candidatura. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Sim (para autenticação principal de serviço) |
| autorização | Clique em **autorizar** o botão no **Editor da Fábrica** de Dados e introduza a sua credencial que atribui o URL de autorização gerado automaticamente a esta propriedade. | Sim (para autenticação credencial do utilizador)|
| sessionId | OAuth session ID da sessão de autorização da OAuth. Cada id da sessão é único e só pode ser usado uma vez. Esta definição é gerada automaticamente quando utiliza o Data Factory Editor. | Sim (para autenticação credencial do utilizador) |

#### <a name="example-using-service-principal-authentication"></a>Exemplo: utilização da autenticação do principal de serviço
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

#### <a name="example-using-user-credential-authentication"></a>Exemplo: utilização da autenticação credencial do utilizador
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

Para mais informações, consulte o artigo [do conector Azure Data Lake Store.](data-factory-azure-datalake-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados da Azure Data Lake Store, defina o **tipo** de conjunto de dados para **a AzureDataLakeStore,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| folderPath |Caminho para o recipiente e pasta na loja Azure Data Lake. |Sim |
| fileName |Nome do ficheiro na loja Azure Data Lake. fileName é opcional e sensível a casos. <br/><br/>Se especificar um nome de ficheiro, a atividade (incluindo cópia) funciona no ficheiro específico.<br/><br/>Quando o nome do ficheiro não é especificado, a Cópia inclui todos os ficheiros na pastaPath para o conjunto de dados de entrada.<br/><br/>Quando o nome do ficheiro não é especificado para um conjunto de dados de `Data.<Guid>.txt` saída, o nome do ficheiro gerado estaria no seguinte formato: (por exemplo: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| divididoBy |partitionedBy é uma propriedade opcional. Pode usá-lo para especificar uma pasta dinâmicaPath e nome de ficheiro para dados da série de tempo. Por exemplo, a pastaO caminho pode ser parametrizado para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [Formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**. Os níveis suportados são: **Optimal** e **Fastest**. Para mais informações, consulte [formatos de ficheiroe de compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

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

Para mais informações, consulte o artigo [do conector Azure Data Lake Store.](data-factory-azure-datalake-connector.md#dataset-properties)

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Azure Data Lake Store Fonte na Atividade de Cópia
Se estiver a copiar dados de uma Loja de Lagos De Dados Azure, delineie o **tipo** de origem da atividade de cópia para **o AzureDataLakeStoreSource,** e especifique as seguintes propriedades na secção **fonte:**

**AzureDataLakeStoreSource** suporta a seguinte secção de propriedades **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. |Verdadeiro (valor predefinido), Falso |Não |

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

Para mais informações, consulte o artigo [do conector Azure Data Lake Store.](data-factory-azure-datalake-connector.md#copy-activity-properties)

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Pia da loja de lagos de dados azure na atividade de cópia
Se estiver a copiar dados para uma Loja de Lagos De Dados Azure, detete o tipo de **sumidouro** da atividade de cópia para **a AzureDataLakeStoreSink,** e especifique as seguintes propriedades na secção **do lavatório:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copiarComportamento |Especifica o comportamento da cópia. |<b>PreserveHierarchy</b>: preserva a hierarquia dos ficheiros na pasta-alvo. O caminho relativo do ficheiro fonte para a pasta fonte é idêntico ao caminho relativo do ficheiro alvo para a pasta-alvo.<br/><br/><b>Hierarquia Plana</b>: todos os ficheiros da pasta fonte são criados no primeiro nível da pasta-alvo. Os ficheiros-alvo são criados com o nome gerado automaticamente.<br/><br/><b>MergeFiles</b>: funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome Ficheiro/Bolha for especificado, o nome do ficheiro fundido será o nome especificado; caso contrário, seria o nome de ficheiro gerado automaticamente. |Não |

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

Para mais informações, consulte o artigo [do conector Azure Data Lake Store.](data-factory-azure-datalake-connector.md#copy-activity-properties)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado ao Azure Cosmos DB, defina o **tipo** de serviço ligado ao **DocumentDb**e especifique as seguintes propriedades na secção **TypeProperties:**

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| conexãoString |Especifique as informações necessárias para se ligar à base de dados Do MBD Azure Cosmos. |Sim |

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
Para mais informações, consulte o artigo do [conector Azure Cosmos DB.](data-factory-azure-documentdb-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Azure Cosmos DB, defina o **tipo** de conjunto de dados para **documentDbCollection,** e especifique as seguintes propriedades na secção **TypeProperties:**

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| coleçãoNome |Nome da coleção Azure Cosmos DB. |Sim |

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
Para mais informações, consulte o artigo do [conector Azure Cosmos DB.](data-factory-azure-documentdb-connector.md#dataset-properties)

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Azure Cosmos DB Collection Source em Atividade de Cópia
Se estiver a copiar dados de um Azure Cosmos DB, delineie o **tipo** de origem da atividade de cópia para **o DocumentDbCollectionSource,** e especifique as seguintes propriedades na secção **fonte:**


| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| consulta |Especifique a consulta para ler dados. |Corda de consulta apoiada por Azure Cosmos DB. <br/><br/>Exemplo: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não especificada, a declaração SQL que é executada:`select <columns defined in structure> from mycollection` |
| nestingSeparator |Caráter especial para indicar que o documento está aninhado |Qualquer personagem. <br/><br/>Azure Cosmos DB é uma loja NoSQL para documentos JSON, onde são permitidas estruturas aninhadas. A Azure Data Factory permite ao utilizador denotar a hierarquia através do nidificação DoSeparator, que é "". nos exemplos acima. Com o separador, a atividade de cópia gerará o objeto "Nome" com três elementos infantis Primeiro, Médio e Último, de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição da tabela. |Não |

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

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Pia de coleção Azure Cosmos DB na atividade de cópia
Se estiver a copiar dados para o Azure Cosmos DB, detete o tipo de **sumidouro** da atividade de cópia para **documentDbCollectionSink,** e especifique as seguintes propriedades na secção **do lavatório:**

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| nestingSeparator |Um personagem especial no nome da coluna de origem para indicar que o documento aninhado é necessário. <br/><br/>Por exemplo: `Name.First` na tabela de saída produz a seguinte estrutura JSON no documento Cosmos DB:<br/><br/>"Nome": {<br/>    "Primeiro": "João"<br/>}, |Caráter utilizado para separar níveis de aninhamento.<br/><br/>O valor `.` predefinido é (ponto). |Caráter utilizado para separar níveis de aninhamento. <br/><br/>O valor `.` predefinido é (ponto). |
| escreverBatchSize |Número de pedidos paralelos ao serviço Azure Cosmos DB para criar documentos.<br/><br/>Pode afinar o desempenho ao copiar dados de/para o Azure Cosmos DB utilizando esta propriedade. Você pode esperar um melhor desempenho quando você aumentar writeBatchSize porque são enviados pedidos mais paralelos para Azure Cosmos DB. No entanto, terá de evitar estrangulamentos que possam lançar a mensagem de erro: "A taxa de pedido é grande".<br/><br/>O estrangulamento é decidido por uma série de fatores, incluindo a dimensão dos documentos, o número de termos em documentos, a política de indexação da recolha de alvos, etc. Para operações de cópia, pode utilizar uma melhor recolha (por exemplo, S3) para ter a maior entrada disponível (2.500 unidades de pedido/segundo). |Número inteiro |Não (padrão: 5) |
| escreverBatchTimeout |Aguarde o tempo para que a operação esteja concluída antes de sair. |timespan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |

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

Para mais informações, consulte o artigo do [conector Azure Cosmos DB.](data-factory-azure-documentdb-connector.md#copy-activity-properties)

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Base de Dados Azure SQL, defina o **tipo** de serviço ligado à **AzureSqlDatabase,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| conexãoString |Especifique as informações necessárias para se ligar à base de dados Azure SQL para a propriedade de ligaçãoString. |Sim |

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

Para mais informações, consulte o artigo do [conector Azure SQL.](data-factory-azure-sql-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados azure SQL Database, defina o **tipo** do conjunto de dados para **AzureSqlTable,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na instância de base de dados Azure SQL a que o serviço ligado se refere. |Sim |

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
Para mais informações, consulte o artigo do [conector Azure SQL.](data-factory-azure-sql-connector.md#dataset-properties)

### <a name="sql-source-in-copy-activity"></a>Fonte SQL na atividade de cópia
Se estiver a copiar dados de uma base de dados Azure SQL, delineie o tipo de **origem** da atividade de cópia para **o SqlSource**e especifique as seguintes propriedades na secção **fonte:**


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| parâmetros de procedimento saqueados |Parâmetros para o procedimento armazenado. |Nome/pares de valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. |Não |

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
Para mais informações, consulte o artigo do [conector Azure SQL.](data-factory-azure-sql-connector.md#copy-activity-properties)

### <a name="sql-sink-in-copy-activity"></a>Pia SQL na atividade de cópia
Se estiver a copiar dados para a Base de Dados Azure SQL, detete o tipo de **sumidouro** da atividade de cópia para **SqlSink,** e especifique as seguintes propriedades na secção **do lavatório:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| escreverBatchTimeout |Aguarde o tempo para que a operação de inserção do lote esteja concluída antes de sair. |timespan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |
| escreverBatchSize |Insere os dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanupScript |Especifique uma consulta para a Atividade de Cópia executar de tal forma que os dados de uma fatia específica são limpos. |Uma declaração de consulta. |Não |
| sliceIdentifierColumnName |Especifique um nome de coluna para a Copy Activity para preencher com identificador de fatias gerado saqueado automaticamente, que é usado para limpar dados de uma fatia específica quando reexecutado. |Nome da coluna de uma coluna com tipo de dados de binário(32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que atualiza os dados (atualizações/inserções) na tabela-alvo. |Nome do procedimento armazenado. |Não |
| parâmetros de procedimento saqueados |Parâmetros para o procedimento armazenado. |Nome/pares de valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. |Não |
| sqlWriterTableType |Especifique um nome de tipo de tabela a utilizar no procedimento armazenado. A atividade de cópia disponibiliza os dados disponíveis numa tabela temporária com este tipo de tabela. O código de procedimento armazenado pode então fundir os dados que estão a ser copiados com os dados existentes. |Um nome tipo de mesa. |Não |

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

Para mais informações, consulte o artigo do [conector Azure SQL.](data-factory-azure-sql-connector.md#copy-activity-properties)

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado ao Armazém de Dados Azure SQL, defina o **tipo** de serviço ligado ao **AzureSqlDW,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| conexãoString |Especifique as informações necessárias para se ligar à instância do Armazém de Dados Azure SQL para a propriedade de conexãoString. |Sim |



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

Para mais informações, consulte o artigo do [conector Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados azure SQL Data Warehouse, defina o **tipo** do conjunto de dados para **AzureSqlDWTable,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na base de dados Azure SQL Data Warehouse a que o serviço ligado se refere. |Sim |

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

Para mais informações, consulte o artigo do [conector Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties)

### <a name="sql-dw-source-in-copy-activity"></a>Fonte SQL DW na atividade de cópia
Se estiver a copiar dados do Armazém de Dados Azure SQL, delineie o tipo de **origem** da atividade de cópia para **o SqlDWSource**e especifique as seguintes propriedades na secção **fonte:**


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Por exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| parâmetros de procedimento saqueados |Parâmetros para o procedimento armazenado. |Nome/pares de valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. |Não |

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

Para mais informações, consulte o artigo do [conector Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

### <a name="sql-dw-sink-in-copy-activity"></a>SQL DW Sink na atividade de cópia
Se estiver a copiar dados para o Azure SQL Data Warehouse, detete o tipo de **sumidouro** da atividade de cópia para **SqlDWSink,** e especifique as seguintes propriedades na secção **do lavatório:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Especifique uma consulta para a Atividade de Cópia executar de tal forma que os dados de uma fatia específica são limpos. |Uma declaração de consulta. |Não |
| permitir a PolyBase |Indica se deve utilizar o PolyBase (quando aplicável) em vez do mecanismo BULKINSERT. <br/><br/> **Utilizar a PolyBase é a forma recomendada de carregar dados no Armazém de Dados SQL.** |Verdadeiro <br/>Falso (predefinição) |Não |
| poliBaseSettings |Um grupo de propriedades que pode ser especificado quando a propriedade **permitida Polybase** é definida como **verdadeira**. |&nbsp; |Não |
| rejeitarValue |Especifica o número ou percentagem de linhas que podem ser rejeitadas antes da consulta falhar. <br/><br/>Saiba mais sobre as opções de rejeição da PolyBase na secção **argumentos** do tópico [CREATE EXTERNAL TABLE (Transact-SQL).](https://msdn.microsoft.com/library/dn935021.aspx) |0 (padrão), 1, 2, ... |Não |
| rejeitarType |Especifica se a opção rejectValue é especificada como um valor literal ou uma percentagem. |Valor (padrão), Percentagem |Não |
| rejeitarSampleValue |Determina o número de linhas a recuperar antes que a PolyBase recalcule a percentagem de linhas rejeitadas. |1, 2, ... |Sim, se **rejeitarType** é **percentual** |
| useTypeDefault |Especifica como lidar com valores em falta em ficheiros de texto delimitados quando a PolyBase recupera dados do ficheiro de texto.<br/><br/>Saiba mais sobre esta propriedade a partir da secção Argumentos em FORMATO DE [ARQUIVO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Verdade, Falso (padrão) |Não |
| escreverBatchSize |Insere os dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize |Inteiro (número de linhas) |Não (padrão: 10000) |
| escreverBatchTimeout |Aguarde o tempo para que a operação de inserção do lote esteja concluída antes de sair. |timespan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |

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

Para mais informações, consulte o artigo do [conector Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Pesquisa Cognitiva Azure, defina o **tipo** de serviço ligado ao **AzureSearch**e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| url | URL para o serviço de pesquisa. | Sim |
| key | Chave de administrador para o serviço de pesquisa. | Sim |

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

Para mais informações, consulte o artigo do [conector Azure Cognitive Search.](data-factory-azure-search-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados de Pesquisa Cognitiva Azure, defina o **tipo** de conjunto de dados para **o AzureSearchIndex,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| tipo | A propriedade tipo deve ser definida para **AzureSearchIndex**.| Sim |
| nome de índice | Nome do índice de pesquisa. Data Factory não cria o índice. O índice deve existir na Pesquisa Cognitiva Azure. | Sim |

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

Para mais informações, consulte o artigo do [conector Azure Cognitive Search.](data-factory-azure-search-connector.md#dataset-properties)

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Sink do Índice de Pesquisa Cognitiva Azure na atividade de cópia
Se estiver a copiar dados para um índice de pesquisa, detete o tipo de **sumidouro** da atividade de cópia para **o AzureSearchIndexSink,** e especifique as seguintes propriedades na secção **do lavatório:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Especifica se deve fundir ou substituir quando um documento já existe no índice. | Fusão (padrão)<br/>Carregar| Não |
| WriteBatchSize | Envia dados para o índice de pesquisa quando o tamanho do tampão atinge o writeBatchSize. | 1 a 1.000. O valor padrão é de 1000. | Não |

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

Para mais informações, consulte o artigo do [conector Azure Cognitive Search.](data-factory-azure-search-connector.md#copy-activity-properties)

## <a name="azure-table-storage"></a>Table Storage do Azure

### <a name="linked-service"></a>Serviço ligado
Existem dois tipos de serviços ligados: serviço ligado ao Armazenamento Azure e serviço ligado ao Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
Para ligar a sua conta de armazenamento Azure a uma fábrica de dados utilizando a **chave da conta,** crie um serviço ligado ao Armazenamento Azure. Para definir um serviço ligado ao Armazenamento Azure, defina o **tipo** de serviço ligado ao **AzureStorage**. Em seguida, pode especificar as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade tipo deve ser definida para: **AzureStorage** |Sim |
| conexãoString |Especifique as informações necessárias para ligar ao armazenamento Azure para a propriedade connectionString. |Sim |

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

#### <a name="azure-storage-sas-linked-service"></a>Serviço Ligado ao Armazenamento Azure SAS
O serviço ligado ao Azure Storage SAS permite-lhe ligar uma Conta de Armazenamento Azure a uma fábrica de dados Azure utilizando uma Assinatura de Acesso Partilhado (SAS). Fornece à fábrica de dados um acesso restrito/limite de tempo a todos/recursos específicos (blob/container) no armazenamento. Para ligar a sua conta de armazenamento Azure a uma fábrica de dados utilizando a Signature de Acesso Partilhado, crie um serviço ligado ao Azure Storage SAS. Para definir um serviço ligado ao Azure Storage SAS, defina o **tipo** de serviço ligado ao **AzureStorageSas**. Em seguida, pode especificar as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade tipo deve ser definida para: **AzureStorageSas** |Sim |
| sasUri |Especifique a assinatura de acesso partilhado URI aos recursos de armazenamento do Azure, tais como blob, contentor ou mesa. |Sim |

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

Para mais informações sobre estes serviços ligados, consulte o artigo do [conector azure table storage.](data-factory-azure-table-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados da Tabela Azure, defina o **tipo** do conjunto de dados para **O AzureTable,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância de base de dados de mesa azure a que o serviço ligado se refere. |Sim. Quando um nome de mesa é especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se for também especificado um azureTableSourceQuery, os registos da tabela que satisfaz a consulta são copiados para o destino. |

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

Para mais informações sobre estes serviços ligados, consulte o artigo do [conector azure table storage.](data-factory-azure-table-connector.md#dataset-properties)

### <a name="azure-table-source-in-copy-activity"></a>Fonte de tabela azure na atividade de cópia
Se estiver a copiar dados do Armazenamento de TabelaS Azure, delineie o tipo de **origem** da atividade de cópia para **o AzureTableSource**e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableSourceQuery |Use a consulta personalizada para ler dados. |Corda de consulta de mesa azul. Veja exemplos na secção seguinte. |Não. Quando um nome de mesa é especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se for também especificado um azureTableSourceQuery, os registos da tabela que satisfaz a consulta são copiados para o destino. |
| azureTableSourceIgnoreTableNotFound |Indique se não existe a exceção da mesa. |TRUE<br/>FALSE |Não |

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

Para mais informações sobre estes serviços ligados, consulte o artigo do [conector azure table storage.](data-factory-azure-table-connector.md#copy-activity-properties)

### <a name="azure-table-sink-in-copy-activity"></a>Pia de mesa azure na atividade de cópia
Se estiver a copiar dados para o Armazenamento de Mesa Seletiva, detete o tipo de **sumidouro** da atividade de cópia para **o AzureTableSink,** e especifique as seguintes propriedades na secção **do lavatório:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Valor da chave da divisória padrão que pode ser usado pela pia. |Um valor de corda. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são utilizados como teclas de partição. Se não especificado, o AzureTableDefaultPartitionKeyValue é utilizado como chave de partição. |Um nome de coluna. |Não |
| nome chave azureTableRow |Especifique o nome da coluna cujos valores de coluna são utilizados como chave de linha. Se não especificado, utilize um GUID para cada linha. |Um nome de coluna. |Não |
| azureTableInsertType |O modo de inserir dados na tabela Azure.<br/><br/>Esta propriedade controla se as linhas existentes na tabela de saída com divisória seletiva e chaves de linha correspondentes têm os seus valores substituídos ou fundidos. <br/><br/>Para saber como funcionam estas definições (fundir e substituir), consulte [inserir ou fundir tópicos de Entidade](https://msdn.microsoft.com/library/azure/hh452241.aspx) e Inserir ou Substituir tópicos da [Entidade.](https://msdn.microsoft.com/library/azure/hh452242.aspx) <br/><br> Esta definição aplica-se ao nível da linha, não ao nível da tabela, e nenhuma das opções elimina linhas no quadro de saída que não existam na entrada. |fusão (padrão)<br/>substituir |Não |
| escreverBatchSize |Insere os dados na tabela Azure quando o writeBatchSize ou o writeBatchTimeout for atingido. |Inteiro (número de linhas) |Não (padrão: 10000) |
| escreverBatchTimeout |Insere os dados na tabela Azure quando o writeBatchSize ou o writeBatchTimeout são atingidos |timespan<br/><br/>Exemplo: "00:20:00" (20 minutos) |Não (Predefinido para armazenar valor de tempo de saída do cliente 90 seg) |

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
Para mais informações sobre estes serviços ligados, consulte o artigo do [conector azure table storage.](data-factory-azure-table-connector.md#copy-activity-properties)

## <a name="amazon-redshift"></a>RedShift da Amazónia

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado amazon Redshift, defina o **tipo** de serviço ligado ao **AmazonRedshift**e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Endereço IP ou nome anfitrião do servidor Amazon Redshift. |Sim |
| porta |O número da porta TCP que o servidor Amazon Redshift usa para ouvir as ligações dos clientes. |Não, valor predefinido: 5439 |
| base de dados |Nome da base de dados Amazon Redshift. |Sim |
| o nome de utilizador |Nome do utilizador que tenha acesso à base de dados. |Sim |
| palavra-passe |Palavra-passe para a conta de utilizador. |Sim |

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

Para mais informações, consulte o artigo do conector Amazon Redshift.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Amazon Redshift, defina o **tipo** de conjunto de dados para **RelationalTable,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na base de dados da Amazon Redshift a que o serviço ligado se refere. |Não (se for especificada **a consulta** do **RelationalSource)** |


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
Para mais informações, consulte o artigo do conector Amazon Redshift.

### <a name="relational-source-in-copy-activity"></a>Fonte Relacional na Atividade de Cópia
Se estiver a copiar dados da Amazon Redshift, delineie o tipo de **origem** da atividade de cópia para **RelationalSource**e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se o nome do **conjunto** de **dados** for especificado) |

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
Para mais informações, consulte o artigo do conector Amazon Redshift.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à IBM DB2, defina o **tipo** de serviço ligado ao **OnPremisesDB2**e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor DB2. |Sim |
| base de dados |Nome da base de dados DB2. |Sim |
| schema |Nome do esquema na base de dados. O nome do esquema é sensível ao caso. |Não |
| authenticationType |Tipo de autenticação utilizada para ligar à base de dados DB2. Os valores possíveis são: Anónimo, Básico e Windows. |Sim |
| o nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Basic ou Windows. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |
| nome gateway |Nome do portal que o serviço Data Factory deve utilizar para ligar à base de dados DB2 no local. |Sim |

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
Para mais informações, consulte o artigo do conector IBM DB2.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados DB2, defina o **tipo** do conjunto de dados para **O TrelaTable,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância de base de dados DB2 a que o serviço ligado se refere. O nome da tabela é sensível a casos. |Não (se for especificada **a consulta** do **RelationalSource)**

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

Para mais informações, consulte o artigo do conector IBM DB2.

### <a name="relational-source-in-copy-activity"></a>Fonte Relacional na Atividade de Cópia
Se estiver a copiar dados do IBM DB2, delineie o tipo de **origem** da atividade de cópia para **relationalSource**e especifique as seguintes propriedades na secção **fonte:**


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""`. |Não (se o nome do **conjunto** de **dados** for especificado) |

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
Para mais informações, consulte o artigo do conector IBM DB2.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado ao MySQL, defina o **tipo** de serviço ligado ao **OnPremisesMySql,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor MySQL. |Sim |
| base de dados |Nome da base de dados MySQL. |Sim |
| schema |Nome do esquema na base de dados. |Não |
| authenticationType |Tipo de autenticação utilizada para ligar à base de dados MySQL. Os valores `Basic`possíveis são: . |Sim |
| userName |Especifique o nome do utilizador para se ligar à base de dados MySQL. |Sim |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada. |Sim |
| nome gateway |Nome do portal que o serviço Data Factory deve utilizar para ligar à base de dados MySQL no local. |Sim |

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

Para mais informações, consulte o artigo do [conector MySQL.](data-factory-onprem-mysql-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados MySQL, defina o **tipo** do conjunto de dados para **O TrelaTable,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância mySQL Database a que o serviço ligado se refere. |Não (se for especificada **a consulta** do **RelationalSource)** |

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
Para mais informações, consulte o artigo do [conector MySQL.](data-factory-onprem-mysql-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte Relacional na Atividade de Cópia
Se estiver a copiar dados de uma base de dados mySQL, delineie o tipo de **origem** da atividade de cópia para **RelationalSource**e especifique as seguintes propriedades na secção **fonte:**


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se o nome do **conjunto** de **dados** for especificado) |


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

Para mais informações, consulte o artigo do [conector MySQL.](data-factory-onprem-mysql-connector.md#copy-activity-properties)

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Oracle, defina o **tipo** de serviço ligado ao **OnPremisesOracle**e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| driverType | Especifique qual o controlador a utilizar para copiar dados de/para a Base de Dados Oracle. Os valores permitidos são **Microsoft** ou **ODP** (predefinição). Consulte a versão suportada e a secção de instalação nos detalhes do condutor. | Não |
| conexãoString | Especifique as informações necessárias para se ligar à base de dados oracle, por exemplo, para a propriedade de ligaçãoString. | Sim |
| nome gateway | Nome do portal que é usado para ligar ao servidor Oracle no local |Sim |

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

Para mais informações, consulte o artigo do [conector Oracle.](data-factory-onprem-oracle-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados oracle, defina o **tipo** do conjunto de dados para **o OracleTable,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na Base de Dados Oracle a que o serviço ligado se refere. |Não (se o **oráculoReaderQuery** do **OracleSource** for especificado) |

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
Para mais informações, consulte o artigo do [conector Oracle.](data-factory-onprem-oracle-connector.md#dataset-properties)

### <a name="oracle-source-in-copy-activity"></a>Fonte do Oráculo na Atividade de Cópia
Se estiver a copiar dados de uma base de dados da Oracle, delineie o tipo de **origem** da atividade de cópia para **o OracleSource**e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| oracleReaderQuery |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Por exemplo: `select * from MyTable` <br/><br/>Se não especificada, a declaração SQL que é executada:`select * from MyTable` |Não (se o nome do **conjunto** de **dados** for especificado) |

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

Para mais informações, consulte o artigo do [conector Oracle.](data-factory-onprem-oracle-connector.md#copy-activity-properties)

### <a name="oracle-sink-in-copy-activity"></a>Pia de oráculo na atividade de cópia
Se estiver a copiar dados para a base de dados da Oracle, detete o tipo de **sumidouro** da atividade de cópia para **o OracleSink,** e especifique as seguintes propriedades na secção **do lavatório:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| escreverBatchTimeout |Aguarde o tempo para que a operação de inserção do lote esteja concluída antes de sair. |timespan<br/><br/> Exemplo: 00:30:00 (30 minutos). |Não |
| escreverBatchSize |Insere os dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 100) |
| sqlWriterCleanupScript |Especifique uma consulta para a Atividade de Cópia executar de tal forma que os dados de uma fatia específica são limpos. |Uma declaração de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a Atividade de Cópia para preencher com identificador de fatias gerado automaticamente, que é usado para limpar dados de uma fatia específica quando reexecutado. |Nome da coluna de uma coluna com tipo de dados de binário(32). |Não |

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
Para mais informações, consulte o artigo do [conector Oracle.](data-factory-onprem-oracle-connector.md#copy-activity-properties)

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado postgresQL, defina o **tipo** de serviço ligado ao **OnPremisesPostgreSql,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor PostgreSQL. |Sim |
| base de dados |Nome da base de dados PostgreSQL. |Sim |
| schema |Nome do esquema na base de dados. O nome do esquema é sensível ao caso. |Não |
| authenticationType |Tipo de autenticação utilizada para ligar à base de dados PostgreSQL. Os valores possíveis são: Anónimo, Básico e Windows. |Sim |
| o nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Basic ou Windows. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |
| nome gateway |Nome do portal que o serviço Data Factory deve utilizar para ligar à base de dados PostgreSQL no local. |Sim |

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
Para mais informações, consulte o artigo do [conector PostgreSQL.](data-factory-onprem-postgresql-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados PostgreSQL, defina o **tipo** do conjunto de dados para **O Quadro Relacional,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância de Base de Dados PostgreSQL a que o serviço ligado se refere. O nome da tabela é sensível a casos. |Não (se for especificada **a consulta** do **RelationalSource)** |

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
Para mais informações, consulte o artigo do [conector PostgreSQL.](data-factory-onprem-postgresql-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte Relacional na Atividade de Cópia
Se estiver a copiar dados de uma base de dados PostgreSQL, delineie o **tipo** de origem da atividade de cópia para **o RelationalSource**e especifique as seguintes propriedades na secção **fonte:**


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Por exemplo: "consulta": "selecione * de \"MySchema\". \"MyTable\"". |Não (se o nome do **conjunto** de **dados** for especificado) |

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

Para mais informações, consulte o artigo do [conector PostgreSQL.](data-factory-onprem-postgresql-connector.md#copy-activity-properties)

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado ao SAP Business Warehouse (BW), defina o **tipo** de serviço ligado à **SapBw**e especifique as seguintes propriedades na secção **TypeProperties:**

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
servidor | Nome do servidor em que reside a instância SAP BW. | string | Sim
sistemaNúmero | Número do sistema do sistema SAP BW. | Número de cimacimal de dois dígitos representado como uma corda. | Sim
clientId | Identificação do cliente do cliente no sistema SAP W. | Número de cimacimal de três dígitos representado como uma corda. | Sim
o nome de utilizador | Nome do utilizador que tem acesso ao servidor SAP | string | Sim
palavra-passe | A palavra-passe do utilizador. | string | Sim
nome gateway | Nome do portal que o serviço Data Factory deve utilizar para ligar à instância SAP BW no local. | string | Sim
credenta encriptado | A corda credencial encriptada. | string | Não

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

Para mais informações, consulte o artigo do [conector SAP Business Warehouse.](data-factory-sap-business-warehouse-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados SAP BW, defina o **tipo** do conjunto de dados para **RelationalTable**. Não existem propriedades específicas do tipo suportadas para o conjunto de dados SAP BW do tipo **RelationalTable**.

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
Para mais informações, consulte o artigo do [conector SAP Business Warehouse.](data-factory-sap-business-warehouse-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte Relacional na Atividade de Cópia
Se estiver a copiar dados do SAP Business Warehouse, delineie o **tipo** de origem da atividade de cópia para **o RelationalSource**e especifique as seguintes propriedades na secção **fonte:**


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta | Especifica a consulta MDX para ler os dados da instância SAP BW. | Consulta MDX. | Sim |

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

Para mais informações, consulte o artigo do [conector SAP Business Warehouse.](data-factory-sap-business-warehouse-connector.md#copy-activity-properties)

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado sAP HANA, defina o **tipo** de serviço ligado à **SapHana**e especifique as seguintes propriedades na secção **typeProperties:**

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
servidor | Nome do servidor em que reside a instância SAP HANA. Se o seu servidor estiver a `server:port`utilizar uma porta personalizada, especifique . | string | Sim
authenticationType | Tipo de autenticação. | corda. "Básico" ou "Windows" | Sim
o nome de utilizador | Nome do utilizador que tem acesso ao servidor SAP | string | Sim
palavra-passe | A palavra-passe do utilizador. | string | Sim
nome gateway | Nome do portal que o serviço Data Factory deve utilizar para ligar à instância SAP HANA no local. | string | Sim
credenta encriptado | A corda credencial encriptada. | string | Não

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
Para mais informações, consulte o artigo do [conector SAP HANA.](data-factory-sap-hana-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados SAP HANA, defina o **tipo** do conjunto de dados para **RelationalTable**. Não existem propriedades específicas do tipo suportadas para o conjunto de dados SAP HANA do tipo **RelationalTable**.

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
Para mais informações, consulte o artigo do [conector SAP HANA.](data-factory-sap-hana-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte Relacional na Atividade de Cópia
Se estiver a copiar dados de uma loja de dados SAP HANA, delineie o **tipo** de origem da atividade de cópia para **o RelationalSource**e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta | Especifica a consulta SQL para ler os dados da instância SAP HANA. | Consulta SQL. | Sim |


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

Para mais informações, consulte o artigo do [conector SAP HANA.](data-factory-sap-hana-connector.md#copy-activity-properties)


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Serviço ligado
Cria um serviço ligado do tipo **OnPremisesSqlServer** para ligar uma base de dados do SQL Server ao local a uma fábrica de dados. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao Servidor SQL no local.

A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao Servidor SQL.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade do tipo deve ser definida para: **OnPremisesSqlServer**. |Sim |
| conexãoString |Especifique as informações de ligação Necessárias à base de dados do SQL Server no local utilizando a autenticação SQL ou a autenticação do Windows. |Sim |
| nome gateway |Nome do portal que o serviço Data Factory deve utilizar para ligar à base de dados do SQL Server no local. |Sim |
| o nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a Autenticação do Windows. Exemplo: **nome\\de utilizador do nome de domínio**. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |

Pode encriptar credenciais utilizando o cmdlet **New-AzDataFactoryEncryptValue** e utilizá-las na cadeia de ligação, como mostra o seguinte exemplo (propriedade**EncryptedCredential):**

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Exemplo: JSON para utilização de autenticação SQL

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

Se o nome de utilizador e a palavra-passe forem especificados, o gateway utiliza-os para personificar a conta de utilizador especificada para se ligar à base de dados do Servidor SQL no local. Caso contrário, o gateway liga-se diretamente ao Servidor SQL com o contexto de segurança do Gateway (a sua conta de arranque).

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

Para mais informações, consulte o artigo do [conector SQL Server.](data-factory-sqlserver-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Servidor SQL, defina o **tipo** de conjunto de dados para **SqlServerTable,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na instância de Base de Dados do Servidor SQL a que o serviço ligado se refere. |Sim |

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

Para mais informações, consulte o artigo do [conector SQL Server.](data-factory-sqlserver-connector.md#dataset-properties)

### <a name="sql-source-in-copy-activity"></a>Fonte Sql na Atividade de Cópia
Se estiver a copiar dados de uma base de dados do SQL Server, detete o tipo de **origem** da atividade de cópia para **o SqlSource**e especifique as seguintes propriedades na secção **fonte:**


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Por exemplo: `select * from MyTable`. Pode fazer referência a várias tabelas da base de dados referenciada pelo conjunto de dados de entrada. Se não especificada, a declaração SQL que é executada: selecione a partir do MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| parâmetros de procedimento saqueados |Parâmetros para o procedimento armazenado. |Nome/pares de valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. |Não |

Se o **sqlReaderQuery** for especificado para o SqlSource, a Atividade de Cópia executa esta consulta contra a fonte da Base de Dados do Servidor SQL para obter os dados.

Em alternativa, pode especificar um procedimento armazenado especificando o nome de **procedimento sqlReaderStoredEdE** e **os parâmetros de procedimento armazenados** (se o procedimento armazenado tiver parâmetros).

Se não especificar o sqlReaderQuery ou o sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura são usadas para construir uma consulta selecionada para ser executada contra a Base de Dados do Servidor SQL. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.

> [!NOTE]
> Quando utiliza o nome de **procedimento sqlReaderStored**, ainda precisa especificar um valor para a propriedade **tableName** no conjunto de dados JSON. No entanto, não há validações realizadas contra esta tabela.


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

Neste exemplo, o **sqlReaderQuery** é especificado para o SqlSource. A Atividade de Cópia executa esta consulta contra a fonte da Base de Dados do Servidor SQL para obter os dados. Em alternativa, pode especificar um procedimento armazenado especificando o nome de **procedimento sqlReaderStoredEdE** e **os parâmetros de procedimento armazenados** (se o procedimento armazenado tiver parâmetros). O sqlReaderQuery pode fazer referência a várias tabelas dentro da base de dados referenciada pelo conjunto de dados de entrada. Não se limita apenas ao conjunto de tabelas como o conjunto de dados NameTypeProperty.

Se não especificar o sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura são usadas para construir uma consulta selecionada para ser executada contra a Base de Dados do Servidor SQL. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.

Para mais informações, consulte o artigo do [conector SQL Server.](data-factory-sqlserver-connector.md#copy-activity-properties)

### <a name="sql-sink-in-copy-activity"></a>Pia Sql na atividade de cópia
Se estiver a copiar dados para uma base de dados do SQL Server, detete o tipo de **sink** da atividade de cópia para **SqlSink,** e especifique as seguintes propriedades na secção **do lavatório:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| escreverBatchTimeout |Aguarde o tempo para que a operação de inserção do lote esteja concluída antes de sair. |timespan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |
| escreverBatchSize |Insere os dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanupScript |Especifique a consulta para a Atividade de Cópia executar de tal forma que os dados de uma fatia específica são limpos. Para mais informações, consulte a secção de repetibilidade. |Uma declaração de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a Atividade de Cópia para preencher com identificador de fatias gerado automaticamente, que é usado para limpar dados de uma fatia específica quando reexecutado. Para mais informações, consulte a secção de repetibilidade. |Nome da coluna de uma coluna com tipo de dados de binário(32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que atualiza os dados (atualizações/inserções) na tabela-alvo. |Nome do procedimento armazenado. |Não |
| parâmetros de procedimento saqueados |Parâmetros para o procedimento armazenado. |Nome/pares de valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. |Não |
| sqlWriterTableType |Especifique o nome do tipo de tabela a utilizar no procedimento armazenado. A atividade de cópia disponibiliza os dados disponíveis numa tabela temporária com este tipo de tabela. O código de procedimento armazenado pode então fundir os dados que estão a ser copiados com os dados existentes. |Um nome tipo de mesa. |Não |

#### <a name="example"></a>Exemplo
O pipeline contém uma Atividade de Cópia que está configurada para utilizar estes conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **BlobSource** e o tipo **de pia** é definido para **SqlSink**.

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

Para mais informações, consulte o artigo do [conector SQL Server.](data-factory-sqlserver-connector.md#copy-activity-properties)

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Sybase, defina o **tipo** de serviço ligado à **Base DeSsícula OnPremisesSy,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor Sybase. |Sim |
| base de dados |Nome da base de dados da Base de Dados Sybase. |Sim |
| schema |Nome do esquema na base de dados. |Não |
| authenticationType |Tipo de autenticação utilizada para ligar à base de dados da Sybase. Os valores possíveis são: Anónimo, Básico e Windows. |Sim |
| o nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Basic ou Windows. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |
| nome gateway |Nome do portal que o serviço Data Factory deve utilizar para ligar à base de dados sybase no local. |Sim |

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

Para mais informações, consulte o artigo do [conector Sybase.](data-factory-onprem-sybase-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Sybase, defina o **tipo** do conjunto de dados para **O TrelaTable,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância base de dados sybase a que o serviço ligado se refere. |Não (se for especificada **a consulta** do **RelationalSource)** |

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

Para mais informações, consulte o artigo do [conector Sybase.](data-factory-onprem-sybase-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte Relacional na Atividade de Cópia
Se estiver a copiar dados de uma base de dados da Sybase, delineie o **tipo** de origem da atividade de cópia para **o RelationalSource**e especifique as seguintes propriedades na secção **fonte:**


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se o nome do **conjunto** de **dados** for especificado) |

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

Para mais informações, consulte o artigo do [conector Sybase.](data-factory-onprem-sybase-connector.md#copy-activity-properties)

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Teradata, defina o **tipo** de serviço ligado ao **OnPremisesTeradata**e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor Teradata. |Sim |
| authenticationType |Tipo de autenticação utilizada para ligar à base de dados Teradata. Os valores possíveis são: Anónimo, Básico e Windows. |Sim |
| o nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Basic ou Windows. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |
| nome gateway |Nome do portal que o serviço Data Factory deve utilizar para ligar à base de dados teradata no local. |Sim |

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

Para mais informações, consulte o artigo do [conector Teradata.](data-factory-onprem-teradata-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Teradata Blob, defina o **tipo** do conjunto de dados para **RelationalTable**. Atualmente, não existem propriedades tipo suportadas para o conjunto de dados Teradata.

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

Para mais informações, consulte o artigo do [conector Teradata.](data-factory-onprem-teradata-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte Relacional na Atividade de Cópia
Se estiver a copiar dados de uma base de dados da Teradata, delineie o **tipo** de origem da atividade de cópia para **o RelationalSource**e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Por exemplo: `select * from MyTable`. |Sim |

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

Para mais informações, consulte o artigo do [conector Teradata.](data-factory-onprem-teradata-connector.md#copy-activity-properties)

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Cassandra, defina o **tipo** de serviço ligado ao **OnPremisesCassandra,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| anfitrião |Um ou mais endereços IP ou nomes de anfitriões dos servidores Cassandra.<br/><br/>Especifique uma lista separada da vírmula de endereços IP ou nomes de anfitriões para ligar a todos os servidores simultaneamente. |Sim |
| porta |A porta TCP que o servidor Cassandra usa para ouvir as ligações do cliente. |Não, valor predefinido: 9042 |
| authenticationType |Básico, ou Anónimo |Sim |
| o nome de utilizador |Especifique o nome do utilizador para a conta de utilizador. |Sim, se a autenticaçãoType estiver definida para Basic. |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador. |Sim, se a autenticaçãoType estiver definida para Basic. |
| nome gateway |O nome da porta de entrada que é usada para ligar à base de dados de Cassandra no local. |Sim |
| credenta encriptado |Credencial encriptado pelo portal. |Não |

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

Para mais informações, consulte o artigo do [conector cassandra.](data-factory-onprem-cassandra-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Cassandra, defina o **tipo** do conjunto de dados para **CassandraTable,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| espaço chave |Nome do espaço-chave ou esquema na base de dados de Cassandra. |Sim (Se a **consulta** para **CassandraSource** não estiver definida). |
| tableName |Nome da mesa na base de dados de Cassandra. |Sim (Se a **consulta** para **CassandraSource** não estiver definida). |

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

Para mais informações, consulte o artigo do [conector cassandra.](data-factory-onprem-cassandra-connector.md#dataset-properties)

### <a name="cassandra-source-in-copy-activity"></a>Cassandra Source em Atividade de Cópia
Se estiver a copiar dados da Cassandra, delineie o tipo de **origem** da atividade de cópia para **CassandraSource**e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler dados. |Consulta SQL-92 ou consulta CQL. Ver [referência CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao utilizar a consulta SQL, especifique o **nome do espaço-chave.nome** da tabela para representar a tabela que pretende consultar. |Não (se o nome do quadro e o espaço-chave no conjunto de dados estiverem definidos). |
| consistênciaN |O nível de consistência especifica quantas réplicas devem responder a um pedido de leitura antes de devolver os dados à aplicação do cliente. Cassandra verifica o número especificado de réplicas para obter dados para satisfazer o pedido de leitura. |UM, DOIS, TRÊS, QUORUM, TODOS, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Consulte [a configuração](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) da consistência dos dados para obter mais detalhes. |Não. O valor padrão é ONE. |

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

Para mais informações, consulte o artigo do [conector cassandra.](data-factory-onprem-cassandra-connector.md#copy-activity-properties)

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço mongoDB ligado, definir o **tipo** de serviço ligado ao **OnPremisesMongoDB,** e especificar as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Endereço IP ou nome de anfitrião do servidor MongoDB. |Sim |
| porta |Porta TCP que o servidor MongoDB usa para ouvir ligações ao cliente. |Opcional, valor por defeito: 27017 |
| authenticationType |Básico, ou Anónimo. |Sim |
| o nome de utilizador |Conta de utilizador para aceder ao MongoDB. |Sim (se for utilizada a autenticação básica). |
| palavra-passe |A palavra-passe do utilizador. |Sim (se for utilizada a autenticação básica). |
| authSource |Nome da base de dados MongoDB que pretende utilizar para verificar as suas credenciais para autenticação. |Opcional (se for utilizada a autenticação básica). predefinição: utiliza a conta de administração e a base de dados especificada utilizando a propriedade databaseName. |
| nome da base de dados |Nome da base de dados MongoDB a que pretende aceder. |Sim |
| nome gateway |Nome do portal que acede à loja de dados. |Sim |
| credenta encriptado |Credencial encriptado por portal. |Opcional |

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

Para mais informações, consulte o artigo do [conector MongoDB](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados MongoDB, defina o **tipo** de conjunto de dados para **MongoDbCollection,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| coleçãoNome |Nome da coleção na base de dados mongoDB. |Sim |

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

Para mais informações, consulte o artigo do [conector MongoDB](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>Fonte MongoDB na atividade de cópia
Se estiver a copiar dados do MongoDB, delineie o tipo de **origem** da atividade de cópia para **o MongoDbSource,** e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler dados. |Fio de consulta SQL-92. Por exemplo: `select * from MyTable`. |Não (se for especificado o nome da **recolha do conjunto** de **dados)** |

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

Para mais informações, consulte o artigo do [conector MongoDB](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado amazon S3, defina o **tipo** de serviço ligado ao **AwsAccessKey,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| acessoKeyID |Identificação da chave de acesso secreta. |string |Sim |
| secretAccessKey |A chave de acesso secreta em si. |Corda secreta encriptada |Sim |

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

Para mais informações, consulte o artigo do [conector Amazon S3.](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Amazon S3, defina o **tipo** de conjunto de dados para **o AmazonS3**, e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| bucketName |O nome do balde S3. |String |Sim |
| key |A chave de objetos S3. |String |Não |
| prefixo |Prefixo para a tecla de objeto S3. São selecionados objetos cujas teclas começam com este prefixo. Aplica-se apenas quando a chave está vazia. |String |Não |
| versão |A versão do objeto S3 se a versão S3 estiver ativada. |String |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [Formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída. |Não | |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**. Os níveis suportados são: **Optimal** e **Fastest**. Para mais informações, consulte [formatos de ficheiroe de compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não | |


> [!NOTE]
> bucketName + tecla especifica a localização do objeto S3 onde o balde é o recipiente de raiz para objetos S3 e a chave é o caminho completo para o objeto S3.

#### <a name="example-sample-dataset-with-prefix"></a>Exemplo: Conjunto de dados da amostra com prefixo

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
#### <a name="example-sample-data-set-with-version"></a>Exemplo: Conjunto de dados de amostra (com versão)

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

#### <a name="example-dynamic-paths-for-s3"></a>Exemplo: Caminhos dinâmicos para S3
Na amostra, utilizamos valores fixos para propriedades chave e bucketName no conjunto de dados Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Pode ter data Factory a calcular a chave e o bucketName dinamicamente no tempo de funcionar, utilizando variáveis do sistema como o SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Pode fazer o mesmo pela propriedade prefixo de um conjunto de dados Amazon S3. Consulte [as funções da Data Factory e as variáveis](data-factory-functions-variables.md) do sistema para obter uma lista de funções e variáveis suportadas.

Para mais informações, consulte o artigo do [conector Amazon S3.](data-factory-amazon-simple-storage-service-connector.md#dataset-properties)

### <a name="file-system-source-in-copy-activity"></a>Fonte do sistema de ficheiros na atividade da cópia
Se estiver a copiar dados do Amazon S3, detete o tipo de **origem** da atividade de cópia para **FileSystemSource**, e especifique as seguintes propriedades na secção **fonte:**


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Especifica se lista risivamente os objetos S3 sob o diretório. |verdadeiro/falso |Não |


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

Para mais informações, consulte o artigo do [conector Amazon S3.](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties)

## <a name="file-system"></a>Sistema de Ficheiros


### <a name="linked-service"></a>Serviço ligado
Pode ligar um sistema de ficheiros no local a uma fábrica de dados Azure com o serviço ligado ao **Servidor de Ficheiros No local.** A tabela seguinte fornece descrições para elementos JSON específicos do serviço ligado ao Servidor de Ficheiros No local.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |Certifique-se de que a propriedade do tipo está definida para **onPremisesFileServer**. |Sim |
| anfitrião |Especifica o caminho raiz da pasta que pretende copiar. Use o personagem de fuga ' \ ' para caracteres especiais na corda. Consulte as definições de serviço ligados à amostra e definições de conjunto de dados, por exemplo. |Sim |
| usado |Especifique a identificação do utilizador que tem acesso ao servidor. |Não (se escolher o Credential encriptado) |
| palavra-passe |Especifique a palavra-passe para o utilizador (userid). |Não (se escolher o Credential encriptado |
| credenta encriptado |Especifique as credenciais encriptadas que pode obter executando o cmdlet New-AzDataFactoryEncryptValue. |Não (se optar por especificar userid e palavra-passe em texto simples) |
| nome gateway |Especifica o nome do portal que a Data Factory deve utilizar para se ligar ao servidor de ficheiros no local. |Sim |

#### <a name="sample-folder-path-definitions"></a>Definições de caminho de pasta de amostra

| Cenário | Anfitrião na definição de serviço ligado | pastaCaminho na definição de conjunto de dados |
| --- | --- | --- |
| Pasta local na máquina Gateway de Gestão de Dados: <br/><br/>Exemplos: D:\\ \* ou D:\folder\subpasta\\* |D:\\ \\ (para data Management Gateway 2.0 e versões posteriores) <br/><br/> localhost (para versões anteriores do que Gateway 2.0 de Gestão de Dados) |. ou\\\\subpasta de pasta (para data Management Gateway 2.0 e versões posteriores) \\ \\ <br/><br/>D:\\ \\ ou\\\\D: subpasta de pasta\\\\(para versão gateway abaixo de 2.0) |
| Pasta partilhada remota: <br/><br/>Exemplos: \\ \\\\myserver\\ \* \\ \\share\\ou\\\\myserver share folder subfolder\\* |\\\\\\\\myserver\\\\partilhar |. ou\\subpasta de pasta\\ \\ \\ |


#### <a name="example-using-username-and-password-in-plain-text"></a>Exemplo: Usar o nome de utilizador e a palavra-passe em texto simples

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

#### <a name="example-using-encryptedcredential"></a>Exemplo: Utilização de um cedcessão encriptado

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

Para mais informações, consulte o [artigo do conector](data-factory-onprem-file-system-connector.md#linked-service-properties)do Sistema de Ficheiros .

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Sistema de Ficheiros, defina o **tipo** do conjunto de dados para **FileShare,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Especifica o subcaminho para a pasta. Use o personagem de fuga '\' para caracteres especiais na corda. Consulte as definições de serviço ligados à amostra e definições de conjunto de dados, por exemplo.<br/><br/>Pode combinar esta propriedade com **partiçãoBy** para ter caminhos de pastas baseados em datas de início/fim de fatias. |Sim |
| fileName |Especifique o nome do ficheiro na **pastaPath** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando o nome do ficheiro não é especificado para um conjunto de dados de saída, o nome do ficheiro gerado está no seguinte formato: <br/><br/>`Data.<Guid>.txt`(Exemplo: Dados.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro a utilizar para selecionar um subconjunto de ficheiros na pastaPath em vez de todos os ficheiros. <br/><br/>Os valores `*` permitidos são: (múltiplos caracteres) e `?` (personagem único).<br/><br/>Exemplo 1: "fileFilter": "*.log"<br/>Exemplo 2: "fileFilter": 2016-1-?. txt "<br/><br/>Note que o filtro de ficheiros é aplicável para um conjunto de dados de FileShare de entrada. |Não |
| divididoBy |Pode utilizar o divididoBy para especificar uma pasta dinâmicaPath/fileName para dados da série de tempo. Um exemplo é a pastaPath parametrizada para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [Formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2**e **ZipDeflate;** e os níveis suportados são: **Ideal** e **Mais Rápido**. ver [formatos de ficheiros e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> Não é possível utilizar o nome de ficheiro e o ficheiroFilter simultaneamente.

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

Para mais informações, consulte o [artigo do conector](data-factory-onprem-file-system-connector.md#dataset-properties)do Sistema de Ficheiros .

### <a name="file-system-source-in-copy-activity"></a>Fonte do sistema de ficheiros na atividade da cópia
Se estiver a copiar dados do Sistema de Ficheiros, delineie o tipo de **origem** da atividade de cópia para **FileSystemSource**e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. |Verdade, Falso (padrão) |Não |

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
Para mais informações, consulte o [artigo do conector](data-factory-onprem-file-system-connector.md#copy-activity-properties)do Sistema de Ficheiros .

### <a name="file-system-sink-in-copy-activity"></a>Sumidouro do sistema de ficheiros na atividade da cópia
Se estiver a copiar dados para o Sistema de Ficheiros, delineie o tipo de **sumidouro** da atividade de cópia para **FileSystemSink**, e especifique as seguintes propriedades na secção **do lavatório:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copiarComportamento |Define o comportamento da cópia quando a fonte é BlobSource ou FileSystem. |**PreserveHierarchy:** Preserva a hierarquia dos ficheiros na pasta-alvo. Ou seja, o caminho relativo do ficheiro fonte para a pasta fonte é o mesmo que o caminho relativo do ficheiro alvo para a pasta-alvo.<br/><br/>**Hierarquia do Achatamento:** Todos os ficheiros da pasta fonte são criados no primeiro nível da pasta alvo. Os ficheiros-alvo são criados com um nome autogerado.<br/><br/>**Ficheiros de fusão:** Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro/nome blob for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, trata-se de um nome de ficheiro gerado automaticamente. |Não |

auto-

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

Para mais informações, consulte o [artigo do conector](data-factory-onprem-file-system-connector.md#copy-activity-properties)do Sistema de Ficheiros .

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado ftp, defina o **tipo** de serviço ligado ao **FtpServer**e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| anfitrião |Nome ou endereço IP do Servidor FTP |Sim |&nbsp; |
| authenticationType |Especificar o tipo de autenticação |Sim |Básico, Anónimo |
| o nome de utilizador |Utilizador que tenha acesso ao servidor FTP |Não |&nbsp; |
| palavra-passe |Palavra-passe para o utilizador (nome de utilizador) |Não |&nbsp; |
| credenta encriptado |Credencial encriptada para aceder ao servidor FTP |Não |&nbsp; |
| nome gateway |Nome do Portal de Gestão de Dados para ligar a um servidor FTP no local |Não |&nbsp; |
| porta |Porta em que o servidor FTP está a ouvir |Não |21 |
| enableSsl |Especificar se utilizar ftp sobre o canal SSL/TLS |Não |true |
| permitirServerCertificateValidação |Especifique se activaa a validação do certificado TLS/SSL do servidor ao utilizar o FTP através do canal SSL/TLS |Não |true |

#### <a name="example-using-anonymous-authentication"></a>Exemplo: Utilização da autenticação anónima

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

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Exemplo: Utilização do nome de utilizador e da palavra-passe em texto simples para autenticação básica

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

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Exemplo: Utilização da porta, enableSsl, enableServerCertificateValidação

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

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Exemplo: Utilização de Credential encriptado para autenticação e gateway

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

Para mais informações, consulte o artigo do [conector FTP.](data-factory-ftp-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados FTP, defina o **tipo** do conjunto de dados para **FileShare,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Sub caminho para a pasta. Use o personagem de fuga ' \ ' para caracteres especiais na corda. Consulte as definições de serviço ligados à amostra e definições de conjunto de dados, por exemplo.<br/><br/>Pode combinar esta propriedade com **partiçãoBy** para ter caminhos de pastas baseados em datas de início/fim de fatias. |Sim
| fileName |Especifique o nome do ficheiro na **pastaPath** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando o nome do ficheiro não é especificado para um conjunto de dados de saída, o nome do ficheiro gerado estaria no seguinte formato: <br/><br/>`Data.<Guid>.txt`(Exemplo: Dados.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro a utilizar para selecionar um subconjunto de ficheiros na pastaPath em vez de todos os ficheiros.<br/><br/>Os valores `*` permitidos são: (múltiplos caracteres) e `?` (personagem único).<br/><br/>Exemplos 1:`"fileFilter": "*.log"`<br/>Exemplo 2:`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter é aplicável para um conjunto de dados de FileShare de entrada. Esta propriedade não é suportada com HDFS. |Não |
| divididoBy |a divisãoBy pode ser usada para especificar uma pasta dinâmicaPath, nome de ficheiro para dados da série de tempo. Por exemplo, pastaPath parametrizado para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [Formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2**e **ZipDeflate;** e os níveis suportados são: **Ideal** e **Mais Rápido**. Para mais informações, consulte [formatos de ficheiroe de compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se utilizar o modo de transferência Binário. Verdadeiro para o modo binário e falso ASCII. Valor padrão: Verdadeiro. Esta propriedade só pode ser utilizada quando o tipo de serviço associado é de tipo: FtpServer. |Não |

> [!NOTE]
> nome de ficheiro e ficheiroSO filtro não pode ser utilizado simultaneamente.

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

Para mais informações, consulte o artigo do [conector FTP.](data-factory-ftp-connector.md#dataset-properties)

### <a name="file-system-source-in-copy-activity"></a>Fonte do sistema de ficheiros na atividade da cópia
Se estiver a copiar dados de um servidor FTP, detete o tipo de **origem** da atividade de cópia para **FileSystemSource**e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. |Verdade, Falso (padrão) |Não |

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

Para mais informações, consulte o artigo do [conector FTP.](data-factory-ftp-connector.md#copy-activity-properties)


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado ao HDFS, defina o **tipo** de serviço ligado ao **Hdfs,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **Hdfs** |Sim |
| Url |URL para o HDFS |Sim |
| authenticationType |Anónimo, ou Windows. <br><br> Para utilizar a **autenticação Kerberos** para o conector HDFS, consulte esta secção para configurar o seu ambiente no local em conformidade. |Sim |
| userName |Nome de utilizador para autenticação do Windows. |Sim (para autenticação do Windows) |
| palavra-passe |Palavra-passe para autenticação do Windows. |Sim (para autenticação do Windows) |
| nome gateway |Nome do portal que o serviço Data Factory deve utilizar para ligar ao HDFS. |Sim |
| credenta encriptado |[Saída new-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) da credencial de acesso. |Não |

#### <a name="example-using-anonymous-authentication"></a>Exemplo: Utilização da autenticação anónima

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

#### <a name="example-using-windows-authentication"></a>Exemplo: Utilização da autenticação do Windows

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

Para mais informações, consulte o artigo do conector HDFS.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados HDFS, defina o **tipo** do conjunto de dados para **FileShare,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para a pasta. Exemplo: `myfolder`<br/><br/>Use o personagem de fuga ' \ ' para caracteres especiais na corda. Por exemplo: para a subpasta\\\\pasta da pasta,especificar subpasta da\\\\pasta e para a pasta d:\samplefolder, especificar d: pasta de amostras.<br/><br/>Pode combinar esta propriedade com **partiçãoBy** para ter caminhos de pastas baseados em datas de início/fim de fatias. |Sim |
| fileName |Especifique o nome do ficheiro na **pastaPath** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando o nome do ficheiro não é especificado para um conjunto de dados de saída, o nome do ficheiro gerado estaria no seguinte formato: <br/><br/>`Data.<Guid>.txt`(por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| divididoBy |a divisãoBy pode ser usada para especificar uma pasta dinâmicaPath, nome de ficheiro para dados da série de tempo. Exemplo: pastaPath parametrizado para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [Formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**. Os níveis suportados são: **Optimal** e **Fastest**. Para mais informações, consulte [formatos de ficheiroe de compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> nome de ficheiro e ficheiroSO filtro não pode ser utilizado simultaneamente.

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

Para mais informações, consulte o artigo do conector HDFS.

### <a name="file-system-source-in-copy-activity"></a>Fonte do sistema de ficheiros na atividade da cópia
Se estiver a copiar dados do HDFS, defina o **tipo** de origem da atividade de cópia para **FileSystemSource,** e especifique as seguintes propriedades na secção **fonte:**

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. |Verdade, Falso (padrão) |Não |

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

Para mais informações, consulte o artigo do conector HDFS.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado sFTP, defina o **tipo** de serviço ligado à **Sftp,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| anfitrião | Nome ou endereço IP do servidor SFTP. |Sim |
| porta |Porta na qual o servidor SFTP está a ouvir. O valor padrão é: 21 |Não |
| authenticationType |Especificar tipo de autenticação. Valores permitidos: **Básico,** **SshPublicKey**. <br><br> Consulte a utilização de autenticações básicas e utilização de secções de [autenticação de chaves públicas SSH](#using-ssh-public-key-authentication) em mais propriedades e amostras JSON, respectivamente. |Sim |
| skipHostKeyValidação | Especifique se saltar a validação da chave do hospedeiro. | Não. O valor padrão: falso |
| anfitriãoKeyFingerprint | Especifique a impressão do dedo da chave hospedeira. | Sim, `skipHostKeyValidation` se o for falso.  |
| nome gateway |Nome do Portal de Gestão de Dados para ligar a um servidor SFTP no local. | Sim, se copiar dados de um servidor SFTP no local. |
| credenta encriptado | Credencial encriptada para aceder ao servidor SFTP. Gerado automaticamente quando especifica a autenticação básica (nome de utilizador + palavra-passe) ou autenticação SshPublicKey (nome de utilizador + caminho ou conteúdo privado) no assistente de cópia ou no diálogo popup ClickOnce. | Não. Aplicar apenas ao copiar dados de um servidor SFTP no local. |

#### <a name="example-using-basic-authentication"></a>Exemplo: Utilização da autenticação básica

Para utilizar a autenticação básica, definida `authenticationType` como `Basic`, e especificar as seguintes propriedades para além das genéricas do conector SFTP introduzidas na última secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| o nome de utilizador | Utilizador que tenha acesso ao servidor SFTP. |Sim |
| palavra-passe | Palavra-passe para o utilizador (nome de utilizador). | Sim |

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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exemplo: **Autenticação básica com credencial encriptada**

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

#### <a name="using-ssh-public-key-authentication"></a>**Utilização da autenticação da chave pública SSH:**

Para utilizar a autenticação básica, definida `authenticationType` como `SshPublicKey`, e especificar as seguintes propriedades para além das genéricas do conector SFTP introduzidas na última secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| o nome de utilizador |Utilizador que tenha acesso ao servidor SFTP |Sim |
| privateKeyPath | Especifique o caminho absoluto para o ficheiro chave privado a que o gateway pode aceder. | Especifique ou o `privateKeyPath` `privateKeyContent`. <br><br> Aplicar apenas ao copiar dados de um servidor SFTP no local. |
| privateKeyContent | Uma sequência serializada do conteúdo da chave privada. O Copy Wizard pode ler o ficheiro de chave privada e extrair automaticamente o conteúdo da chave privada. Se estiver a utilizar qualquer outra ferramenta/SDK, utilize a propriedade privateKeyPath. | Especifique ou o `privateKeyPath` `privateKeyContent`. |
| passFrase | Especifique a frase/palavra-passe de passe para desencriptar a chave privada se o ficheiro chave estiver protegido por uma frase de passe. | Sim, se o ficheiro da chave privada estiver protegido por uma frase de passe. |

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exemplo: **Autenticação SShPublicKey utilizando conteúdo de chave privada**

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

Para mais informações, consulte o artigo do [conector SFTP.](data-factory-sftp-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados SFTP, defina o **tipo** do conjunto de dados para **FileShare,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Sub caminho para a pasta. Use o personagem de fuga ' \ ' para caracteres especiais na corda. Consulte as definições de serviço ligados à amostra e definições de conjunto de dados, por exemplo.<br/><br/>Pode combinar esta propriedade com **partiçãoBy** para ter caminhos de pastas baseados em datas de início/fim de fatias. |Sim |
| fileName |Especifique o nome do ficheiro na **pastaPath** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando o nome do ficheiro não é especificado para um conjunto de dados de saída, o nome do ficheiro gerado estaria no seguinte formato: <br/><br/>`Data.<Guid>.txt`(Exemplo: Dados.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro a utilizar para selecionar um subconjunto de ficheiros na pastaPath em vez de todos os ficheiros.<br/><br/>Os valores `*` permitidos são: (múltiplos caracteres) e `?` (personagem único).<br/><br/>Exemplos 1:`"fileFilter": "*.log"`<br/>Exemplo 2:`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter é aplicável para um conjunto de dados de FileShare de entrada. Esta propriedade não é suportada com HDFS. |Não |
| divididoBy |a divisãoBy pode ser usada para especificar uma pasta dinâmicaPath, nome de ficheiro para dados da série de tempo. Por exemplo, pastaPath parametrizado para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [Formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**. Os níveis suportados são: **Optimal** e **Fastest**. Para mais informações, consulte [formatos de ficheiroe de compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se utilizar o modo de transferência Binário. Verdadeiro para o modo binário e falso ASCII. Valor padrão: Verdadeiro. Esta propriedade só pode ser utilizada quando o tipo de serviço associado é de tipo: FtpServer. |Não |

> [!NOTE]
> nome de ficheiro e ficheiroSO filtro não pode ser utilizado simultaneamente.

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

Para mais informações, consulte o artigo do [conector SFTP.](data-factory-sftp-connector.md#dataset-properties)

### <a name="file-system-source-in-copy-activity"></a>Fonte do sistema de ficheiros na atividade da cópia
Se estiver a copiar dados de uma fonte SFTP, delineie o tipo de **origem** da atividade de cópia para **FileSystemSource**e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. |Verdade, Falso (padrão) |Não |



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

Para mais informações, consulte o artigo do [conector SFTP.](data-factory-sftp-connector.md#copy-activity-properties)


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado http, defina o **tipo** de serviço ligado a **Http,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| url | URL base para o Servidor Web | Sim |
| authenticationType | Especifica o tipo de autenticação. Os valores permitidos são: **Anónimo,** **Básico,** **Digest,** **Windows,** **ClientCertificate**. <br><br> Consulte as secções abaixo desta tabela sobre mais propriedades e amostras JSON para esses tipos de autenticação, respectivamente. | Sim |
| permitirServerCertificateValidação | Especifique se ativar a validação do certificado TLS/SSL do servidor se a fonte for HTTPS Web Server | Não, o padrão é verdade. |
| nome gateway | Nome do Portal de Gestão de Dados para ligar a uma fonte http no local. | Sim, se copiar dados de uma fonte http no local. |
| credenta encriptado | Credencial encriptada para aceder ao ponto final http. Gerado automaticamente quando configurar as informações de autenticação no assistente de cópia ou no diálogo popup ClickOnce. | Não. Aplicar apenas ao copiar dados de um servidor HTTP no local. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Exemplo: Utilização de autenticação básica, digestiva ou windows
Definir `authenticationType` `Basic`como, `Digest` `Windows`ou , e especificar as seguintes propriedades para além das genéricas do conector HTTP introduzidas acima:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| o nome de utilizador | Nome de utilizador para aceder ao ponto final http. | Sim |
| palavra-passe | Palavra-passe para o utilizador (nome de utilizador). | Sim |

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

#### <a name="example-using-clientcertificate-authentication"></a>Exemplo: Utilização da autenticação do ClientCertificate

Para utilizar a autenticação básica, definida `authenticationType` como `ClientCertificate`, e especificar as seguintes propriedades para além das genéricas do conector HTTP introduzidas acima:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| embeddedCertData | O conteúdo codificado pela Base64 de dados binários do ficheiro Personal Information Exchange (PFX). | Especifique ou o `embeddedCertData` `certThumbprint`. |
| certThumbprint | A impressão digital do certificado que foi instalado na loja cert da sua máquina de gateway. Aplicar apenas ao copiar dados de uma fonte http no local. | Especifique ou o `embeddedCertData` `certThumbprint`. |
| palavra-passe | Senha associada ao certificado. | Não |

Se utilizar `certThumbprint` para autenticação e o certificado estiver instalado na loja pessoal do computador local, tem de conceder a permissão de leitura ao serviço gateway:

1. Lançar consola de gestão microsoft (MMC). Adicione o encaixe dos **Certificados** que visa o **Computador Local**.
2. Expandir **Certificados,** **Pessoais,** e clicar **em Certificados.**
3. Clique no certificado da loja pessoal e selecione Todas as Tarefas Gerir Chaves **Privadas...**->**Manage Private Keys...**
3. No separador **Segurança,** adicione a conta de utilizador sob a qual o Serviço de Hospedaria gateway de gestão de dados está a funcionar com o acesso de leitura ao certificado.

**Exemplo: utilização de certificado** de cliente: Este serviço ligado liga a sua fábrica de dados a um servidor web HTTP no local. Utiliza um certificado de cliente instalado na máquina com gateway de gestão de dados instalado.

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

#### <a name="example-using-client-certificate-in-a-file"></a>Exemplo: utilização de certificado de cliente num ficheiro
Este serviço ligado liga a sua fábrica de dados a um servidor web HTTP no local. Utiliza um ficheiro de certificado de cliente na máquina com gateway de gestão de dados instalado.

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

Para mais informações, consulte o artigo do [conector HTTP.](data-factory-http-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados HTTP, defina o **tipo** do conjunto de dados para **Http,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| parenteUrl | Um URL relativo ao recurso que contém os dados. Quando o caminho não é especificado, apenas é utilizado o URL especificado na definição de serviço ligado. <br><br> Para construir URL dinâmico, pode utilizar funções de Fábrica `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`de Dados e [variáveis do sistema,](data-factory-functions-variables.md)Exemplo: . | Não |
| solicitarMétodo | Método http. Os valores permitidos são **GET** ou **POST**. | Não. A predefinição é `GET`. |
| cabeçalhos adicionais | Cabeçalhos adicionais de pedido HTTP. | Não |
| requestBody | Corpo para pedido http. | Não |
| formato | Se pretender simplesmente **recuperar os dados do ponto final http como está** sem os analisar, ignore as definições deste formato. <br><br> Se pretender analisar o conteúdo de resposta HTTP durante a cópia, são suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat,** **OrcFormat,** **ParquetFormat**. Para mais informações, consulte as secções de [Formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [Formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**. Os níveis suportados são: **Optimal** e **Fastest**. Para mais informações, consulte [formatos de ficheiroe de compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

#### <a name="example-using-the-get-default-method"></a>Exemplo: utilização do método GET (padrão)

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

#### <a name="example-using-the-post-method"></a>Exemplo: utilização do método POST

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
Para mais informações, consulte o artigo do [conector HTTP.](data-factory-http-connector.md#dataset-properties)

### <a name="http-source-in-copy-activity"></a>HTTP Fonte na Atividade de Cópia
Se estiver a copiar dados de uma fonte http, delineie o tipo de **origem** da atividade de cópia para **httpSource**e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| httpRequestTimeout | O prazo (TimeSpan) para o pedido http para obter uma resposta. É o tempo livre para obter uma resposta, não o tempo para ler os dados de resposta. | Não. Valor padrão: 00:01:40 |


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

Para mais informações, consulte o artigo do [conector HTTP.](data-factory-http-connector.md#copy-activity-properties)

## <a name="odata"></a>OData

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado ao OData, defina o **tipo** de serviço ligado ao **OData**e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| url |Url do serviço OData. |Sim |
| authenticationType |Tipo de autenticação utilizada para ligar à fonte OData. <br/><br/> Para o OData em nuvem, os valores possíveis são Anónimos, Básicos e OAuth (nota Azure Data Factory atualmente apenas suporta o OAuth baseado em Diretório Ativo Azure). <br/><br/> Para o OData no local, os valores possíveis são Anónimos, Básicos e Windows. |Sim |
| o nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Básica. |Sim (apenas se estiver a usar a autenticação básica) |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Sim (apenas se estiver a usar a autenticação básica) |
| credenciadoCredential |Se estiver a utilizar o OAuth, clique em **autorizar** o botão no Data Factory Copy Wizard ou Editor e introduza a sua credencial, então o valor desta propriedade será gerado automaticamente. |Sim (apenas se estiver a usar a autenticação OAuth) |
| nome gateway |Nome do portal que o serviço Data Factory deve utilizar para ligar ao serviço OData no local. Especificar apenas se estiver a copiar dados a partir de origem OData no local. |Não |

#### <a name="example---using-basic-authentication"></a>Exemplo - Utilização da autenticação básica
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

#### <a name="example---using-anonymous-authentication"></a>Exemplo - Utilização da autenticação anónima

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

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Exemplo - Utilização da autenticação do Windows a aceder à fonte OData no local

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

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Exemplo - Utilização da autenticação OAuth a aceder à nuvem Fonte OData
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

Para mais informações, consulte o artigo [do conector OData.](data-factory-odata-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados OData, defina o **tipo** do conjunto de dados para **ODataResource,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| path |Caminho para o recurso OData |Não |

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

Para mais informações, consulte o artigo [do conector OData.](data-factory-odata-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte Relacional na Atividade de Cópia
Se estiver a copiar dados de uma fonte OData, delineie o **tipo** de origem da atividade de cópia para **o RelationalSource**e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Exemplo | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler dados. |"?$select=Nome, Descrição&$top=5" |Não |

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

Para mais informações, consulte o artigo [do conector OData.](data-factory-odata-connector.md#copy-activity-properties)


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à ODBC, defina o **tipo** de serviço ligado ao **OnPremisesOdbc,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| conexãoString |A parte credencial de não acesso da cadeia de ligação e uma credencial encriptada opcional. Consulte exemplos nas seguintes secções. |Sim |
| credencial |A parte credencial de acesso da cadeia de ligação especificada no formato de valor de propriedade específico do condutor. Exemplo: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Não |
| authenticationType |Tipo de autenticação utilizada para se ligar à loja de dados ODBC. Os valores possíveis são: Anónimos e Básicos. |Sim |
| o nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Básica. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |
| nome gateway |Nome do portal que o serviço Data Factory deve utilizar para ligar à loja de dados ODBC. |Sim |

#### <a name="example---using-basic-authentication"></a>Exemplo - Utilização da autenticação básica

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
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Exemplo - Utilização da autenticação básica com credenciais encriptadas
Pode encriptar as credenciais utilizando o [cmdlet New-AzDataFactoryEncryptValue.](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue)

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

#### <a name="example-using-anonymous-authentication"></a>Exemplo: Utilização da autenticação anónima

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

Para mais informações, consulte o artigo do [conector ODBC.](data-factory-odbc-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados ODBC, defina o **tipo** do conjunto de dados para **O TrelaTable,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na loja de dados ODBC. |Sim |


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

Para mais informações, consulte o artigo do [conector ODBC.](data-factory-odbc-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte Relacional na Atividade de Cópia
Se estiver a copiar dados de uma loja de dados da ODBC, delineie o **tipo** de origem da atividade de cópia para **o RelationalSource**e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Por exemplo: `select * from MyTable`. |Sim |

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

Para mais informações, consulte o artigo do [conector ODBC.](data-factory-odbc-connector.md#copy-activity-properties)

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Salesforce, defina o **tipo** de serviço ligado à **Salesforce**e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| ambienteUrl | Especifique o URL da instância Salesforce. <br><br> - O predefinição é "https:\//login.salesforce.com". <br> - Para copiar dados da caixahttps://test.salesforce.comde areia, especifique ". <br> - Para copiar dados de domínio personalizado, especifique, por exemplo, "https://[domain].my.salesforce.com". |Não |
| o nome de utilizador |Especifique um nome de utilizador para a conta de utilizador. |Sim |
| palavra-passe |Especifique uma palavra-passe para a conta de utilizador. |Sim |
| segurançaToken |Especifique um sinal de segurança para a conta de utilizador. Consulte a ficha de [segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para obter instruções sobre como redefinir/obter um sinal de segurança. Para saber sobre fichas de segurança em geral, consulte [a Segurança e a API.](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) |Sim |

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

Para mais informações, consulte o artigo do [conector Salesforce.](data-factory-salesforce-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados salesforce, defina o **tipo** do conjunto de dados para **RelationalTable,** e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da mesa na Salesforce. |Não (se for especificada uma **consulta** de **RelationalSource)** |

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

Para mais informações, consulte o artigo do [conector Salesforce.](data-factory-salesforce-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte Relacional na Atividade de Cópia
Se estiver a copiar dados da Salesforce, delineie o **tipo** de origem da atividade de cópia para **o RelationalSource**e especifique as seguintes propriedades na secção **fonte:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler dados. |Uma consulta SQL-92 ou linguagem de consulta de [objetos salesforce (SOQL).](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) Por exemplo: `select * from MyTable__c`. |Não (se o nome do **conjunto** de **dados** for especificado) |

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
> A parte "__c" do Nome API é necessária para qualquer objeto personalizado.

Para mais informações, consulte o artigo do [conector Salesforce.](data-factory-salesforce-connector.md#copy-activity-properties)

## <a name="web-data"></a>Dados Web

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Web, defina o **tipo** de serviço ligado à **Web**e especifique as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Url |URL para a fonte web |Sim |
| authenticationType |Anónimo. |Sim |


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

Para mais informações, consulte o artigo do [conector da Web Table.](data-factory-web-table-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados web, defina o **tipo** do conjunto de dados para **WebTable,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |tipo de conjunto de dados. deve ser definido para **WebTable** |Sim |
| path |Um URL relativo ao recurso que contém a tabela. |Não. Quando o caminho não é especificado, apenas é utilizado o URL especificado na definição de serviço ligado. |
| índice |O índice da tabela no recurso. Consulte o índice de uma tabela numa página HTML para obter passos para obter o índice de uma tabela numa página HTML. |Sim |

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

Para mais informações, consulte o artigo do [conector da Web Table.](data-factory-web-table-connector.md#dataset-properties)

### <a name="web-source-in-copy-activity"></a>Fonte Web na Atividade de Cópia
Se estiver a copiar dados de uma tabela web, delineie o tipo de **origem** da atividade de cópia para **o WebSource**. Atualmente, quando a fonte na atividade de cópia é do tipo **WebSource,** não são suportadas propriedades adicionais.

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

Para mais informações, consulte o artigo do [conector da Web Table.](data-factory-web-table-connector.md#copy-activity-properties)

## <a name="compute-environments"></a>AMBIENTES COMPUTADOS
A tabela seguinte lista os ambientes computacionais suportados pela Data Factory e as atividades de transformação que podem decorrer sobre eles. Clique no link para a computação em que está interessado em ver os schemas JSON para o serviço ligado para ligá-lo a uma fábrica de dados.

| Ambiente de computação | Atividades |
| --- | --- |
| [Cluster HDInsight a pedido](#on-demand-azure-hdinsight-cluster) ou [o seu próprio cluster HDInsight](#existing-azure-hdinsight-cluster) |[.NET atividade personalizada](#net-custom-activity), [Atividade da Colmeia,](#hdinsight-hive-activity) [Atividade do porco,](#hdinsight-pig-activity) [MapReduce atividade,](#hdinsight-mapreduce-activity)atividade de streaming hadoop, [atividade de faísca](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[Atividade personalizada do .NET](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Atividade de execução de lotes de aprendizagem automática,](#machine-learning-batch-execution-activity)atividade de recursos de [atualização de aprendizagem automática](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Base de Dados Azure SQL,](#azure-sql-database)Armazém de [Dados Azure SQL,](#azure-sql-data-warehouse) [Servidor SQL](#sql-server-stored-procedure) |[Procedimento Armazenado](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Cluster Azure HDInsight a pedido
O serviço Azure Data Factory pode criar automaticamente um cluster HDInsight baseado no Windows/Linux para processar dados. O cluster é criado na mesma região que a conta de armazenamento (propriedade linkedServiceName no JSON) associada ao cluster. Pode executar as seguintes atividades de transformação neste serviço ligado: [.NET atividade personalizada,](#net-custom-activity) [Atividade da Colmeia,](#hdinsight-hive-activity) [Atividade do porco,](#hdinsight-pig-activity) [Atividade MapReduce,](#hdinsight-mapreduce-activity)Atividade de streaming hadoop, [atividade spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição Azure JSON de um serviço ligado ao HDInsight a pedido.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade do tipo deve ser definida para **HDInsightOnDemand**. |Sim |
| clusterSize |Número de nós de trabalhador/dados no cluster. O cluster HDInsight é criado com 2 nós de cabeça juntamente com o número de nós de trabalhador que especifica para esta propriedade. Os nós são de tamanho Standard_D3 que tem 4 núcleos, por isso um\*cluster de nó de 4 trabalhadores\*leva 24 núcleos (4 4 = 16 núcleos para nós operários, mais 2 4 = 8 núcleos para nós de cabeça). Consulte os [clusters Hadoop baseados em Linux no HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) para obter detalhes sobre o nível Standard_D3. |Sim |
| timetolive |O tempo de inatividade permitido para o cluster HDInsight a pedido. Especifica quanto tempo o cluster HDInsight a pedido permanece vivo após a conclusão de uma atividade executada se não houver outros empregos ativos no cluster.<br/><br/>Por exemplo, se uma execução de atividade demorar 6 minutos e o tempo de vida for programado para 5 minutos, o cluster permanece vivo durante 5 minutos após os 6 minutos de processamento da atividade. Se outra execução de atividade for executada com a janela de 6 minutos, é processada pelo mesmo cluster.<br/><br/>Criar um cluster HDInsight a pedido é uma operação dispendiosa (pode demorar algum tempo), por isso use esta definição conforme necessário para melhorar o desempenho de uma fábrica de dados, reutilizando um cluster HDInsight a pedido.<br/><br/>Se definir o valor de tempo para viver para 0, o cluster é eliminado assim que a atividade é executada em processo. Por outro lado, se definir um valor elevado, o cluster pode permanecer inativo desnecessariamente resultando em custos elevados. Por isso, é importante que detetete o valor adequado com base nas suas necessidades.<br/><br/>Vários oleodutos podem partilhar a mesma instância do cluster HDInsight a pedido se o valor da propriedade timetolive for devidamente definido |Sim |
| versão |Versão do cluster HDInsight. Para mais detalhes, consulte [as versões HDInsight suportadas na Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Não |
| linkedServiceName |Serviço ligado ao Armazenamento Azure a utilizar pelo cluster a pedido para armazenar e processar dados. <p>Atualmente, não é possível criar um cluster HDInsight a pedido que utiliza uma Loja de Lagos De Dados Azure como armazenamento. Se pretender armazenar os dados do resultado do processamento da HDInsight numa Loja de Lagos De Dados Azure, utilize uma Atividade de Cópia para copiar os dados do Armazenamento de Blob Azure para a Loja do Lago de Dados Azure.</p>  | Sim |
| nomes adicionais LinkedServiceNames |Especifica contas de armazenamento adicionais para o serviço ligado ao HDInsight para que o serviço Data Factory as possa registar em seu nome. |Não |
| osType |Tipo de sistema operativo. Os valores permitidos são: Windows (predefinido) e Linux |Não |
| hcatalogLinkedServiceName |O nome do serviço ligado ao Azure SQL que aponta para a base de dados Do HCatalog. O cluster HDInsight a pedido é criado utilizando a base de dados Azure SQL como metaloja. |Não |

### <a name="json-example"></a>Exemplo jSON
O seguinte JSON define um serviço ligado hDInsight baseado em Linux. O serviço Data Factory cria automaticamente um cluster HDInsight **baseado em Linux** ao processar uma fatia de dados.

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

Para mais informações, consulte o artigo [dos serviços ligados à Compute.](data-factory-compute-linked-services.md)

## <a name="existing-azure-hdinsight-cluster"></a>Cluster Azure HDInsight existente
Pode criar um serviço ligado ao Azure HDInsight para registar o seu próprio cluster HDInsight com data factory. Pode executar as seguintes atividades de transformação de dados neste serviço ligado: [.NET atividade personalizada](#net-custom-activity), [Atividade da Colmeia,](#hdinsight-hive-activity) [Atividade de porco,](#hdinsight-pig-activity) [MapReduce atividade,](#hdinsight-mapreduce-activity)atividade de streaming hadoop, [atividade spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição Azure JSON de um serviço ligado ao Azure HDInsight.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade do tipo deve ser definida para **HDInsight**. |Sim |
| clusterUri |O URI do cluster HDInsight. |Sim |
| o nome de utilizador |Especifique o nome do utilizador a utilizar para se ligar a um cluster HDInsight existente. |Sim |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador. |Sim |
| linkedServiceName | Nome do serviço ligado ao Armazenamento Azure que se refere ao armazenamento de blob Azure utilizado pelo cluster HDInsight. <p>Atualmente, não é possível especificar um serviço ligado à Azure Data Lake Store para esta propriedade. Pode aceder a dados na Loja do Lago de Dados Azure a partir de scripts Hive/Pig se o cluster HDInsight tiver acesso à Data Lake Store. </p>  |Sim |

Para versões de clusters HDInsight suportadas, consulte [versões HDInsight suportadas](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

#### <a name="json-example"></a>Exemplo jSON

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
Pode criar um serviço ligado ao Lote Azure para registar um conjunto de máquinas virtuais (VMs) com uma fábrica de dados. Pode executar atividades personalizadas .NET utilizando o Azure Batch ou o Azure HDInsight. Pode executar uma [atividade personalizada .NET](#net-custom-activity) neste serviço ligado.

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição Azure JSON de um serviço ligado ao Lote Azure.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para **AzureBatch**. |Sim |
| accountName |Nome da conta Azure Batch. |Sim |
| acessoChave |Chave de acesso para a conta Azure Batch. |Sim |
| nome da piscina |Nome da piscina de máquinas virtuais. |Sim |
| linkedServiceName |Nome do serviço ligado ao Armazenamento Azure associado a este serviço ligado ao Lote Azure. Este serviço ligado é utilizado para encenar ficheiros necessários para executar a atividade e armazenar os registos de execução da atividade. |Sim |


#### <a name="json-example"></a>Exemplo jSON

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
Cria um serviço ligado à Aprendizagem automática Azure para registar um lote de machine learning que marca ponto final com uma fábrica de dados. Duas atividades de transformação de dados que podem ser executadas neste serviço ligado: Atividade de execução de lotes de [aprendizagem automática,](#machine-learning-batch-execution-activity)Atividade de recursos de atualização de [aprendizagem automática.](#machine-learning-update-resource-activity)

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição Azure JSON de um serviço ligado à Aprendizagem automática Azure.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Tipo |A propriedade tipo deve ser definida para: **AzureML**. |Sim |
| mlEndpoint |O URL de pontuação do lote. |Sim |
| apiKey |A API do modelo de espaço de trabalho publicado. |Sim |

#### <a name="json-example"></a>Exemplo jSON

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
Cria um serviço ligado ao **Azure Data Lake Analytics** para ligar um serviço de computação Azure Data Lake Analytics a uma fábrica de dados Azure antes de utilizar a [atividade U-SQL](data-factory-usql-activity.md) do Data Lake Analytics num oleoduto.

### <a name="linked-service"></a>Serviço ligado

A tabela seguinte fornece descrições para as propriedades utilizadas na definição JSON de um serviço ligado ao Lago de Dados Azure.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Tipo |A propriedade do tipo deve ser definida para: **AzureDataLakeAnalytics**. |Sim |
| accountName |Nome da conta azure Data Lake Analytics. |Sim |
| dadosLakeAnalyticsUri |Azure Data Lake Analytics URI. |Não |
| autorização |O código de autorização é automaticamente recuperado após clicar **em autorizar** o botão no Editor da Fábrica de Dados e completar o login OAuth. |Sim |
| subscriptionId |ID de subscrição azure |Não (se não especificado, utiliza-se a subscrição da fábrica de dados). |
| resourceGroupName |Nome do grupo de recursos do Azure |Não (se não especificado, é utilizado um grupo de recursos da fábrica de dados). |
| sessionId |id da sessão de autorização da OAuth. Cada id da sessão é único e só pode ser usado uma vez. Quando utiliza o Editor da Fábrica de Dados, este ID é gerado automaticamente. |Sim |


#### <a name="json-example"></a>Exemplo jSON
O exemplo que se segue fornece a definição JSON para um serviço ligado ao Lago de Dados Azure.

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

## <a name="sql-server-stored-procedure"></a>Procedimento armazenado por servidor SQL

Cria um serviço ligado ao SQL Server e utiliza-o com a Atividade do [Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado a partir de um pipeline data Factory.

### <a name="linked-service"></a>Serviço ligado
Cria um serviço ligado do tipo **OnPremisesSqlServer** para ligar uma base de dados do SQL Server ao local a uma fábrica de dados. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao Servidor SQL no local.

A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao Servidor SQL.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade do tipo deve ser definida para: **OnPremisesSqlServer**. |Sim |
| conexãoString |Especifique as informações de ligação Necessárias à base de dados do SQL Server no local utilizando a autenticação SQL ou a autenticação do Windows. |Sim |
| nome gateway |Nome do portal que o serviço Data Factory deve utilizar para ligar à base de dados do SQL Server no local. |Sim |
| o nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a Autenticação do Windows. Exemplo: **nome\\de utilizador do nome de domínio**. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |

Pode encriptar credenciais utilizando o cmdlet **New-AzDataFactoryEncryptValue** e utilizá-las na cadeia de ligação, como mostra o seguinte exemplo (propriedade**EncryptedCredential):**

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Exemplo: JSON para utilização de autenticação SQL

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

Se o nome de utilizador e a palavra-passe forem especificados, o gateway utiliza-os para personificar a conta de utilizador especificada para se ligar à base de dados do Servidor SQL no local. Caso contrário, o gateway liga-se diretamente ao Servidor SQL com o contexto de segurança do Gateway (a sua conta de arranque).

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

Para mais informações, consulte o artigo do [conector SQL Server.](data-factory-sqlserver-connector.md#linked-service-properties)

## <a name="data-transformation-activities"></a>ATIVIDADES DE TRANSFORMAÇÃO DE DADOS

Atividade | Descrição
-------- | -----------
[Atividade da Colmeia HDInsight](#hdinsight-hive-activity) | A atividade da Hive HDInsight num pipeline data factory executa consultas de Hive por si só ou a pedido do cluster HDInsight baseado em HDInsight.
[Atividade de porco HDInsight](#hdinsight-pig-activity) | A atividade do Suíno HDInsight num gasoduto data Factory executa consultas de porco por si só ou a pedido do cluster HDInsight baseado em Linux.
[Atividade MapReduce do HDInsight](#hdinsight-mapreduce-activity) | O HDInsight MapReduce atividade num pipeline data factory executa programas MapReduce por si só ou a pedido do cluster HDInsight baseado em HDInsight.
[Atividade Streaming do HDInsight](#hdinsight-streaming-activity) | A Atividade de Streaming HDInsight num pipeline data Factory executa programas de Streaming Hadoop por conta própria ou a pedido do cluster HDInsight baseado em HDInsight baseado em HDInsight.
[Atividade do HDInsight Spark](#hdinsight-spark-activity) | A atividade HDInsight Spark num pipeline data Factory executa programas Spark no seu próprio cluster HDInsight.
[Atividade de Execução em Lote do Machine Learning](#machine-learning-batch-execution-activity) | A Azure Data Factory permite-lhe criar facilmente oleodutos que utilizem um serviço web azure machine learning publicado para análise preditiva. Utilizando a Atividade de Execução de Lotes num oleoduto Azure Data Factory, pode invocar um serviço web de Aprendizagem automática para fazer previsões sobre os dados em lote.
[Atividade de Recursos de Atualização de Machine Learning](#machine-learning-update-resource-activity) | Com o tempo, os modelos preditivos nas experiências de pontuação de Machine Learning precisam de ser retreinados usando novos conjuntos de dados de entrada. Depois de terminar a reconversão, pretende atualizar o serviço web de pontuação com o modelo de Machine Learning retreinado. Pode utilizar a Atividade de Recursos atualizados para atualizar o serviço web com o modelo recém-treinado.
[Atividade de Procedimento Armazenado](#stored-procedure-activity) | Pode utilizar a atividade do Procedimento Armazenado num pipeline data factory para invocar um procedimento armazenado numa das seguintes lojas de dados: Base de Dados Azure SQL, Armazém de Dados Azure SQL, Base de Dados do Servidor SQL na sua empresa ou um VM Azure.
[Atividade u-SQL do Lago de Dados](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL Activity executa um script U-SQL em um cluster Azure Data Lake Analytics.
[Atividade personalizada do .NET](#net-custom-activity) | Se precisar de transformar dados de uma forma que não seja suportada pela Data Factory, pode criar uma atividade personalizada com a sua própria lógica de processamento de dados e utilizar a atividade no pipeline. Pode configurar a atividade personalizada .NET para funcionar utilizando um serviço De Lote Azure ou um cluster Azure HDInsight.


## <a name="hdinsight-hive-activity"></a>Atividade Hive do HDInsight
Pode especificar as seguintes propriedades numa definição JSON de Atividade da Colmeia. A propriedade tipo para a atividade deve ser: **HDInsightHive**. Primeiro, deve criar um serviço ligado ao HDInsight e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **typeProperties** quando define o tipo de atividade para HDInsightHive:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| . |Especifique a linha inline do script da Colmeia |Não |
| caminho do script |Guarde o script da Colmeia num armazenamento de blob Azure e forneça o caminho para o ficheiro. Utilize a propriedade 'script' ou 'scriptPath'. Ambos não podem ser usados juntos. O nome do ficheiro é sensível ao caso. |Não |
| define |Especificar parâmetros como par de chaves/valor para referência dentro do script da Colmeia usando 'hiveconf' |Não |

Estes tipos de propriedades são específicos da Atividade da Colmeia. Outras propriedades (fora da secção TypeProperties) são suportadas para todas as atividades.

### <a name="json-example"></a>Exemplo jSON
O JSON seguinte define uma atividade da Hive HDInsight num oleoduto.

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

Para mais informações, consulte o artigo [hive Activity.](data-factory-hive-activity.md)

## <a name="hdinsight-pig-activity"></a>Atividade Pig do HDInsight
Pode especificar as seguintes propriedades numa definição JSON de Atividade de Porco. A propriedade tipo para a atividade deve ser: **HDInsightPig**. Primeiro, deve criar um serviço ligado ao HDInsight e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **TypeProperties** quando define o tipo de atividade para HDInsightPig:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| . |Especifique a linha inline do roteiro do porco |Não |
| caminho do script |Guarde o guião pig num armazenamento de blob Azure e forneça o caminho para o ficheiro. Utilize a propriedade 'script' ou 'scriptPath'. Ambos não podem ser usados juntos. O nome do ficheiro é sensível ao caso. |Não |
| define |Especificar parâmetros como par de chaves/valor para referência dentro do roteiro do Porco |Não |

Estes tipos de propriedades são específicos da Atividade do Porco. Outras propriedades (fora da secção TypeProperties) são suportadas para todas as atividades.

### <a name="json-example"></a>Exemplo jSON

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

Para mais informações, consulte o artigo da Atividade do Porco.

## <a name="hdinsight-mapreduce-activity"></a>Atividade MapReduce do HDInsight
Pode especificar as seguintes propriedades numa definição MapReduce Activity JSON. A propriedade do tipo para a atividade deve ser: **HDInsightMapReduce**. Primeiro, deve criar um serviço ligado ao HDInsight e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **TypeProperties** quando define o tipo de atividade para HDInsightMapReduce:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| jarLinkedService | Nome do serviço ligado para o Armazenamento Azure que contém o ficheiro JAR. | Sim |
| jarFilePath | Caminho para o ficheiro JAR no Armazém Azure. | Sim |
| className | Nome da classe principal no ficheiro JAR. | Sim |
| argumentos | Uma lista de argumentos separados pela vírcula para o programa MapReduce. No tempo de execução, você vê alguns argumentos extra (por exemplo: mapreduce.job.tags) do quadro MapReduce. Para diferenciar os seus argumentos com os argumentos MapReduce, considere usar tanto a opção como o valor como argumentos como mostrado no exemplo seguinte (-s, --entrada, --output etc., são opções imediatamente seguidas pelos seus valores) | Não |

### <a name="json-example"></a>Exemplo jSON

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

Para mais informações, consulte o artigo [MapReduce Activity.](data-factory-map-reduce.md)

## <a name="hdinsight-streaming-activity"></a>Atividade Streaming do HDInsight
Pode especificar as seguintes propriedades numa definição JSON de Atividade de Streaming hadoop. A propriedade tipo para a atividade deve ser: **HDInsightStreaming**. Primeiro, deve criar um serviço ligado ao HDInsight e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **TypeProperties** quando define o tipo de atividade para HDInsightStreaming:

| Propriedade | Descrição |
| --- | --- |
| mapper | Nome do mapeador executável. No exemplo, cat.exe é o mapeexecutável.|
| redutor | Nome do redutor executável. No exemplo, wc.exe é o redutor executável. |
| entrada | Ficheiro de entrada (incluindo localização) para o mapeador. No exemplo: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"`adfsample é o recipiente blob, exemplo/dados/Gutenberg é a pasta, e davinci.txt é a bolha. |
| saída | Ficheiro de saída (incluindo localização) para o redutor. A saída do trabalho de Streaming Hadoop está escrita para o local especificado para esta propriedade. |
| arquivoSPaths | Caminhos para o mapper e redutor executáveis. No exemplo: "adfsample/example/apps/wc.exe", a adfsample é o recipiente blob, exemplo/apps é a pasta, e wc.exe é o executável. |
| ficheiroSLinkedService | Serviço ligado ao Armazenamento Azure que representa o armazenamento Azure que contém os ficheiros especificados na secção ficheiroSCaminhos. |
| argumentos | Uma lista de argumentos separados pela vírcula para o programa MapReduce. No tempo de execução, você vê alguns argumentos extra (por exemplo: mapreduce.job.tags) do quadro MapReduce. Para diferenciar os seus argumentos com os argumentos MapReduce, considere usar tanto a opção como o valor como argumentos como mostrado no exemplo seguinte (-s, --entrada, --output etc., são opções imediatamente seguidas pelos seus valores) |
| getDebugInfo | Um elemento opcional. Quando está definido para falha, os registos são descarregados apenas por falha. Quando está definido para Tudo, os registos são sempre descarregados independentemente do estado de execução. |

> [!NOTE]
> Deve especificar um conjunto de dados de saída para a Atividade de Streaming hadoop para a propriedade de **saídas.** Este conjunto de dados pode ser apenas um conjunto de dados de boneco que é necessário para conduzir o horário do gasoduto (hora, diariamente, etc.). Se a atividade não tomar uma entrada, pode ignorar especificar um conjunto de dados de entrada para a atividade para a propriedade de **inputs.**

## <a name="json-example"></a>Exemplo jSON

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

Para mais informações, consulte o artigo [da Atividade de Streaming hadoop.](data-factory-hadoop-streaming-activity.md)

## <a name="hdinsight-spark-activity"></a>Atividade do HDInsight Spark
Pode especificar as seguintes propriedades numa definição JSON de Atividade de Faísca. A propriedade tipo para a atividade deve ser: **HDInsightSpark**. Primeiro, deve criar um serviço ligado ao HDInsight e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **TypeProperties** quando define o tipo de atividade para HDInsightSpark:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| rootPath | O recipiente e pasta Azure Blob que contém o ficheiro Spark. O nome do ficheiro é sensível ao caso. | Sim |
| entradaFilePath | Caminho relativo para a pasta raiz do código/embalagem Spark. | Sim |
| className | Classe principal java/faísca da aplicação | Não |
| argumentos | Uma lista de argumentos de linha de comando para o programa Spark. | Não |
| proxyUser | A conta de utilizador para personificar para executar o programa Spark | Não |
| sparkConfig | Propriedades de configuração de faíscas. | Não |
| getDebugInfo | Especifica quando os ficheiros de registo Spark são copiados para o armazenamento Azure utilizado pelo cluster HDInsight (ou) especificado pelo sparkJobLinkedService. Valores permitidos: Nenhum, sempre ou falha. Valor predefinido: Nenhum. | Não |
| sparkJobLinkedService | O serviço ligado ao Armazenamento Azure que detém o ficheiro de trabalho spark, dependências e registos.  Se não especificar um valor para esta propriedade, o armazenamento associado ao cluster HDInsight é utilizado. | Não |

### <a name="json-example"></a>Exemplo jSON

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

- A propriedade **do tipo** está definida para **HDInsightSpark**.
- O **rootPath** está definido para **\\adfspark pyFiles** onde a adfspark é o recipiente Azure Blob e pyFiles é uma pasta fina nesse recipiente. Neste exemplo, o Armazenamento de Blob Azure é o que está associado ao cluster Spark. Pode fazer o upload do ficheiro para um Armazém Azure diferente. Se o fizer, crie um serviço ligado ao Armazenamento Azure para ligar essa conta de armazenamento à fábrica de dados. Em seguida, especifique o nome do serviço ligado como um valor para a propriedade **sparkJobLinkedService.** Consulte propriedades da Spark Activity para mais detalhes sobre este imóvel e outros imóveis suportados pela Atividade de Faísca.
- A **entradaFilePath** está definida para o **test.py**, que é o ficheiro python.
- A propriedade **getDebugInfo** está definida para **Sempre**, o que significa que os ficheiros de registo são sempre gerados (sucesso ou falha).

    > [!IMPORTANT]
    > Recomendamos que não coloque esta propriedade sempre num ambiente de produção, a menos que esteja a resolver um problema.
- A secção **de saídas** tem um conjunto de dados de saída. Deve especificar um conjunto de dados de saída mesmo que o programa de faíscas não produza qualquer saída. O conjunto de dados de saída impulsiona o horário do gasoduto (hora, diariamente, etc.).

Para mais informações sobre a atividade, consulte o artigo [Spark Activity.](data-factory-spark.md)

## <a name="machine-learning-batch-execution-activity"></a>Atividade de Execução em Lote do Machine Learning
Pode especificar as seguintes propriedades numa definição JSON de atividade de execução de lotes do estúdio de aprendizagem automática Azure Machine Learning. O tipo de propriedade para a atividade deve ser: **AzureMLBatchExecution**. Primeiro, deve criar um serviço ligado ao Azure Machine Learning e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **typeProperties** quando definir o tipo de atividade para AzureMLBatchExecution:

Propriedade | Descrição | Necessário
-------- | ----------- | --------
webServiceInput | O conjunto de dados a ser passado como uma entrada para o serviço web do estúdio Azure Machine Learning. Este conjunto de dados também deve ser incluído nas inputs para a atividade. |Utilize webServiceInput ou webServiceInputs. |
webServiceInputs | Especifique os conjuntos de dados a serem passados como inputs para o serviço web do estúdio Azure Machine Learning. Se o serviço web tiver várias inputs, utilize a propriedade webServiceInputs em vez de utilizar a propriedade webServiceInput. Os conjuntos de dados referenciados pela **webServiceInputs** também devem ser incluídos nas **inputs**de Atividade . | Utilize webServiceInput ou webServiceInputs. |
webServiceOutputs | Os conjuntos de dados que são atribuídos como saídas para o serviço web do estúdio Azure Machine Learning. O serviço web devolve os dados de saída neste conjunto de dados. | Sim |
parâmetros globais | Especifique os valores dos parâmetros do serviço web nesta secção. | Não |

### <a name="json-example"></a>Exemplo jSON
Neste exemplo, a atividade tem o conjunto de dados **MLSqlInput** como entrada e **MLSqlOutput** como saída. O **MLSqlInput** é passado como uma entrada para o serviço web utilizando a propriedade **WebServiceInput** JSON. O **MLSqlOutput** é passado como uma saída para o serviço Web utilizando a propriedade **WebServiceOutputs** JSON.

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

No exemplo da JSON, o serviço Web de aprendizagem automática Azure implantado utiliza um leitor e um módulo de escritor para ler/escrever dados de/para uma Base de Dados Azure SQL. Este serviço Web expõe os seguintes quatro parâmetros: Nome do servidor de base de dados, nome da base de dados, nome da conta do servidor e palavra-passe da conta do servidor.

> [!NOTE]
> Apenas as inputs e saídas da atividade AzureMLBatchExecution podem ser passadas como parâmetros para o serviço Web. Por exemplo, no snippet JSON acima, mlSqlInput é uma entrada para a atividade AzureMLBatchExecution, que é passada como uma entrada para o serviço Web através do parâmetro webServiceInput.

## <a name="machine-learning-update-resource-activity"></a>Atividade de Recursos de Atualização de Machine Learning
Pode especificar as seguintes propriedades numa definição JSON do estúdio de Aprendizagem automática Azure Machine Learning. A propriedade do tipo para a atividade deve ser: **AzureMLUpdateResource**. Primeiro, deve criar um serviço ligado ao Azure Machine Learning e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **TypeProperties** quando definir o tipo de atividade para AzureMLUpdateResource:

Propriedade | Descrição | Necessário
-------- | ----------- | --------
nome de modelo treinado | Nome do modelo retreinado. | Sim |
nome treinado ModelDatasetName | Conjunto de dados que aponta para o ficheiro iLearner devolvido pela operação de reconversão. | Sim |

### <a name="json-example"></a>Exemplo jSON
O gasoduto tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução do estúdio De execução de Machine Learning Azure toma os dados de formação como entrada e produz um ficheiro iLearner como uma saída. A atividade invoca o serviço web de formação (experiência de formação exposta como um serviço web) com os dados de formação de entrada e recebe o ficheiro ilearner do serviço web. O placeholderBlob é apenas um conjunto de dados de saída de bonecos que é exigido pelo serviço Azure Data Factory para executar o pipeline.


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
Pode especificar as seguintes propriedades numa definição JSON de atividade U-SQL. A propriedade tipo para a atividade deve ser: **DataLakeAnalyticsU-SQL**. Deve criar um serviço ligado ao Azure Data Lake Analytics e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **TypeProperties** quando define o tipo de atividade para DataLakeAnalyticsU-SQL:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| scriptPath |Caminho para pasta que contém o script U-SQL. O nome do ficheiro é sensível a casos. |Não (se usar o script) |
| scriptLinkedService |Serviço ligado que liga o armazenamento que contém o script à fábrica de dados |Não (se usar o script) |
| . |Especifique o script inline em vez de especificar scriptPath e scriptLinkedService. Por exemplo: "script": "CREATE DATABASE test". |Não (se utilizar scriptPath e scriptLinkedService) |
| degreeOfParallelismo |O número máximo de nós usado simultaneamente para gerir o trabalho. |Não |
| prioridade |Determina quais os postos de trabalho que estão em fila devem ser selecionados para serem executados primeiro. Quanto menor o número, maior a prioridade. |Não |
| parâmetros |Parâmetros para o script U-SQL |Não |

### <a name="json-example"></a>Exemplo jSON

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

Para mais informações, consulte [Data Lake Analytics U-SQL Activity](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Atividade de Procedimento Armazenado
Pode especificar as seguintes propriedades numa definição JSON de Atividade de Procedimento Armazenado. A propriedade do tipo para a atividade deve ser: **SqlServerStoredProcedure**. Deve criar um dos seguintes serviços ligados e especificar o nome do serviço ligado como um valor para a propriedade **linkedServiceName:**

- SQL Server
- Base de Dados SQL do Azure
- Azure SQL Data Warehouse

As seguintes propriedades são suportadas na secção **TypeProperties** quando define o tipo de atividade para SqlServerStoredProcedure:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome de procedimento armazenado |Especifique o nome do procedimento armazenado na base de dados Azure SQL ou no Azure SQL Data Warehouse que está representado pelo serviço ligado que a tabela de saída utiliza. |Sim |
| parâmetros de procedimento saqueados |Especifique os valores dos parâmetros de procedimento armazenados. Se precisar de passar nulo por um parâmetro, utilize a sintaxe: "param1": nulo (todos os casos inferiores). Consulte a seguinte amostra para aprender sobre a utilização desta propriedade. |Não |

Se especificar um conjunto de dados de entrada, este deve estar disponível (em estado 'Pronto') para que a atividade do procedimento armazenado seja executada. O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como parâmetro. É utilizado apenas para verificar a dependência antes de iniciar a atividade do procedimento armazenado. Deve especificar um conjunto de dados de saída para uma atividade de procedimento armazenado.

O conjunto de dados de saída especifica o **calendário** para a atividade do procedimento armazenado (hora, semanal, mensal, etc.). O conjunto de dados de saída deve utilizar um **serviço ligado** que se refira a uma Base de Dados Azure SQL ou a um Armazém de Dados Azure SQL ou a uma Base de Dados de Servidores SQL em que pretende que o procedimento armazenado seja executado. O conjunto de dados de saída pode servir como forma de passar o resultado do procedimento armazenado para posterior processamento por outra atividade[(atividades de cadeia](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)) no gasoduto. No entanto, a Data Factory não escreve automaticamente a saída de um procedimento armazenado para este conjunto de dados. É o procedimento armazenado que escreve para uma tabela SQL que o conjunto de dados de saída aponta. Em alguns casos, o conjunto de dados de saída pode ser um conjunto de **dados de bonecos,** que é usado apenas para especificar o horário para executar a atividade do procedimento armazenado.

### <a name="json-example"></a>Exemplo jSON

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

Para mais informações, consulte o artigo [Atividade do Procedimento Armazenado.](data-factory-stored-proc-activity.md)

## <a name="net-custom-activity"></a>Atividade personalizada do .NET
Pode especificar as seguintes propriedades numa definição JSON de atividade personalizada .NET. O tipo de propriedade para a atividade deve ser: **DotNetActivity**. Deve criar um serviço ligado ao Azure HDInsight ou um serviço ligado ao Azure Batch e especificar o nome do serviço ligado como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **typeProperties** quando define o tipo de atividade para DotNetActivity:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| Nome da Assembléia | Nome da montagem. No exemplo, é: **MyDotnetActivity.dll**. | Sim |
| EntryPoint |Nome da classe que implementa a interface IDotNetActivity. No exemplo, é: **MyDotNetActivityNS.MyDotNetActivity** onde myDotNetActivityNS é o espaço de nome e MyDotNetActivity é a classe.  | Sim |
| PacoteLinkedService | Nome do serviço ligado ao Armazenamento Azure que aponta para o armazenamento blob que contém o ficheiro zip de atividade personalizada. No exemplo, é: **AzureStorageLinkedService**.| Sim |
| PacoteFile | Nome do ficheiro postal. No exemplo, é: **customactivitycontainer/MyDotNetActivity.zip**. | Sim |
| propriedades estendidas | Propriedades estendidas que pode definir e passar para o código .NET. Neste exemplo, a variável **SliceStart** é definida para um valor baseado na variável do sistema SliceStart. | Não |

### <a name="json-example"></a>Exemplo jSON

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

Para obter informações detalhadas, consulte [Utilize atividades personalizadas no](data-factory-use-custom-activities.md) artigo data Factory.

## <a name="next-steps"></a>Passos Seguintes
Veja os tutoriais seguintes:

- [Tutorial: criar um pipeline com uma atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Tutorial: criar um oleoduto com atividade de colmeia](data-factory-build-your-first-pipeline.md)
