---
title: Agendamento e Execução com Fábrica de Dados
description: Conheça os aspetos de agendamento e execução do modelo de aplicação da Azure Data Factory.
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
ms.openlocfilehash: 15a2d6ae5d8b80468ffcdd00d60b1f36843ed677
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281070"
---
# <a name="data-factory-scheduling-and-execution"></a>Agendamento e execução da fábrica de dados
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte a [execução do gasoduto e desencadeie](../concepts-pipeline-execution-triggers.md) o artigo.

Este artigo explica os aspetos de agendamento e execução do modelo de aplicação do Azure Data Factory. Este artigo assume que compreende os conceitos básicos dos conceitos de modelos de aplicação data Factory, incluindo atividade, oleodutos, serviços ligados e conjuntos de dados. Para conceitos básicos da Azure Data Factory, consulte os seguintes artigos:

* [Introdução à Fábrica de Dados](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Conjuntos de dados](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Tempos de início e fim do gasoduto
Um gasoduto está ativo apenas entre o seu tempo de **início** e **o seu tempo de fim.** Não é executado antes da hora de início ou após o fim do tempo. Se o gasoduto for interrompido, não é executado independentemente do seu início e fim. Para que um gasoduto possa funcionar, não deve ser interrompido. Encontra estas definições (iniciar, terminar, pausas) na definição do gasoduto: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Para mais informações, consulte a criação de artigo [pipelines.](data-factory-create-pipelines.md) 


## <a name="specify-schedule-for-an-activity"></a>Especificar o horário de uma atividade
Não é o oleoduto que é executado. São as atividades no gasoduto que são executadas no contexto geral do gasoduto. Pode especificar um horário recorrente para uma atividade utilizando a secção de **programação** da Atividade JSON. Por exemplo, pode agendar uma atividade para executar de hora a hora da seguinte forma:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Como mostrado no diagrama seguinte, especificar um horário para uma atividade cria uma série de janelas caindo com no início e no fim do gasoduto. As janelas de tropeçar são uma série de intervalos de tempo não sobrepostos e contíguos. Estas janelas lógicas para uma atividade são chamadas janelas de **atividade.**

![Exemplo de programador de atividades](media/data-factory-scheduling-and-execution/scheduler-example.png)

A propriedade **de programador** para uma atividade é opcional. Se especificar esta propriedade, deve corresponder à cadência que especifica na definição de conjunto de dados de saída para a atividade. Atualmente, é o conjunto de dados de saída que controla a agenda. Por isso, deve criar um conjunto de dados de saída mesmo que a atividade não produza qualquer saída. 

## <a name="specify-schedule-for-a-dataset"></a>Especificar o calendário para um conjunto de dados
Uma atividade num pipeline data Factory pode tomar zero ou mais **conjuntos** de dados de entrada e produzir um ou mais conjuntos de dados de saída. Para uma atividade, pode especificar a cadência em que os dados de entrada estão disponíveis ou os dados de saída são produzidos utilizando a secção **de disponibilidade** nas definições do conjunto de dados. 

**A frequência** na secção **de disponibilidade** especifica a unidade de tempo. Os valores permitidos para a frequência são: Minuto, Hora, Dia, Semana e Mês. A propriedade **de intervalo** na secção de disponibilidade especifica um multiplicador para a frequência. Por exemplo: se a frequência for definida para o Dia e o intervalo for definido para 1 para um conjunto de dados de saída, os dados de saída são produzidos diariamente. Se especificar a frequência como minuto, recomendamos que detetete o intervalo para pelo menos 15. 

No exemplo seguinte, os dados de entrada estão disponíveis de hora em hora e os dados de saída são produzidos de hora em hora (`"frequency": "Hour", "interval": 1`). 

**Conjunto de dados de entrada:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Conjunto de dados de saída**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Atualmente, o conjunto de dados de **saída impulsiona o horário**. Por outras palavras, o calendário especificado para o conjunto de dados de saída é utilizado para executar uma atividade no prazo de execução. Por isso, deve criar um conjunto de dados de saída mesmo que a atividade não produza qualquer saída. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. 

Na seguinte definição de pipeline, a propriedade do **programador** é utilizada para especificar o horário da atividade. Esta propriedade é opcional. Atualmente, o calendário da atividade deve corresponder ao calendário especificado para o conjunto de dados de saída.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

Neste exemplo, a atividade funciona de hora a hora entre o início e o fim do gasoduto. Os dados de saída são produzidos de hora em hora para janelas de três horas (8:00 - 9:00, 9:00 - 10:00 e 10:00 - 11:00). 

Cada unidade de dados consumidos ou produzidos por uma execução de atividade é chamada **de fatia**de dados . O diagrama seguinte mostra um exemplo de uma atividade com um conjunto de dados de entrada e um conjunto de dados de saída: 

![Programador de disponibilidade](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

O diagrama mostra as fatias de dados horárias para a entrada e o conjunto de dados de saída. O diagrama mostra três fatias de entrada que estão prontas para ser processadas. A atividade de 10-11 AM está em andamento, produzindo a fatia de saída de 10-11 AM. 

Pode aceder ao intervalo de tempo associado à fatia atual no conjunto de dados JSON utilizando variáveis: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) e [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Da mesma forma, pode aceder ao intervalo de tempo associado a uma janela de atividade utilizando o WindowStart e o WindowEnd. O calendário de uma atividade deve corresponder ao calendário do conjunto de dados de saída para a atividade. Por isso, os valores SliceStart e SliceEnd são os mesmos que os valores WindowStart e WindowEnd, respectivamente. Para obter mais informações sobre estas variáveis, consulte funções da [Data Factory e artigos](data-factory-functions-variables.md#data-factory-system-variables) de variáveis do sistema.  

Pode utilizar estas variáveis para diferentes fins na sua atividade JSON. Por exemplo, pode utilizá-los para selecionar dados a partir de conjuntos de dados de entrada e de saída que representam dados da série de tempo (por exemplo: 8:00 a 9 AM). Este exemplo também utiliza **o WindowStart** e **o WindowEnd** para selecionar dados relevantes para uma execução de atividade e copiá-lo para uma bolha com a **pasta adequadaPath**. A **pastaPath** é parametrizada para ter uma pasta separada por cada hora.  

No exemplo anterior, o calendário especificado para os conjuntos de dados de entrada e saída é o mesmo (hora a hora). Se o conjunto de dados de entrada para a atividade estiver disponível numa frequência diferente, por exemplo, a cada 15 minutos, a atividade que produz este conjunto de dados de saída ainda funciona uma vez por hora, uma vez que o conjunto de dados de saída é o que impulsiona o calendário de atividades. Para obter mais informações, consulte conjuntos de [dados do Modelo com frequências diferentes](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Disponibilidade e políticas de conjunto de dados
Viu o uso de propriedades de frequência e intervalo na secção de disponibilidade da definição de conjunto de dados. Existem algumas outras propriedades que afetam o agendamento e execução de uma atividade. 

### <a name="dataset-availability"></a>Disponibilidade de conjunto de dados 
A tabela seguinte descreve propriedades que pode utilizar na secção **de disponibilidade:**

| Propriedade | Descrição | Required | Predefinição |
| --- | --- | --- | --- |
| frequency |Especifica a unidade de tempo para a produção de fatias de conjunto de dados.<br/><br/><b>Frequência suportada</b>: Minuto, Hora, Dia, Semana, Mês |Sim |ND |
| interval |Especifica um multiplicador para a frequência<br/><br/>O "intervalo de frequência x" determina a frequência com que a fatia é produzida.<br/><br/>Se precisar que o conjunto de dados seja cortado de hora em hora, coloque <b>a Frequência</b> para <b>hora</b> <b>e</b> intervalo para <b>1</b>.<br/><br/><b>Nota:</b>Se especificar frequência como Minuto, recomendamos que detetete o intervalo para pelo menos 15 |Sim |ND |
| style |Especifica se a fatia deve ser produzida no início/fim do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se a Frequência estiver definida para mês e o estilo estiver definido para EndOfInterval, a fatia é produzida no último dia do mês. Se o estilo estiver definido para o StartOfInterval, a fatia é produzida no primeiro dia do mês.<br/><br/>Se a frequência estiver definida para o Dia e o estilo estiver definido para EndOfInterval, a fatia é produzida na última hora do dia.<br/><br/>Se a frequência estiver definida para hora e o estilo estiver definido para EndOfInterval, a fatia é produzida no final da hora. Por exemplo, para uma fatia para o período das 13:00 às 14:00 horas, a fatia é produzida às 14:00. |Não |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo utilizado pelo programador para calcular os limites da fatia de conjunto de dados. <br/><br/><b>Nota:</b>Se o AnchorDateTime tiver peças de data mais granulares do que a frequência, as peças mais granulares são ignoradas. <br/><br/>Por exemplo, se o <b>intervalo</b> for <b>de hora em hora</b> (frequência: hora e intervalo: 1) e o <b>AnchorDateTime</b> contiver <b>minutos e segundos,</b>então as partes dos <b>minutos e segundos</b> do AnchorDateTime são ignoradas. |Não |01/01/0001 |
| compensado |Timepan pelo qual o início e o fim de todas as fatias de conjunto de dados são deslocados. <br/><br/><b>Nota:</b>Se forem especificados o âncoraDateTime e offset, o resultado é o turno combinado. |Não |ND |

### <a name="offset-example"></a>exemplo de compensação
Por predefinição, as fatias diárias (`"frequency": "Day", "interval": 1`) começam às 12 horas utc (meia-noite). Se pretender que a hora de início seja de 6 AM UTC, em vez disso, detete a contrapartida como mostrado no seguinte corte: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>exemplo anchorDateTime
No exemplo seguinte, o conjunto de dados é produzido uma vez a cada 23 horas. A primeira fatia começa no momento especificado pelo âncoraDateTime, que está definido para `2017-04-19T08:00:00` (tempo UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>exemplo de compensação/estilo
O conjunto de dados seguinte é um conjunto de dados mensal e é produzido no dia 3 de cada mês às 8:00 am (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Política de conjunto de dados
Um conjunto de dados pode ter uma política de validação definida que especifica como os dados gerados por uma execução de fatias podem ser validados antes de estar pronto para consumo. Nesses casos, após a execução da fatia, o estado da fatia de saída é alterado para **Espera** com um subestatuto de **Validação**. Após a validação das fatias, o estado da fatia muda para **Ready**. Se uma fatia de dados tiver sido produzida mas não passou na validação, a atividade corre para fatias a jusante que dependem desta fatia não são processadas. [Monitorizar e gerir os gasodutos](data-factory-monitor-manage-pipelines.md) abrange os vários estados das fatias de dados na Fábrica de Dados.

A secção **de política** na definição de conjunto de dados define os critérios ou a condição que as fatias de conjunto de dados devem cumprir. A tabela seguinte descreve propriedades que pode utilizar na secção **política:**

| Nome da Política | Descrição | Aplicado a | Required | Predefinição |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Valida que os dados de uma **bolha Azure** satisfazem os requisitos mínimos de tamanho (em megabytes). |Blob do Azure |Não |ND |
| minimumRows | Valida que os dados numa base de **dados Azure SQL** ou numa **tabela Azure** contenham o número mínimo de linhas. |<ul><li>Base de Dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |ND |

#### <a name="examples"></a>Exemplos
**mínimoTamanhoMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**mínimoS filas**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Para obter mais informações sobre estas propriedades e exemplos, consulte o artigo [Criar conjuntos](data-factory-create-datasets.md) de dados. 

## <a name="activity-policies"></a>Políticas de atividade
As políticas afetam o comportamento de tempo de execução de uma atividade, especificamente quando a fatia de uma mesa é processada. A tabela seguinte fornece os detalhes.

| Propriedade | Valores permitidos | Default Value | Descrição |
| --- | --- | --- | --- |
| concurrency |Número inteiro <br/><br/>Valor máximo: 10 |1 |Número de execuções simultâneas da atividade.<br/><br/>Determina o número de execuções paralelas de atividade que podem acontecer em diferentes fatias. Por exemplo, se uma atividade precisa passar por um grande conjunto de dados disponíveis, ter um valor de condivisa maior acelera o processamento de dados. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Determina a encomenda de fatias de dados que estão a ser processadas.<br/><br/>Por exemplo, se tiver 2 fatias (uma a acontecer às 16h, e outra às 17h), e ambas estão pendentes de execução. Se definir a execuçãoPriorityOrder para ser NewestFirst, a fatia às 17:00 é processada primeiro. Da mesma forma, se definir a execuçãoPriorityORder como O FIrst mais antigo, então a fatia às 16:00 é processada. |
| retry |Número inteiro<br/><br/>O valor máximo pode ser 10 |0 |O número de repetições antes do processamento de dados para a fatia é marcado como Falha. A execução da atividade para uma fatia de dados é novamente experimentada até à contagem de retry especificada. A reprovação é feita o mais rápido possível após o fracasso. |
| timeout |TimeSpan |00:00:00 |Intervalo para a atividade. Exemplo: 00:10:00 (implica tempo de 10 minutos)<br/><br/>Se um valor não for especificado ou for 0, o tempo limite é infinito.<br/><br/>Se o tempo de processamento de dados numa fatia exceder o valor de tempo limite, é cancelado e o sistema tenta voltar a tentar o processamento. O número de tentativas depende da propriedade de retry. Quando o tempo de tempo ocorre, o estado é definido para TimedOut. |
| delay |TimeSpan |00:00:00 |Especifique o atraso antes do processamento de dados da fatia.<br/><br/>A execução da atividade para uma fatia de dados é iniciada após o atraso ter passado o tempo de execução esperado.<br/><br/>Exemplo: 00:10:00 (implica atraso de 10 minutos) |
| longRetry |Número inteiro<br/><br/>Valor máximo: 10 |1 |O número de tentativas de retry longas antes da execução da fatia é falhado.<br/><br/>as tentativas de longRetry são espaçadas por longRetryInterval. Por isso, se precisar especificar um tempo entre tentativas de retry, use longRetry. Se forespecificado tanto o Retry como o longRetry, cada tentativa de longa-retry inclui tentativas de retry e o número máximo de tentativas é Retry * longRetry.<br/><br/>Por exemplo, se tivermos as seguintes definições na política de atividade:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Assuma que só há uma fatia para executar (o estado é espera) e a execução da atividade falha sempre. Inicialmente haveria 3 tentativas de execução consecutivas. Após cada tentativa, o estado da fatia seria Retry. Depois de terminarem as primeiras 3 tentativas, o estado da fatia seria LongRetry.<br/><br/>Após uma hora (isto é, valor de LongRetryInteval), haveria outro conjunto de 3 tentativas de execução consecutivas. Depois disso, o estado da fatia seria falhado e não se tentariam mais tentativas de tentativa. Daí que no total foram feitas 6 tentativas.<br/><br/>Se qualquer execução for bem sucedida, o estado da fatia estará pronto e não se tentarem mais tentativas de tentativas.<br/><br/>O longRetry pode ser utilizado em situações em que os dados dependentes chegam a tempos não determinísticos ou o ambiente global é excêntrico sob o qual ocorre o tratamento de dados. Nesses casos, fazer repetições um após o outro pode não ajudar e fazê-lo após um intervalo de tempo resulta na saída desejada.<br/><br/>Palavra de precaução: não detete valores elevados para longRetry ou longRetryInterval. Tipicamente, valores mais elevados implicam outras questões sistémicas. |
| longRetryInterval |TimeSpan |00:00:00 |O atraso entre longas tentativas de retry |

Para mais informações, consulte o artigo [da Pipelines.](data-factory-create-pipelines.md) 

## <a name="parallel-processing-of-data-slices"></a>Processamento paralelo de fatias de dados
Pode definir a data de início para o oleoduto no passado. Quando o faz, a Data Factory calcula automaticamente (enchimentos traseiros) todas as fatias de dados no passado e começa a processá-las. Por exemplo: se criar um pipeline com data de início 2017-04-01 e a data atual for 2017-04-10. Se a cadência do conjunto de dados de saída for diária, então a Data Factory começa a processar todas as fatias de 2017-04-01 a 2017-04-09 imediatamente porque a data de início está no passado. A fatia de 2017-04-10 ainda não é processada porque o valor da propriedade de estilo na secção de disponibilidade é EndOfInterval por padrão. A fatia mais antiga é processada primeiro, uma vez que o valor padrão da execuçãoPriorityOrder é o Mais AntigoPrimeiro. Para obter uma descrição da propriedade de estilo, consulte a secção de disponibilidade do conjunto de [dados.](#dataset-availability) Para obter uma descrição da secção 'PriorityOrder' de execução, consulte a secção de políticas de [atividade.](#activity-policies) 

Pode configurar fatias de dados cheias de volta a serem processadas paralelamente, definindo a propriedade **de moeda concurrency** na **secção** política da atividade JSON. Esta propriedade determina o número de execuções paralelas de atividade que podem acontecer em diferentes fatias. O valor padrão para a propriedade de moeda é 1. Portanto, uma fatia é processada de cada vez por padrão. O valor máximo é 10. Quando um gasoduto precisa de passar por um grande conjunto de dados disponíveis, ter um valor de condivisa maior acelera o processamento de dados. 

## <a name="rerun-a-failed-data-slice"></a>Reexecutar uma fatia de dados falhada
Quando ocorre um erro durante o processamento de uma fatia de dados, pode descobrir porque é que o processamento de uma fatia falhou utilizando lâminas de portal Azure ou Monitor e Manage App. Consulte [a monitorização e gestão de gasodutos utilizando lâminas](data-factory-monitor-manage-pipelines.md) de portal Azure ou app de [monitorização e gestão](data-factory-monitor-manage-app.md) para mais detalhes.

Considere o seguinte exemplo, que mostra duas atividades. Atividade1 e Atividade 2. A Atividade1 consome uma fatia de Dataset1 e produz uma fatia de Dataset2, que é consumida como entrada pela Activity2 para produzir uma fatia do Conjunto de Dados Finais.

![Fatia falhada](./media/data-factory-scheduling-and-execution/failed-slice.png)

O diagrama mostra que de três fatias recentes, houve uma falha produzindo a fatia de 9-10 AM para Dataset2. A Data Factory rastreia automaticamente a dependência do conjunto de dados da série de tempo. Como resultado, não inicia a atividade para a fatia a jusante das 9-10 AM.

As ferramentas de monitorização e gestão da Fábrica de Dados permitem-lhe perfurar os registos de diagnóstico para que a fatia falhada encontre facilmente a causa principal do problema e o corrija. Depois de ter corrigido o problema, pode facilmente iniciar a execução da atividade para produzir a fatia falhada. Para obter mais informações sobre como reexecutar e compreender as transições estatais para as fatias de dados, consulte [monitorização e gestão de gasodutos utilizando lâminas](data-factory-monitor-manage-pipelines.md) de portal Azure ou [app de monitorização e gestão.](data-factory-monitor-manage-app.md)

Depois de reexecutar a fatia de 9-10 AM para **Dataset2,** data Factory inicia a corrida para a fatia dependente de 9-10 AM no conjunto de dados final.

![Reexecutar fatia falhada](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Múltiplas atividades num pipeline
Pode ter mais de uma atividade num pipeline. Se tiver múltiplas atividades num pipeline e a saída de uma atividade não for uma entrada de outra atividade, as atividades podem ser executadas em paralelo se as fatias de dados de entrada para as atividades estiverem prontas.

Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. As atividades podem estar no mesmo oleoduto ou em diferentes oleodutos. A segunda atividade só executa quando a primeira termina com sucesso.

Por exemplo, considere o seguinte caso em que um gasoduto tem duas atividades:

1. Atividade A1 que requer conjunto de dados de entrada externa D1, e produz conjunto de dados de saída D2.
2. Atividade A2 que requer entrada do conjunto de dados D2, e produz o conjunto de dados de saída D3.

Neste cenário, as atividades A1 e A2 estão no mesmo oleoduto. A atividade A1 funciona quando os dados externos estão disponíveis e a frequência de disponibilidade programada é alcançada. A atividade A2 funciona quando as fatias programadas de D2 ficam disponíveis e a frequência de disponibilidade programada é alcançada. Se houver um erro numa das fatias do conjunto de dados D2, a A2 não funciona para esta fatia até que fique disponível.

A vista do Diagrama com ambas as atividades no mesmo oleoduto seria como o seguinte diagrama:

![Atividades de cadeia no mesmo oleoduto](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Como mencionado anteriormente, as atividades poderiam estar em diferentes oleodutos. Em tal cenário, a visão do diagrama seria como o seguinte diagrama:

![Atividades de cadeia em dois oleodutos](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Consulte a secção sequencialmente da cópia no apêndice, por exemplo.

## <a name="model-datasets-with-different-frequencies"></a>Conjuntos de dados de modelos com frequências diferentes
Nas amostras, as frequências para os conjuntos de dados de entrada e saída e a janela de horário de atividade eram as mesmas. Alguns cenários requerem a capacidade de produzir produção numa frequência diferente das frequências de uma ou mais inputs. Data Factory suporta modelação destes cenários.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Amostra 1: Produzir um relatório de saída diário para os dados de entrada que estão disponíveis a cada hora
Considere um cenário em que tem dados de medição de entrada de sensores disponíveis a cada hora no armazenamento do Azure Blob. Pretende produzir um relatório agregado diário com estatísticas como média, máxima e mínima para o dia com atividade de [colmeia data Factory.](data-factory-hive-activity.md)

Eis como pode modelar este cenário com data factory:

**Conjunto de dados de entrada**

Os ficheiros de entrada horária são colocados na pasta para o dia seguinte. A disponibilidade para entrada é definida à **Hora** (frequência: Hora, intervalo: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Conjunto de dados de saída**

Um ficheiro de saída é criado todos os dias na pasta do dia. A disponibilidade de saída é definida no **dia** (frequência: dia e intervalo: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Atividade: atividade da colmeia num oleoduto**

O script da colmeia recebe as informações de *DataTime* apropriadas como parâmetros que utilizam a variável **WindowStart** como mostrado no seguinte corte. O script da colmeia utiliza esta variável para carregar os dados da pasta correta para o dia e executar a agregação para gerar a saída.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
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
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

O diagrama seguinte mostra o cenário do ponto de vista da dependência de dados.

![Dependência de dados](./media/data-factory-scheduling-and-execution/data-dependency.png)

A fatia de saída para cada dia depende de 24 fatias por hora de um conjunto de dados de entrada. Data Factory calcula estas dependências automaticamente através da elaboração das fatias de dados de entrada que caem no mesmo período de tempo que a fatia de saída a produzir. Se alguma das 24 fatias de entrada não estiver disponível, a Data Factory aguarda que a fatia de entrada esteja pronta antes de iniciar a execução diária da atividade.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Amostra 2: Especificar dependência com expressões e funções de fábrica de dados
Vamos considerar outro cenário. Suponha que tenha uma atividade de colmeia que processa dois conjuntos de dados de entrada. Um deles tem novos dados diariamente, mas um deles recebe novos dados todas as semanas. Suponha que queria fazer uma união entre as duas inputs e produzir uma saída todos os dias.

A abordagem simples em que a Data Factory descobre automaticamente as fatias de entrada certas para processar, alinhando-se com o período de tempo da fatia de dados de saída não funciona.

Deve especificar que para cada execução de atividade, a Fábrica de Dados deve utilizar a fatia de dados da semana passada para o conjunto de dados de entrada semanal. Utiliza funções da Azure Data Factory, como mostra o seguinte corte para implementar este comportamento.

**Entrada1: Blob Azure**

A primeira entrada é a bolha Azure a ser atualizada diariamente.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Entrada2: Blob Azure**

Input2 é a bolha Azure sendo atualizada semanalmente.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Saída: Blob Azure**

Um ficheiro de saída é criado todos os dias na pasta para o dia. A disponibilidade de saída é definida para **o dia** (frequência: dia, intervalo: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Atividade: atividade da colmeia num oleoduto**

A atividade da colmeia pega nas duas inputs e produz uma fatia de saída todos os dias. Pode especificar a fatia de saída de cada dia para depender da fatia de entrada da semana anterior para a entrada semanal da seguinte forma.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

Consulte [as funções da Data Factory e as variáveis](data-factory-functions-variables.md) do sistema para uma lista de funções e variáveis do sistema que a Data Factory suporta.

## <a name="appendix"></a>Anexo

### <a name="example-copy-sequentially"></a>Exemplo: copiar sequencialmente
É possível executar várias operações de cópia uma após a outra de forma sequencial/ordenada. Por exemplo, pode ter duas atividades de cópia num pipeline (CopyActivity1 e CopyActivity2) com os seguintes conjuntos de dados de saída de dados de entrada:   

CopyActivity1

Entrada: Conjunto de dados. Saída: Dataset2.

CopyActivity2

Entrada: Dataset2.  Saída: Dataset3.

CopyActivity2 só seria executado se o CopyActivity1 tiver executado com sucesso e dataset2 estiver disponível.

Aqui está o pipeline de amostraJSON:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Note que, no exemplo, o conjunto de dados de saída da primeira atividade de cópia (Dataset2) é especificado como entrada para a segunda atividade. Portanto, a segunda atividade só funciona quando o conjunto de dados de saída da primeira atividade estiver pronto.  

No exemplo, o CopyActivity2 pode ter uma entrada diferente, como dataset3, mas especifica Dataset2 como uma entrada para copyActivity2, para que a atividade não seja executada até que o CopyActivity1 termine. Por exemplo:

CopyActivity1

Entrada: Dataset1. Saída: Dataset2.

CopyActivity2

Inputs: Dataset3, Dataset2. Saída: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Note que, no exemplo, são especificados dois conjuntos de dados de entrada para a segunda atividade de cópia. Quando várias inputs são especificadas, apenas o primeiro conjunto de dados de entrada é usado para copiar dados, mas outros conjuntos de dados são usados como dependências. A CopyActivity2 só começaria depois de satisfeitas as seguintes condições:

* CopyActivity1 concluiu com sucesso e dataset2 está disponível. Este conjunto de dados não é utilizado na cópia dos dados para dataset4. Funciona apenas como uma dependência de agendamento para copyActivity2.   
* Dataset3 está disponível. Este conjunto de dados representa os dados copiados para o destino. 
