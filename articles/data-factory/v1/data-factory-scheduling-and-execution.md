---
title: Agendamento e execução com Data Factory
description: Aprenda aspectos de agendamento e execução do modelo de aplicativo Azure Data Factory.
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
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666138"
---
# <a name="data-factory-scheduling-and-execution"></a>Agendamento e execução de Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte o artigo [execução e gatilhos de pipeline](../concepts-pipeline-execution-triggers.md) .

Este artigo explica os aspetos de agendamento e execução do modelo de aplicação do Azure Data Factory. Este artigo pressupõe que você compreende noções básicas de conceitos de modelo de aplicativo Data Factory, incluindo atividade, pipelines, serviços vinculados e conjuntos de aplicativos. Para obter os conceitos básicos do Azure Data Factory, consulte os seguintes artigos:

* [Introdução ao Data Factory](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Conjuntos de dados](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Horários de início e término do pipeline
Um pipeline está ativo somente entre a hora de **início** e a hora de **término** . Ele não é executado antes da hora de início ou após a hora de término. Se o pipeline estiver em pausa, ele não será executado independentemente de sua hora de início e de término. Para que um pipeline seja executado, ele não deve ser pausado. Você encontra essas configurações (início, fim, em pausa) na definição do pipeline: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Para obter mais informações sobre essas propriedades, consulte o artigo [criar pipelines](data-factory-create-pipelines.md) . 


## <a name="specify-schedule-for-an-activity"></a>Especificar o agendamento de uma atividade
Não é o pipeline que é executado. São as atividades no pipeline que são executadas no contexto geral do pipeline. Você pode especificar um agendamento recorrente para uma atividade usando a seção **Agendador** da atividade JSON. Por exemplo, você pode agendar uma atividade para ser executada a cada hora, da seguinte maneira:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Conforme mostrado no diagrama a seguir, especificar uma agenda para uma atividade cria uma série de janelas em cascata com nas horas de início e término do pipeline. As janelas em cascata são uma série de intervalos de tempo contíguos e sem sobreposição de tamanho fixo. Essas janelas em cascata lógicas para uma atividade são chamadas de **janelas de atividades**.

![Exemplo de Agendador de atividades](media/data-factory-scheduling-and-execution/scheduler-example.png)

A propriedade **Scheduler** de uma atividade é opcional. Se você especificar essa propriedade, ela deverá corresponder à cadência especificada na definição do conjunto de resultados de saída para a atividade. Atualmente, é o conjunto de dados de saída que controla a agenda. Portanto, você deve criar um conjunto de uma saída mesmo que a atividade não produza nenhuma saída. 

## <a name="specify-schedule-for-a-dataset"></a>Especificar o agendamento de um conjunto de uma
Uma atividade em um pipeline Data Factory pode usar zero ou mais **conjuntos** de dados de entrada e produzir um ou mais conjuntos de valores de saída. Para uma atividade, você pode especificar a cadência na qual os dados de entrada estão disponíveis ou os dados de saída são produzidos usando a seção **disponibilidade** nas definições do conjunto de dados. 

**Frequência** na seção **disponibilidade** especifica a unidade de tempo. Os valores permitidos para frequência são: minuto, hora, dia, semana e mês. A propriedade **Interval** na seção de disponibilidade especifica um multiplicador para frequência. Por exemplo: se a frequência for definida como dia e o intervalo for definido como 1 para um conjunto de dados de saída, os dados de saída serão produzidos diariamente. Se você especificar a frequência como minuto, recomendamos que defina o intervalo como não menor que 15. 

No exemplo a seguir, os dados de entrada estão disponíveis por hora e os dados de saída são produzidos por hora (`"frequency": "Hour", "interval": 1`). 

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


**Conjunto de saída**

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

Atualmente, **o conjunto de resultados de saída orienta o agendamento**. Em outras palavras, o agendamento especificado para o conjunto de resultados de saída é usado para executar uma atividade em tempo de execução. Portanto, você deve criar um conjunto de uma saída mesmo que a atividade não produza nenhuma saída. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. 

Na definição de pipeline a seguir, a propriedade **Scheduler** é usada para especificar o agendamento da atividade. Esta propriedade é opcional. Atualmente, o agendamento da atividade deve corresponder ao agendamento especificado para o conjunto de saída.
 
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

Neste exemplo, a atividade é executada por hora entre as horas de início e de término do pipeline. Os dados de saída são produzidos por hora para janelas de três horas (8:00 às 9h, 9h às 12h e às 12h). 

Cada unidade de dados consumida ou produzida por uma execução de atividade é chamada de **fatia de dados**. O diagrama a seguir mostra um exemplo de uma atividade com um conjunto de dados de entrada e um de saída: 

![Agendador de disponibilidade](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

O diagrama mostra as fatias de dados por hora para o conjunto de dado de entrada e saída. O diagrama mostra três fatias de entrada que estão prontas para processamento. A atividade de 10-11 AM está em andamento, produzindo a fatia de saída de 10-11 AM. 

Você pode acessar o intervalo de tempo associado à fatia atual no DataSet JSON usando variáveis: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) e [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Da mesma forma, você pode acessar o intervalo de tempo associado a uma janela de atividade usando o WindowStart e o WindowEnd. O agendamento de uma atividade deve corresponder à agenda do conjunto de resultados de saída para a atividade. Portanto, os valores SliceStart e SliceEnd são os mesmos que os valores WindowStart e WindowEnd, respectivamente. Para obter mais informações sobre essas variáveis, consulte [funções do data Factory e](data-factory-functions-variables.md#data-factory-system-variables) artigos sobre variáveis do sistema.  

Você pode usar essas variáveis para finalidades diferentes em seu JSON de atividade. Por exemplo, você pode usá-los para selecionar dados de conjuntos de dado de entrada e saída representando dados de série temporal (por exemplo: 8:00 às 9h). Este exemplo também usa **WindowStart** e **WindowEnd** para selecionar dados relevantes para uma execução de atividade e copiá-los para um blob com o **FolderPath**apropriado. O **FolderPath** é parametrizado para ter uma pasta separada para cada hora.  

No exemplo anterior, o agendamento especificado para conjuntos de dados de entrada e saída é o mesmo (por hora). Se o conjunto de dados de entrada para a atividade estiver disponível em uma frequência diferente, digamos a cada 15 minutos, a atividade que produz esse conjunto de dados de saída ainda será executada uma vez por hora, pois o conjunto de dados de saída é o que impulsiona o agendamento da atividade. Para saber mais, confira conjuntos de dados de [modelo com frequências diferentes](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Disponibilidade e políticas do conjunto de configurações
Você viu o uso de propriedades de frequência e de intervalo na seção de disponibilidade da definição do conjunto de conjuntos. Há algumas outras propriedades que afetam o agendamento e a execução de uma atividade. 

### <a name="dataset-availability"></a>Disponibilidade do conjunto de 
A tabela a seguir descreve as propriedades que podem ser usadas na seção **disponibilidade** :

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| frequência |Especifica a unidade de tempo para produção da fatia do conjunto de um.<br/><br/><b>Frequência com suporte</b>: minuto, hora, dia, semana, mês |Sim |ND |
| intervalo |Especifica um multiplicador para a frequência<br/><br/>"Intervalo de frequência x" determina a frequência com que a fatia é produzida.<br/><br/>Se você precisar que o conjunto de um seja dividido por hora, defina a <b>frequência</b> como <b>hora</b>e o <b>intervalo</b> como <b>1</b>.<br/><br/><b>Observação</b>: se você especificar a frequência como minuto, recomendamos que defina o intervalo como não menor que 15 |Sim |ND |
| estilo |Especifica se a fatia deve ser produzida no início/fim do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se Frequency for definido como Month e Style for definido como EndOfInterval, a fatia será produzida no último dia do mês. Se o estilo for definido como StartOfInterval, a fatia será produzida no primeiro dia do mês.<br/><br/>Se Frequency for definido como Day e Style for definido como EndOfInterval, a fatia será produzida na última hora do dia.<br/><br/>Se Frequency for definida como hour e Style for definido como EndOfInterval, a fatia será produzida no final da hora. Por exemplo, para uma fatia do período de 1 PM – 2 PM, a fatia é produzida às 2 PM. |Não |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo usada pelo Agendador para computar os limites de fatia do conjunto de cálculo. <br/><br/><b>Observação</b>: se o AnchorDateTime tiver partes de data mais granulares do que a frequência, as partes mais granulares serão ignoradas. <br/><br/>Por exemplo, se o <b>intervalo</b> for por <b>hora</b> (frequência: hora e intervalo: 1) e o <b>AnchorDateTime</b> contiver <b>minutos e segundos</b>, as partes de <b>minutos e segundos</b> do AnchorDateTime serão ignoradas. |Não |01/01/0001 |
| desvio |Período de tempo pelo qual o início e o término de todas as fatias do conjunto de todos são deslocados. <br/><br/><b>Observação</b>: se anchorDateTime e offset forem especificados, o resultado será o deslocamento combinado. |Não |ND |

### <a name="offset-example"></a>exemplo de deslocamento
Por padrão, as fatias diárias (`"frequency": "Day", "interval": 1`) começam às 12 horas de UTC (meia-noite). Se você quiser que a hora de início seja 6, hora UTC, defina o deslocamento conforme mostrado no trecho a seguir: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>exemplo de anchorDateTime
No exemplo a seguir, o DataSet é produzido uma vez a cada 23 horas. A primeira fatia começa no momento especificado pelo anchorDateTime, que é definido como `2017-04-19T08:00:00` (hora UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Exemplo de deslocamento/estilo
O conjunto de um conjunto de um é mensal e é produzido em 3 de cada mês às 8:00 AM (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Política de conjunto de
Um conjunto de dados pode ter uma política de validação definida que especifica como os dados gerados por uma execução de fatia podem ser validados antes de serem prontos para consumo. Nesses casos, depois que a fatia tiver terminado a execução, o status da fatia de saída será alterado para **aguardar** com um substatus de **validação**. Depois que as fatias são validadas, o status da fatia é alterado para **pronto**. Se uma fatia de dados tiver sido produzida, mas não tiver passado a validação, as execuções de atividade para fatias downstream que dependem dessa fatia não serão processadas. [Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md) abrange os vários Estados de fatias de dados em data Factory.

A seção de **política** na definição do conjunto de conjuntos define os critérios ou a condição que as fatias do conjunto de os deve atender. A tabela a seguir descreve as propriedades que você pode usar na seção **política** :

| Nome da Política | Descrição | Aplicado a | Necessário | Predefinição |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Valida que os dados em um **blob do Azure** atendem aos requisitos mínimos de tamanho (em megabytes). |Blob do Azure |Não |ND |
| minimumRows | Valida que os dados em um banco de dados **SQL do Azure** ou uma **tabela do Azure** contêm o número mínimo de linhas. |<ul><li>Base de Dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |ND |

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

Para obter mais informações sobre essas propriedades e exemplos, consulte o artigo [criar conjuntos](data-factory-create-datasets.md) de dados. 

## <a name="activity-policies"></a>Políticas de atividade
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

Para obter mais informações, consulte o artigo [pipelines](data-factory-create-pipelines.md) . 

## <a name="parallel-processing-of-data-slices"></a>Processamento paralelo de fatias de dados
Você pode definir a data de início para o pipeline no passado. Quando você faz isso, Data Factory calcula automaticamente (faz preenchimento de fundo) todas as fatias de dados no passado e começa a processá-las. Por exemplo: se você criar um pipeline com a data de início 2017-04-01 e a data atual for 2017-04-10. Se a cadência do conjunto de dados de saída for diária, Data Factory começará a processar todas as fatias de 2017-04-01 a 2017-04-09 imediatamente porque a data de início está no passado. A fatia de 2017-04-10 não é processada ainda porque o valor da propriedade de estilo na seção de disponibilidade é EndOfInterval por padrão. A fatia mais antiga é processada primeiro, pois o valor padrão de Executionpriorityorder como é OldestFirst. Para obter uma descrição da Propriedade Style, consulte a seção [disponibilidade do conjunto](#dataset-availability) de propriedades. Para obter uma descrição da seção Executionpriorityorder como, consulte a seção [políticas de atividade](#activity-policies) . 

Você pode configurar as fatias de dados preenchidas de volta para serem processadas em paralelo, definindo a propriedade **Concurrency** na seção **Policy** da atividade JSON. Essa propriedade determina o número de execuções de atividade paralelas que podem ocorrer em diferentes fatias. O valor padrão para a propriedade Concurrency é 1. Portanto, uma fatia é processada por vez por padrão. O valor máximo é 10. Quando um pipeline precisa passar por um grande conjunto de dados disponíveis, ter um valor de simultaneidade maior acelera o processamento de dados. 

## <a name="rerun-a-failed-data-slice"></a>Executar novamente uma fatia de dados com falha
Quando ocorre um erro durante o processamento de uma fatia de dados, você pode descobrir por que o processamento de uma fatia falhou usando portal do Azure Blades ou monitorar e gerenciar o aplicativo. Consulte [monitorando e gerenciando pipelines usando portal do Azure Blades](data-factory-monitor-manage-pipelines.md) ou o [aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md) para obter detalhes.

Considere o exemplo a seguir, que mostra duas atividades. Atividade1 e atividade 2. Atividade1 consome uma fatia de dataSet1 e produz uma fatia de Dataset2, que é consumida como uma entrada pelo da atividade2 para produzir uma fatia do conjunto de dados final.

![Fatia com falha](./media/data-factory-scheduling-and-execution/failed-slice.png)

O diagrama mostra que a partir de três fatias recentes, houve uma falha ao produzir a fatia 9-10 AM para Dataset2. Data Factory rastreia automaticamente a dependência para o conjunto de tempo de série temporal. Como resultado, ele não inicia a execução da atividade para a fatia downstream de 9-10.

Data Factory ferramentas de monitoramento e gerenciamento permitem detalhar os logs de diagnóstico da fatia com falha para encontrar facilmente a causa raiz do problema e corrigi-lo. Depois de corrigir o problema, você pode iniciar facilmente a execução da atividade para produzir a fatia com falha. Para obter mais informações sobre como executar novamente e entender as transições de estado para fatias de dados, consulte [monitorando e gerenciando pipelines usando portal do Azure Blades](data-factory-monitor-manage-pipelines.md) ou o [aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md).

Depois de executar novamente a fatia de 9-10 AM para **Dataset2**, data Factory iniciará a execução para a fatia de 9-10 dependente do conjunto de e final.

![Reexecutar fatia com falha](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Múltiplas atividades num pipeline
Pode ter mais de uma atividade num pipeline. Se você tiver várias atividades em um pipeline e a saída de uma atividade não for uma entrada de outra atividade, as atividades poderão ser executadas em paralelo se as fatias de dados de entrada para as atividades estiverem prontas.

Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. As atividades podem estar no mesmo pipeline ou em pipelines diferentes. A segunda atividade é executada somente quando a primeira é concluída com êxito.

Por exemplo, considere o seguinte caso em que um pipeline tem duas atividades:

1. Atividade a1 que requer DataSet de entrada externo D1 e produz o conjunto de dados de saída D2.
2. Atividade a2 que requer entrada do DataSet D2 e produz o conjunto de dados de saída D3.

Nesse cenário, as atividades a1 e a2 estão no mesmo pipeline. A atividade a1 é executada quando os dados externos estão disponíveis e a frequência de disponibilidade agendada é atingida. A atividade a2 é executada quando as fatias agendadas de D2 ficam disponíveis e a frequência de disponibilidade agendada é atingida. Se houver um erro em uma das fatias no DataSet D2, a2 não será executado para essa fatia até que ela se torne disponível.

O modo de exibição de diagrama com ambas as atividades no mesmo pipeline seria semelhante ao seguinte diagrama:

![Encadeando atividades no mesmo pipeline](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Conforme mencionado anteriormente, as atividades podem estar em pipelines diferentes. Nesse cenário, o modo de exibição de diagrama se pareceria com o diagrama a seguir:

![Encadeando atividades em dois pipelines](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Consulte a seção copiar sequencialmente no apêndice para obter um exemplo.

## <a name="model-datasets-with-different-frequencies"></a>Modelar conjuntos de itens com frequências diferentes
Nos exemplos, as frequências dos conjuntos de dados de entrada e saída e da janela de agendamento de atividade eram as mesmas. Alguns cenários exigem a capacidade de produzir saída em uma frequência diferente das frequências de uma ou mais entradas. O Data Factory dá suporte à modelagem desses cenários.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Exemplo 1: produzir um relatório de saída diário para dados de entrada que estão disponíveis a cada hora
Considere um cenário no qual você tenha dados de medição de entrada de sensores disponíveis a cada hora no armazenamento de BLOBs do Azure. Você deseja produzir um relatório agregado diário com estatísticas como média, máximo e mínimo para o dia com [Data Factory atividade de Hive](data-factory-hive-activity.md).

Veja como você pode modelar esse cenário com Data Factory:

**Conjunto de dados de entrada**

Os arquivos de entrada por hora são descartados na pasta para o dia determinado. A disponibilidade para entrada é definida em **hora** (frequência: hora, intervalo: 1).

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
**Conjunto de saída**

Um arquivo de saída é criado todos os dias na pasta do dia. A disponibilidade da saída é definida no **dia** (frequência: dia e intervalo: 1).

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

**Atividade: atividade do hive em um pipeline**

O script do hive recebe as informações de *DateTime* apropriadas como parâmetros que usam a variável **WindowStart** , conforme mostrado no trecho a seguir. O script do hive usa essa variável para carregar os dados da pasta correta para o dia e executar a agregação para gerar a saída.

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

O diagrama a seguir mostra o cenário de um ponto de vista de dependência de dados.

![Dependência de dados](./media/data-factory-scheduling-and-execution/data-dependency.png)

A fatia de saída para todos os dias depende de 24 fatias por hora de um conjunto de dados de entrada. O Data Factory computa essas dependências automaticamente, descobrindo as fatias de dados de entrada que estão no mesmo período de tempo que a fatia de saída a ser produzida. Se nenhuma das 24 fatias de entrada não estiver disponível, Data Factory aguardará que a fatia de entrada esteja pronta antes de iniciar a execução da atividade diária.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Exemplo 2: especificar a dependência com expressões e funções de Data Factory
Vamos considerar outro cenário. Suponha que você tenha uma atividade de Hive que processa dois conjuntos de dados de entrada. Um deles tem novos dados diariamente, mas um deles Obtém novos dados toda semana. Suponha que você quisesse fazer uma junção entre as duas entradas e produzir uma saída todos os dias.

A abordagem simples na qual Data Factory ilustra automaticamente as fatias de entrada à direita para processar, alinhando ao período de tempo da fatia de dados de saída não funciona.

Você deve especificar que para cada execução de atividade, o Data Factory deve usar a fatia de dados da semana passada para o conjunto de dado de entrada semanal. Você usa Azure Data Factory funções conforme mostrado no trecho a seguir para implementar esse comportamento.

**Entrada1: BLOB do Azure**

A primeira entrada é o blob do Azure que está sendo atualizado diariamente.

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

**Entrada2: BLOB do Azure**

Entrada2 é o blob do Azure que está sendo atualizado semanalmente.

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

**Saída: BLOB do Azure**

Um arquivo de saída é criado todos os dias na pasta para o dia. A disponibilidade da saída é definida como **dia** (frequência: dia, intervalo: 1).

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

**Atividade: atividade do hive em um pipeline**

A atividade do hive usa as duas entradas e produz uma fatia de saída todos os dias. Você pode especificar a fatia de saída de cada dia para depender da fatia de entrada da semana anterior para a entrada semanal da seguinte maneira.

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

Consulte [funções de data Factory e variáveis de sistema](data-factory-functions-variables.md) para obter uma lista de funções e variáveis de sistema às quais o data Factory dá suporte.

## <a name="appendix"></a>Anexo

### <a name="example-copy-sequentially"></a>Exemplo: copiar sequencialmente
É possível executar várias operações de cópia uma após a outra, de maneira sequencial/ordenada. Por exemplo, você pode ter duas atividades de cópia em um pipeline (CopyActivity1 e CopyActivity2) com os seguintes conjuntos de dados de saída de dado de entrada:   

CopyActivity1

Entrada: conjunto de dados. Saída: Dataset2.

CopyActivity2

Entrada: Dataset2.  Saída: Dataset3.

CopyActivity2 será executado somente se o CopyActivity1 tiver sido executado com êxito e Dataset2 estiver disponível.

Aqui está o exemplo de JSON de pipeline:

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

Observe que, no exemplo, o conjunto de dados de saída da primeira atividade de cópia (Dataset2) é especificado como entrada para a segunda atividade. Portanto, a segunda atividade é executada somente quando o conjunto de resultados de saída da primeira atividade está pronto.  

No exemplo, CopyActivity2 pode ter uma entrada diferente, como Dataset3, mas você especifica Dataset2 como uma entrada para CopyActivity2, portanto, a atividade não é executada até que CopyActivity1 seja concluído. Por exemplo:

CopyActivity1

Entrada: dataSet1. Saída: Dataset2.

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

Observe que, no exemplo, dois conjuntos de dados de entrada são especificados para a segunda atividade de cópia. Quando várias entradas são especificadas, somente o primeiro conjunto de dados de entrada é usado para copiar os dados, mas outros DataSets são usados como dependências. CopyActivity2 só iniciará depois que as seguintes condições forem atendidas:

* CopyActivity1 foi concluído com êxito e Dataset2 está disponível. Esse conjunto de dados não é usado durante a cópia em Dataset4. Ele atua apenas como uma dependência de agendamento para CopyActivity2.   
* Dataset3 está disponível. Esse DataSet representa os dados que são copiados para o destino. 
