---
title: Agendamento e execução com o Data Factory | Documentos da Microsoft
description: Saiba mais aspetos de agendamento e execução do modelo de aplicação do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 2d7fc45faf1fb77c7d9181e5a2419096dd1ad0f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258954"
---
# <a name="data-factory-scheduling-and-execution"></a>Agendamento da fábrica de dados e execução
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [execuções de pipelines e acionadores](../concepts-pipeline-execution-triggers.md) artigo.

Este artigo explica os aspetos de agendamento e execução do modelo de aplicação do Azure Data Factory. Este artigo parte do princípio de que compreende os fundamentos básicos dos conceitos de modelo de aplicação de fábrica de dados, incluindo atividades, pipelines, serviços ligados e conjuntos de dados. Para obter conceitos básicos do Azure Data Factory, veja os artigos seguintes:

* [Introdução ao Data Factory](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Conjuntos de dados](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Horas de início e de fim do pipeline
Um pipeline está ativo apenas entre seus **começar** tempo e **final** tempo. Ele não é executado antes da hora de início ou após a hora de fim. Se o pipeline está em pausa, ele não é executado, independentemente de seu tempo de início e de fim. Para um execução do pipeline, ele deve não ser colocada em pausa. Encontrar estas definições (início, fim, em pausa) na definição de pipeline: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Para obter mais informações essas propriedades, consulte [Criar pipelines de](data-factory-create-pipelines.md) artigo. 


## <a name="specify-schedule-for-an-activity"></a>Especifique o agendamento de uma atividade
Não é o pipeline que é executado. É as atividades no pipeline, que são executadas no contexto geral do pipeline. Pode especificar uma agenda periódica para uma atividade com o **agendador** secção do JSON da atividade. Por exemplo, pode agendar uma atividade executar hora a hora da seguinte forma:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Conforme mostrado no diagrama seguinte, especificando que um agendamento para uma atividade cria uma série de em cascata windows com o início do pipeline e tempos de fim. As janelas em cascata são uma série de intervalos de tempo de contígua, sem sobreposição de tamanho fixo. Essas janelas em cascata lógico para uma atividade são chamadas **janelas de atividade**.

![Exemplo de agendador da atividade](media/data-factory-scheduling-and-execution/scheduler-example.png)

O **agendador** propriedade de uma atividade é opcional. Se especificar esta propriedade, tem de corresponder a cadência que especificou na definição do conjunto de dados de saída para a atividade. Atualmente, é o conjunto de dados de saída que controla a agenda. Por conseguinte, tem de criar um conjunto de dados de saída, mesmo que a atividade não produza dados. 

## <a name="specify-schedule-for-a-dataset"></a>Especifique a agenda para um conjunto de dados
Uma atividade no pipeline do Data Factory pode ter zero ou mais entradas **conjuntos de dados** e produzem um ou mais conjuntos de dados de saída. Para uma atividade, pode especificar a cadência em que os dados de entrada estão disponíveis ou os dados de saída são produzidos utilizando o **disponibilidade** secção nas definições de conjunto de dados. 

**Frequência** no **disponibilidade** secção especifica a unidade de tempo. Os valores permitidos para a frequência são: Minuto, hora, dia, semana e mês. O **intervalo** na seção disponibilidade Especifica um multiplicador para a frequência. Por exemplo: se a frequência está definida para o dia e intervalo está definido como 1 para um conjunto de dados de saída, os dados de saída são produzidos diariamente. Se especificar a frequência como minuto, recomendamos que defina o intervalo para não menos do que 15. 

No exemplo a seguir, os dados de entrada estão disponíveis uma vez por hora e os dados de saída são produzidos de hora a hora (`"frequency": "Hour", "interval": 1`). 

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

Atualmente, **conjunto de dados de saída controla a agenda**. Em outras palavras, a agenda especificada para o conjunto de dados de saída é utilizada para executar uma atividade em tempo de execução. Por conseguinte, tem de criar um conjunto de dados de saída, mesmo que a atividade não produza dados. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. 

Na definição de pipeline seguinte, o **agendador** propriedade é utilizada para especificar a agenda para a atividade. Esta propriedade é opcional. Atualmente, a agenda para a atividade tem de corresponder a agenda especificada para o conjunto de dados de saída.
 
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

Neste exemplo, a atividade é executada uma vez por hora entre as horas de início e de fim do pipeline. Os dados de saída são produzidos de hora a hora para o windows de três horas (8 AM - AM, às 09:-10 9h e às 11H 10 AM -). 

Cada unidade de dados consumidos ou produzido por uma execução de atividade é chamada um **setor de dados**. O diagrama seguinte mostra um exemplo de uma atividade com um conjunto de dados de entrada e um conjunto de dados de saída: 

![Agendador de disponibilidade](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

O diagrama mostra os setores de dados por hora para o conjunto de dados de entrada e saído. O diagrama mostra os três setores de entrada que estão prontos para serem processados. A atividade de às 11H 10 está em curso, produzindo o setor de saída às 11H 10. 

Pode acessar o intervalo de tempo associado com o setor atual no conjunto de dados JSON através da utilização de variáveis: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) e [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Da mesma forma, pode acessar o intervalo de tempo associado a uma janela de atividade com o WindowStart e WindowEnd. O agendamento de uma atividade deve coincidir com a agenda do conjunto de dados de saída para a atividade. Por conseguinte, os valores do SliceStart e SliceEnd são os mesmos valores WindowStart e WindowEnd respectivamente. Para obter mais informações sobre estas variáveis, consulte [funções de Data Factory e variáveis de sistema](data-factory-functions-variables.md#data-factory-system-variables) artigos.  

Pode utilizar estas variáveis para diferentes fins em sua atividade JSON. Por exemplo, pode usá-los para selecionar dados de entrada e saídos conjuntos de dados que representa dados de séries de tempo (por exemplo: 8-me para 9 AM). Este exemplo também usa **WindowStart** e **WindowEnd** selecionar relevantes dados para uma atividade executar em copie-o para um blob com o adequado **folderPath**. O **folderPath** é parametrizada para ter uma pasta separada para cada hora.  

No exemplo anterior, o agendamento especificado para a entrada e conjuntos de dados de saída é a mesmo (hora a hora). Se o conjunto de dados de entrada para a atividade estiver disponível uma frequência diferente, digamos que a cada 15 minutos, a atividade que produz este conjunto de dados de saída ainda é executado uma vez por hora como o conjunto de dados de saída é o que pauta a agenda de atividades. Para obter mais informações, consulte [modelar conjuntos de dados com diferentes frequências](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Disponibilidade do conjunto de dados e as políticas
Viu a utilização de frequência e propriedades de intervalo na secção da definição do conjunto de dados de disponibilidade. Existem algumas outras propriedades que afetam o agendamento e execução de uma atividade. 

### <a name="dataset-availability"></a>Disponibilidade do conjunto de dados 
A tabela seguinte descreve as propriedades que pode utilizar o **disponibilidade** secção:

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| frequency |Especifica a unidade de tempo para produção do setor de conjunto de dados.<br/><br/><b>Suportado frequência</b>: Minuto, hora, dia, semana, mês |Sim |ND |
| interval |Especifica um multiplicador para a frequência<br/><br/>"Intervalo de frequência x" determina a frequência com que o setor é produzido.<br/><br/>Se precisar do conjunto de dados para ser segmentadas numa base horária, defina <b>frequência</b> ao <b>hora</b>, e <b>intervalo</b> para <b>1</b>.<br/><br/><b>Nota</b>: Se especificar a frequência como minuto, recomendamos que defina o intervalo para não menos do que 15 |Sim |ND |
| Estilo |Especifica se o setor deve ser gerado no início/fim do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se a frequência está definida para o mês e estilo está definido como EndOfInterval, o setor é produzido no último dia do mês. Se o estilo é definido como StartOfInterval, o setor é produzido no primeiro dia do mês.<br/><br/>Se a frequência está definida como o dia e estilo está definido como EndOfInterval, o setor é produzido na última hora do dia.<br/><br/>Se a frequência está definida como hora e estilo está definido como EndOfInterval, o setor é produzido no fim da hora. Por exemplo, para um setor durante o período de 1 PM – 2 PM, o setor é produzido em 2 PM. |Não |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo utilizado pelo agendador para computar os limites de setor de conjunto de dados. <br/><br/><b>Nota</b>: Se o AnchorDateTime tem partes de data que são mais granulares do que a frequência, em seguida, as partes mais granulares são ignoradas. <br/><br/>Por exemplo, se o <b>intervalo</b> é <b>por hora</b> (frequência: hora e intervalo: 1) e o <b>AnchorDateTime</b> contém <b>minutos e segundos</b>, o <b>minutos e segundos</b> partes do AnchorDateTime são ignorados. |Não |01/01/0001 |
| offset |O período de tempo através do qual o início e de fim de todos os setores do conjunto de dados são mudou. <br/><br/><b>Nota</b>: Se forem especificados anchorDateTime e desvio, o resultado é a mudança combinada. |Não |ND |

### <a name="offset-example"></a>exemplo de deslocamento
Por predefinição, diariamente (`"frequency": "Day", "interval": 1`) setores de iniciar o fuso horário UTC 12AM (meia-noite). Se pretender que a hora de início para ser o fuso horário UTC 6 da Manhã em vez disso, defina o deslocamento, conforme mostrado no seguinte fragmento: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>exemplo de anchorDateTime
No exemplo a seguir, o conjunto de dados é produzido uma vez a cada 23 horas. O primeiro setor é iniciado no momento especificado pelo anchorDateTime, o que é definida como `2017-04-19T08:00:00` (hora UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Exemplo de desvio/estilo
O seguinte conjunto de dados é um conjunto de dados mensal e é produzido 3rd de cada mês às 8:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Política de conjunto de dados
Um conjunto de dados pode ter uma política de validação definida que especifica como os dados gerados pela execução de um setor podem ser validados antes de ele está pronto para consumo. Nesses casos, após o setor de conclusão da execução, o estado do setor de saída é alterado para **aguardando** com um substatus de **validação**. Depois dos setores são validados, o estado do setor é alterado para **pronto**. Se um setor de dados tiver sido produzido, mas não passou na validação, as execuções de atividades downstream setores que dependem deste setor não são processadas. [Monitorizar e gerir pipelines](data-factory-monitor-manage-pipelines.md) aborda os diversos Estados de setores de dados no Data Factory.

O **política** secção na definição do conjunto de dados define os critérios ou a condição que tem de preencher os setores do conjunto de dados. A tabela seguinte descreve as propriedades que pode utilizar o **política** secção:

| Nome da Política | Descrição | Aplicado a | Necessário | Predefinição |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Valida que os dados num **BLOBs do Azure** cumpre os requisitos de tamanho mínimo (em megabytes). |Blob do Azure |Não |ND |
| minimumRows | Valida que os dados num **base de dados SQL do Azure** ou uma **tabelas do Azure** contém o número mínimo de linhas. |<ul><li>Base de Dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |ND |

#### <a name="examples"></a>Exemplos
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Para obter mais informações sobre estas propriedades e exemplos, consulte [criar conjuntos de dados](data-factory-create-datasets.md) artigo. 

## <a name="activity-policies"></a>Políticas de atividades
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

Para obter mais informações, consulte [Pipelines](data-factory-create-pipelines.md) artigo. 

## <a name="parallel-processing-of-data-slices"></a>Processamento paralelo de setores de dados
Pode definir a data de início para o pipeline no passado. Ao fazê-lo, o Data Factory é automaticamente calcula (back-preenchimentos) todos os setores de dados no passado e começa a processá-los. Por exemplo: se vai criar um pipeline com a data de início de 2017-04-01 e a data atual for 2017-04-10. Se a cadência de conjunto de dados de saída for diariamente, em seguida, começa a fábrica de dados a processar todos os setores de 2017-04-01 para 2017-04-09 imediatamente porque a data de início é no passado. O setor de 2017-04-10 não é processado ainda porque o valor de propriedade de estilo na secção de disponibilidade é EndOfInterval por predefinição. O setor mais antigo é processado pela primeira vez como padrão o valor da executionPriorityOrder é OldestFirst. Para obter uma descrição de propriedade de estilo, consulte [conjunto de dados disponibilidade](#dataset-availability) secção. Para obter uma descrição da seção executionPriorityOrder, consulte a [políticas de atividade](#activity-policies) secção. 

Pode configurar setores de dados back preenchido para serem processados em paralelo, definindo a **simultaneidade** propriedade na **política** secção do JSON de atividade. Esta propriedade determina o número de execuções de atividade parallel, que podem ser efetuadas em setores diferentes. O valor predefinido para a propriedade de simultaneidade é 1. Por conseguinte, um setor é processado cada vez, por predefinição. O valor máximo é 10. Quando um pipeline tem de passar por um grande conjunto de dados disponíveis, ter um valor maior da simultaneidade acelera o processamento de dados. 

## <a name="rerun-a-failed-data-slice"></a>Voltar a executar um setor de dados com falhas
Quando ocorre um erro ao processar um setor de dados, pode descobrir por que o processamento de um setor falhou ao utilizar os painéis do portal do Azure ou um Monitor e gerir aplicações. Ver [monitorizar e gerir pipelines com os painéis do portal do Azure](data-factory-monitor-manage-pipelines.md) ou [aplicação de monitorização e gestão](data-factory-monitor-manage-app.md) para obter detalhes.

Considere o exemplo a seguir, que mostra duas atividades. Activity1 e a atividade 2. Activity1 consome uma fatia da Dataset1 e produz um setor de Dataset2, que é utilizada como entrada pelo Activity2 para produzir um setor do conjunto de dados Final.

![Falha de setor](./media/data-factory-scheduling-and-execution/failed-slice.png)

O diagrama mostra que fora de três setores recentes, Ocorreu uma falha que produz o setor de AM 9-10 para Dataset2. Fábrica de dados controla automaticamente a dependência do conjunto de dados de séries de tempo. Como resultado, não inicia a atividade executar o setor de downstream 9h - 10.

Ferramentas de monitorização e gestão de fábrica de dados permitem-lhe analisar os registos de diagnóstico para o setor com falhas encontrar a causa de raiz para o problema e corrigi-lo facilmente. Depois de ter resolvido o problema, pode começar facilmente a execução de atividade para produzir o setor com falha. Para obter mais informações sobre como executar novamente e compreender as transições de estado para setores de dados, consulte [monitorizar e gerir pipelines com os painéis do portal do Azure](data-factory-monitor-manage-pipelines.md) ou [aplicação de monitorização e gestão](data-factory-monitor-manage-app.md).

Depois de voltar a executar o setor de AM 9-10 para **Dataset2**, Data Factory inicia a execução para o setor de dependentes 9-10 AM no conjunto de dados final.

![Setor de voltar a executar com falhas](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Múltiplas atividades num pipeline
Pode ter mais de uma atividade num pipeline. Se tiver múltiplas atividades num pipeline e a saída de uma atividade não é uma entrada de outra atividade, as atividades podem executar em paralelo, se os setores de dados de entrada para as atividades estão prontos.

Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. As atividades podem ser no mesmo pipeline ou em pipelines de diferentes. A segunda atividade é executado apenas quando primeiro for concluída com êxito.

Por exemplo, considere o seguinte caso em que um pipeline com duas atividades:

1. A1 de atividade que requer a entrada conjunto de dados externo D1 e o conjunto de dados de saída produz D2.
2. A2 de atividade que requer a entrada do conjunto de dados D2 e produz o conjunto de dados D3 de saída.

Neste cenário, as atividades A1 e A2 estão no mesmo pipeline. A atividade é executada A1 quando os dados externos estão disponíveis e a frequência de disponibilidade agendada for atingida. A atividade é executada A2 quando os setores agendados do D2 fiquem disponíveis e a frequência de disponibilidade agendada for atingida. Se existir um erro dos setores no conjunto de dados D2, A2 não é executado para essas fatias até que ele fique disponível.

A vista de diagrama com ambas as atividades no mesmo pipeline ficaria o diagrama seguinte:

![Encadeamento de atividades no mesmo pipeline](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Como mencionado anteriormente, as atividades podem ser nos pipelines diferentes. Neste cenário, a vista de diagrama teria o seguinte aspeto o diagrama seguinte:

![Encadeamento de atividades em dois pipelines](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Consulte a secção sequencialmente no apêndice para obter um exemplo de cópia.

## <a name="model-datasets-with-different-frequencies"></a>Conjuntos de dados do modelo com diferentes frequências
Nos exemplos, as frequências para conjuntos de dados de entrada e saídos e a janela de agendamento de atividade eram os mesmos. Alguns cenários exigem a capacidade de produzir a saída a uma frequência diferente das frequências de uma ou mais entradas. O Data Factory suporta estes cenários de modelagem.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Exemplo 1: Produzir um relatório diário de saída para dados de entrada que está disponíveis a cada hora
Considere um cenário em que tem dados de entrada medição de sensores disponíveis a cada hora no armazenamento de Blobs do Azure. Pretende produzir um relatório diário de agregação com estatísticas como média, máximo e mínimo para o dia com [atividade de ramo de registo de fábrica de dados](data-factory-hive-activity.md).

Eis como pode modelar este cenário com o Data Factory:

**Conjunto de dados de entrada**

Os ficheiros de entrada por hora são largados na pasta para o determinado dia. Disponibilidade de entrada estiver definida como **hora** (frequência: Hora, intervalo: 1).

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

Um ficheiro de saída é criado a todos os dias na pasta do dia. Disponibilidade da saída estiver definida como **dia** (frequência: Dia e intervalo: 1).

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

**Atividade: atividade de ramo de registo num pipeline**

O script de ramo de registo recebe o adequado *DateTime* informações como parâmetros que utilizam o **WindowStart** conforme mostrado no seguinte fragmento. O script de ramo de registo utiliza esta variável para carregar os dados a partir da pasta correta para o dia e executar a agregação para gerar a saída.

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

O diagrama seguinte mostra o cenário de um ponto de vista da dependência de dados.

![Dependência de dados](./media/data-factory-scheduling-and-execution/data-dependency.png)

O setor de saída para todos os dias depende 24 setores por hora de um conjunto de dados de entrada. Fábrica de dados calcula automaticamente estas dependências ao descobrir os dados de entrada setores que se encontra no mesmo período de tempo como o setor de saída produzido. Se qualquer um dos 24 setores de entrada não estiver disponível, o Data Factory aguarda o setor de entrada estar pronto antes de iniciar a execução da atividade diária.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Exemplo 2: Especifique a dependência com expressões e funções de Data Factory
Vamos considerar outro cenário. Suponhamos que tenha uma atividade do hive que processa os dois conjuntos de dados de entrada. Uma delas tiver novos dados diariamente, mas uma delas obtém dados novos todas as semanas. Suponha que quiser fazer uma associação entre as duas entradas e produzir um resultado todos os dias.

A abordagem simple no qual o Data Factory automaticamente descobre o direito de entrada setores para processar alinhando na saída da hora de setor de dados não funciona período.

Tem de especificar que para cada execução de atividade, o Data Factory deverá utilizar o setor de dados da semana passada para o conjunto de dados de entrada semanal. Utilize as funções do Azure Data Factory conforme mostrado no seguinte fragmento para implementar esse comportamento.

**Input1: Azure blob**

A primeira entrada é o blob do Azure a ser atualizado diariamente.

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

**Input2: Azure blob**

Input2 é o blob do Azure que está a ser atualizado semanalmente.

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

**Saída: Azure blob**

Um ficheiro de saída é criado todos os dias na pasta para o dia. Disponibilidade de saída está definida como **dia** (frequência: Dia, intervalo: 1).

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

**Atividade: atividade de ramo de registo num pipeline**

A atividade de ramo de registo leva as duas entradas e produz um setor de saída de todos os dias. Pode especificar o setor de saída de todos os dias a depender do setor de entrada a semana anterior para entrada semanal da seguinte forma.

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

Ver [funções de Data Factory e variáveis de sistema](data-factory-functions-variables.md) para obter uma lista de funções e variáveis do sistema que o Data Factory suporta.

## <a name="appendix"></a>Anexo

### <a name="example-copy-sequentially"></a>Exemplo: Copie sequencialmente
É possível executar várias operações de cópia de um após o outro de forma seqüencial/ordenada. Por exemplo, pode ter duas atividades de cópia num pipeline (CopyActivity1 e CopyActivity2) com os conjuntos de dados de saída de dados de entrada seguinte:   

CopyActivity1

Entrada: Conjunto de dados. Saída: Dataset2.

CopyActivity2

Entrada: Dataset2.  Saída: Dataset3.

CopyActivity2 seria executado apenas se o CopyActivity1 foi executada com êxito e Dataset2 está disponível.

Eis o exemplo de pipeline JSON:

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

Tenha em atenção que, no exemplo, o conjunto de dados de saída da primeira atividade de cópia (Dataset2) é especificado como entrada para a atividade de segundo. Por conseguinte, a segunda atividade é executada apenas quando o conjunto de dados de saída da primeira atividade está pronto.  

No exemplo, CopyActivity2 pode ter uma entrada diferente, como Dataset3, mas especifique Dataset2 como entrada para CopyActivity2, para que a atividade não é executado até que seja CopyActivity1 concluída. Por exemplo:

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

Tenha em atenção que, no exemplo, dois conjuntos de dados de entrada estão especificados para a segunda atividade de cópia. Quando são especificadas várias entradas, apenas o primeiro conjunto de dados entrado é utilizado para copiar dados, mas outros conjuntos de dados são utilizados como as dependências. CopyActivity2 começariam apenas depois das seguintes condições são cumpridas:

* CopyActivity1 foi concluída com êxito e Dataset2 está disponível. Este conjunto de dados não é utilizado quando se copiam dados para Dataset4. Ele atua apenas como uma dependência de agendamento para CopyActivity2.   
* Dataset3 está disponível. Este conjunto de dados representa os dados que são copiados para o destino. 
