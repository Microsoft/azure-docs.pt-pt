---
title: Agendamento e Execução com Fábrica de Dados
description: Aprenda os aspetos de agendamento e execução do modelo de aplicação da Azure Data Factory.
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
ms.openlocfilehash: e0707f9a7694741f54771699f5aeb3b452b11b8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85319725"
---
# <a name="data-factory-scheduling-and-execution"></a>Agendamento e execução da Fábrica de Dados
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte a execução do [gasoduto e desencadeie](../concepts-pipeline-execution-triggers.md) artigo.

Este artigo explica os aspetos de agendamento e execução do modelo de aplicação do Azure Data Factory. Este artigo assume que compreende os fundamentos dos conceitos de modelo de aplicação data factory, incluindo atividade, oleodutos, serviços ligados e conjuntos de dados. Para conceitos básicos da Azure Data Factory, consulte os seguintes artigos:

* [Introdução ao Data Factory](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Conjuntos de dados](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Início e fim dos tempos do gasoduto
Um gasoduto só está ativo entre a hora **de início** e **o fim.** Não é executado antes da hora de início ou depois do fim do tempo. Se o gasoduto for pausado, não será executado independentemente do seu início e fim de tempo. Para que um oleoduto corra, não deve ser interrompido. Encontra estas definições (iniciar, terminar, pausar) na definição do gasoduto: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Para obter mais informações sobre estas propriedades, consulte criar artigo [de pipelines.](data-factory-create-pipelines.md) 


## <a name="specify-schedule-for-an-activity"></a>Especificar horário para uma atividade
Não é o oleoduto que é executado. São as atividades no oleoduto que são executadas no contexto global do gasoduto. Pode especificar um horário recorrente para uma atividade utilizando a secção de **horário** de atividade JSON. Por exemplo, pode agendar uma atividade a funcionar de hora a hora da seguinte forma:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Como mostrado no diagrama seguinte, especificar um horário para uma atividade cria uma série de janelas de tropeçar com os tempos de início e fim do gasoduto. As janelas caindo são uma série de intervalos de tempo contíguos e sem sobreposição de tamanho fixo. Estas janelas lógicos para uma atividade são chamadas **janelas de atividade.**

![Exemplo de programador de atividades](media/data-factory-scheduling-and-execution/scheduler-example.png)

A propriedade **do agendador** para uma atividade é opcional. Se especificar esta propriedade, deve corresponder à cadência que especifica na definição de conjunto de dados de saída para a atividade. Atualmente, é o conjunto de dados de saída que controla a agenda. Portanto, deve criar um conjunto de dados de saída mesmo que a atividade não produza qualquer saída. 

## <a name="specify-schedule-for-a-dataset"></a>Especificar a programação para um conjunto de dados
Uma atividade num oleoduto da Data Factory pode tomar zero ou mais **conjuntos de dados de** entrada e produzir um ou mais conjuntos de dados de saída. Para uma atividade, pode especificar a cadência em que os dados de entrada estão disponíveis ou os dados de saída são produzidos utilizando a secção **de disponibilidade** nas definições do conjunto de dados. 

**A frequência** na secção **de disponibilidade** especifica a unidade de tempo. Os valores permitidos para a frequência são: Minuto, Hora, Dia, Semana e Mês. A propriedade **de intervalo** na secção de disponibilidade especifica um multiplicador para a frequência. Por exemplo: se a frequência for definida para o Dia e o intervalo for definido para 1 para um conjunto de dados de saída, os dados de saída são produzidos diariamente. Se especificar a frequência como minuto, recomendamos que desemende o intervalo para nada menos do que 15. 

No exemplo seguinte, os dados de entrada estão disponíveis por hora e os dados de saída são produzidos à hora `"frequency": "Hour", "interval": 1` (). 

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

Atualmente, **o conjunto de dados de saída impulsiona o horário**. Por outras palavras, o calendário especificado para o conjunto de dados de saída é utilizado para executar uma atividade em tempo de execução. Portanto, deve criar um conjunto de dados de saída mesmo que a atividade não produza qualquer saída. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. 

Na seguinte definição de pipeline, a propriedade **do programador** é usada para especificar o calendário para a atividade. Esta propriedade é opcional. Atualmente, o calendário da atividade deve corresponder ao calendário especificado para o conjunto de dados de saída.
 
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

Neste exemplo, a atividade decorre de hora a hora entre os tempos de início e fim do gasoduto. Os dados de saída são produzidos de hora em hora para janelas de três horas (8:00 - 09:00, 09:00 - 10:00 e 10:00 - 11:00). 

Cada unidade de dados consumidos ou produzidos por uma atividade executada é chamada de fatia de **dados**. O diagrama a seguir mostra um exemplo de uma atividade com um conjunto de dados de entrada e um conjunto de dados de saída: 

![Programador de disponibilidade](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

O diagrama mostra as fatias de dados horárias para o conjunto de dados de entrada e saída. O diagrama mostra três fatias de entrada prontas para o processamento. A atividade das 10-11 AM está em andamento, produzindo a fatia de saída das 10-11 am. 

Pode aceder ao intervalo de tempo associado à fatia atual no conjunto de dados JSON utilizando variáveis: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) e [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Da mesma forma, pode aceder ao intervalo de tempo associado a uma janela de atividade utilizando o WindowsStart e o WindowEnd. O calendário de uma atividade deve corresponder ao calendário do conjunto de dados de saída para a atividade. Por isso, os valores SliceStart e SliceEnd são os mesmos que os valores WindowStart e WindowEnd, respectivamente. Para obter mais informações sobre estas variáveis, consulte [funções de Data Factory e artigos de variáveis do sistema.](data-factory-functions-variables.md#data-factory-system-variables)  

Pode utilizar estas variáveis para diferentes finalidades na sua atividade JSON. Por exemplo, pode usá-los para selecionar dados de conjuntos de dados de entrada e saída que representam dados da série de tempo (por exemplo: 8:00 às 9:00). Este exemplo também utiliza **o WindowStart** e **o WindowEnd** para selecionar dados relevantes para uma execução de atividade e copiá-lo para uma bolha com a **pasta apropriadaPath**. A **pastaApata** é parametrizada para ter uma pasta separada para cada hora.  

No exemplo anterior, o calendário especificado para conjuntos de dados de entrada e saída é o mesmo (à hora). Se o conjunto de dados de entrada para a atividade estiver disponível numa frequência diferente, digamos a cada 15 minutos, a atividade que produz este conjunto de dados de saída ainda funciona uma vez por hora, uma vez que o conjunto de dados de saída é o que impulsiona o calendário de atividade. Para obter mais informações, consulte [conjuntos de dados do Modelo com diferentes frequências.](#model-datasets-with-different-frequencies)

## <a name="dataset-availability-and-policies"></a>Disponibilidade e políticas do conjunto de dados
Viu o uso de propriedades de frequência e intervalo na secção de disponibilidade da definição de conjunto de dados. Existem algumas outras propriedades que afetam o agendamento e execução de uma atividade. 

### <a name="dataset-availability"></a>Disponibilidade do conjunto de dados 
A tabela a seguir descreve propriedades que pode utilizar na secção **de disponibilidade:**

| Propriedade | Descrição | Obrigatório | Predefinição |
| --- | --- | --- | --- |
| frequência |Especifica a unidade de tempo para a produção de fatias de conjunto de dados.<br/><br/><b>Frequência suportada</b>: Minuto, Hora, Dia, Semana, Mês |Sim |ND |
| interval |Especifica um multiplicador para a frequência<br/><br/>"Intervalo de frequência x" determina a frequência com que a fatia é produzida.<br/><br/>Se necessitar do conjunto de dados para ser cortado de hora a hora, define <b>a frequência</b> para a <b>hora</b>, e <b>intervalo</b> para <b>1</b>.<br/><br/><b>Nota:</b>Se especificar a frequência como Minuto, recomendamos que desemende o intervalo para nada menos do que 15 |Sim |ND |
| estilo |Especifica se a fatia deve ser produzida no início/fim do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se a frequência está definida para mês e o estilo está definido para EndOfInterval, a fatia é produzida no último dia do mês. Se o estilo estiver definido para StartOfInterval, a fatia é produzida no primeiro dia do mês.<br/><br/>Se a frequência estiver definida para o Dia e o estilo estiver definido para EndOfInterval, a fatia é produzida na última hora do dia.<br/><br/>Se a frequência estiver definida para Hora e o estilo estiver definido para EndOfInterval, a fatia é produzida no final da hora. Por exemplo, para uma fatia para o período das 13:00 às 14:00, a fatia é produzida às 14:00. |Não |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo utilizado pelo programador para calcular os limites da fatia de conjunto de dados. <br/><br/><b>Nota:</b>Se o AnchorDateTime tiver peças de data mais granulares do que a frequência, as partes mais granulares são ignoradas. <br/><br/>Por exemplo, se o <b>intervalo</b> for <b>de hora</b> a hora (frequência: hora e intervalo: 1) e o <b>AnchorDateTime</b> contiver <b>minutos e segundos,</b>então as partes de <b>minutos e segundos</b> do AnchorDateTime são ignoradas. |Não |01/01/0001 |
| offset |Timepan pelo qual o início e o fim de todas as fatias de conjunto de dados são deslocados. <br/><br/><b>Nota:</b>Se forem especificados tanto o anchorDateTime como o offset, o resultado é a mudança combinada. |Não |ND |

### <a name="offset-example"></a>exemplo offset
Por padrão, `"frequency": "Day", "interval": 1` as fatias diárias () começam às 12 horas utc (meia-noite). Se pretender que a hora de início seja 6 HORAS UTC, desapeje a compensação como mostrado no seguinte corte: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime exemplo
No exemplo seguinte, o conjunto de dados é produzido uma vez a cada 23 horas. A primeira fatia começa no momento especificado pelo anchorDateTime, que está definido para `2017-04-19T08:00:00` (tempo UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>offset/style Exemplo
O conjunto de dados seguinte é um conjunto de dados mensal e é produzido no dia 3 de cada mês às 8:00 AM `3.08:00:00` ( ):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Política de dataset
Um conjunto de dados pode ter uma política de validação definida que especifica como os dados gerados por uma execução de fatias podem ser validados antes de estar pronto para consumo. Nestes casos, após a execução da fatia, o estado da fatia de saída é alterado para **Esperar** com um substatus de **Validação**. Depois de validadas as fatias, o estado da fatia muda para **Ready**. Se uma fatia de dados tiver sido produzida mas não passou a validação, a atividade corre para fatias a jusante que dependem desta fatia não são processadas. [Monitorizar e gerir os oleodutos](data-factory-monitor-manage-pipelines.md) abrange os vários estados de fatias de dados na Data Factory.

A secção **de política** na definição de conjunto de dados define os critérios ou a condição que as fatias de conjunto de dados devem cumprir. A tabela a seguir descreve propriedades que pode utilizar na secção **política:**

| Nome da Política | Descrição | Aplicado a | Necessário | Predefinição |
| --- | --- | --- | --- | --- |
| tamanho mínimoS | Valida que os dados numa **bolha Azure** satisfaz os requisitos mínimos de tamanho (em megabytes). |Blob do Azure |Não |ND |
| sobrancelhas mínimas | Valida que os dados na **Base de Dados Azure SQL** ou **na tabela Azure** contém o número mínimo de linhas. |<ul><li>Base de Dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |ND |

#### <a name="examples"></a>Exemplos
**tamanho mínimoS:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**sobrancelhas mínimas**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Para obter mais informações sobre estas propriedades e exemplos, consulte Criar artigo [conjuntos de dados.](data-factory-create-datasets.md) 

## <a name="activity-policies"></a>Políticas de atividade
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

Para mais informações, consulte o artigo [Pipelines.](data-factory-create-pipelines.md) 

## <a name="parallel-processing-of-data-slices"></a>Tratamento paralelo de fatias de dados
Pode definir a data de início para o oleoduto no passado. Quando o fizer, a Data Factory calcula automaticamente (reencama) todas as fatias de dados no passado e começa a processá-las. Por exemplo: se criar um pipeline com data de início 2017-04-01 e a data atual for 2017-04-10. Se a cadência do conjunto de dados de saída for diária, então a Data Factory começa a processar todas as fatias de 2017-04-01 a 2017-04-09 imediatamente porque a data de início é no passado. A fatia de 2017-04-10 ainda não é processada porque o valor da propriedade de estilo na secção de disponibilidade é EndOfInterval por padrão. A fatia mais antiga é processada primeiro como o valor padrão de execuçãoPriorityOrder é o Mais Antigo Primeiro. Para uma descrição da propriedade de estilo, consulte a secção [de disponibilidade do conjunto de dados.](#dataset-availability) Para obter uma descrição da secção 'Preenreser' de execução, consulte a secção [de políticas de atividade.](#activity-policies) 

Pode configurar fatias de dados reenchidas para serem processadas paralelamente, definindo a propriedade **de concurrency** na secção **de política** da atividade JSON. Esta propriedade determina o número de execuções paralelas de atividade que podem acontecer em diferentes fatias. O valor padrão para a propriedade de concurrency é 1. Portanto, uma fatia é processada de cada vez por defeito. O valor máximo é de 10. Quando um oleoduto precisa passar por um grande conjunto de dados disponíveis, ter um maior valor de concordância acelera o processamento de dados. 

## <a name="rerun-a-failed-data-slice"></a>Reenquadrem uma fatia de dados falhada
Quando ocorre um erro durante o processamento de uma fatia de dados, pode descobrir por que o processamento de uma fatia falhou usando lâminas do portal Azure ou Monitor e Manage App. Consulte [a monitorização e gestão de gasodutos utilizando lâminas do portal Azure](data-factory-monitor-manage-pipelines.md) ou [app de monitorização e gestão](data-factory-monitor-manage-app.md) para obter mais detalhes.

Considere o exemplo a seguir, que mostra duas atividades. Atividade1 e Atividade 2. A Atividade1 consome uma fatia de Dataset1 e produz uma fatia de Dataset2, que é consumida como uma entrada pela Activity2 para produzir uma fatia do Conjunto de Dados Finais.

![Fatia falhada](./media/data-factory-scheduling-and-execution/failed-slice.png)

O diagrama mostra que de três fatias recentes, houve uma falha na produção da fatia das 9-10 AM para o Dataset2. A Data Factory rastreia automaticamente a dependência do conjunto de dados da série de tempo. Como resultado, não inicia a atividade para a fatia a jusante das 9-10 am.

As ferramentas de monitorização e gestão da Data Factory permitem perfurar os registos de diagnóstico para que a fatia falhada encontre facilmente a causa principal para o problema e corrija-a. Depois de ter corrigido o problema, pode facilmente iniciar a atividade para produzir a fatia falhada. Para obter mais informações sobre como refazer e compreender as transições estatais para as fatias de dados, consulte [monitorizar e gerir os oleodutos utilizando lâminas do portal Azure](data-factory-monitor-manage-pipelines.md) ou app de [monitorização e gestão](data-factory-monitor-manage-app.md).

Depois de repetir a fatia de 9-10 AM para **dataset2,** a Data Factory inicia a execução para a fatia dependente de 9-10 AM no conjunto de dados final.

![Corte falhada de repetição](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Múltiplas atividades num pipeline
Pode ter mais de uma atividade num pipeline. Se tiver múltiplas atividades num oleoduto e a produção de uma atividade não for uma entrada de outra atividade, as atividades podem ser executadas em paralelo se as fatias de dados de entrada para as atividades estiverem prontas.

Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. As atividades podem estar no mesmo oleoduto ou em diferentes oleodutos. A segunda atividade só executa quando a primeira termina com sucesso.

Por exemplo, considere o seguinte caso em que um gasoduto tenha duas atividades:

1. Atividade A1 que requer o conjunto de dados de entrada externa D1, e produz o conjunto de dados de saída D2.
2. Atividade A2 que requer entrada do conjunto de dados D2, e produz o conjunto de dados de saída D3.

Neste cenário, as atividades A1 e A2 encontram-se no mesmo oleoduto. A atividade A1 é executado quando os dados externos estão disponíveis e a frequência de disponibilidade programada é alcançada. A atividade A2 é executado quando as fatias programadas de D2 ficam disponíveis e a frequência de disponibilidade programada é alcançada. Se houver um erro numa das fatias do conjunto de dados D2, o A2 não corre para essa fatia até estar disponível.

A vista diagrama com ambas as atividades no mesmo oleoduto seria como o seguinte diagrama:

![Atividades de acorrentamento no mesmo oleoduto](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Tal como já foi referido, as atividades poderiam estar em diferentes oleodutos. Em tal cenário, a vista do diagrama seria como o seguinte diagrama:

![Atividades de acorrentamento em dois oleodutos](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Consulte a secção de cópia sequencialmente no apêndice para um exemplo.

## <a name="model-datasets-with-different-frequencies"></a>Conjuntos de dados de modelos com frequências diferentes
Nas amostras, as frequências para conjuntos de dados de entrada e saída e a janela de agendamento de atividades eram as mesmas. Alguns cenários requerem a capacidade de produzir a produção a uma frequência diferente das frequências de uma ou mais entradas. A Data Factory suporta a modelação destes cenários.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Amostra 1: Elaborar um relatório diário de saída para os dados de entrada que esteja disponível a cada hora
Considere um cenário em que tenha dados de medição de entrada de sensores disponíveis a cada hora no armazenamento de Azure Blob. Pretende-se produzir um relatório agregado diário com estatísticas como média, máxima e mínima para o dia com [a atividade da Colmeia data Factory.](data-factory-hive-activity.md)

Eis como pode modelar este cenário com a Data Factory:

**Conjunto de dados de entrada**

Os ficheiros de entrada de hora a hora são deixados na pasta durante o dia. A disponibilidade para entrada é definida à **hora** (frequência: hora, intervalo: 1).

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

O script da colmeia recebe as informações adequadas *do DateTime* como parâmetros que utilizam a variável **WindowStart,** como mostrado no seguinte corte. O script da colmeia utiliza esta variável para carregar os dados da pasta correta durante o dia e executar a agregação para gerar a saída.

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

A fatia de saída para cada dia depende de fatias de 24 horas por hora a partir de um conjunto de dados de entrada. A Data Factory calcula estas dependências automaticamente, calculando as fatias de dados de entrada que caem no mesmo período de tempo que a fatia de saída a produzir. Se alguma das 24 fatias de entrada não estiver disponível, a Data Factory aguarda que a fatia de entrada esteja pronta antes de iniciar a atividade diária.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Amostra 2: Especificar a dependência com expressões e funções de Data Factory
Vamos considerar outro cenário. Suponha que tenha uma atividade de colmeia que processa dois conjuntos de dados de entrada. Um deles tem novos dados diariamente, mas um deles recebe novos dados todas as semanas. Suponha que queria fazer uma junção através das duas entradas e produzir uma saída todos os dias.

A abordagem simples em que a Data Factory calcula automaticamente as fatias de entrada certas para processar, alinhando-se com o período de tempo da fatia de dados de saída, não funciona.

Deve especificar que, para cada atividade executada, a Data Factory deve utilizar a fatia de dados da semana passada para o conjunto de dados de entrada semanal. Utiliza funções de Azure Data Factory, como mostrado no seguinte corte para implementar este comportamento.

**Input1: Bolha de Azure**

A primeira entrada é a bolha Azure sendo atualizada diariamente.

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

**Input2: Bolha de Azure**

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

**Saída: Bolha azul**

Um ficheiro de saída é criado todos os dias na pasta durante o dia. A disponibilidade de saída é definida para **o dia** (frequência: dia, intervalo: 1).

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

A atividade da colmeia pega as duas entradas e produz uma fatia de saída todos os dias. Pode especificar a fatia de saída de todos os dias para depender da fatia de entrada da semana anterior para a entrada semanal da seguinte forma.

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

## <a name="appendix"></a>Apêndice

### <a name="example-copy-sequentially"></a>Exemplo: copiar sequencialmente
É possível executar várias operações de cópia uma após a outra de forma sequencial/ordenada. Por exemplo, pode ter duas atividades de cópia num pipeline (CopyActivity1 e CopyActivity2) com os seguintes conjuntos de dados de saída de dados de entrada:   

CopyActivity1

Entrada: Conjunto de dados. Saída: Dataset2.

CopyActivity2

Entrada: Dataset2.  Saída: Dataset3.

CopyActivity2 só funcionaria se o CopyActivity1 tivesse funcionado com sucesso e o Dataset2 estivesse disponível.

Aqui está o gasoduto de amostra JSON:

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

Note que, no exemplo, o conjunto de dados de saída da primeira atividade de cópia (Dataset2) é especificado como entrada para a segunda atividade. Portanto, a segunda atividade funciona apenas quando o conjunto de dados de saída da primeira atividade está pronto.  

No exemplo, copyActivity2 pode ter uma entrada diferente, como o Dataset3, mas especifica o Dataset2 como uma entrada para copyActivity2, para que a atividade não seja executada até que o CopyActivity1 termine. Por exemplo:

CopyActivity1

Entrada: Dataset1. Saída: Dataset2.

CopyActivity2

Entradas: Dataset3, Dataset2. Saída: Dataset4.

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

Note que, no exemplo, são especificados dois conjuntos de dados de entrada para a segunda atividade de cópia. Quando são especificadas várias entradas, apenas o primeiro conjunto de dados de entrada é utilizado para copiar dados, mas outros conjuntos de dados são usados como dependências. CopyActivity2 só começaria depois de cumpridas as seguintes condições:

* CopyActivity1 foi concluído com sucesso e o Dataset2 está disponível. Este conjunto de dados não é utilizado ao copiar dados para o Dataset4. Atua apenas como uma dependência de agendamento para copyActivity2.   
* O Dataset3 está disponível. Este conjunto de dados representa os dados que são copiados para o destino. 
