---
title: Azure Data Factory - Referência de scripts JSON
description: Fornece esquemas JSON para entidades da Data Factory.
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
ms.openlocfilehash: 6372976d85c7fdce2a729047d3ae36911412ecf1
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636889"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory - Referência de scripts JSON
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory.


Este artigo fornece esquemas e exemplos JSON para definir entidades da Azure Data Factory (pipeline, atividade, conjunto de dados e serviço ligado).

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

| Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
| name | Nome do pipeline. Especificar um nome que represente a ação que a atividade ou o gasoduto está configurado para fazer<br/><ul><li>Número máximo de carateres: 260</li><li>Deve começar com um número de letra, ou um sublinhado \_ ()</li><li>Não são permitidos personagens seguintes: ".", "+", "" "/", "<", ">","*","%", "&", \\ ""</li></ul> |Sim |
| descrição |Texto que descreve para que a atividade ou o gasoduto é usado | Não |
| atividades | Contém uma lista de atividades. | Sim |
| iniciar |Hora de início para o oleoduto. Deve estar no [formato ISO.](https://en.wikipedia.org/wiki/ISO_8601) Por exemplo: 2014-10-14T16:32:41. <br/><br/>É possível especificar uma hora local, por exemplo, uma hora EST. Aqui está um exemplo: `2016-02-27T06:00:00**-05:00` , que é 6 AM EST.<br/><br/>As propriedades de início e de fim em conjunto especificam o período ativo para o gasoduto. As fatias de saída só são produzidas neste período ativo. |Não<br/><br/>Se especificar um valor para a propriedade final, deve especificar valor para a propriedade inicial.<br/><br/>Os tempos de início e fim podem estar vazios para criar um oleoduto. Tem de especificar ambos os valores para definir um período ativo para o gasoduto funcionar. Se não especificar os tempos de início e de fim ao criar um oleoduto, pode defini-los utilizando o Set-AzDataFactoryPipelineActivePeriod cmdlet mais tarde. |
| fim |Fim da data para o oleoduto. Se especificado deve estar no formato ISO. Por exemplo: 2014-10-14T17:32:41 <br/><br/>É possível especificar uma hora local, por exemplo, uma hora EST. Aqui está um exemplo: `2016-02-27T06:00:00**-05:00` , que é 6 AM EST.<br/><br/>Para executar o pipeline de forma indefinida, especifique 9999-09-09 como o valor da propriedade end. |Não <br/><br/>Se especificar um valor para a propriedade inicial, deve especificar valor para a propriedade final.<br/><br/>Consulte as notas para a propriedade **inicial.** |
| isPaused |Se for definido como verdadeiro, o gasoduto não funciona. Valor predefinido = falso. Pode utilizar esta propriedade para ativar ou desativar. |Não |
| pipelineMode |O método de agendamento vai para o oleoduto. Os valores permitidos são: programado (predefinição), uma vez.<br/><br/>O «Programado» indica que o gasoduto funciona com um intervalo de tempo especificado de acordo com o período ativo (tempo de início e fim). 'Onetime' indica que o gasoduto funciona apenas uma vez. Os gasodutos únicos criados não podem ser modificados/atualizados atualmente. Consulte [o pipeline OneTime](data-factory-create-pipelines.md#onetime-pipeline) para obter detalhes sobre a definição única. |Não |
| tempo de expiração |Duração do tempo após a criação para a qual o gasoduto é válido e deve permanecer a provisionado. Se não tiver qualquer execução ativa, falhada ou pendente, o gasoduto é apagado automaticamente assim que atingir o prazo de validade. |Não |


## <a name="activity"></a>Atividade
A estrutura de alto nível para uma atividade dentro de uma definição de gasoduto (elemento de atividades) é a seguinte:

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

A tabela seguinte descreve as propriedades dentro da definição JSON da atividade:

| Etiqueta | Descrição | Obrigatório |
| --- | --- | --- |
| name |Nome da atividade. Especificar um nome que represente a ação que a atividade está configurada para fazer<br/><ul><li>Número máximo de carateres: 260</li><li>Deve começar com um número de letra, ou um sublinhado \_ ()</li><li>Não são permitidos personagens seguintes: ".", "+", "" "/", "<", ">","*","%", "&", \\ ""</li></ul> |Sim |
| descrição |Texto que descreve para que a atividade é usada. |Não |
| tipo |Especifica o tipo de atividade. Consulte as secções [data stores](#data-stores) e data [transformation activities](#data-transformation-activities) para diferentes tipos de atividades. |Sim |
| entradas |Tabelas de entrada utilizadas pela atividade<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Não para atividades hdInsightStreaming e SqlServerStoredProcedure <br/> <br/> Sim para todos os outros |
| saídas |Tabelas de saída usadas pela atividade.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Sim |
| linkedServiceName |Nome do serviço ligado utilizado pela atividade. <br/><br/>Uma atividade pode exigir que especifique o serviço ligado que liga ao ambiente de computação necessário. |Sim para atividades HDInsight, atividades do Azure Machine Learning Studio (clássico) e Atividade de Procedimento Armazenado. <br/><br/>Não para todas as outras. |
| typeProperties |As propriedades na secção de tipoProperties dependem do tipo de atividade. |Não |
| política |Políticas que afetam o comportamento de runtime da atividade. Se não for especificado, são utilizadas políticas predefinidas. |Não |
| scheduler |A propriedade "scheduler" é usada para definir o agendamento desejado para a atividade. As suas subpropriedades são as mesmas que estão na [propriedade disponível num conjunto de dados.](data-factory-create-datasets.md#dataset-availability) |Não |

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

### <a name="typeproperties-section"></a>secção de tipos
A secção typeProperties é diferente para cada atividade. As atividades de transformação têm apenas propriedades tipo. Consulte a secção [de ATIVIDADES DE TRANSFORMAÇÃO DE DADOS](#data-transformation-activities) neste artigo para amostras JSON que definem as atividades de transformação num oleoduto.

**A atividade de cópia** tem duas subsecções na secção de tipos de propriedades: **fonte** e **pia** . Consulte a secção [DATA STORES](#data-stores) neste artigo para amostras JSON que mostrem como utilizar uma loja de dados como fonte e/ou pia.

### <a name="sample-copy-pipeline"></a>Pipeline de cópia de exemplo
No pipeline de exemplo seguinte, existe uma atividade do tipo **Cópia** na secção **activities** . Nesta amostra, a [atividade Copy](data-factory-data-movement-activities.md) copia dados de um armazenamento Azure Blob para Azure SQL Database.

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

* Na secção atividades, existe apenas uma atividade cujo **type** está definido como **Copy** .
* A entrada da atividade está definida como **InputDataset** e a saída como **OutputDataset** .
* Na secção **typeProperties** , **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink.

Consulte a secção [DATA STORES](#data-stores) neste artigo para amostras JSON que mostrem como utilizar uma loja de dados como fonte e/ou pia.

Para obter uma passagem completa da criação deste pipeline, consulte [Tutorial: Copiar dados do Blob Storage para a SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="sample-transformation-pipeline"></a>Pipeline de transformação de exemplos
No pipeline de exemplo seguinte, existe uma atividade do tipo **HDInsightHive** na secção **activities** . Neste exemplo, a [atividade Hive do HDInsight](data-factory-hive-activity.md) transforma dados de um armazenamento de Blobs do Azure mediante a execução de um ficheiro de script de Hive num cluster do Hadoop para o Azure HDInsight.

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

* Na secção “activities”, existe apenas uma atividade cujo **type** está definido como **HDInsightHive** .
* O ficheiro do Script de ramo de registo **partitionweblogs.hql** é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService, denominado **AzureStorageLinkedService** ) e na pasta **script** no contentor **adfgetstarted** .
* A secção **de definições** é utilizada para especificar as definições de tempo de execução que são passadas para o script da colmeia como valores de configuração da Colmeia (por `${hiveconf:inputtable}` exemplo, `${hiveconf:partitionedtable}` ).

Consulte a secção [de ATIVIDADES DE TRANSFORMAÇÃO DE DADOS](#data-transformation-activities) neste artigo para amostras JSON que definem as atividades de transformação num oleoduto.

Para uma caminhada completa da criação deste oleoduto, consulte [Tutorial: Construa o seu primeiro oleoduto para processar dados utilizando o cluster Hadoop.](data-factory-build-your-first-pipeline.md)

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

A tabela seguinte descreve as propriedades dentro da definição JSON da atividade:

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| name | Nome do serviço ligado. | Sim |
| propriedades - tipo | Tipo de serviço ligado. Por exemplo: Azure Storage, Base de Dados Azure SQL. |
| typeProperties | A secção typeProperties tem elementos diferentes para cada data store ou ambiente computacional. Consulte a secção de lojas de dados para todos os serviços ligados à loja de dados e [ambientes de computação](#compute-environments) para todos os serviços ligados ao computação |

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

A tabela a seguir descreve propriedades no JSON acima:

| Propriedade | Descrição | Obrigatório | Predefinição |
| --- | --- | --- | --- |
| name | Nome do conjunto de dados. Ver [Azure Data Factory - Regras de nomeação](data-factory-naming-rules.md) para regras de nomeação. |Sim |ND |
| tipo | Tipo do conjunto de dados. Especifique um dos tipos suportados pela Azure Data Factory (por exemplo: AzureBlob, AzureSqlTable). Consulte a secção [DATA STORES](#data-stores) para todas as lojas de dados e tipos de conjuntos de dados suportados pela Data Factory. |
| estrutura | Esquema do conjunto de dados. Contém colunas, os seus tipos, etc. | Não |ND |
| typeProperties | Propriedades correspondentes ao tipo selecionado. Consulte a secção [DATA STORES](#data-stores) para os tipos suportados e as suas propriedades. |Sim |ND |
| externo | Bandeira booleana para especificar se um conjunto de dados é explicitamente produzido por um oleoduto de fábrica de dados ou não. |Não |false |
| disponibilidade | Define a janela de processamento ou o modelo de corte para a produção do conjunto de dados. Para obter mais informações sobre o modelo de corte de conjuntos de dados, consulte o artigo [agendamento e execução.](data-factory-scheduling-and-execution.md) |Sim |ND |
| política |Define os critérios ou a condição que as fatias de conjunto de dados devem cumprir. <br/><br/>Para mais detalhes, consulte a secção Política do Conjunto de Dados. |Não |ND |

Cada coluna na secção de **estrutura** contém as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| name |O nome da coluna. |Sim |
| tipo |Tipo de dados da coluna.  |Não |
| cultura |.CULTURA baseada em REDE a ser utilizada quando o tipo é especificado e é .NET type `Datetime` ou `Datetimeoffset` . A predefinição é `en-us`. |Não |
| formato |Cadeia de formato a utilizar quando o tipo é especificado e é .NET tipo `Datetime` ou `Datetimeoffset` . |Não |

No exemplo seguinte, o conjunto de dados tem três colunas `slicetimestamp` , e são de `projectname` `pageviews` tipo: String, String e Decimal, respectivamente.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

A tabela a seguir descreve propriedades que pode utilizar na secção **de disponibilidade:**

| Propriedade | Descrição | Obrigatório | Predefinição |
| --- | --- | --- | --- |
| frequência |Especifica a unidade de tempo para a produção de fatias de conjunto de dados.<br/><br/><b>Frequência suportada</b>: Minuto, Hora, Dia, Semana, Mês |Sim |ND |
| interval |Especifica um multiplicador para a frequência<br/><br/>"Intervalo de frequência x" determina a frequência com que a fatia é produzida.<br/><br/>Se necessitar do conjunto de dados para ser cortado de hora a hora, define <b>a frequência</b> para a <b>hora</b>, e <b>intervalo</b> para <b>1</b>.<br/><br/><b>Nota:</b>Se especificar a frequência como Minuto, recomendamos que desemende o intervalo para nada menos do que 15 |Sim |ND |
| estilo |Especifica se a fatia deve ser produzida no início/fim do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se a frequência está definida para mês e o estilo está definido para EndOfInterval, a fatia é produzida no último dia do mês. Se o estilo estiver definido para StartOfInterval, a fatia é produzida no primeiro dia do mês.<br/><br/>Se a frequência estiver definida para o Dia e o estilo estiver definido para EndOfInterval, a fatia é produzida na última hora do dia.<br/><br/>Se a frequência estiver definida para Hora e o estilo estiver definido para EndOfInterval, a fatia é produzida no final da hora. Por exemplo, para uma fatia para o período das 13:00 às 14:00, a fatia é produzida às 14:00. |Não |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo utilizado pelo programador para calcular os limites da fatia de conjunto de dados. <br/><br/><b>Nota:</b>Se o AnchorDateTime tiver peças de data mais granulares do que a frequência, as partes mais granulares são ignoradas. <br/><br/>Por exemplo, se o <b>intervalo</b> for <b>de hora</b> a hora (frequência: hora e intervalo: 1) e o <b>AnchorDateTime</b> contiver <b>minutos e segundos,</b> os <b>minutos e segundos</b> partes do AnchorDateTime são ignorados. |Não |01/01/0001 |
| offset |Timepan pelo qual o início e o fim de todas as fatias de conjunto de dados são deslocados. <br/><br/><b>Nota:</b>Se forem especificados tanto o anchorDateTime como o offset, o resultado é a mudança combinada. |Não |ND |

A seguinte secção de disponibilidade especifica que o conjunto de dados de saída é produzido de hora a hora (ou) conjunto de dados de entrada disponível por hora:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

A secção **de política** na definição de conjunto de dados define os critérios ou a condição que as fatias de conjunto de dados devem cumprir.

| Nome da Política | Descrição | Aplicado a | Obrigatório | Predefinição |
| --- | --- | --- | --- | --- |
| tamanho mínimoS |Valida que os dados numa **bolha Azure** satisfaz os requisitos mínimos de tamanho (em megabytes). |Blob do Azure |Não |ND |
| sobrancelhas mínimas |Valida que os dados na **Base de Dados Azure SQL** ou numa **tabela Azure** contém o número mínimo de linhas. |<ul><li>Base de Dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |ND |

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

A menos que um conjunto de dados seja produzido pela Azure Data Factory, deve ser marcado como **externo** . Esta definição aplica-se geralmente às entradas da primeira atividade num gasoduto, a menos que a atividade ou a corrente do gasoduto sejam utilizadas.

| Nome | Descrição | Obrigatório | Valor Predefinido |
| --- | --- | --- | --- |
| dataDelaia |Hora de atrasar a verificação da disponibilidade dos dados externos para a fatia dada. Por exemplo, se os dados estiverem disponíveis a hora, a verificação para ver os dados externos está disponível e a fatia correspondente está Pronta pode ser retardada usando dataDelay.<br/><br/>Só se aplica ao presente.  Por exemplo, se forem 13:00 pm agora e este valor é de 10 minutos, a validação começa às 13:10.<br/><br/>Esta definição não afeta fatias no passado (fatias com Tempo final de fatia + dadosDelay < Agora) são processadas sem demora.<br/><br/>Tempo superior a 23:59 horas precisa de ser especificado usando o `day.hours:minutes:seconds` formato. Por exemplo, para especificar 24 horas, não use 24:00:00; em vez disso, use 1:00:00:00. Se utilizar 24:00:00, é tratado como 24 dias (24.00:00:00). Por 1 dia e 4 horas, especifique 1:04:00:00. |Não |0 |
| retryInterval |O tempo de espera entre um fracasso e a próxima tentativa de repetição. Se uma tentativa falhar, a próxima tentativa é depois de redaval. <br/><br/>Se são 13:00 agora, começamos a primeira tentativa. Se a duração para completar a primeira verificação de validação for de 1 minuto e a operação tiver falhado, a próxima repetição é de 1:00 + 1 min (duração) + 1 min (intervalo de repetição) = 1:02 PM. <br/><br/>Para fatias no passado, não há atraso. A repetição acontece imediatamente. |Não |00:01:00 (1 minuto) |
| retryTimeout |O tempo limite para cada tentativa de repetição.<br/><br/>Se esta propriedade estiver definida para 10 minutos, a validação tem de ser concluída dentro de 10 minutos. Se demorar mais de 10 minutos a efetuar a validação, o tempo de retítu disso.<br/><br/>Se todas as tentativas de validação esgotarem, a fatia é marcada como TimedOut. |Não |00:10:00 (10 minutos) |
| máximaSRetry |Número de vezes para verificar a disponibilidade dos dados externos. O valor máximo permitido é de 10. |Não |3 |


## <a name="data-stores"></a>LOJAS DE DADOS
A secção [de serviços Linked](#linked-service) forneceu descrições para elementos JSON que são comuns a todos os tipos de serviços ligados. Esta secção fornece detalhes sobre elementos JSON específicos de cada loja de dados.

A secção [dataset](#dataset) forneceu descrições para elementos JSON que são comuns a todos os tipos de conjuntos de dados. Esta secção fornece detalhes sobre elementos JSON específicos de cada loja de dados.

A secção [de Atividades](#activity) forneceu descrições para elementos JSON que são comuns a todos os tipos de atividades. Esta secção fornece detalhes sobre elementos JSON específicos de cada loja de dados quando é usado como fonte/pia numa atividade de cópia.

Clique no link para a loja que está interessado em ver os esquemas JSON para serviço ligado, conjunto de dados e a fonte/pia para a atividade da cópia.

| Categoria | Arquivo de dados
|:--- |:--- |
| **Azure** |[Armazenamento de Blobs do Azure](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[BD do Cosmos para o Azure](#azure-cosmos-db) |
| &nbsp; |[Base de Dados SQL do Azure](#azure-sql-database) |
| &nbsp; |[Azure Synapse Analytics (anteriormente SQL Data Warehouse)](#azure-synapse-analytics) |
| &nbsp; |[Azure Cognitive Search](#azure-cognitive-search) |
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
Existem dois tipos de serviços ligados: serviço ligado a Azure Storage e serviço ligado ao Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
Para ligar a sua conta de armazenamento Azure a uma fábrica de dados utilizando a chave de **conta,** crie um serviço ligado ao Azure Storage. Para definir um serviço ligado ao Azure Storage, defina o **tipo** de serviço ligado ao **AzureStorage** . Em seguida, pode especificar as seguintes propriedades na secção **de tipos de direito:**

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| conexãoStragem |Especifique as informações necessárias para ligar ao armazenamento Azure para a propriedade connectionString. |Sim |

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

#### <a name="azure-storage-sas-linked-service"></a>Serviço ligado a Azure Storage SAS
O serviço Azure Storage SAS permite-lhe ligar uma Conta de Armazenamento Azure a uma fábrica de dados Azure utilizando uma Assinatura de Acesso Partilhado (SAS). Fornece à fábrica de dados um acesso restrito/temporal a todos/recursos específicos (blob/contentor) no armazenamento. Para ligar a sua conta de armazenamento Azure a uma fábrica de dados utilizando a Assinatura de Acesso Partilhado, crie um serviço ligado ao Azure Storage SAS. Para definir um serviço ligado ao Azure Storage SAS, defina o **tipo** de serviço ligado ao **AzureStorageSas** . Em seguida, pode especificar as seguintes propriedades na secção **de tipos de direito:**

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| SasUri |Especifique a assinatura de acesso partilhado URI aos recursos de armazenamento Azure, tais como bolha, recipiente ou mesa. |Sim |

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

Para obter mais informações sobre estes serviços ligados, consulte o artigo [do conector de armazenamento Azure Blob.](data-factory-azure-blob-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Azure Blob, defina o **tipo** de conjunto de dados para **AzureBlob** . Em seguida, especifique as seguintes propriedades específicas do Azure Blob na secção **de tipos de propriedades:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Caminho para o recipiente e pasta no armazenamento do blob. Exemplo: myblobcontainer\myblobfolder\ |Sim |
| fileName |O nome da bolha. fileName é opcional e sensível a casos.<br/><br/>Se especificar um nome de ficheiro, a atividade (incluindo Copy) funciona na Bolha específica.<br/><br/>Quando o data de ficheiroName não é especificado, copy inclui todas as Blobs na pastaPa para o conjunto de dados de entrada.<br/><br/>Quando o data de ficheiro não for especificado para um conjunto de dados de saída, o nome do ficheiro gerado estaria no seguinte formato: `Data.<Guid>.txt` (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy é uma propriedade opcional. Pode usá-lo para especificar uma pasta dinâmicaPath e nome de ficheiro para dados da série de tempo. Por exemplo, a pastaPath pode ser parametrizada para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat** . Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte [formato de texto,](data-factory-supported-file-and-compression-formats.md#text-format) [formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e secções [de Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como-está** entre lojas baseadas em ficheiros (cópia binária), salte a secção de formato nas definições de conjunto de dados de entrada e saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2** e **ZipDeflate** . Os níveis suportados são: **Ideal** e **Mais rápido** . Para obter mais informações, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

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


Para mais informações, consulte o artigo [do conector Azure Blob.](data-factory-azure-blob-connector.md#dataset-properties)

### <a name="blobsource-in-copy-activity"></a>BlobSource na Atividade de Cópia
Se estiver a copiar dados de um Azure Blob Storage, descreva o tipo de **fonte** da atividade de cópia para **BlobSource** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos novamente a partir das sub-pastas ou apenas a partir da pasta especificada. |Verdadeiro (valor predefinido), Falso |Não |

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
### <a name="blobsink-in-copy-activity"></a>BlobSink na Atividade de Cópia
Se estiver a copiar dados para um Azure Blob Storage, descreva o **tipo** de pia da atividade da cópia para **BlobSink,** e especifique as seguintes propriedades na secção do **lavatório:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| copyOportundo |Define o comportamento da cópia quando a fonte é BlobSource ou FileSystem. |<b>PreserveHierarchy</b>: preserva a hierarquia do ficheiro na pasta alvo. O percurso relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro alvo para a pasta alvo.<br/><br/><b>FlattenHierarchy</b>: todos os ficheiros da pasta de origem estão no primeiro nível de pasta alvo. Os ficheiros-alvo têm nome gerado automaticamente. <br/><br/><b>MergeFiles (predefinição):</b> combina todos os ficheiros da pasta de origem a um ficheiro. Se o nome de ficheiro/blob for especificado, o nome do ficheiro fundido será o nome especificado; caso contrário, seria nome de ficheiro gerado automaticamente. |Não |

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

Para mais informações, consulte o artigo [do conector Azure Blob.](data-factory-azure-blob-connector.md#copy-activity-properties)

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Azure Data Lake Store, defina o tipo de serviço ligado à **AzureDataLakeStore,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **AzureDataLakeStore** | Sim |
| dataLakeStoreUri | Especifique informações sobre a conta Azure Data Lake Store. Está no seguinte formato: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/` . | Sim |
| subscriptionId | ID de assinatura Azure a que pertence a Data Lake Store. | Necessário para a pia |
| resourceGroupName | Nome do grupo de recursos Azure a que pertence a Data Lake Store. | Necessário para a pia |
| servicePrincipalId | Especifique a identificação do cliente da aplicação. | Sim (para autenticação principal do serviço) |
| servicePrincipalKey | Especifique a chave da aplicação. | Sim (para autenticação principal do serviço) |
| inquilino | Especifique a informação do inquilino (nome de domínio ou ID do inquilino) sob a qual a sua aplicação reside. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Sim (para autenticação principal do serviço) |
| autorização | Clique no botão **Authorize** no **Data Factory Editor** e introduza a sua credencial que atribui o URL de autorização gerado automaticamente a esta propriedade. | Sim (para autenticação credencial do utilizador)|
| sessionId | Identificação da Sessão OAuth da Sessão de Autorização da OAuth. Cada ID de sessão é único e só pode ser usado uma vez. Esta definição é gerada automaticamente quando utiliza o Editor de Fábrica de Dados. | Sim (para autenticação credencial do utilizador) |

#### <a name="example-using-service-principal-authentication"></a>Exemplo: utilização da autenticação principal do serviço
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
Para definir um conjunto de dados da Azure Data Lake Store, defina o **tipo** de conjunto de dados para **AzureDataLakeStore** , e especifique as seguintes propriedades na secção **de tiposproperties:**

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| folderPath |Caminho para o recipiente e pasta na loja Azure Data Lake. |Sim |
| fileName |Nome do ficheiro na loja Azure Data Lake. fileName é opcional e sensível a casos. <br/><br/>Se especificar um nome de ficheiro, a atividade (incluindo Copy) funciona no ficheiro específico.<br/><br/>Quando o data de ficheiro não é especificado, a Cópia inclui todos os ficheiros na pastaPa para o conjunto de dados de entrada.<br/><br/>Quando o data de ficheiro não for especificado para um conjunto de dados de saída, o nome do ficheiro gerado estaria no seguinte formato: `Data.<Guid>.txt` (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy é uma propriedade opcional. Pode usá-lo para especificar uma pasta dinâmicaPath e nome de ficheiro para dados da série de tempo. Por exemplo, a pastaPath pode ser parametrizada para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat** . Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte [formato de texto,](data-factory-supported-file-and-compression-formats.md#text-format) [formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e secções [de Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como-está** entre lojas baseadas em ficheiros (cópia binária), salte a secção de formato nas definições de conjunto de dados de entrada e saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2** e **ZipDeflate** . Os níveis suportados são: **Ideal** e **Mais rápido** . Para obter mais informações, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

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

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Fonte da loja de lagos de dados Azure na atividade de cópia
Se estiver a copiar dados de uma Loja de Lagos de Dados Azure, deslote o tipo de **fonte** da atividade de cópia para **AzureDataLakeStoreSource,** e especifique as seguintes propriedades na secção **de origem:**

**A AzureDataLakeStoreSource** suporta a seguinte secção **de propriedades tipoProperties:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos novamente a partir das sub-pastas ou apenas a partir da pasta especificada. |Verdadeiro (valor predefinido), Falso |Não |

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

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Azure Data Lake Store Sink in Copy Activity
Se estiver a copiar dados para uma Loja Azure Data Lake, deslote o tipo de **pia** da atividade da cópia para **AzureDataLakeStoreSink,** e especifique as seguintes propriedades na secção do **lavatório:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| copyOportundo |Especifica o comportamento da cópia. |<b>PreserveHierarchy</b>: preserva a hierarquia do ficheiro na pasta alvo. O percurso relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro alvo para a pasta alvo.<br/><br/><b>FlattenHierarchy</b>: todos os ficheiros da pasta de origem são criados no primeiro nível de pasta alvo. Os ficheiros-alvo são criados com nome gerado por automático.<br/><br/><b>MergeFiles</b>: funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome de ficheiro/blob for especificado, o nome do ficheiro fundido será o nome especificado; caso contrário, seria nome de ficheiro gerado automaticamente. |Não |

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
Para definir um serviço ligado a Azure Cosmos, defina o **tipo** de serviço ligado ao **DocumentDb** e especifique as seguintes propriedades na secção **typeProperties:**

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| conexãoStragem |Especifique as informações necessárias para ligar à base de dados DB do Azure Cosmos. |Sim |

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
Para mais informações, consulte o artigo [do conector DB da Azure Cosmos.](data-factory-azure-documentdb-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados DB Azure Cosmos, defina o **tipo** do conjunto de dados para **DocumentDbCollection** e especifique as seguintes propriedades na secção **de tiposproperias:**

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| coleçãoName |Nome da coleção Azure Cosmos DB. |Sim |

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
Para mais informações, consulte o artigo [do conector DB da Azure Cosmos.](data-factory-azure-documentdb-connector.md#dataset-properties)

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Fonte de recolha DB da Azure Cosmos na atividade de cópia
Se estiver a copiar dados de um DB Azure Cosmos, decreva o tipo de **fonte** da atividade de cópia para **DocumentDbCollectionSource,** e especifique as seguintes propriedades na secção **de origem:**


| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| consulta |Especifique a consulta para ler dados. |Linha de consulta suportada por Azure Cosmos DB. <br/><br/>Exemplo: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificado, a declaração SQL que é executada: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Personagem especial para indicar que o documento está aninhado |Qualquer personagem. <br/><br/>AZure Cosmos DB é uma loja NoSQL para documentos JSON, onde são permitidas estruturas aninhadas. A Azure Data Factory permite ao utilizador denotar hierarquia através de nidificaçãoSeparator, que é "". nos exemplos acima. Com o separador, a atividade da cópia gerará o objeto "Nome" com três elementos infantis Primeiro, Médio e Último, de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição de tabela. |Não |

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

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Pia de coleção Azure Cosmos DB em atividade de cópia
Se estiver a copiar dados para a Azure Cosmos DB, **decreva** o tipo de pia da atividade da cópia para **o DocumentDbCollectionSink,** e especifique as seguintes propriedades na secção do **lavatório:**

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| nestingSeparator |Um carácter especial no nome da coluna de origem indica que o documento aninhado é necessário. <br/><br/>Por exemplo: `Name.First` na tabela de saída produz-se a seguinte estrutura JSON no documento Cosmos DB:<br/><br/>"Nome": {<br/>    "Primeiro": "John"<br/>}, |Caráter utilizado para separar níveis de aninhamento.<br/><br/>O valor predefinido é `.` (ponto). |Caráter utilizado para separar níveis de aninhamento. <br/><br/>O valor predefinido é `.` (ponto). |
| escreverBatchSize |Número de pedidos paralelos ao serviço DB da Azure Cosmos para criar documentos.<br/><br/>Pode afinar o desempenho ao copiar dados de/para a Azure Cosmos DB utilizando esta propriedade. Pode esperar um melhor desempenho quando aumentar a escritaBatchSize porque são enviados pedidos mais paralelos ao Azure Cosmos DB. No entanto, terá de evitar estrangulamentos que possam passar a mensagem de erro: "A taxa de pedido é grande".<br/><br/>O estrangulamento é decidido por uma série de fatores, incluindo a dimensão dos documentos, o número de termos em documentos, a política de indexação da recolha de destinos, etc. Para operações de cópia, pode utilizar uma melhor recolha (por exemplo, S3) para ter a produção mais disponível (2.500 unidades de pedido/segundo). |Número inteiro |Não (predefinição: 5) |
| escreverBatchTimeout |Tempo de espera para a operação terminar antes que se esmua. |timespan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |

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

Para mais informações, consulte o artigo [do conector DB da Azure Cosmos.](data-factory-azure-documentdb-connector.md#copy-activity-properties)

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Base de Dados Azure SQL, defina o **tipo** de serviço ligado à **AzureSqlDatabase,** e especifique as seguintes propriedades na secção **de tiposproperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| conexãoStragem |Especifique as informações necessárias para ligar à instância de Base de Dados Azure SQL para a propriedade connectionString. |Sim |

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

Para mais informações, consulte o artigo [do conector Azure SQL.](data-factory-azure-sql-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados de base de dados Azure SQL, defina o **tipo** de conjunto de dados para **AzureSqlTable** e especifique as seguintes propriedades na secção **de tipos de transferências:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na página da Base de Dados Azure SQL a que o serviço ligado se refere. |Sim |

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
Para mais informações, consulte o artigo [do conector Azure SQL.](data-factory-azure-sql-connector.md#dataset-properties)

### <a name="sql-source-in-copy-activity"></a>Fonte SQL na Atividade de Cópia
Se estiver a copiar dados de uma Base de Dados Azure SQL, desaver o **tipo** de fonte da atividade de cópia para **SqlSource,** e especifique as seguintes propriedades na secção **de origem:**


| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| parametrómetros de reserva armazenados |Parâmetros para o procedimento armazenado. |Pares de nomes/valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. |Não |

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
Para mais informações, consulte o artigo [do conector Azure SQL.](data-factory-azure-sql-connector.md#copy-activity-properties)

### <a name="sql-sink-in-copy-activity"></a>Pia SQL na atividade de cópia
Se estiver a copiar dados para a Base de Dados Azure SQL, desaperte o tipo de **pia** da atividade de cópia para **o SqlSink,** e especifique as seguintes propriedades na secção do **lavatório:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| escreverBatchTimeout |Tempo de espera para que o funcionamento do encaixe do lote esteja concluído antes de esgotar o tempo. |timespan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |
| escreverBatchSize |Insere dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanUpScript |Especifique uma consulta para a Copy Activity para executar de modo a que os dados de uma fatia específica seja limpo. |Uma declaração de consulta. |Não |
| sliceIdentifierColumnName |Especifique um nome de coluna para a Atividade de Cópia para preencher com identificador de fatias gerados automaticamente, que é utilizado para limpar dados de uma fatia específica quando se revesse. |Nome da coluna de uma coluna com tipo de dados binário (32). |Não |
| sqlWriterStorEdProcedureName |Nome do procedimento armazenado que aumenta os dados (atualizações/inserções) na tabela-alvo. |Nome do procedimento armazenado. |Não |
| parametrómetros de reserva armazenados |Parâmetros para o procedimento armazenado. |Pares de nomes/valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. |Não |
| SqlWriterTableType |Especifique um nome de tipo de mesa a ser utilizado no procedimento armazenado. A atividade de cópia torna os dados disponíveis numa tabela temporária com este tipo de tabela. O código de procedimento armazenado pode então fundir os dados que estão a ser copiados com os dados existentes. |Um nome do tipo de mesa. |Não |

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

Para mais informações, consulte o artigo [do conector Azure SQL.](data-factory-azure-sql-connector.md#copy-activity-properties)

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado a Azure Synapse Analytics, defina o **tipo** de serviço ligado ao **AzureSqlDW,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| conexãoStragem |Especifique as informações necessárias para ligar à instância Azure Synapse Analytics para a propriedade connectionString. |Sim |



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

Para mais informações, consulte o artigo [do conector Azure Synapse Analytics.](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Azure Synapse Analytics, defina o **tipo** de conjunto de dados para **AzureSqlDWTable** e especifique as seguintes propriedades na secção **de tiposproperias:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na base de dados Azure Synapse Analytics a que o serviço ligado se refere. |Sim |

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

Para mais informações, consulte o artigo [do conector Azure Synapse Analytics.](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties)

### <a name="azure-synapse-analytics-source-in-copy-activity"></a>Fonte de análise Azure Synapse na atividade de cópia
Se estiver a copiar dados da Azure Synapse Analytics, descreva o **tipo** de fonte da atividade de cópia para **o SqlDWSource,** e especifique as seguintes propriedades na secção **de origem:**


| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| parametrómetros de reserva armazenados |Parâmetros para o procedimento armazenado. |Pares de nomes/valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. |Não |

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

Para mais informações, consulte o artigo [do conector Azure Synapse Analytics.](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

### <a name="azure-synapse-analytics-sink-in-copy-activity"></a>Azure Synapse Analytics Sink in Copy Activity
Se estiver a copiar dados para a Azure Synapse Analytics, descreva o **tipo** de pia da atividade da cópia para **o SqlDWSink,** e especifique as seguintes propriedades na secção do **lavatório:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| sqlWriterCleanUpScript |Especifique uma consulta para a Copy Activity para executar de modo a que os dados de uma fatia específica seja limpo. |Uma declaração de consulta. |Não |
| permitir A Base DePoly |Indica se deve utilizar a PolyBase (quando aplicável) em vez do mecanismo BULKINSERT. <br/><br/> **A utilização do PolyBase é a forma recomendada de carregar dados em Synapse Analytics.** |Verdadeiro <br/>Falso (predefinição) |Não |
| poliBaseSettings |Um grupo de propriedades que podem ser especificadas quando a propriedade **allowPolybase** é definida como **verdadeira** . |&nbsp; |Não |
| rejeitarValue |Especifica o número ou percentagem de linhas que podem ser rejeitadas antes da consulta falhar. <br/><br/>Saiba mais sobre as opções de rejeição da PolyBase na secção **de Argumentos** do tema CREATE EXTERNAL [TABLE (Transact-SQL).](/sql/t-sql/statements/create-external-table-transact-sql) |0 (padrão), 1, 2, ... |Não |
| rejeitarType |Especifica se a opção rejeitar oValue é especificada como um valor literal ou uma percentagem. |Valor (padrão), Percentagem |Não |
| rejeitarSampleValue |Determina o número de linhas a recuperar antes que o PolyBase recalcule a percentagem de linhas rejeitadas. |1, 2, ... |Sim, se **rejeitarType** é **percentagem** |
| utilizarTypeDefault |Especifica como lidar com valores em falta em ficheiros de texto delimitados quando o PolyBase recupera dados do ficheiro de texto.<br/><br/>Saiba mais sobre esta propriedade a partir da secção Argumentos em [FORMATO DE FICHEIRO EXTERNO (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql). |Verdadeiro, Falso (padrão) |Não |
| escreverBatchSize |Insere dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize |Inteiro (número de linhas) |Não (padrão: 10000) |
| escreverBatchTimeout |Tempo de espera para que o funcionamento do encaixe do lote esteja concluído antes de esgotar o tempo. |timespan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |

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

Para mais informações, consulte o artigo [do conector Azure Synapse Analytics.](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Pesquisa Cognitiva Azure, defina o **tipo** de serviço ligado ao **AzureSearch,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
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

Para obter mais informações, consulte o artigo [do conector Azure Cognitive Search.](data-factory-azure-search-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados de pesquisa cognitiva Azure, defina o **tipo** de conjunto de dados para **AzureSearchIndex** , e especifique as seguintes propriedades na secção **de tipoProperties:**

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| tipo | A propriedade tipo deve ser definida para **AzureSearchIndex** .| Sim |
| nome do índice | Nome do índice de pesquisa. A Data Factory não cria o índice. O índice deve existir na Pesquisa Cognitiva Azure. | Sim |

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

Para obter mais informações, consulte o artigo [do conector Azure Cognitive Search.](data-factory-azure-search-connector.md#dataset-properties)

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Azure Cognitive Search Index Sink in Copy Activity
Se estiver a copiar dados para um índice de pesquisa, defina o tipo de **pia** da atividade de cópia para **AzureSearchIndexSink,** e especifique as seguintes propriedades na secção do **lavatório:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| Escrever Comportamento | Especifica se deve fundir ou substituir quando um documento já existe no índice. | Fusão (padrão)<br/>Carregar| Não |
| WriteBatchsize | Envia dados para o índice de pesquisa quando o tamanho do tampão atinge o writeBatchSize. | De 1 a 1.000. O valor predefinido é de 1000. | Não |

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

Para obter mais informações, consulte o artigo [do conector Azure Cognitive Search.](data-factory-azure-search-connector.md#copy-activity-properties)

## <a name="azure-table-storage"></a>Armazenamento de Tabelas do Azure

### <a name="linked-service"></a>Serviço ligado
Existem dois tipos de serviços ligados: serviço ligado a Azure Storage e serviço ligado ao Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
Para ligar a sua conta de armazenamento Azure a uma fábrica de dados utilizando a chave de **conta,** crie um serviço ligado ao Azure Storage. Para definir um serviço ligado ao Azure Storage, defina o **tipo** de serviço ligado ao **AzureStorage** . Em seguida, pode especificar as seguintes propriedades na secção **de tipos de direito:**

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo |O tipo de propriedade deve ser definido para: **AzureStorage** |Sim |
| conexãoStragem |Especifique as informações necessárias para ligar ao armazenamento Azure para a propriedade connectionString. |Sim |

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

#### <a name="azure-storage-sas-linked-service"></a>Serviço ligado a Azure Storage SAS
O serviço Azure Storage SAS permite-lhe ligar uma Conta de Armazenamento Azure a uma fábrica de dados Azure utilizando uma Assinatura de Acesso Partilhado (SAS). Fornece à fábrica de dados um acesso restrito/temporal a todos/recursos específicos (blob/contentor) no armazenamento. Para ligar a sua conta de armazenamento Azure a uma fábrica de dados utilizando a Assinatura de Acesso Partilhado, crie um serviço ligado ao Azure Storage SAS. Para definir um serviço ligado ao Azure Storage SAS, defina o **tipo** de serviço ligado ao **AzureStorageSas** . Em seguida, pode especificar as seguintes propriedades na secção **de tipos de direito:**

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo |O tipo de propriedade deve ser definido para: **AzureStorageSas** |Sim |
| SasUri |Especifique a assinatura de acesso partilhado URI aos recursos de armazenamento Azure, tais como bolha, recipiente ou mesa. |Sim |

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

Para obter mais informações sobre estes serviços ligados, consulte o artigo [do conector de armazenamento de mesa Azure.](data-factory-azure-table-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Quadro Azure, defina o **tipo** do conjunto de dados para **AzureTable** e especifique as seguintes propriedades na secção **de tiposproperias:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na instância Azure Table Database a que o serviço ligado se refere. |Sim. Quando um nome de mesa é especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se for também especificado um azureTableSourceQuery, os registos da tabela que satisfaz a consulta são copiados para o destino. |

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

Para obter mais informações sobre estes serviços ligados, consulte o artigo [do conector de armazenamento de mesa Azure.](data-factory-azure-table-connector.md#dataset-properties)

### <a name="azure-table-source-in-copy-activity"></a>Fonte de tabela Azure na atividade de cópia
Se estiver a copiar dados do Azure Table Storage, desapedaça o tipo de **fonte** da atividade de cópia para **AzureTableSource** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| azureTableSourceQuery |Utilize a consulta personalizada para ler dados. |Corda de consulta de mesa azul. Veja os exemplos na secção seguinte. |Não. Quando um nome de mesa é especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se for também especificado um azureTableSourceQuery, os registos da tabela que satisfaz a consulta são copiados para o destino. |
| azureTableSourceIgnoreTableNotFound |Indicar se engolir a exceção da tabela não existe. |TRUE<br/>FALSE |Não |

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

Para obter mais informações sobre estes serviços ligados, consulte o artigo [do conector de armazenamento de mesa Azure.](data-factory-azure-table-connector.md#copy-activity-properties)

### <a name="azure-table-sink-in-copy-activity"></a>Pia de mesa Azure na atividade de cópia
Se estiver a copiar dados para o Azure Table Storage, desaperte o tipo de **pia** da atividade da cópia para **AzureTableSink,** e especifique as seguintes propriedades na secção do **lavatório:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Valor da chave de partição padrão que pode ser usado pela pia. |Um valor de corda. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são utilizados como teclas de partição. Se não for especificado, a AzureTableDefaultPartitionKeyValue é utilizada como chave de partição. |Um nome de coluna. |Não |
| nome azureTableRowKey |Especifique o nome da coluna cujos valores de coluna são utilizados como chave de linha. Se não for especificado, utilize um GUID para cada linha. |Um nome de coluna. |Não |
| azureTableInsertType |O modo de inserir dados na tabela Azure.<br/><br/>Esta propriedade controla se as linhas existentes na tabela de saída com chaves de divisórias e linha correspondentes têm os seus valores substituídos ou fundidos. <br/><br/>Para saber como funcionam estas configurações (fundir e substituir), consulte [inserir ou fundir tópicos da Entidade](/rest/api/storageservices/Insert-Or-Merge-Entity) e inserir ou [substituir](/rest/api/storageservices/Insert-Or-Replace-Entity) entidades. <br/><br> Esta definição aplica-se ao nível da linha, não ao nível da tabela, e nenhuma das opções elimina linhas na tabela de saída que não existem na entrada. |fusão (padrão)<br/>substituir |Não |
| escreverBatchSize |Insere dados na tabela Azure quando a escritaBatchSize ou escreverBatchTimeout é atingida. |Inteiro (número de linhas) |Não (padrão: 10000) |
| escreverBatchTimeout |Insere dados na tabela Azure quando a escritaBatchSize ou escreverBatchTimeout é atingido |timespan<br/><br/>Exemplo: "00:20:00" (20 minutos) |Não (Padrão para armazenamento valor de tempo limite 90 seg) |

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
Para obter mais informações sobre estes serviços ligados, consulte o artigo [do conector de armazenamento de mesa Azure.](data-factory-azure-table-connector.md#copy-activity-properties)

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Amazon Redshift, defina o **tipo** de serviço ligado ao **AmazonRedshift** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| servidor |Endereço IP ou nome de anfitrião do servidor Amazon Redshift. |Sim |
| porta |O número da porta TCP que o servidor Amazon Redshift utiliza para ouvir as ligações dos clientes. |Não, valor predefinido: 5439 |
| base de dados |Nome da base de dados Amazon Redshift. |Sim |
| nome de utilizador |Nome do utilizador que tem acesso à base de dados. |Sim |
| palavra-passe |Senha para a conta de utilizador. |Sim |

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
Para definir um conjunto de dados Redshift da Amazon, defina o **tipo** de conjunto de dados para **RelationalTable** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na base de dados Amazon Redshift a que o serviço ligado se refere. |Não (se a **consulta** de **RelationalSource** for especificada) |


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

### <a name="relational-source-in-copy-activity"></a>Fonte relacional na atividade de cópia
Se estiver a copiar dados da Amazon Redshift, desaprote o tipo de **fonte** da atividade de cópia para **RelationalSource** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **o nome** de tabela do conjunto de **dados** for especificado) |

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
Para definir um serviço ligado à IBM DB2, defina o **tipo** de serviço ligado ao **OnPremisesDB2** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| servidor |Nome do servidor DB2. |Sim |
| base de dados |Nome da base de dados DB2. |Sim |
| esquema |O nome do esquema na base de dados. O nome do esquema é sensível a maiíssimos. |Não |
| authenticationType |Tipo de autenticação usada para ligar à base de dados DB2. Os valores possíveis são: Anónimo, Básico e Windows. |Sim |
| nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Básica ou o Windows. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço data factory deve usar para ligar à base de dados DB2 no local. |Sim |

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
Para definir um conjunto de dados DB2, defina o **tipo** de conjunto de dados para **RelationalTable** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome do quadro na placa DB2 Database a que o serviço ligado se refere. A mesa No nome é sensível a casos. |Não (se a **consulta** de **RelationalSource** for especificada)

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

### <a name="relational-source-in-copy-activity"></a>Fonte relacional na atividade de cópia
Se estiver a copiar dados da IBM DB2, descreva o **tipo** de fonte da atividade de cópia para **RelationalSource,** e especifique as seguintes propriedades na secção **de origem:**


| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""`. |Não (se **o nome** de tabela do conjunto de **dados** for especificado) |

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
Para definir um serviço ligado ao MySQL, defina o **tipo** de serviço ligado ao **OnPremisesMySql,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| servidor |Nome do servidor MySQL. |Sim |
| base de dados |Nome da base de dados MySQL. |Sim |
| esquema |O nome do esquema na base de dados. |Não |
| authenticationType |Tipo de autenticação usada para ligar à base de dados MySQL. Os valores possíveis são: `Basic` . |Sim |
| userName |Especifique o nome do utilizador para ligar à base de dados MySQL. |Sim |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador que especificou. |Sim |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados MySQL no local. |Sim |

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

Para mais informações, consulte o artigo [do conector MySQL.](data-factory-onprem-mysql-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados MySQL, defina o **tipo** de conjunto de dados para **RelationalTable** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na página da Base de Dados MySQL a que o serviço ligado se refere. |Não (se a **consulta** de **RelationalSource** for especificada) |

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
Para mais informações, consulte o artigo [do conector MySQL.](data-factory-onprem-mysql-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte relacional na atividade de cópia
Se estiver a copiar dados a partir de uma base de dados MySQL, desaprote o tipo de **fonte** da atividade de cópia para **RelationalSource** e especifique as seguintes propriedades na secção **de origem:**


| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **o nome** de tabela do conjunto de **dados** for especificado) |


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

Para mais informações, consulte o artigo [do conector MySQL.](data-factory-onprem-mysql-connector.md#copy-activity-properties)

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Oracle, defina o **tipo** de serviço ligado ao **OnPremisesOracle,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| Tipo de motorista | Especificar qual o condutor a utilizar para copiar dados de/para a Oracle Database. Os valores permitidos são **Microsoft** ou **ODP** (predefinição). Consulte a versão e a secção de instalação suportadas nos detalhes do controlador. | Não |
| conexãoStragem | Especifique as informações necessárias para ligar à placa oracle Database para a propriedade connectionString. | Sim |
| gatewayName | Nome do gateway que é usado para ligar ao servidor Oracle no local |Sim |

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

Para mais informações, consulte o artigo [do conector oracle.](data-factory-onprem-oracle-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Oracle, defina o **tipo** de conjunto de dados para **o OracleTable** e especifique as seguintes propriedades na secção **de tipos de direitos:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na Base de Dados Oráculo a que o serviço ligado se refere. |Não (se **o oracleReaderQuery** da **OracleSource** for especificado) |

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
Para mais informações, consulte o artigo [do conector oracle.](data-factory-onprem-oracle-connector.md#dataset-properties)

### <a name="oracle-source-in-copy-activity"></a>Fonte do Oráculo na Atividade de Cópia
Se estiver a copiar dados de uma base de dados da Oracle, desavere o tipo de **fonte** da atividade de cópia para o **OracleSource,** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| OracleReaderQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable` <br/><br/>Se não for especificado, a declaração SQL que é executada: `select * from MyTable` |Não (se **o nome** de tabela do conjunto de **dados** for especificado) |

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

Para mais informações, consulte o artigo [do conector oracle.](data-factory-onprem-oracle-connector.md#copy-activity-properties)

### <a name="oracle-sink-in-copy-activity"></a>Pia oráculo na atividade de cópia
Se estiver a copiar dados para a base de dados da Oráculo, desaperte o tipo de **pia** da atividade de cópia para o **OracleSink,** e especifique as seguintes propriedades na secção do **lavatório:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| escreverBatchTimeout |Tempo de espera para que o funcionamento do encaixe do lote esteja concluído antes de esgotar o tempo. |timespan<br/><br/> Exemplo: 00:30:00 (30 minutos). |Não |
| escreverBatchSize |Insere dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 100) |
| sqlWriterCleanUpScript |Especifique uma consulta para a Copy Activity para executar de modo a que os dados de uma fatia específica seja limpo. |Uma declaração de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a Atividade de Cópia para preencher com identificador de fatias gerados automaticamente, que é utilizado para limpar dados de uma fatia específica quando se revesse. |Nome da coluna de uma coluna com tipo de dados binário (32). |Não |

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
Para mais informações, consulte o artigo [do conector oracle.](data-factory-onprem-oracle-connector.md#copy-activity-properties)

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado postgreSQL, defina o **tipo** de serviço ligado ao **OnPremisesPostgreSql,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| servidor |Nome do servidor PostgreSQL. |Sim |
| base de dados |Nome da base de dados PostgreSQL. |Sim |
| esquema |O nome do esquema na base de dados. O nome do esquema é sensível a maiíssimos. |Não |
| authenticationType |Tipo de autenticação utilizada para ligar à base de dados PostgreSQL. Os valores possíveis são: Anónimo, Básico e Windows. |Sim |
| nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Básica ou o Windows. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço data factory deve usar para ligar à base de dados PostgreSQL no local. |Sim |

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
Para mais informações, consulte o artigo [do conector PostgreSQL.](data-factory-onprem-postgresql-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados PostgreSQL, defina o **tipo** de conjunto de dados para **RelationalTable** e especifique as seguintes propriedades na secção **de tipos de direitos:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na página da Base de Dados PostgreSQL a que o serviço ligado se refere. A mesa No nome é sensível a casos. |Não (se a **consulta** de **RelationalSource** for especificada) |

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
Para mais informações, consulte o artigo [do conector PostgreSQL.](data-factory-onprem-postgresql-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte relacional na atividade de cópia
Se estiver a copiar dados a partir de uma base de dados PostgreSQL, desenverda o tipo de **fonte** da atividade de cópia para **RelationalSource** e especifique as seguintes propriedades na secção **de origem:**


| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: "consulta": "selecione * do \" MySchema \" . \" MyTable \" ". |Não (se **o nome** de tabela do conjunto de **dados** for especificado) |

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

Para mais informações, consulte o artigo [do conector PostgreSQL.](data-factory-onprem-postgresql-connector.md#copy-activity-properties)

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado ao SAP Business Warehouse (BW), defina o **tipo** de serviço ligado ao **SapBw,** e especifique as seguintes propriedades na secção **typeProperties:**

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
servidor | Nome do servidor em que reside a instância SAP BW. | cadeia | Sim
sistemaNumbre | Número do sistema do sistema SAP BW. | Número decimal de dois dígitos representado como uma corda. | Sim
clientId | Identificação do cliente do cliente no sistema SAP W. | Número decimal de três dígitos representado como uma corda. | Sim
nome de utilizador | Nome do utilizador que tem acesso ao servidor SAP | cadeia | Sim
palavra-passe | A palavra-passe do utilizador. | cadeia | Sim
gatewayName | Nome do gateway que o serviço data factory deve usar para ligar ao caso SAP BW no local. | cadeia | Sim
criptografadoCredential | A cadeia de credencial encriptada. | cadeia (de carateres) | No

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

Para mais informações, consulte o artigo [do conector SAP Business Warehouse.](data-factory-sap-business-warehouse-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados SAP BW, defina o **tipo** de conjunto de dados **para RelationalTable** . Não existem propriedades específicas do tipo suportadas para o conjunto de dados SAP BW do tipo **RelationalTable** .

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
Para mais informações, consulte o artigo [do conector SAP Business Warehouse.](data-factory-sap-business-warehouse-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte relacional na atividade de cópia
Se estiver a copiar dados do SAP Business Warehouse, desaprote o **tipo** de fonte da atividade de cópia para **RelationalSource,** e especifique as seguintes propriedades na secção **de origem:**


| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta | Especifica a consulta MDX para ler dados a partir da instância SAP BW. | Consulta MDX. | Sim |

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

Para mais informações, consulte o artigo [do conector SAP Business Warehouse.](data-factory-sap-business-warehouse-connector.md#copy-activity-properties)

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado a SAP HANA, defina o **tipo** de serviço ligado ao **SapHana,** e especifique as seguintes propriedades na secção **typeProperties:**

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
servidor | Nome do servidor em que reside a instância SAP HANA. Se o seu servidor estiver a utilizar uma porta personalizada, especifique `server:port` . | cadeia | Sim
authenticationType | Tipo de autenticação. | corda. "Básico" ou "Windows" | Sim
nome de utilizador | Nome do utilizador que tem acesso ao servidor SAP | cadeia | Sim
palavra-passe | A palavra-passe do utilizador. | cadeia | Sim
gatewayName | Nome do gateway que o serviço data factory deve utilizar para ligar ao local de entrada SAP HANA. | cadeia | Sim
criptografadoCredential | A cadeia de credencial encriptada. | cadeia (de carateres) | No

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
Para mais informações, consulte o artigo [do conector SAP HANA.](data-factory-sap-hana-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados SAP HANA, defina o **tipo** de conjunto de dados **para RelationalTable** . Não existem propriedades específicas do tipo suportadas para o conjunto de dados SAP HANA do tipo **RelationalTable** .

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
Para mais informações, consulte o artigo [do conector SAP HANA.](data-factory-sap-hana-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte relacional na atividade de cópia
Se estiver a copiar dados de uma loja de dados SAP HANA, desaprote o tipo de **fonte** da atividade de cópia para **RelationalSource** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta | Especifica a consulta SQL para ler dados a partir da instância SAP HANA. | Consulta SQL. | Sim |


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

Para mais informações, consulte o artigo [do conector SAP HANA.](data-factory-sap-hana-connector.md#copy-activity-properties)


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Serviço ligado
Cria um serviço de tipo ligado **OnPremisesSqlServer** para ligar uma base de dados do SQL Server a uma fábrica de dados. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao SQL Server.

A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao SQL Server.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **OnPremisesSqlServer** . |Sim |
| conexãoStragem |Especifique a informação de ligação Desaquipeia as informações necessárias para ligar à base de dados do SQL Server utilizando a autenticação SQL ou a autenticação do Windows. |Sim |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados do SQL Server. |Sim |
| nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a Autenticação do Windows. Exemplo: **nome de utilizador do nome \\ de domínio** . |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |

Pode encriptar credenciais utilizando o cmdlet **New-AzDataFactoryEncryptValue** e usá-las na cadeia de ligação, como mostrado no exemplo seguinte (Propriedade **Criptografada)**

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
#### <a name="example-json-for-using-windows-authentication"></a>Exemplo: JSON para utilização de autenticação do Windows

Se o nome de utilizador e a palavra-passe forem especificados, gateway usa-os para personificar a conta de utilizador especificada para se ligar à base de dados do SQL Server. Caso contrário, o gateway conecta-se diretamente ao SQL Server com o contexto de segurança do Gateway (a sua conta de arranque).

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

Para obter mais informações, consulte o artigo [do conector SQL Server.](data-factory-sqlserver-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do SQL Server, defina o **tipo** de conjunto de dados para **SqlServerTable** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela ou visualização na página da Base de Dados do Servidor SQL a que o serviço ligado se refere. |Sim |

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

Para obter mais informações, consulte o artigo [do conector SQL Server.](data-factory-sqlserver-connector.md#dataset-properties)

### <a name="sql-source-in-copy-activity"></a>Fonte sql em atividade de cópia
Se estiver a copiar dados a partir de uma base de dados do SQL Server, desaver o tipo de **fonte** da atividade de cópia para **SqlSource** e especifique as seguintes propriedades na secção **de origem:**


| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. Pode fazer referência a várias tabelas da base de dados referenciadas pelo conjunto de dados de entrada. Se não for especificado, a declaração SQL que é executada: selecione a partir do MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| parametrómetros de reserva armazenados |Parâmetros para o procedimento armazenado. |Pares de nomes/valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. |Não |

Se o **SqlReaderQuery** for especificado para o SqlSource, a Atividade de Cópia executa esta consulta com a fonte de Base de Dados do Servidor SQL para obter os dados.

Em alternativa, pode especificar um procedimento armazenado especificando o **nome sqlReaderStoredProcedureName** e **os Computadores Deprocedures armazenados** (se o procedimento armazenado tiver parâmetros).

Se não especificar o sqlReaderQuery ou o sqlReaderStorStoredProcedureName, as colunas definidas na secção de estrutura são utilizadas para construir uma consulta selecionada para correr contra a Base de Dados do Servidor SQL. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.

> [!NOTE]
> Quando utilizar **o nome sqlReaderStoredProcedureName,** ainda precisa de especificar um valor para a propriedade **tableName** no conjunto de dados JSON. No entanto, não há validações feitas contra esta tabela.


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

Neste exemplo, **sqlReaderQuery** é especificado para o SqlSource. A Atividade de Cópia executa esta consulta com a fonte de Base de Dados do Servidor SQL para obter os dados. Em alternativa, pode especificar um procedimento armazenado especificando o **nome sqlReaderStoredProcedureName** e **os Computadores Deprocedures armazenados** (se o procedimento armazenado tiver parâmetros). O SqlReaderQuery pode fazer referência a várias tabelas dentro da base de dados referenciada pelo conjunto de dados de entrada. Não se limita apenas ao conjunto de tabelas como tabela Do nome de dadosProperty.

Se não especificar sqlReaderQuery ou sqlReaderStorEdProcedureName, as colunas definidas na secção de estrutura são utilizadas para construir uma consulta selecionada para correr contra a Base de Dados do Servidor SQL. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.

Para obter mais informações, consulte o artigo [do conector SQL Server.](data-factory-sqlserver-connector.md#copy-activity-properties)

### <a name="sql-sink-in-copy-activity"></a>Pia sql na atividade de cópia
Se estiver a copiar dados para uma base de dados do SQL Server, desaperte o **tipo** de pia da atividade de cópia para **o SqlSink** e especifique as seguintes propriedades na secção do **lavatório:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| escreverBatchTimeout |Tempo de espera para que o funcionamento do encaixe do lote esteja concluído antes de esgotar o tempo. |timespan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |
| escreverBatchSize |Insere dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanUpScript |Especifique a consulta para a Atividade de Cópia para executar de modo a que os dados de uma fatia específica seja limpo. Para mais informações, consulte a secção de repetibilidade. |Uma declaração de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a Atividade de Cópia para preencher com identificador de fatias gerados automaticamente, que é utilizado para limpar dados de uma fatia específica quando se revesse. Para mais informações, consulte a secção de repetibilidade. |Nome da coluna de uma coluna com tipo de dados binário (32). |Não |
| sqlWriterStorEdProcedureName |Nome do procedimento armazenado que aumenta os dados (atualizações/inserções) na tabela-alvo. |Nome do procedimento armazenado. |Não |
| parametrómetros de reserva armazenados |Parâmetros para o procedimento armazenado. |Pares de nomes/valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. |Não |
| SqlWriterTableType |Especifique o nome do tipo de mesa a ser utilizado no procedimento armazenado. A atividade de cópia torna os dados disponíveis numa tabela temporária com este tipo de tabela. O código de procedimento armazenado pode então fundir os dados que estão a ser copiados com os dados existentes. |Um nome do tipo de mesa. |Não |

#### <a name="example"></a>Exemplo
O pipeline contém uma Atividade de Cópia que está configurada para utilizar estes conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **BlobSource** e o tipo **de pia** é definido para **SqlSink** .

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

Para obter mais informações, consulte o artigo [do conector SQL Server.](data-factory-sqlserver-connector.md#copy-activity-properties)

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Sybase, defina o **tipo** de serviço ligado à **Base OnPremisesSybase** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| servidor |Nome do servidor Sybase. |Sim |
| base de dados |Nome da base de dados Sybase. |Sim |
| esquema |O nome do esquema na base de dados. |Não |
| authenticationType |Tipo de autenticação usada para ligar à base de dados Sybase. Os valores possíveis são: Anónimo, Básico e Windows. |Sim |
| nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Básica ou o Windows. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço data factory deve usar para ligar à base de dados Sybase no local. |Sim |

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

Para mais informações, consulte o artigo [do conector Sybase.](data-factory-onprem-sybase-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados da Sybase, defina o **tipo** de conjunto de dados para **RelationalTable** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na instância Sybase Database a que o serviço ligado se refere. |Não (se a **consulta** de **RelationalSource** for especificada) |

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

Para mais informações, consulte o artigo [do conector Sybase.](data-factory-onprem-sybase-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte relacional na atividade de cópia
Se estiver a copiar dados a partir de uma base de dados da Sybase, desaprove o tipo de **fonte** da atividade de cópia para **RelationalSource** e especifique as seguintes propriedades na secção **de origem:**


| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **o nome** de tabela do conjunto de **dados** for especificado) |

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

Para mais informações, consulte o artigo [do conector Sybase.](data-factory-onprem-sybase-connector.md#copy-activity-properties)

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Teradata, defina o **tipo** de serviço ligado ao **OnPremisesTeradata,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| servidor |Nome do servidor Teradata. |Sim |
| authenticationType |Tipo de autenticação usada para ligar à base de dados Teradata. Os valores possíveis são: Anónimo, Básico e Windows. |Sim |
| nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Básica ou o Windows. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço data factory deve usar para ligar à base de dados Teradata no local. |Sim |

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

Para mais informações, consulte o artigo [do conector Teradata.](data-factory-onprem-teradata-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Teradata Blob, defina o **tipo** de conjunto de dados **para RelationalTable** . Atualmente, não existem propriedades tipo suportadas para o conjunto de dados Teradata.

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

Para mais informações, consulte o artigo [do conector Teradata.](data-factory-onprem-teradata-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte relacional na atividade de cópia
Se estiver a copiar dados a partir de uma base de dados teradata, desaprote o tipo de **fonte** da atividade de cópia para **RelationalSource** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Sim |

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

Para mais informações, consulte o artigo [do conector Teradata.](data-factory-onprem-teradata-connector.md#copy-activity-properties)

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Cassandra, defina o **tipo** de serviço ligado ao **OnPremisesCassandra,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| anfitrião |Um ou mais endereços IP ou nomes de anfitrião de servidores Cassandra.<br/><br/>Especifique uma lista separada de vírgulas de endereços IP ou nomes de anfitrião para ligar a todos os servidores simultaneamente. |Sim |
| porta |A porta TCP que o servidor Cassandra usa para ouvir as ligações do cliente. |Não, valor predefinido: 9042 |
| authenticationType |Básico, ou Anónimo |Sim |
| nome de utilizador |Especifique o nome de utilizador para a conta do utilizador. |Sim, se a autenticaçãoType estiver definida como Basic. |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador. |Sim, se a autenticaçãoType estiver definida como Basic. |
| gatewayName |O nome do portal que é usado para ligar à base de dados cassandra no local. |Sim |
| criptografadoCredential |Credencial encriptada pelo portal. |Não |

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

Para mais informações, consulte o artigo [do conector Cassandra.](data-factory-onprem-cassandra-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Cassandra, defina o **tipo** de conjunto de dados para **CassandraTable** e especifique as seguintes propriedades na secção **de tipos de direitos:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| keyspace |Nome do espaço-chave ou esquema na base de dados de Cassandra. |Sim (Se **a consulta** para **CassandraSource** não estiver definida). |
| tableName |O nome da mesa na base de dados da Cassandra. |Sim (Se **a consulta** para **CassandraSource** não estiver definida). |

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

Para mais informações, consulte o artigo [do conector Cassandra.](data-factory-onprem-cassandra-connector.md#dataset-properties)

### <a name="cassandra-source-in-copy-activity"></a>Fonte cassandra na atividade de cópia
Se estiver a copiar dados da Cassandra, desaver o tipo de **fonte** da atividade de cópia para **CassandraSource,** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Consulta SQL-92 ou consulta CQL. Consulte [a referência CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao utilizar a consulta SQL, especifique **o nome do keyspace.table** para representar a tabela que pretende consultar. |Não (se o nome de tabela e o espaço de teclas no conjunto de dados forem definidos). |
| consistênciaLevel |O nível de consistência especifica quantas réplicas devem responder a um pedido de leitura antes de devolver os dados à aplicação do cliente. Cassandra verifica o número especificado de réplicas de dados para satisfazer o pedido de leitura. |UM, DOIS, TRÊS, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Consulte [a consistência dos dados](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) para obter informações mais pormenores. |Não. O valor predefinido é ONE. |

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

Para mais informações, consulte o artigo [do conector Cassandra.](data-factory-onprem-cassandra-connector.md#copy-activity-properties)

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado a MongoDB, defina o **tipo** de serviço ligado ao **OnPremisesMongoDB,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| servidor |Endereço IP ou nome de anfitrião do servidor MongoDB. |Sim |
| porta |Porta TCP que o servidor MongoDB utiliza para ouvir as ligações do cliente. |Valor opcional, padrão: 27017 |
| authenticationType |Básico, ou Anónimo. |Sim |
| nome de utilizador |Conta de utilizador para aceder a MongoDB. |Sim (se for utilizada a autenticação básica). |
| palavra-passe |A palavra-passe do utilizador. |Sim (se for utilizada a autenticação básica). |
| authSource |Nome da base de dados MongoDB que pretende utilizar para verificar as suas credenciais para autenticação. |Opcional (se for utilizada a autenticação básica). padrão: utiliza a conta de administração e a base de dados especificada através da base de dados PropriedadeName. |
| base de dados Nome |Nome da base de dados mongoDB a que pretende aceder. |Sim |
| gatewayName |Nome do portal que acede à loja de dados. |Sim |
| criptografadoCredential |Credencial encriptada por gateway. |Opcional |

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

Para mais informações, consulte o [artigo do conector MongoDB](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados MongoDB, defina o **tipo** de conjunto de dados para **MongoDbCollection** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| coleçãoName |Nome da coleção na base de dados mongoDB. |Sim |

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

Para mais informações, consulte o [artigo do conector MongoDB](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>Fonte mongoDB na atividade de cópia
Se estiver a copiar dados da MongoDB, descreva o **tipo** de fonte da atividade de cópia para **a MongoDbSource,** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL-92. Por exemplo: `select * from MyTable`. |Não (se o nome de **recolha** do conjunto de **dados** for especificado) |

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

Para mais informações, consulte o [artigo do conector MongoDB](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado ao Amazon S3, defina o **tipo** de serviço ligado ao **AwsAccessKey** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| accessKeyID |Identificação da chave de acesso secreta. |cadeia |Sim |
| SecretAccessKey |A chave de acesso secreto em si. |Cadeia secreta encriptada |Sim |

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

Para mais informações, consulte o [artigo do conector Amazon S3.](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Amazon S3, defina o **tipo** de conjunto de dados para **o AmazonS3** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| baldeName |O nome do balde S3. |String |Sim |
| key |A chave do objeto S3. |String |Não |
| prefixo |Prefixo para a tecla de objeto S3. São selecionados objetos cujas teclas começam com este prefixo. Só se aplica quando a chave estiver vazia. |String |Não |
| versão |A versão do objeto S3 se a versão S3 estiver ativada. |String |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat** . Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte [formato de texto,](data-factory-supported-file-and-compression-formats.md#text-format) [formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e secções [de Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como-está** entre lojas baseadas em ficheiros (cópia binária), salte a secção de formato nas definições de conjunto de dados de entrada e saída. |Não | |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2** e **ZipDeflate** . Os níveis suportados são: **Ideal** e **Mais rápido.** Para obter mais informações, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não | |


> [!NOTE]
> bucketName + tecla especifica a localização do objeto S3 onde o balde é o recipiente raiz para objetos S3 e a chave é o caminho completo para o objeto S3.

#### <a name="example-sample-dataset-with-prefix"></a>Exemplo: Conjunto de dados de amostra com prefixo

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
#### <a name="example-sample-data-set-with-version"></a>Exemplo: Conjunto de dados da amostra (com versão)

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
Na amostra, utilizamos valores fixos para propriedades chave e baldename no conjunto de dados Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Pode fazer com que a Data Factory calcule a chave e o baldeName dinamicamente no tempo de execução, utilizando variáveis do sistema como o SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Pode fazer o mesmo pela propriedade prefixada de um conjunto de dados Amazon S3. Consulte [as funções da Data Factory e as variáveis](data-factory-functions-variables.md) do sistema para obter uma lista de funções e variáveis suportadas.

Para mais informações, consulte o [artigo do conector Amazon S3.](data-factory-amazon-simple-storage-service-connector.md#dataset-properties)

### <a name="file-system-source-in-copy-activity"></a>Fonte do sistema de ficheiros na atividade de cópia
Se estiver a copiar dados do Amazon S3, desave o tipo de **fonte** da atividade de cópia para **FileSystemSource** e especifique as seguintes propriedades na secção **de origem:**


| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursivo |Especifica se deve listar novamente os objetos S3 sob o diretório. |verdadeiro/falso |Não |


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

Para mais informações, consulte o [artigo do conector Amazon S3.](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties)

## <a name="file-system"></a>Sistema de Ficheiros


### <a name="linked-service"></a>Serviço ligado
Pode ligar um sistema de ficheiros no local a uma fábrica de dados Azure com o serviço de **ficheiros No-local.** A tabela seguinte fornece descrições para elementos JSON específicos do serviço linked Server de ficheiros no local.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |Certifique-se de que a propriedade tipo está definida para **OnPremisesFileServer** . |Sim |
| anfitrião |Especifica o caminho da raiz da pasta que pretende copiar. Utilize o personagem de fuga ' \ ' para caracteres especiais na corda. Consulte por exemplo o serviço ligado à amostra e definições de conjunto de dados. |Sim |
| userid |Especifique o ID do utilizador que tem acesso ao servidor. |Não (se escolher o Criptografial) |
| palavra-passe |Especifique a palavra-passe para o utilizador (userid). |Não (se escolher o Criptografial |
| criptografadoCredential |Especifique as credenciais encriptadas que pode obter executando o New-AzDataFactoryEncryptValue cmdlet. |Não (se optar por especificar userid e palavra-passe em texto simples) |
| gatewayName |Especifica o nome do gateway que a Data Factory deve utilizar para ligar ao servidor de ficheiros no local. |Sim |

#### <a name="sample-folder-path-definitions"></a>Definições de trajetória de pasta de amostra

| Cenário | Hospedagem na definição de serviço ligado | fase de pasta na definição de conjunto de dados |
| --- | --- | --- |
| Pasta local na máquina Data Management Gateway: <br/><br/>Exemplos: D: \\ \* ou D:\pasta\sub-dobra\\* |D: \\ \\ (para a Gestão de Dados Gateway 2.0 e versões posteriores) <br/><br/> local (para versões anteriores do que data Management Gateway 2.0) |.\\\\ sub-dobragem ou pasta \\ \\ (para a gestão de dados Gateway 2.0 e versões posteriores) <br/><br/>D: \\ \\ ou D: \\ \\ \\ \\ sub-dobragem de pasta (para a versão gateway abaixo de 2.0) |
| Pasta partilhada remotamente: <br/><br/>Exemplos: \\ \\ \\ myserver share ou \\ \* \\ \\ \\ myserver share pasta \\ \\ subfolder\\* |\\\\\\\\partilha de myserver \\ \\ |.\\\\ ou \\ \\ sub-dobragem de pasta |


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

#### <a name="example-using-encryptedcredential"></a>Exemplo: Utilização de credenciais encriptadas

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

Para obter mais informações, consulte [o artigo do conector do Sistema de Ficheiros](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Sistema de Ficheiros, defina o **tipo** de conjunto de dados para **FileShare** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Especifica o subpato à pasta. Utilize o personagem de fuga '\' para caracteres especiais na corda. Consulte por exemplo o serviço ligado à amostra e definições de conjunto de dados.<br/><br/>Pode combinar esta propriedade com **partição Para** ter caminhos de pasta baseados em intervalos de datas de início/fim da fatia. |Sim |
| fileName |Especifique o nome do ficheiro na **pastaPata** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando o data de ficheiro não é especificado para um conjunto de dados de saída, o nome do ficheiro gerado encontra-se no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| ficheiroFiltro |Especifique um filtro a ser utilizado para selecionar um subconjunto de ficheiros na pastaPalho em vez de todos os ficheiros. <br/><br/>Os valores permitidos são: `*` (múltiplos caracteres) e `?` (único carácter).<br/><br/>Exemplo 1: "fileFilter": "*.log"<br/>Exemplo 2: "fileFilter": 2016-1-?. txt "<br/><br/>Note que o ficheiroFiltro é aplicável para um conjunto de dados de FileShare de entrada. |Não |
| partitionedBy |Pode utilizar partitionedBy para especificar uma pasta dinâmicaPa/nome de ficheiros Para dados da série de tempo. Um exemplo é pastaPamota parametrizada para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat** . Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte [formato de texto,](data-factory-supported-file-and-compression-formats.md#text-format) [formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e secções [de Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como-está** entre lojas baseadas em ficheiros (cópia binária), salte a secção de formato nas definições de conjunto de dados de entrada e saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2** e **ZipDeflate;** e os níveis suportados são: **Ideal** e **Mais rápido.** ver [formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> Não é possível utilizar o ficheiroName e o ficheiroFiltro simultaneamente.

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

Para obter mais informações, consulte [o artigo do conector do Sistema de Ficheiros](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Fonte do sistema de ficheiros na atividade de cópia
Se estiver a copiar dados do Sistema de Ficheiros, desave o tipo de **fonte** da atividade de cópia para **FileSystemSource** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. |Verdadeiro, Falso (padrão) |Não |

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
Para obter mais informações, consulte [o artigo do conector do Sistema de Ficheiros](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Sinke do sistema de ficheiros na atividade de cópia
Se estiver a copiar dados para o Sistema de Ficheiros, desaperte o tipo de **pia** da atividade de cópia para **FileSystemSink** e especifique as seguintes propriedades na secção do **lavatório:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| copyOportundo |Define o comportamento da cópia quando a fonte é BlobSource ou FileSystem. |**PreservarHierarquia:** Preserva a hierarquia do ficheiro na pasta alvo. Ou seja, o percurso relativo do ficheiro de origem para a pasta de origem é o mesmo que o caminho relativo do ficheiro-alvo para a pasta alvo.<br/><br/>**Achatamento da Achata:** Todos os ficheiros da pasta de origem são criados no primeiro nível de pasta alvo. Os ficheiros-alvo são criados com um nome autogerado.<br/><br/>**Filtros de fusão:** Combina todos os ficheiros da pasta de origem a um ficheiro. Se o nome do ficheiro/nome blob for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro gerado automaticamente. |Não |

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

Para obter mais informações, consulte [o artigo do conector do Sistema de Ficheiros](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado FTP, defina o **tipo** de serviço ligado ao **FtpServer,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório | Predefinição |
| --- | --- | --- | --- |
| anfitrião |Nome ou endereço IP do Servidor FTP |Sim |&nbsp; |
| authenticationType |Especificar o tipo de autenticação |Sim |Básico, Anónimo |
| nome de utilizador |Utilizador que tem acesso ao servidor FTP |Não |&nbsp; |
| palavra-passe |Palavra-passe para o utilizador (nome de utilizador) |Não |&nbsp; |
| criptografadoCredential |Credencial encriptada para aceder ao servidor FTP |Não |&nbsp; |
| gatewayName |Nome do Gateway de Gestão de Dados para ligar a um servidor FTP no local |Não |&nbsp; |
| porta |Porta na qual o servidor FTP está a ouvir |Não |21 |
| ativarSl |Especificar se deve utilizar o FTP sobre o canal SSL/TLS |Não |true |
| enableServerCertificateValidation |Especificar se deve ativar a validação do certificado TLS/SSL do servidor ao utilizar o ftp sobre o canal SSL/TLS |Não |true |

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

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Exemplo: Usar o nome de utilizador e a palavra-passe em texto simples para autenticação básica

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

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Exemplo: Utilizar a porta, ativar o Ssl, permitir a Concessão de Certificação

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

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Exemplo: Utilização criptografada Para autenticação e gateway

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

Para obter mais informações, consulte o artigo [do conector FTP.](data-factory-ftp-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados FTP, defina o **tipo** do conjunto de dados para **FileShare** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Sub caminho para a pasta. Utilize o personagem de fuga ' \ ' para caracteres especiais na corda. Consulte por exemplo o serviço ligado à amostra e definições de conjunto de dados.<br/><br/>Pode combinar esta propriedade com **partição Para** ter caminhos de pasta baseados em intervalos de datas de início/fim da fatia. |Sim
| fileName |Especifique o nome do ficheiro na **pastaPata** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando o data de ficheiro não for especificado para um conjunto de dados de saída, o nome do ficheiro gerado estará no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| ficheiroFiltro |Especifique um filtro a ser utilizado para selecionar um subconjunto de ficheiros na pastaPalho em vez de todos os ficheiros.<br/><br/>Os valores permitidos são: `*` (múltiplos caracteres) e `?` (único carácter).<br/><br/>Exemplos 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter é aplicável para um conjunto de dados de FileShare de entrada. Esta propriedade não é suportada com HDFS. |Não |
| partitionedBy |partitionedBy pode ser usado para especificar uma pasta dinâmicaPath, nome de ficheiro para dados da série de tempo. Por exemplo, a pastaPata parametrizada para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat** . Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte [formato de texto,](data-factory-supported-file-and-compression-formats.md#text-format) [formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e secções [de Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como-está** entre lojas baseadas em ficheiros (cópia binária), salte a secção de formato nas definições de conjunto de dados de entrada e saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2** e **ZipDeflate;** e os níveis suportados são: **Ideal** e **Mais rápido.** Para obter mais informações, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useTransfera |Especificar se utiliza o modo de transferência binário. Verdade para o modo binário e falso ASCII. Valor predefinido: Verdadeiro. Esta propriedade só pode ser usada quando o tipo de serviço associado ligado é do tipo: FtpServer. |Não |

> [!NOTE]
> o nome de ficheiro e o ficheiroFiltro não podem ser utilizados simultaneamente.

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

Para obter mais informações, consulte o artigo [do conector FTP.](data-factory-ftp-connector.md#dataset-properties)

### <a name="file-system-source-in-copy-activity"></a>Fonte do sistema de ficheiros na atividade de cópia
Se estiver a copiar dados de um servidor FTP, desave o tipo de **fonte** da atividade de cópia para **FileSystemSource** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos novamente a partir das sub-pastas ou apenas a partir da pasta especificada. |Verdadeiro, Falso (padrão) |Não |

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

Para obter mais informações, consulte o artigo [do conector FTP.](data-factory-ftp-connector.md#copy-activity-properties)


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado a HDFS, defina o **tipo** de serviço ligado aos **Hdfs,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **Hdfs** |Sim |
| Url |URL para o HDFS |Sim |
| authenticationType |Anónimo, ou Windows. <br><br> Para utilizar **a autenticação Kerberos** para o conector HDFS, consulte esta secção para configurar o ambiente no local em conformidade. |Sim |
| userName |Nome de utilizador para autenticação do Windows. |Sim (para autenticação do Windows) |
| palavra-passe |Palavra-passe para autenticação do Windows. |Sim (para autenticação do Windows) |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar ao HDFS. |Sim |
| criptografadoCredential |[Saída new-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) da credencial de acesso. |Não |

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
Para definir um conjunto de dados HDFS, defina o **tipo** do conjunto de dados para **FileShare** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Caminho para a pasta. Exemplo: `myfolder`<br/><br/>Utilize o personagem de fuga ' \ ' para caracteres especiais na corda. Por exemplo: para a sub-dobragem pasta\, especifique \\ \\ a sub-dobradeira e para d:\samplefolder, especificar d: \\ \\ amostragem.<br/><br/>Pode combinar esta propriedade com **partição Para** ter caminhos de pasta baseados em intervalos de datas de início/fim da fatia. |Sim |
| fileName |Especifique o nome do ficheiro na **pastaPata** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando o data de ficheiro não for especificado para um conjunto de dados de saída, o nome do ficheiro gerado estará no seguinte formato: <br/><br/>`Data.<Guid>.txt` (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy pode ser usado para especificar uma pasta dinâmicaPath, nome de ficheiro para dados da série de tempo. Exemplo: pastaPametante parametrizada para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat** . Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte [formato de texto,](data-factory-supported-file-and-compression-formats.md#text-format) [formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e secções [de Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como-está** entre lojas baseadas em ficheiros (cópia binária), salte a secção de formato nas definições de conjunto de dados de entrada e saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2** e **ZipDeflate** . Os níveis suportados são: **Ideal** e **Mais rápido** . Para obter mais informações, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> o nome de ficheiro e o ficheiroFiltro não podem ser utilizados simultaneamente.

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

### <a name="file-system-source-in-copy-activity"></a>Fonte do sistema de ficheiros na atividade de cópia
Se estiver a copiar dados do HDFS, desave o tipo de **fonte** da atividade de cópia para **FileSystemSource** e especifique as seguintes propriedades na secção **de origem:**

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos novamente a partir das sub-pastas ou apenas a partir da pasta especificada. |Verdadeiro, Falso (padrão) |Não |

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
Para definir um serviço ligado SFTP, defina o **tipo** de serviço ligado ao **Sftp,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| anfitrião | Nome ou endereço IP do servidor SFTP. |Sim |
| porta |Porta na qual o servidor SFTP está a ouvir. O valor predefinido é: 21 |Não |
| authenticationType |Especificar o tipo de autenticação. Valores permitidos: **Básico,** **SshPublicKey** . <br><br> Consulte a Utilização de autenticação básica e utilização de secções [de autenticação de chaves públicas SSH](#using-ssh-public-key-authentication) em mais propriedades e amostras de JSON, respectivamente. |Sim |
| skipHostKeyValidation | Especificar se deve ignorar a validação da chave do anfitrião. | Não. O valor predefinido: falso |
| hostKeyFingerprint | Especifique a impressão do dedo da chave do anfitrião. | Sim, se o `skipHostKeyValidation` é falso.  |
| gatewayName |Nome do Gateway de Gestão de Dados para ligar a um servidor SFTP no local. | Sim, se copiar dados de um servidor SFTP no local. |
| criptografadoCredential | Credencial encriptada para aceder ao servidor SFTP. Gerada automaticamente quando especifica a autenticação básica (nome de utilizador + palavra-passe) ou autenticação SshPublicKey (nome de utilizador + caminho ou conteúdo de chave privada) no assistente de cópia ou no diálogo popup ClickOnce. | Não. Aplicar apenas ao copiar dados de um servidor SFTP no local. |

#### <a name="example-using-basic-authentication"></a>Exemplo: Utilização da autenticação básica

Para utilizar a autenticação básica, definir `authenticationType` como `Basic` , e especificar as seguintes propriedades para além das genéricas do conector SFTP introduzidas na última secção:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| nome de utilizador | Utilizador que tenha acesso ao servidor SFTP. |Sim |
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

#### <a name="using-ssh-public-key-authentication"></a>**Utilização da autenticação de chaves públicas SSH:**

Para utilizar a autenticação básica, definir `authenticationType` como `SshPublicKey` , e especificar as seguintes propriedades para além das genéricas do conector SFTP introduzidas na última secção:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| nome de utilizador |Utilizador que tem acesso ao servidor SFTP |Sim |
| privateKeyPath | Especifique o caminho absoluto para o ficheiro chave privado que o gateway pode aceder. | Especificar o `privateKeyPath` ou `privateKeyContent` . . <br><br> Aplicar apenas ao copiar dados de um servidor SFTP no local. |
| privateKeyContent | Uma cadeia serializada do conteúdo da chave privada. O Copy Wizard pode ler o ficheiro de chave privada e extrair automaticamente o conteúdo da chave privada. Se estiver a utilizar qualquer outra ferramenta/SDK, utilize a propriedade privateKeyPath. | Especificar o `privateKeyPath` ou `privateKeyContent` . . |
| passPhrase | Especifique a frase/palavra-passe para desencriptar a chave privada se o ficheiro chave estiver protegido por uma frase de passe. | Sim, se o ficheiro de chave privada estiver protegido por uma frase de passe. |

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exemplo: **Autenticação SshPublicKey utilizando conteúdo de chave privada**

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

Para mais informações, consulte o artigo [do conector SFTP.](data-factory-sftp-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados SFTP, defina o **tipo** do conjunto de dados para **FileShare** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Sub caminho para a pasta. Utilize o personagem de fuga ' \ ' para caracteres especiais na corda. Consulte por exemplo o serviço ligado à amostra e definições de conjunto de dados.<br/><br/>Pode combinar esta propriedade com **partição Para** ter caminhos de pasta baseados em intervalos de datas de início/fim da fatia. |Sim |
| fileName |Especifique o nome do ficheiro na **pastaPata** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando o data de ficheiro não for especificado para um conjunto de dados de saída, o nome do ficheiro gerado estará no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| ficheiroFiltro |Especifique um filtro a ser utilizado para selecionar um subconjunto de ficheiros na pastaPalho em vez de todos os ficheiros.<br/><br/>Os valores permitidos são: `*` (múltiplos caracteres) e `?` (único carácter).<br/><br/>Exemplos 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter é aplicável para um conjunto de dados de FileShare de entrada. Esta propriedade não é suportada com HDFS. |Não |
| partitionedBy |partitionedBy pode ser usado para especificar uma pasta dinâmicaPath, nome de ficheiro para dados da série de tempo. Por exemplo, a pastaPata parametrizada para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat** . Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte [formato de texto,](data-factory-supported-file-and-compression-formats.md#text-format) [formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e secções [de Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como-está** entre lojas baseadas em ficheiros (cópia binária), salte a secção de formato nas definições de conjunto de dados de entrada e saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2** e **ZipDeflate** . Os níveis suportados são: **Ideal** e **Mais rápido** . Para obter mais informações, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useTransfera |Especificar se utiliza o modo de transferência binário. Verdade para o modo binário e falso ASCII. Valor predefinido: Verdadeiro. Esta propriedade só pode ser usada quando o tipo de serviço associado ligado é do tipo: FtpServer. |Não |

> [!NOTE]
> o nome de ficheiro e o ficheiroFiltro não podem ser utilizados simultaneamente.

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

Para mais informações, consulte o artigo [do conector SFTP.](data-factory-sftp-connector.md#dataset-properties)

### <a name="file-system-source-in-copy-activity"></a>Fonte do sistema de ficheiros na atividade de cópia
Se estiver a copiar dados de uma fonte SFTP, desaprove o tipo de **fonte** da atividade de cópia para **FileSystemSource** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos novamente a partir das sub-pastas ou apenas a partir da pasta especificada. |Verdadeiro, Falso (padrão) |Não |



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

Para mais informações, consulte o artigo [do conector SFTP.](data-factory-sftp-connector.md#copy-activity-properties)


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço http ligado, defina o **tipo** de serviço ligado a **Http,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| url | URL base para o Servidor Web | Sim |
| authenticationType | Especifica o tipo de autenticação. Os valores permitidos são: **Anónimo,** **Básico,** **Digest,** **Windows,** **ClientCertificate** . <br><br> Consulte as secções abaixo desta tabela sobre mais propriedades e amostras JSON para esses tipos de autenticação, respectivamente. | Sim |
| enableServerCertificateValidation | Especificar se deve ativar a validação do certificado TLS/SSL do servidor se a fonte for HTTPS Web Server | Não, o padrão é verdade. |
| gatewayName | Nome do Gateway de Gestão de Dados para ligar a uma fonte HTTP no local. | Sim, se copiar dados de uma fonte HTTP no local. |
| criptografadoCredential | Credencial encriptada para aceder ao ponto final HTTP. Gerado automaticamente quando configura as informações de autenticação no assistente de cópia ou no diálogo popup ClickOnce. | Não. Aplicar apenas ao copiar dados de um servidor HTTP no local. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Exemplo: Utilização da autenticação Básica, Digest ou Windows
Configurar `authenticationType` como `Basic` , ou `Digest` `Windows` especificar as seguintes propriedades para além das genéricas do conector HTTP introduzidas acima:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| nome de utilizador | Nome de utilizador para aceder ao ponto final HTTP. | Sim |
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

#### <a name="example-using-clientcertificate-authentication"></a>Exemplo: Utilização da autenticação ClientCertificate

Para utilizar a autenticação básica, definir `authenticationType` como `ClientCertificate` , e especificar as seguintes propriedades para além das genéricas do conector HTTP introduzidas acima:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| EmbeddedCertData | O conteúdo codificado base64 de dados binários do ficheiro Personal Information Exchange (PFX). | Especificar o `embeddedCertData` ou `certThumbprint` . . |
| certThumbprint | A impressão digital do certificado que foi instalado na loja de certificados da sua máquina de gateway. Aplicar apenas ao copiar dados a partir de uma fonte HTTP no local. | Especificar o `embeddedCertData` ou `certThumbprint` . . |
| palavra-passe | Palavra-passe associada ao certificado. | Não |

Se utilizar `certThumbprint` para autenticação e o certificado estiver instalado na loja pessoal do computador local, tem de conceder a permissão de leitura ao serviço gateway:

1. Lançar consola de gestão da Microsoft (MMC). Adicione o encaixe **de certificados** que visa o **Computador Local.**
2. Expandir **Certificados,** **Pessoais,** e clicar em **Certificados.**
3. Clique com o direito no certificado da loja pessoal e selecione **Todas as Tarefas** -> **Gerencie As Chaves Privadas...**
3. No separador **Segurança,** adicione a conta de utilizador sob a qual o Serviço de Anfitriões gateway de gestão de dados está a funcionar com o acesso de leitura ao certificado.

**Exemplo: utilização do certificado de cliente:** Este serviço ligado liga a sua fábrica de dados a um servidor web HTTP no local. Utiliza um certificado de cliente que é instalado na máquina com o Data Management Gateway instalado.

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

#### <a name="example-using-client-certificate-in-a-file"></a>Exemplo: usar o certificado de cliente num ficheiro
Este serviço ligado liga a sua fábrica de dados a um servidor web HTTP no local. Utiliza um ficheiro de certificado de cliente na máquina com o Data Management Gateway instalado.

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

Para mais informações, consulte o artigo [do conector HTTP.](data-factory-http-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados HTTP, defina o **tipo** de conjunto de dados em **Http** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| relativoUrl | Um URL relativo ao recurso que contém os dados. Quando o caminho não é especificado, apenas é utilizado o URL especificado na definição de serviço ligada. <br><br> Para construir URL dinâmico, pode utilizar [funções de Data Factory e variáveis do sistema](data-factory-functions-variables.md), Exemplo: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"` . | Não |
| requestMethod | Método http. Os valores permitidos são **GET** ou **POST.** | Não. A predefinição é `GET`. |
| cabeçalhos adicionais | Cabeçalhos de pedido HTTP adicionais. | Não |
| requestCorp | Corpo para pedido HTTP. | Não |
| formato | Se pretender simplesmente **recuperar os dados do ponto final HTTP como-é** sem analisá-los, ignore as definições deste formato. <br><br> Se pretender analisar o conteúdo de resposta HTTP durante a cópia, suportam-se os seguintes tipos de formato: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat** . Para mais informações, consulte [formato de texto,](data-factory-supported-file-and-compression-formats.md#text-format) [formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e secções [de Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2** e **ZipDeflate** . Os níveis suportados são: **Ideal** e **Mais rápido** . Para obter mais informações, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

#### <a name="example-using-the-get-default-method"></a>Exemplo: utilização do método GET (predefinido)

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

#### <a name="example-using-the-post-method"></a>Exemplo: utilizando o método POST

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
Para mais informações, consulte o artigo [do conector HTTP.](data-factory-http-connector.md#dataset-properties)

### <a name="http-source-in-copy-activity"></a>HTTP Fonte na Atividade de Cópia
Se estiver a copiar dados de uma fonte HTTP, desacione o tipo de **fonte** da atividade de cópia para **HttpSource** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| httpRequestTimeout | O tempo limite (TimeSpan) para o pedido HTTP obter uma resposta. É o tempo limite para obter uma resposta, não o tempo limite para ler os dados de resposta. | Não. Valor predefinido: 00:01:40 |


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

Para mais informações, consulte o artigo [do conector HTTP.](data-factory-http-connector.md#copy-activity-properties)

## <a name="odata"></a>OData

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado ao OData, defina o **tipo** de serviço ligado ao **OData,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| url |Url do serviço OData. |Sim |
| authenticationType |Tipo de autenticação utilizada para ligar à fonte OData. <br/><br/> Para o OData na nuvem, os valores possíveis são Anónimos, Básicos e OAuth (nota Azure Data Factory atualmente apenas suporta OAuth baseado em Azure Ative Directory). <br/><br/> Para o OData no local, os valores possíveis são Anónimos, Básicos e Windows. |Sim |
| nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Básica. |Sim (apenas se estiver a utilizar a autenticação básica) |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Sim (apenas se estiver a utilizar a autenticação básica) |
| autorizadoCredential |Se estiver a utilizar o OAuth, clique no botão **Authorize** no Assistente de Cópia de Data Factory ou editor e introduza a sua credencial, então o valor desta propriedade será gerado automaticamente. |Sim (apenas se estiver a utilizar a autenticação OAuth) |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar ao serviço OData no local. Especifique apenas se estiver a copiar dados a partir da fonte OData das instalações. |Não |

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

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Exemplo - Utilização de autenticação do Windows acedendo a fonte OData no local

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

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Exemplo - Utilização de autenticação OAuth que aceda à fonte de OData em nuvem
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
Para definir um conjunto de dados OData, defina o **tipo** do conjunto de dados para **ODataResource** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| caminho |Caminho para o recurso OData |Não |

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

### <a name="relational-source-in-copy-activity"></a>Fonte relacional na atividade de cópia
Se estiver a copiar dados a partir de uma fonte OData, desaprote o tipo de **fonte** da atividade de cópia para **RelationalSource** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Exemplo | Obrigatório |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |"?$select=Nome, Descrição&$top=5" |Não |

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
Para definir um serviço ligado ao ODBC, defina o **tipo** de serviço ligado ao **OnPremisesOdbc,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| conexãoStragem |A parte credencial de não acesso da cadeia de ligação e uma credencial encriptada opcional. Consulte os exemplos nas seguintes secções. |Sim |
| credencial |A parte credencial de acesso da cadeia de ligação especificada no formato de valor da propriedade específica do condutor. Exemplo: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Não |
| authenticationType |Tipo de autenticação utilizada para ligar à loja de dados ODBC. Os valores possíveis são: Anónimo e Básico. |Sim |
| nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Básica. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à loja de dados ODBC. |Sim |

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
Pode encriptar as credenciais utilizando o [cmdlet New-AzDataFactoryEncryptValue.](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue)

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

Para mais informações, consulte o artigo [do conector ODBC.](data-factory-odbc-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados ODBC, defina o **tipo** de conjunto de dados para **RelationalTable** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
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

Para mais informações, consulte o artigo [do conector ODBC.](data-factory-odbc-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte relacional na atividade de cópia
Se estiver a copiar dados de uma loja de dados ODBC, desaprote o tipo de **fonte** da atividade de cópia para **RelationalSource** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Sim |

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

Para mais informações, consulte o artigo [do conector ODBC.](data-factory-odbc-connector.md#copy-activity-properties)

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Salesforce, defina o **tipo** de serviço ligado à **Salesforce,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| ambienteUrl | Especifique o URL da instância Salesforce. <br><br> - Predefinido é "https: \/ /login.salesforce.com". <br> - Para copiar dados da caixa de areia, especifique https://test.salesforce.com " " <br> - Para copiar dados do domínio personalizado, especifique, por exemplo, "https://[domain].my.salesforce.com". |Não |
| nome de utilizador |Especifique um nome de utilizador para a conta de utilizador. |Sim |
| palavra-passe |Especifique uma palavra-passe para a conta de utilizador. |Sim |
| securityToken |Especifique um sinal de segurança para a conta de utilizador. Consulte [obter um sinal de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para obter instruções sobre como reiniciar/obter um sinal de segurança. Para conhecer os tokens de segurança em geral, consulte [a Segurança e a API.](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) |Sim |

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

Para mais informações, consulte o artigo [do conector Salesforce.](data-factory-salesforce-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados salesforce, defina o **tipo** de conjunto de dados para **RelationalTable** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |O nome da mesa na Salesforce. |Não (se for especificada uma **consulta** de **RelationalSource)** |

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

Para mais informações, consulte o artigo [do conector Salesforce.](data-factory-salesforce-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Fonte relacional na atividade de cópia
Se estiver a copiar dados da Salesforce, desaprote o **tipo** de fonte da atividade de cópia para **RelationalSource,** e especifique as seguintes propriedades na secção **de origem:**

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Uma consulta SQL-92 ou uma consulta [de idioma de consulta de objetos salesforce (SOQL).](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) Por exemplo: `select * from MyTable__c`. |Não (se o nome de **tabela** do conjunto de **dados** for especificado) |

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

Para mais informações, consulte o artigo [do conector Salesforce.](data-factory-salesforce-connector.md#copy-activity-properties)

## <a name="web-data"></a>Dados da Web

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado à Web, defina o **tipo** de serviço ligado à **Web,** e especifique as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Obrigatório |
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

Para obter mais informações, consulte o artigo [do conector web Table.](data-factory-web-table-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados web, defina o **tipo** do conjunto de dados para **WebTable** e especifique as seguintes propriedades na secção **de tipos de direitos:**

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo |tipo do conjunto de dados. deve ser definido para **WebTable** |Sim |
| caminho |Um URL relativo ao recurso que contém a tabela. |Não. Quando o caminho não é especificado, apenas é utilizado o URL especificado na definição de serviço ligada. |
| índice |O índice da tabela no recurso. Consulte o índice de uma tabela numa secção de página HTML para obter o índice de uma tabela numa página HTML. |Sim |

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

Para obter mais informações, consulte o artigo [do conector web Table.](data-factory-web-table-connector.md#dataset-properties)

### <a name="web-source-in-copy-activity"></a>Fonte web na atividade de cópia
Se estiver a copiar dados de uma tabela web, desa um tipo de **fonte** da atividade de cópia para **WebSource** . Atualmente, quando a origem na atividade de cópia é do tipo **WebSource,** não são suportadas propriedades adicionais.

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

Para obter mais informações, consulte o artigo [do conector web Table.](data-factory-web-table-connector.md#copy-activity-properties)

## <a name="compute-environments"></a>AMBIENTES COMPUTACIONAL
A tabela que se segue lista os ambientes computacionais suportados pela Data Factory e as atividades de transformação que podem decorrer neles. Clique no link para o cálculo que está interessado em ver os esquemas JSON para serviço ligado para ligá-lo a uma fábrica de dados.

| Ambiente de computação | Atividades |
| --- | --- |
| [Cluster HDInsight a pedido](#on-demand-azure-hdinsight-cluster) ou [o seu próprio cluster HDInsight](#existing-azure-hdinsight-cluster) |[.NET atividade personalizada,](#net-custom-activity) [atividade de Colmeia, Atividade](#hdinsight-hive-activity)do [Porco,](#hdinsight-pig-activity) [Atividade mapreduce,](#hdinsight-mapreduce-activity)atividade de streaming hadoop, [atividade de faísca](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[Atividade personalizada do .NET](#net-custom-activity) |
| [Azure Machine Learning Studio (clássico)](#azure-machine-learning-studio-classic) | [Azure Machine Learning Studio (clássico) Atividade de execução de lote,](#azure-machine-learning-studio-classic-batch-execution-activity) [Azure Machine Learning Studio (clássico) Atualização De Recursos Atividade](#azure-machine-learning-studio-classic-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database), [Azure Synapse Analytics,](#azure-synapse-analytics) [SQL Server](#sql-server-stored-procedure) |[Procedimento armazenado](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Cluster Azure HDInsight a pedido
O serviço Azure Data Factory pode criar automaticamente um cluster HDInsight baseado no Windows/Linux para processar dados. O cluster é criado na mesma região que a conta de armazenamento (propriedade linkedServiceName no JSON) associada ao cluster. Pode executar as seguintes atividades de transformação neste serviço ligado: [atividade personalizada .NET,](#net-custom-activity) [atividade de Hive,](#hdinsight-hive-activity) [atividade do porco,](#hdinsight-pig-activity) [atividade mapReduce,](#hdinsight-mapreduce-activity)atividade de streaming Hadoop, [atividade spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição Azure JSON de um serviço ligado a hdinsight a pedido.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para **HDInsightOnDemand** . |Sim |
| clusterSize |Número de nós de trabalhador/dados no cluster. O cluster HDInsight é criado com 2 nós de cabeça juntamente com o número de nós de trabalhadores que especifica para esta propriedade. Os nódinhos são de tamanho Standard_D3 que tem 4 núcleos, por isso um conjunto de nó de 4 trabalhadores leva 24 \* núcleos (4 4 = 16 núcleos para os nóns operários, mais \* 2 4 = 8 núcleos para os nosdes da cabeça). Consulte [os clusters Hadoop baseados em Linux em HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) para obter detalhes sobre o nível Standard_D3. |Sim |
| timetolive |O tempo de inatividade permitido para o cluster HDInsight a pedido. Especifica quanto tempo o cluster HDInsight on-demand permanece vivo após a conclusão de uma atividade executada se não houver outros empregos ativos no cluster.<br/><br/>Por exemplo, se uma corrida de atividade demorar 6 minutos e o tempo de vida estiver definido para 5 minutos, o cluster permanece vivo durante 5 minutos após os 6 minutos de processamento da atividade. Se outra atividade for executada com a janela de 6 minutos, é processada pelo mesmo cluster.<br/><br/>A criação de um cluster HDInsight a pedido é uma operação dispendiosa (pode demorar algum tempo), por isso use esta definição como necessário para melhorar o desempenho de uma fábrica de dados reutilizando um cluster HDInsight a pedido.<br/><br/>Se definir o valor de live timetolive para 0, o cluster é eliminado assim que a atividade é executada. Por outro lado, se definir um valor elevado, o cluster pode ficar inativo desnecessariamente resultando em custos elevados. Por isso, é importante que desaproprie o valor adequado com base nas suas necessidades.<br/><br/>Vários oleodutos podem partilhar a mesma instância do cluster HDInsight a pedido se o valor da propriedade timetolive for devidamente definido |Sim |
| versão |Versão do cluster HDInsight. Para mais detalhes, consulte [as versões HDInsight suportadas na Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Não |
| linkedServiceName |Serviço ligado a Azure Storage a ser utilizado pelo cluster a pedido para armazenar e processar dados. <p>Atualmente, não é possível criar um cluster HDInsight a pedido que utilize uma Loja de Lagos de Dados Azure como armazenamento. Se pretender armazenar os dados dos resultados do processamento de HDInsight numa Loja de Lagos de Dados Azure, utilize uma Atividade de Cópia para copiar os dados do Azure Blob Storage para a Azure Data Lake Store.</p>  | Sim |
| adicionalLinkedServiceNames |Especifica contas de armazenamento adicionais para o serviço ligado ao HDInsight para que o serviço Data Factory possa registá-los em seu nome. |Não |
| osTipos |Tipo de sistema operativo. Os valores permitidos são: Windows (padrão) e Linux |Não |
| hcatalogLinkedServiceName |O nome do serviço ligado Azure SQL que aponta para a base de dados HCatalog. O cluster HDInsight a pedido é criado utilizando a Base de Dados Azure SQL como a metástaria. |Não |

### <a name="json-example"></a>Exemplo JSON
O JSON seguinte define um serviço hdinsight baseado na procura do Linux. O serviço Data Factory cria automaticamente um cluster HDInsight **baseado em Linux** ao processar uma fatia de dados.

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

Para mais informações, consulte o artigo [de serviços ligados ao Compute.](data-factory-compute-linked-services.md)

## <a name="existing-azure-hdinsight-cluster"></a>Cluster Azure HDInsight existente
Pode criar um serviço ligado a Azure HDInsight para registar o seu próprio cluster HDInsight com data factory. Pode executar as seguintes atividades de transformação de dados neste serviço ligado: [atividade personalizada .NET,](#net-custom-activity) [atividade de Hive,](#hdinsight-hive-activity) [atividade do porco,](#hdinsight-pig-activity) [atividade mapReduce,](#hdinsight-mapreduce-activity)atividade de streaming Hadoop, [atividade spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição Azure JSON de um serviço ligado a Azure HDInsight.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para **HDInsight** . |Sim |
| clusterUri |O URI do cluster HDInsight. |Sim |
| nome de utilizador |Especifique o nome do utilizador a utilizar para ligar a um cluster HDInsight existente. |Sim |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador. |Sim |
| linkedServiceName | Nome do serviço ligado ao Azure Storage que se refere ao armazenamento de bolhas Azure utilizado pelo cluster HDInsight. <p>Atualmente, não é possível especificar um serviço ligado à Azure Data Lake Store para esta propriedade. Pode aceder a dados na Azure Data Lake Store a partir de scripts Hive/Pig se o cluster HDInsight tiver acesso à Data Lake Store. </p>  |Sim |

Para versões de clusters HDInsight suportados, consulte as [versões HDInsight suportadas](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

#### <a name="json-example"></a>Exemplo JSON

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
Pode criar um serviço ligado a Azure Batch para registar um lote de máquinas virtuais (VMs) com uma fábrica de dados. Pode executar atividades personalizadas .NET utilizando o Azure Batch ou o Azure HDInsight. Pode executar uma [atividade personalizada .NET](#net-custom-activity) neste serviço ligado.

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição Azure JSON de um serviço ligado a Azure Batch.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para **AzureBatch** . |Sim |
| accountName |Nome da conta Azure Batch. |Sim |
| acessoKey |Chave de acesso para a conta Azure Batch. |Sim |
| poolName |Nome da piscina de máquinas virtuais. |Sim |
| linkedServiceName |Nome do serviço ligado ao Azure Storage associado a este serviço ligado a Azure Batch. Este serviço ligado é utilizado para a realização de ficheiros necessários para executar a atividade e armazenar os registos de execução da atividade. |Sim |


#### <a name="json-example"></a>Exemplo JSON

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

## <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (clássico)
Você cria um serviço ligado ao Azure Machine Learning Studio (clássico) para registar um ponto final de pontuação de lote studio (clássico) com uma fábrica de dados. Duas atividades de transformação de dados que podem funcionar neste serviço ligado: [Azure Machine Learning Studio (clássico) Atividade de execução de lotes,](#azure-machine-learning-studio-classic-batch-execution-activity) [Azure Machine Learning Studio (clássico) Atualização De Recursos De Atualização Atividade](#azure-machine-learning-studio-classic-update-resource-activity).

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição Azure JSON de um serviço ligado studio (clássico).

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| Tipo |A propriedade tipo deve ser definida para: **AzureML** . |Sim |
| mlEndpoint |O URL de pontuação do lote. |Sim |
| apiKey |A API do modelo de espaço de trabalho publicado. |Sim |

#### <a name="json-example"></a>Exemplo JSON

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
Você cria um serviço ligado a **Azure Data Lake Analytics** para ligar um serviço de computação Azure Data Lake Analytics a uma fábrica de dados Azure antes de usar a [atividade U-SQL do Data Lake Analytics](data-factory-usql-activity.md) num oleoduto.

### <a name="linked-service"></a>Serviço ligado

A tabela seguinte fornece descrições para as propriedades utilizadas na definição JSON de um serviço ligado a Azure Data Lake Analytics.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| Tipo |A propriedade tipo deve ser configurada para: **AzureDataLakeAnalytics** . |Sim |
| accountName |Nome da conta Azure Data Lake Analytics. |Sim |
| dataLakeAnalyticsUri |Azure Data Lake Analytics URI. |Não |
| autorização |O código de autorização é automaticamente recuperado após clicar no botão **Authorize** no Editor de Fábrica de Dados e completar o login OAuth. |Sim |
| subscriptionId |ID de assinatura Azure |Não (Se não for especificada, utiliza-se a subscrição da fábrica de dados). |
| resourceGroupName |Nome do grupo de recursos do Azure |Não (Se não for especificado, é utilizado o grupo de recursos da fábrica de dados). |
| sessionId |ID da sessão da sessão de autorização da OAuth. Cada ID de sessão é único e só pode ser usado uma vez. Quando utiliza o Editor de Fábrica de Dados, este ID é gerado automaticamente. |Sim |


#### <a name="json-example"></a>Exemplo JSON
O exemplo a seguir fornece a definição JSON para um serviço Azure Data Lake Analytics ligado.

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

## <a name="sql-server-stored-procedure"></a>Procedimento armazenado do servidor SQL

Cria um serviço ligado ao SQL Server e utiliza-o com a [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado a partir de um pipeline da Data Factory.

### <a name="linked-service"></a>Serviço ligado
Cria um serviço de tipo ligado **OnPremisesSqlServer** para ligar uma base de dados do SQL Server a uma fábrica de dados. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao SQL Server.

A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao SQL Server.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **OnPremisesSqlServer** . |Sim |
| conexãoStragem |Especifique a informação de ligação Desaquipeia as informações necessárias para ligar à base de dados do SQL Server utilizando a autenticação SQL ou a autenticação do Windows. |Sim |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados do SQL Server. |Sim |
| nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a Autenticação do Windows. Exemplo: **nome de utilizador do nome \\ de domínio** . |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |

Pode encriptar credenciais utilizando o cmdlet **New-AzDataFactoryEncryptValue** e usá-las na cadeia de ligação, como mostrado no exemplo seguinte (Propriedade **Criptografada)**

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
#### <a name="example-json-for-using-windows-authentication"></a>Exemplo: JSON para utilização de autenticação do Windows

Se o nome de utilizador e a palavra-passe forem especificados, gateway usa-os para personificar a conta de utilizador especificada para se ligar à base de dados do SQL Server. Caso contrário, o gateway conecta-se diretamente ao SQL Server com o contexto de segurança do Gateway (a sua conta de arranque).

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

Para obter mais informações, consulte o artigo [do conector SQL Server.](data-factory-sqlserver-connector.md#linked-service-properties)

## <a name="data-transformation-activities"></a>ATIVIDADES DE TRANSFORMAÇÃO DE DADOS

Atividade | Descrição
-------- | -----------
[Atividade da Colmeia HDInsight](#hdinsight-hive-activity) | A atividade de Hive HDInsight num oleoduto da Data Factory executa consultas de Hive no seu próprio ou a pedido do grupo HDInsight baseado em Windows/Linux.
[Atividade do Porco HDInsight](#hdinsight-pig-activity) | A atividade do Porco HDInsight num oleoduto da Data Factory executa consultas de porco no seu próprio conjunto de Windows/HdInsight baseado em Windows/Linux.
[Atividade MapReduce do HDInsight](#hdinsight-mapreduce-activity) | A atividade HDInsight MapReduce num oleoduto data factory executa programas MapReduce por conta própria ou a pedido do grupo HDInsight baseado em Windows/Linux.
[Atividade Streaming do HDInsight](#hdinsight-streaming-activity) | A Atividade de Streaming HDInsight num oleoduto de Data Factory executa programas de streaming Hadoop no seu próprio ou a pedido do grupo HDInsight baseado em Windows/Linux.
[Atividade do HDInsight Spark](#hdinsight-spark-activity) | A atividade HDInsight Spark num oleoduto data factory executa programas Spark no seu próprio cluster HDInsight.
[Atividade de Execução em Lotes do Azure Machine Learning Studio (clássico)](#azure-machine-learning-studio-classic-batch-execution-activity) | O Azure Data Factory permite-lhe criar facilmente oleodutos que utilizem um serviço web de Estúdio (clássico) publicado para análise preditiva. Utilizando a Atividade de Execução de Lotes num oleoduto Azure Data Factory, pode invocar um serviço web Studio (clássico) para fazer previsões sobre os dados em lote.
[Atividade de Recursos de Atualização do Azure Machine Learning Studio (clássico)](#azure-machine-learning-studio-classic-update-resource-activity) | Com o tempo, os modelos preditivos no Azure Machine Learning Studio (clássico) experiências de pontuação precisam de ser retreinados usando novos conjuntos de dados de entrada. Depois de terminar a reconversão, pretende atualizar o serviço web de pontuação com o modelo de aprendizagem automática retreinado. Pode utilizar a Atividade de Recursos de Atualização para atualizar o serviço web com o modelo recém-treinado.
[Atividade de Procedimento Armazenado](#stored-procedure-activity) | Pode utilizar a atividade do Procedimento Armazenado num oleoduto da Data Factory para invocar um procedimento armazenado numa das seguintes lojas de dados: Azure SQL Database, Azure Synapse Analytics, SQL Server Database na sua empresa ou um Azure VM.
[Atividade U-SQL do Data Lake Analytics](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL Activity executa um script U-SQL em um cluster Azure Data Lake Analytics.
[Atividade personalizada do .NET](#net-custom-activity) | Se precisar de transformar dados de uma forma que não seja suportada pela Data Factory, pode criar uma atividade personalizada com a sua própria lógica de processamento de dados e utilizar a atividade no pipeline. Pode configurar a atividade personalizada .NET para executar utilizando um serviço Azure Batch ou um cluster Azure HDInsight.


## <a name="hdinsight-hive-activity"></a>Atividade Hive do HDInsight
Pode especificar as seguintes propriedades numa definição de JSON de atividade da colmeia. A propriedade tipo para a atividade deve ser: **HDInsightHive** . Você deve criar um serviço ligado HDInsight primeiro e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **typeProperties** quando define o tipo de atividade para HDInsightHive:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| script |Especificar o script da Colmeia inline |Não |
| caminho de script |Guarde o script hive num armazenamento de bolhas Azure e forneça o caminho para o arquivo. Use a propriedade 'script' ou 'scriptPath'. Ambos não podem ser usados juntos. O nome do ficheiro é sensível a casos. |Não |
| define |Especifique os parâmetros como pares chave/valor para referência dentro do script da Colmeia usando 'hiveconf' |Não |

Estas propriedades tipo são específicas da Atividade da Colmeia. Outras propriedades (fora da secção typeProperties) são suportadas para todas as atividades.

### <a name="json-example"></a>Exemplo JSON
O seguinte JSON define uma atividade de Colmeia HDInsight num oleoduto.

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

Para mais informações, consulte o artigo [Atividade da Colmeia.](data-factory-hive-activity.md)

## <a name="hdinsight-pig-activity"></a>Atividade Pig do HDInsight
Pode especificar as seguintes propriedades numa definição de JSON da Atividade do Porco. A propriedade tipo para a atividade deve ser: **HDInsightPig** . Você deve criar um serviço ligado HDInsight primeiro e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **typeProperties** quando define o tipo de atividade para HDInsightPig:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| script |Especificar o script do porco inline |Não |
| caminho de script |Guarde o script Pig num armazenamento de bolhas Azure e forneça o caminho para o arquivo. Use a propriedade 'script' ou 'scriptPath'. Ambos não podem ser usados juntos. O nome do ficheiro é sensível a casos. |Não |
| define |Especifique os parâmetros como pares chave/valor para referências dentro do script do Porco |Não |

Estas propriedades tipo são específicas da Atividade do Porco. Outras propriedades (fora da secção typeProperties) são suportadas para todas as atividades.

### <a name="json-example"></a>Exemplo JSON

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

Para mais informações, consulte o artigo Atividade do Porco.

## <a name="hdinsight-mapreduce-activity"></a>Atividade MapReduce do HDInsight
Pode especificar as seguintes propriedades numa definição de Atividade de MapReduce JSON. A propriedade tipo para a atividade deve ser: **HDInsightMapReduce** . Você deve criar um serviço ligado HDInsight primeiro e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **typeProperties** quando define o tipo de atividade para HDInsightMapReduce:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| jarLinkedService | Nome do serviço ligado para o Azure Storage que contém o ficheiro JAR. | Sim |
| jarFilePath | Caminho para o ficheiro JAR no Armazenamento Azure. | Sim |
| nome de classeName | Nome da classe principal no ficheiro JAR. | Sim |
| argumentos | Uma lista de argumentos separados por vírgula para o programa MapReduce. No tempo de execução, você vê alguns argumentos extra (por exemplo: mapreduce.job.tags) a partir do quadro MapReduce. Para diferenciar os seus argumentos com os argumentos MapReduce, considere usar a opção e o valor como argumentos como mostrados no exemplo seguinte (-s, --input, --output etc., são opções imediatamente seguidas pelos seus valores) | Não |

### <a name="json-example"></a>Exemplo JSON

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

Para mais informações, consulte o artigo [Atividade de MapReduce.](data-factory-map-reduce.md)

## <a name="hdinsight-streaming-activity"></a>Atividade Streaming do HDInsight
Pode especificar as seguintes propriedades numa definição de JSON de atividade de streaming Hadoop. A propriedade tipo para a atividade deve ser: **HDInsightStreaming** . Você deve criar um serviço ligado HDInsight primeiro e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **typeProperties** quando define o tipo de atividade para HDInsightStreaming:

| Propriedade | Descrição |
| --- | --- |
| mapper | Nome do mapper executável. No exemplo, cat.exe é o mapper executável.|
| redutor | Nome do redutor executável. No exemplo, wc.exe é o redutor executável. |
| entrada | Ficheiro de entrada (incluindo localização) para o mapper. No exemplo: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"` adfsample é o recipiente blob, exemplo/dados/Gutenberg é a pasta, e davinci.txt é a bolha. |
| saída | Ficheiro de saída (incluindo localização) para o redutor. A saída do trabalho de streaming Hadoop é escrita para o local especificado para esta propriedade. |
| filePaths | Caminhos para o mapper e executáveis redutores. No exemplo: "adfsample/exemplo/apps/wc.exe", adfsample é o recipiente blob, exemplo/apps é a pasta, e wc.exe é o executável. |
| fileLinkedService | Serviço ligado a Azure Storage que representa o armazenamento Azure que contém os ficheiros especificados na secção filePaths. |
| argumentos | Uma lista de argumentos separados por vírgula para o programa MapReduce. No tempo de execução, você vê alguns argumentos extra (por exemplo: mapreduce.job.tags) a partir do quadro MapReduce. Para diferenciar os seus argumentos com os argumentos MapReduce, considere usar a opção e o valor como argumentos como mostrados no exemplo seguinte (-s, --input, --output etc., são opções imediatamente seguidas pelos seus valores) |
| obterDebugInfo | Um elemento opcional. Quando está definido para Falha, os registos são descarregados apenas por falha. Quando é definido para All, os registos são sempre descarregados independentemente do estado de execução. |

> [!NOTE]
> Tem de especificar um conjunto de dados de saída para a Atividade de Streaming Hadoop para a propriedade **de saídas.** Este conjunto de dados pode ser apenas um conjunto de dados falso que é necessário para conduzir o horário do pipeline (hora, dia, etc.). Se a atividade não tomar uma entrada, pode saltar especificando um conjunto de dados de entrada para a atividade para a propriedade **de entradas.**

## <a name="json-example"></a>Exemplo JSON

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

Para mais informações, consulte o artigo [de Atividades de Streaming hadoop.](data-factory-hadoop-streaming-activity.md)

## <a name="hdinsight-spark-activity"></a>Atividade do HDInsight Spark
Pode especificar as seguintes propriedades numa definição de JSON da Atividade de Faísca. A propriedade tipo para a atividade deve ser: **HDInsightSpark** . Você deve criar um serviço ligado HDInsight primeiro e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **typeProperties** quando define o tipo de atividade para HDInsightSpark:

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| rootPath | O recipiente e pasta Azure Blob que contém o ficheiro Spark. O nome do ficheiro é sensível a casos. | Sim |
| ingressoFilePata | Caminho relativo para a pasta raiz do código/embalagem Spark. | Sim |
| nome de classeName | Classe principal java/faísca da aplicação | Não |
| argumentos | Uma lista de argumentos de linha de comando para o programa Spark. | Não |
| proxyUser | A conta de utilizador a personificar para executar o programa Spark | Não |
| sparkConfig | Propriedades de configuração de faíscas. | Não |
| obterDebugInfo | Especifica quando os ficheiros de registo spark são copiados para o armazenamento Azure utilizado pelo cluster HDInsight (ou) especificado pelo sparkJobLinkedService. Valores permitidos: Nenhum, Sempre ou Fracasso. Valor predefinido: Nenhum. | Não |
| sparkJobLinkedService | O serviço de armazenamento Azure que detém o ficheiro de trabalho spark, dependências e registos.  Se não especificar um valor para esta propriedade, o armazenamento associado ao cluster HDInsight é utilizado. | Não |

### <a name="json-example"></a>Exemplo JSON

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

- A propriedade **tipo** está definida para **HDInsightSpark.**
- O **rootPath** está definido para **adfspark \\ pyFiles** onde adfspark é o recipiente Azure Blob e pyFiles é uma pasta fina nesse recipiente. Neste exemplo, o Azure Blob Storage é o que está associado ao cluster Spark. Pode fazer o upload do ficheiro para um Armazenamento Azure diferente. Se o fizer, crie um serviço ligado ao Azure Storage para ligar essa conta de armazenamento à fábrica de dados. Em seguida, especifique o nome do serviço ligado como um valor para a propriedade **sparkJobLinkedService.** Consulte as propriedades da Spark Activity para mais detalhes sobre esta propriedade e outros imóveis suportados pela Atividade spark.
- A **entradaFilePath** está definida para o **test.py** , que é o ficheiro python.
- A propriedade **getDebugInfo** está definida para **Always** , o que significa que os ficheiros de registo são sempre gerados (sucesso ou falha).

    > [!IMPORTANT]
    > Recomendamos que não coloque esta propriedade para Sempre em ambiente de produção, a menos que esteja a resolver problemas.
- A secção **de saídas** tem um conjunto de dados de saída. Deve especificar um conjunto de dados de saída mesmo que o programa de faíscas não produza qualquer saída. O conjunto de dados de saída impulsiona o calendário do gasoduto (hora a hora, diariamente, etc.).

Para mais informações sobre a atividade, consulte o artigo [Atividades da Faísca.](data-factory-spark.md)

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Atividade de Execução em Lotes do Azure Machine Learning Studio (clássico)
Pode especificar as seguintes propriedades numa definição JSON de Atividade de Execução de Lote (clássico) do Azure Machine Learning Studio (clássico). O tipo de propriedade para a atividade deve ser: **AzureMLBatchExecution** . Você deve criar um serviço ligado Studio (clássico) primeiro e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **de tipoProperties** quando define o tipo de atividade para AzureMLBatchExecution:

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
webServiceInput | O conjunto de dados a ser passado como uma entrada para o serviço web Studio (clássico). Este conjunto de dados também deve ser incluído nas entradas para a atividade. |Utilize webServiceInput ou webServiceInputs. |
webServiceInputs | Especifique conjuntos de dados a serem passados como entradas para o serviço web Studio (clássico). Se o serviço web tiver múltiplas entradas, use a propriedade webServiceInputs em vez de usar a propriedade webServiceInput. Os conjuntos de dados que são referenciados pelos **webServiceInputs** também devem ser **incluídos** nas entradas De Atividade . | Utilize webServiceInput ou webServiceInputs. |
webServiceOutputs | Os conjuntos de dados que são atribuídos como saídas para o serviço web Studio (clássico). O serviço web devolve os dados de saída neste conjunto de dados. | Sim |
globalParameters | Especifique os valores para os parâmetros do serviço web nesta secção. | Não |

### <a name="json-example"></a>Exemplo JSON
Neste exemplo, a atividade tem o conjunto de **dados MLSqlInput** como entrada e **MLSqlOutput** como a saída. O **MLSqlInput** é passado como uma entrada para o serviço web usando a propriedade **webServiceInput** JSON. O **MLSqlOutput** é passado como uma saída para o serviço Web utilizando a propriedade **webServiceOutputs** JSON.

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

No exemplo JSON, o serviço Web do Estúdio (clássico) implantado utiliza um leitor e um módulo de escritor para ler/escrever dados de/para uma Base de Dados Azure SQL. Este serviço Web expõe os seguintes quatro parâmetros: nome do servidor de base de dados, nome da base de dados, nome da conta do utilizador do servidor e senha de conta de utilizador do Servidor.

> [!NOTE]
> Apenas as entradas e saídas da atividade AzureMLBatchExecution podem ser passadas como parâmetros para o serviço Web. Por exemplo, no snippet JSON acima, MLSqlInput é uma entrada para a atividade AzureMLBatchExecution, que é passada como uma entrada para o serviço Web através do parâmetro webServiceInput.

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Atividade de Recursos de Atualização do Azure Machine Learning Studio (clássico)
Pode especificar as seguintes propriedades numa definição JSON de Azure Machine Learning Studio (clássico) de Atualização de Recursos JSON. O tipo de propriedade para a atividade deve ser: **AzureMLUpdateResource** . Você deve criar um serviço ligado Studio (clássico) primeiro e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **typeProperties** quando define o tipo de atividade para AzureMLUpdateResource:

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
me de Modelo treinado | Nome do modelo retreinado. | Sim |
treinadoModelDatasetName | Conjunto de dados que aponta para o ficheiro iLearner devolvido pela operação de reconversão. | Sim |

### <a name="json-example"></a>Exemplo JSON
O gasoduto tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource** . A atividade de execução de lote (clássica) toma os dados de formação como entrada e produz um ficheiro iLearner como uma saída. A atividade invoca o serviço web de formação (experiência de formação exposta como um serviço web) com os dados de formação de entrada e recebe o ficheiro ilearner do serviço web. O espaço-reservadoBlob é apenas um conjunto de dados de saída falso que é exigido pelo serviço Azure Data Factory para executar o pipeline.


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
Pode especificar as seguintes propriedades numa definição de JSON de atividade U-SQL. A propriedade tipo para a atividade deve ser: **DataLakeAnalyticsU-SQL** . Você deve criar um serviço ligado a Azure Data Lake Analytics e especificar o nome do mesmo como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **de tipos de propriedades** quando define o tipo de atividade para DataLakeAnalyticsU-SQL:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| scriptPath |Caminho para a pasta que contém o script U-SQL. O nome do ficheiro é sensível a casos. |Não (se utilizar o script) |
| scriptLinkedService |Serviço ligado que liga o armazenamento que contém o script à fábrica de dados |Não (se utilizar o script) |
| script |Especifique o script inline em vez de especificar scriptPath e scriptLinkedService. Por exemplo: "script": "CREATE DATABASE test". |Não (se utilizar scriptPath e scriptLinkedService) |
| graus DeParallelismo |O número máximo de nós usados simultaneamente para gerir o trabalho. |Não |
| prioridade |Determina quais os trabalhos de todos os que estão na fila que devem ser selecionados para serem executados primeiro. Quanto menor for o número, maior é a prioridade. |Não |
| parâmetros |Parâmetros para o script U-SQL |Não |

### <a name="json-example"></a>Exemplo JSON

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

Para mais informações, consulte [data lake analytics U-SQL Activity](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Atividade de Procedimento Armazenado
Pode especificar as seguintes propriedades numa definição JSON de Atividade de Procedimento Armazenado. A propriedade tipo para a atividade deve ser: **SqlServerStoredProcedure** . Deve criar um dos seguintes serviços ligados e especificar o nome do serviço ligado como um valor para a propriedade **linkedServiceName:**

- SQL Server
- Base de Dados SQL do Azure
- Azure Synapse Analytics

As seguintes propriedades são suportadas na secção **typeProperties** quando define o tipo de atividade para SqlServerStoredProcedure:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| nome de procedure armazenado |Especifique o nome do procedimento armazenado na Base de Dados Azure SQL ou na Azure Synapse Analytics que é representada pelo serviço ligado que a tabela de saída utiliza. |Sim |
| parametrómetros de reserva armazenados |Especifique os valores para os parâmetros de procedimento armazenados. Se precisar de passar nulo para um parâmetro, utilize a sintaxe: "param1": nulo (todos os casos inferiores). Consulte a seguinte amostra para saber sobre a utilização deste imóvel. |Não |

Se especificar um conjunto de dados de entrada, deve estar disponível (em estado 'Pronto') para que a atividade de procedimento armazenado seja executada. O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como parâmetro. É utilizado apenas para verificar a dependência antes de iniciar a atividade de procedimento armazenado. Deve especificar um conjunto de dados de saída para uma atividade de procedimento armazenado.

O conjunto de dados de saída especifica o **calendário** para a atividade do procedimento armazenado (hora, semanais, mensal, etc.). O conjunto de dados de saída deve utilizar um **serviço ligado** que se refira a uma Base de Dados Azure SQL ou a uma Azure Synapse Analytics ou a uma Base de Dados de Servidor SQL na qual pretende que o procedimento armazenado seja executado. O conjunto de dados de saída pode servir como forma de passar o resultado do procedimento armazenado para posterior processamento por outra atividade[(atividades](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)de cadeia) no pipeline. No entanto, a Data Factory não escreve automaticamente a saída de um procedimento armazenado para este conjunto de dados. É o procedimento armazenado que escreve para uma tabela SQL que o conjunto de dados de saída aponta. Em alguns casos, o conjunto de dados de saída pode ser um **conjunto de dados falso** , que é utilizado apenas para especificar o calendário para a execução da atividade de procedimento armazenado.

### <a name="json-example"></a>Exemplo JSON

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

Para mais informações, consulte o artigo [Atividades procedimentos armazenadas.](data-factory-stored-proc-activity.md)

## <a name="net-custom-activity"></a>Atividade personalizada do .NET
Pode especificar as seguintes propriedades numa definição JSON de atividade personalizada .NET. A propriedade tipo para a atividade deve ser: **DotNetActivity** . Tem de criar um serviço ligado a Azure HDInsight ou um serviço ligado a Azure Batch e especificar o nome do serviço ligado como um valor para a propriedade **linkedServiceName.** As seguintes propriedades são suportadas na secção **de tipos de propriedades** quando define o tipo de atividade para DotNetActivity:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| Nome da assembléia | O nome da assembléia. No exemplo, é: **MyDotnetActivity.dll** . | Sim |
| EntryPoint |Nome da classe que implementa a interface IDotNetActivity. No exemplo, é: **MyDotNetActivityNS.MyDotNetActivity** onde myDotNetActivityNS é o espaço de nome e MyDotNetActivity é a classe.  | Sim |
| Serviço PacoteLinked | Nome do serviço ligado ao Azure Storage que aponta para o armazenamento de bolhas que contém o ficheiro zip de atividade personalizada. No exemplo, é: **AzureStorageLinkedService** .| Sim |
| PacoteFile | Nome do ficheiro zip. No exemplo, é: **personalactivitycontainer/MyDotNetActivity.zip** . | Sim |
| extensões | Propriedades estendidas que pode definir e passar para o código .NET. Neste exemplo, a variável **SliceStart** é definida como um valor baseado na variável do sistema SliceStart. | Não |

### <a name="json-example"></a>Exemplo JSON

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

Para obter informações detalhadas, consulte [Utilizar as atividades personalizadas no](data-factory-use-custom-activities.md) artigo da Data Factory.

## <a name="next-steps"></a>Passos Seguintes
Veja os tutoriais seguintes:

- [Tutorial: criar um oleoduto com uma atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Tutorial: criar um oleoduto com uma atividade de colmeia](data-factory-build-your-first-pipeline.md)