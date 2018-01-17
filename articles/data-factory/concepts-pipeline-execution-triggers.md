---
title: "Execução de pipelines e acionadores no Azure Data Factory | Microsoft Docs"
description: "Este artigo disponibiliza informações sobre como executar um pipeline no Azure Data Factory a pedido ou mediante a criação de um acionador."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2018
ms.author: shlo
ms.openlocfilehash: 88ae5dfbf6246ecf92d6528ad3d9a8e5fb57e4b0
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2018
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Execução de pipelines e acionadores no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-scheduling-and-execution.md)
> * [Versão 2 - Pré-visualização](concepts-pipeline-execution-triggers.md)

Uma **execução de pipeline** é um termo da Versão 2 do Azure Data Factory que define uma instância de uma execução de pipeline. Por exemplo, imagine que tem um pipeline que é executado às 08:00, às 09: 00 e às 10:00. Neste caso, existem três execuções separadas do pipeline (execuções de pipeline). Cada execução de pipeline tem um ID exclusivo, que é um GUID que as define exclusivamente. Normalmente, as execuções de pipeline são instanciadas pela transmissão de argumentos a parâmetros definidos nos pipelines. Há duas formas de executar pipelines: **manualmente** ou através de um **acionador**. Este artigo disponibiliza detalhes sobre ambas as formas de executar pipelines.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço Data Factory, que está disponível em geral (GA), veja [scheduling and execution in Data Factory V1](v1/data-factory-scheduling-and-execution.md) (Agendamento e execuções no Data Factory V1).

## <a name="run-pipeline-on-demand"></a>Executar o pipeline a pedido
Neste método, vai executar o pipeline manualmente. Também é considerada como uma execução a pedido.

Por exemplo, digamos que tem um pipeline chamado **copyPipeline** e quer executá-lo. O pipeline é um pipeline simples com uma única atividade que copia a partir de uma pasta de origem no Armazenamento de Blobs do Azure para uma pasta de destino no mesmo armazenamento. Segue-se a definição de pipeline de exemplo:

```json
{
  "name": "copyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "sourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```
O pipeline utiliza dois parâmetros: sourceBlobContainer e sinkBlobContainer, conforme mostrado na definição de JSON. Transmita os valores para estes parâmetros no runtime.

Para executar o pipeline manualmente, pode utilizar uma das seguintes formas: .NET, PowerShell, REST e Python.

### <a name="rest-api"></a>API REST
Eis um comando REST de exemplo:  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```
Veja [Quickstart: create a data factory using REST API](quickstart-create-data-factory-rest-api.md) (Início rápido: criar uma fábrica de dados com uma API REST) para obter um exemplo completo.

### <a name="powershell"></a>PowerShell
Eis um comando do PowerShell de exemplo:

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

Transmita os parâmetros no corpo no payload de pedidos. No .NET, no Powershell e no Python, transmita os valores num dicionário que é transmitido como argumento para a chamada.

```json
{
  “sourceBlobContainer”: “MySourceFolder”,
  “sinkBlobCountainer”: “MySinkFolder”
}
```

O payload de resposta é um ID exclusivo da execução de pipeline:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```


Veja [Quickstart: create a data factory using PowerShell](quickstart-create-data-factory-powershell.md) (Início rápido: criar uma fábrica de dados com o PowerShell) para obter um exemplo completo.

### <a name="net"></a>.NET
Eis uma chamada de .NET de exemplo:

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Veja [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Início rápido: criar uma fábrica de dados com o .NET) para obter um exemplo completo.

> [!NOTE]
> Pode utilizar a API .NET para invocar pipelines do Data Factory a partir das Funções do Azure, dos seus próprios serviços Web, etc.

## <a name="triggers"></a>Acionadores
Os acionadores proporcionam a segunda forma de executar uma execução de pipeline. Representam uma unidade de processamento que determina quando é que uma execução de pipeline tem de arrancar. Atualmente, o Data Factory suporta dois tipos de acionadores: 1)**Acionador de Agenda**, um acionador que invoca um pipeline de acordo com uma agenda 2)**Acionador de Janela em Cascata**: para acionadores que funcionam num intervalo periódico, mantendo o estado. Atualmente o Data Factory não suporta acionadores baseados em eventos, como, por exemplo, um acionador de execução de pipeline que é acionado quando é recebido um ficheiro.

Os pipelines e os acionadores têm uma relação muitos para muitos. Múltiplos acionadores podem arrancar um pipeline individual ou um acionador único pode arrancar vários pipelines. Na seguinte definição de JSON de um acionador, a propriedade **pipelines** refere-se a uma lista dos pipelines que são acionados pelo acionador específico e aos valores dos parâmetros do pipeline.

### <a name="basic-trigger-definition"></a>Definição básica do acionador:
```json
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {
            …
        },
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                          "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
            }
        ]
    }
```

## <a name="schedule-trigger"></a>Acionador de Agenda
O acionador de agenda executa pipelines de acordo com uma agenda. Suporta opções de calendário periódicas e avançadas (semanal, segunda-feira às 17:00 e quinta-feira às 21:00). É flexível, pois é agnóstico em termos de padrão de conjuntos de dados e não discerne entre dados que são de séries temporais e dados não o são.

Para obter informações mais específicas sobre os Acionadores de Agenda e exemplos, veja [Como: Criar um Acionador de Agenda](how-to-create-schedule-trigger.md)

## <a name="tumbling-window-trigger"></a>Acionador de Janela em Cascata
Os acionadores de janela em cascata são um tipo de acionador que é acionado num intervalo de tempo periódico a partir de uma hora de início especificada, mantendo o estado. As janelas em cascata são uma série de intervalos de tempo com tamanho fixo, não sobrepostos e contínuos.
Para obter informações mais específicas sobre os acionadores de janela em cascata e exemplos, veja [Como: Criar um Acionador de Janela em Cascata](how-to-create-tumbling-window-trigger.md)

### <a name="scheduler-trigger-json-definition"></a>Definição de JSON do acionador Scheduler
Quando cria um acionador Scheduler, pode especificar o agendamento e a periodicidade com o JSON, conforme mostrado no exemplo desta secção. 

Para que o acionador Scheduler arranque uma execução de pipeline, inclua uma referência ao pipeline desse pipeline na definição do acionador. Os pipelines e os acionadores têm uma relação muitos para muitos. Múltiplos acionadores podem arrancar um pipeline individual. Um acionador único pode arrancar vários pipelines.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // how often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": ": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ] 
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  A propriedade **parameters** é uma propriedade obrigatória dentro dos **pipelines**. Mesmo que o seu pipeline não assuma nenhum parâmetro, inclua um json vazio para os parâmetros, pois a propriedade tem de existir.


### <a name="overview-scheduler-trigger-schema"></a>Descrição geral: esquema do acionador Scheduler
A tabela seguinte mostra uma descrição geral de alto nível dos principais elementos relacionados com a periodicidade e o agendamento nos acionadores:

Propriedade JSON |     Descrição
------------- | -------------
startTime | startTime é uma Data-Hora. Para agendas simples, startTime é a primeira ocorrência. Para agendas complexas, o acionador não é iniciado antes de startTime.
endTime | Especifica a data-hora de fim do acionador. O acionador não é executado após esta hora. endTime não pode ser uma hora no passado.
timeZone | Atualmente, só é suportado UTC. 
recurrence | O objeto de periodicidade especifica as regras de periodicidade do acionador. O objeto de periodicidade suporta os elementos frequência, intervalo, endTime, contagem e agenda. Se a periodicidade for definida, a frequência é necessária; os outros elementos de periodicidade são opcionais.
frequência | Representa a unidade da frequência com que o acionador voltar a ocorrer. Os valores suportados são `minute`, `hour`, `day`, `week`, ou `month`.
intervalo | O intervalo é um número inteiro positivo. Indica o intervalo da frequência que determina quantas vezes o acionador é executado. Por exemplo, se o intervalo for 3 e a frequência "semanal", o acionador repete-se de três em três semanas.
agenda | Os acionadores que tenham uma frequência especificada alteram a repetição com base numa agenda de periodicidade. As agendas contêm modificações baseadas em minutos, horas, dias de semana, dias do mês e número da semana.


## <a name="tumbling-window-trigger-vs-schedule-trigger"></a>Acionador de Janela em Cascata vs. Acionador de Agenda
Tendo em conta que o acionador de janela em cascata e o acionador de agenda operam em heartbeats de tempo, o que os diferencia?
Para o acionador de janela em cascata:
* **Cenários de preenchimento**: os acionadores de janela em cascata suportam cenários de preenchimento, tendo a capacidade de agendar execuções para janelas no passado. O Acionador de Agenda só pode ser executado em períodos de tempo do presente para o futuro.
* **Fiabilidade:** os acionadores de janela em cascata irão agendar as execuções de pipeline para todas as janelas a partir de uma data de início sem lacunas, com fiabilidade de 100%.
* **Tentar novamente**: os acionadores de janela em cascata dispõem de capacidade de repetição. As execuções de pipeline com falhas têm uma política de repetição predefinida de 0 ou especificada pelo utilizador como parte da definição do acionador. Irá também voltar a tentar automaticamente nas instâncias quando as execuções falharem devido aos limites de simultaneidade/servidor/limitação, ou seja, isto inclui o código de estado 400 (Erro de Utilizador) 429 (Demasiados pedidos) e 500 (Erro de Servidor interno).
* **Simultaneidade**: os acionadores de janela em cascata permitem que os utilizadores definam explicitamente limites de simultaneidade para o acionador (máximo de 1 a 50 execuções de pipeline em simultâneo)
* **Variáveis Início de Janela e Fim de Janela**: para acionadores de janela em cascata, os utilizadores podem aceder a triggerOutputs().windowStartTime e triggerOutputs().windowEndTime como variáveis do sistema acionador na definição do acionador, que serão as horas de início da janela e fim da janela, respetivamente. Por exemplo, se tiver um acionador de janela em cascata em execução de hora a hora, para a janela 1:00-2:00, o triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z e triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z.
* **Pipeline para Ativar Relação**: os acionadores de agenda têm uma relação de n:m com pipelines. Um acionador de agenda pode acionar vários pipelines. Os acionadores de Janela em Cascata têm uma relação de 1:1 com os pipelines. Um acionador de janela em cascata só pode acionar um pipeline.

### <a name="schedule-trigger-example"></a>Exemplo de acionador Schedule

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-01T09:00:00-08:00",
                "endTime": "2017-11-02T22:00:00-08:00"
            }
        },
        "pipelines": [{
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToBlobPipeline"
                },
                "parameters": {}
            },
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToAzureSQLPipeline"
                },
                "parameters": {}
            }
        ]
    }
}
```

### <a name="overview-scheduler-trigger-schema-defaults-limits-and-examples"></a>Descrição geral: predefinições, limites e exemplos de esquema do acionador Scheduler

Nome JSON | Tipo de valor | Necessário? | Valor predefinido | Valores válidos | Exemplo
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | Cadeia (de carateres) | Sim | Nenhum | Datas-Horas ISO 8601 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | Objeto | Sim | Nenhum | Objeto de periodicidade | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
intervalo | Número | Sim | Nenhum | 1 a 1000. | ```"interval":10```
endTime | Cadeia (de carateres) | Sim | Nenhum | Valor de data-hora que representa uma hora no futuro | `"endTime" : "2013-02-09T09:30:00-08:00"`
agenda | Objeto | Não | Nenhum | Objeto da agenda | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>Descrição aprofundada: startTime
A tabela seguinte mostra como é que startTime controla a forma como os acionadores são executados:

valor de startTime | Periodicidade sem agenda | Periodicidade com agenda
--------------- | --------------------------- | ------------------------
Hora de início no passado | Calcula a primeira hora de execução no futuro após a hora de início e executa nessa hora.<p>Executa as execuções subsequentes com base na determinação da última hora de execução.</p><p>Veja o exemplo a seguir à tabela.</p> | O acionador _não é acionado antes_ da hora de início especificada. A primeira ocorrência é baseada na agenda calculada a partir da hora de início. <p>Executa as execuções subsequentes com base na agenda de periodicidade.</p>
Hora de início no futuro ou no presente | Executa uma vez na hora de início especificada. <p>Executa as execuções subsequentes com base na determinação da última hora de execução.</p> | O acionador _não é acionado antes_ da hora de início especificada. A primeira ocorrência é baseada na agenda calculada a partir da hora de início.<p>Executa as execuções subsequentes com base na agenda de periodicidade.</p>

Vamos ver um exemplo do que acontece quando startTime está no passado, com periodicidade, mas sem agenda. Partamos do princípio de que a hora atual é `2017-04-08 13:00`, startTime é `2017-04-07 14:00` e a periodicidade é de dois em dois dias (definida com a frequência: dia e intervalo: 2). Repare que startTime está no passado e que ocorre antes da hora atual.

Nestas condições, a primeira execução é em `2017-04-09 at 14:00`. O motor do Scheduler calcula as ocorrências de execução a partir da hora de início. Quaisquer instâncias no passado são eliminadas. O motor utiliza a instância seguinte que ocorre no futuro. Por isso, neste caso, startTime é `2017-04-07 at 2:00pm`, pelo que a instância seguinte é dois dias a contar dessa hora, que é `2017-04-09 at 2:00pm`.

A primeira hora de execução é a mesma, mesmo que startTime seja `2017-04-05 14:00` ou `2017-04-01 14:00`. Após a primeira execução, as execuções subsequentes são calculadas com a agenda. Consequentemente, ocorrem em `2017-04-11 at 2:00pm`, depois em `2017-04-13 at 2:00pm` e, por fim, em `2017-04-15 at 2:00pm`.

Finalmente, se um acionador tiver uma agenda e se não forem definidas as horas e/ou os minutos na mesma, estas são predefinidas para as horas e/ou minutos da primeira execução, respetivamente.

### <a name="deep-dive-schedule"></a>Descrição aprofundada: agenda
Por um lado, as agendas podem limitar o número de execuções de acionadores. Por exemplo, se um acionador com a frequência “mês” tiver uma agenda que só é executada no dia 31, o acionador só é executado nos meses que têm 31 dias.

Por outro lado, as agendas também podem expandir o número de execuções de acionadores. Por exemplo, se um acionador com a frequência “mês” tiver uma agenda que é executada nos dias 1 e 2, o acionador é executado no primeiro e no segundo dias do mês em vez de uma vez por mês.

Se forem especificados vários elementos de agenda, a ordem de avaliação é do maior para o mais pequeno – número da semana, dia do mês, dia da semana, hora e minuto.

A tabela seguinte descreve os elementos de agenda detalhadamente:


Nome JSON | Descrição | Valores válidos
--------- | ----------- | ------------
minutes | Minutos da hora em que o acionador é executado. | <ul><li>Matriz de números inteiros</li></ul>
hours | Horas do dia em que o acionador é executado. | <ul><li>Matriz de números inteiros</li></ul>
weekDays | Dias da semana em que o acionador é executado. Só podem ser especificados com uma frequência semanal. | <ul><li>Matriz de qualquer um dos valores abaixo (tamanho da matriz máximo 7)<ul><li>Segunda-feira</li><li>Terça-feira</li><li>Quarta-feira</li><li>Quinta-feira</li><li>Sexta-feira</li><li>Sábado</li><li>Domingo</li></ul></li></p>Não sensíveis a maiúsculas e minúsculas</p>
monthlyOccurrences | Determina em que dias do mês o acionador é executado. Só podem ser especificadas com uma frequência semanal. | Matriz de objetos de monthlyOccurence: `{ "day": day,  "occurrence": occurence }`. <p> O dia é o dia da semana em que o acionador é executado; por exemplo, `{Sunday}` é cada domingo do mês. Necessário.<p>A ocorrência é a ocorrência do dia durante o mês; por exemplo, `{Sunday, -1}` é o último domingo do mês. Opcional.
monthDays | Dia do mês em que o acionador é executado. Só podem ser especificadas com uma frequência semanal. | <ul><li>Uma matriz de valores abaixo</li><ul><li>Qualquer valor <= -1 e >= -31</li><li>Qualquer valor >= 1 e <= 31</li></ul></ul> |


## <a name="examples-recurrence-schedules"></a>Exemplos: agendas de periodicidade
Esta secção mostra exemplos de agendas de periodicidade, que se focam no objeto da agenda e nos subelementos.

As agendas de exemplo partem do princípio de que o intervalo está definido como 1. Além disso, também presumem a frequência certa de acordo com o que está definido na agenda. Por exemplo, não pode utilizar a frequência “dia” e ter a modificação "monthDays" na agenda. Estas restrições são mencionadas na tabela da secção anterior. 

Exemplo | Descrição
------- | -----------
`{"hours":[5]}` | É executada às 05:00 Todos os Dias
`{"minutes":[15], "hours":[5]}` | É executada às 05:15 Todos os Dias
`{"minutes":[15], "hours":[5,17]}` | É executada às 05:15 e às 17:15 Todos os Dias
`{"minutes":[15,45], "hours":[5,17]}` | É executada às 05:15, às 05:45, às 17:15 e às 17:45 Todos os Dias
`{"minutes":[0,15,30,45]}` | É executada A Cada 15 Minutos
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | É executada de Hora em Hora. Este acionador é executado de hora em hora. O minuto é controlado por startTime, se for especificado; se não for especificado, é controlado pela hora de criação. Por exemplo, se a hora de início ou a hora de criação (a que se aplicar) for 12:25, o acionador é executado às 00:25, às 01:25, às 02:25,..., às 23:25. A agenda é equivalente a ter um acionador com a frequência de "horas", um intervalo de 1 e nenhuma agenda. A diferença é que esta agenda pode ser utilizada com diferentes frequências e intervalos para criar outros acionadores também. Por exemplo, se a frequência fosse "mês", a agenda seria executada uma só vez por mês, em vez de todos os dias se fosse "dia".
`{"minutes":[0]}` | É executada de hora em hora à hora certa. Este acionador também é executado de hora em hora, mas à hora certa (por exemplo, 00:00, 01:00, 02:00, etc.). Esta definição é equivalente a um acionador com a frequência "horas", startTime com zero minutos e nenhuma agenda se a frequência fosse "dia"; contudo se a frequência fosse “semana” ou “mês”, a agenda só seria executada uma vez por semana ou um dia por mês, respetivamente.
`{"minutes":[15]}` | É executada 15 minutos após cada hora. É executada de hora em hora, com início às 00:15, 01:15, 02:15, etc., e fim às 22:15 e 23:15.
`{"hours":[17], "weekDays":["saturday"]}` | É executada às 17:00 aos sábados todas as semanas
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | É executada às 17:00 à segunda-feira, quarta-feira e sexta-feira todas as semanas
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | É executada às 17:15 e às 17:45 à segunda-feira, quarta-feira e sexta-feira todas as semanas
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | É executada a cada 15 minutos nos dias de semana
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | É executada a cada 15 minutos nos dias de semana entre as 09:00 e as 16:45
`{"weekDays":["tuesday", "thursday"]}` | É executada às terças-feiras e quintas-feiras à hora de início especificada
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | É executada às 06:00 do dia 28 de cada mês (presumindo que a frequência é “mês”).
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | É executada às 06:00 no último dia do mês Se quiser executar um acionador no último dia de um mês, utilize -1 em vez de dia 28, 28, 30 ou 31
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | É executada às 06:00 no primeiro e último dias de todos os meses
`{monthDays":[1,14]}` | É executada no primeiro e no 14.º dia de todos os meses à hora de início especificada
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | É executada na primeira sexta-feira de cada mês às 05:00
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | É executada na primeira sexta-feira de cada mês à hora de início especificada
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | É executada na terceira sexta-feira do fim do mês, de cada mês, à hora de início
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | É executada na primeira e na última sexta-feira de todos os meses às 05:15
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | É executada na primeira e na última sexta-feira de cada mês à hora de início especificada
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | É executada na quinta sexta-feira de cada mês à hora especificada. Se o mês não tiver cinco sextas-feiras, o pipeline não é executado, pois está agendado para ser executado apenas à quinta sexta-feira.  Se quiser executar o acionador na última sexta-feira do mês, considere utilizar -1 em vez de 5 na ocorrência.
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | É executada a cada 15 minutos na última sexta-feira do mês
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | É executada às 05:15, às 05:45 AM, às 17:15 e às 17:45 na terceira quarta-feira de cada mês




## <a name="next-steps"></a>Passos seguintes
Veja os tutoriais seguintes:

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Início rápido: criar uma fábrica de dados com .NET)
- [Como: Criar um Acionador de Agenda](how-to-create-schedule-trigger.md)
- [Como: Criar um Acionador de Janela em Cascata](how-to-create-tumbling-window-trigger.md)
