---
title: Criar gatilhos de janela saqueados na Fábrica de Dados De Azure
description: Aprenda a criar um gatilho na Azure Data Factory que executa um oleoduto numa janela caindo.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 97c8f8a5bb2111264e9459a7d2128c1ab7c2503d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414420"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Create a trigger that runs a pipeline on a tumbling window(Criar um acionador que execute um pipeline numa janela em cascata)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo fornece passos para criar, iniciar e monitorizar um gatilho da janela caindo. Para obter informações gerais sobre os gatilhos e os tipos suportados, consulte a [execução do Gasoduto e](concepts-pipeline-execution-triggers.md)os gatilhos .

Os acionadores de janela em cascata são um tipo de acionador que é acionado num intervalo de tempo periódico a partir de uma hora de início especificada, mantendo o estado. As janelas em cascata são uma série de intervalos de tempo com tamanho fixo, não sobrepostos e contínuos. Um gatilho de janela caindo tem uma relação um-para-um com um oleoduto e só pode referenciar um oleoduto singular.

## <a name="data-factory-ui"></a>IU do Data Factory

1. Para criar um gatilho de janela caindo na UI da Fábrica de Dados, selecione o separador **Triggers** e, em seguida, selecione **New**. 
1. Depois do painel de configuração do gatilho abrir, selecione **Janela tumbling**, e, em seguida, defina as propriedades do gatilho da janela caindo. 
1. Quando tiver terminado, selecione **Guardar**.

![Crie um gatilho de janela caindo no portal Azure](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Propriedades do tipo de gatilho da janela caindo

Uma janela caindo tem as seguintes propriedades do tipo de gatilho:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

A tabela seguinte fornece uma visão geral de alto nível dos principais elementos JSON que estão relacionados com a recorrência e agendamento de um gatilho de janela caindo:

| Elemento JSON | Descrição | Tipo | Valores permitidos | Necessário |
|:--- |:--- |:--- |:--- |:--- |
| **tipo** | O tipo do gatilho. O tipo é o valor fixo "TumblingWindowTrigger". | String | "TumblingWindowTrigger" | Sim |
| **tempo de execuçãoEstado** | O estado atual do tempo de execução do gatilho.<br/>**Nota:** Este \<elemento é lidoApenas>. | String | "Iniciado", "Parou", "Deficiente" | Sim |
| **frequência** | Uma cadeia que representa a unidade de frequência (minutos ou horas) na qual o gatilho se repete. Se os valores da data **de início** da data de tempo forem mais granulares do que o valor de **frequência,** as datas de **início** são consideradas quando os limites da janela são calculados. Por exemplo, se o valor de **frequência** for horalgéo e **o** valor inicial é 2017-09-01T10:10:10Z, a primeira janela é (2017-09-01T10:10:10Z, 2017-09-01T11:10:10:10Z). | String | "minuto", "hora"  | Sim |
| **intervalo** | Um valor inteiro positivo que indica o intervalo do valor **frequency**, que determina o número de vezes que o acionador é executado. Por exemplo, se o **intervalo** for de 3 e a **frequência** for "hora", o gatilho repete-se a cada 3 horas. <br/>**Nota:** O intervalo mínimo da janela é de 5 minutos. | Número inteiro | Um inteiro positivo. | Sim |
| **startTime**| A primeira ocorrência, que pode ser no passado. O primeiro intervalo do gatilho é (**início,****intervalo** **de inícioTime** + ). | DateTime | Um valor datetime. | Sim |
| **endTime**| A última ocorrência, que pode ser no passado. | DateTime | Um valor datetime. | Sim |
| **atraso** | O tempo para atrasar o início do processamento de dados para a janela. A execução do gasoduto é iniciada após o tempo de execução esperado mais a quantidade de **atraso**. O **atraso** define quanto tempo o gatilho espera para além do devido tempo antes de desencadear uma nova execução. O **atraso** não altera o início da **janelaTempo.** Por exemplo, um valor de **atraso** de 00:10:00 implica um atraso de 10 minutos. | Timespan<br/>(hh:mm:ss)  | Um valor de tempo onde o padrão é 00:00:00. | Não |
| **maxConcurrency** | O número de disparos simultâneos que são disparados para janelas prontas. Por exemplo, para voltar a encher de hora a hora para ontem resulta em 24 janelas. Se **a moeda máxima** = 10, os eventos de disparo são disparados apenas para as primeiras 10 janelas (00:00-01:00 - 09:00-10:00). Depois de concluídas as primeiras 10 corridas de gasodutos acionados, são disparados disparos para as próximas 10 janelas (10:00-11:00 - 19:00-20:00). Continuando com este exemplo de **maxConcurrency** = 10, se houver 10 janelas prontas, existem 10 execuções totais de gasodutos. Se só houver uma janela pronta, só há uma corrida de oleoduto. | Número inteiro | Um inteiro entre 1 e 50. | Sim |
| **retryPolítica: Contar** | O número de repetições antes da execução do gasoduto é marcado como "Falhado".  | Número inteiro | Um inteiro, onde o padrão é 0 (sem repetições). | Não |
| **retryPolicy: intervaloInSeconds** | O atraso entre as tentativas de retry especificadas em segundos. | Número inteiro | O número de segundos, onde o padrão é de 30. | Não |
| **depende: tipo** | O tipo de TumblingWindowTriggerReference. Necessário se for definida uma dependência. | String |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | Não |
| **depende: tamanho** | Do tamanho da janela de tropeçar da dependência. | Timespan<br/>(hh:mm:ss)  | Um valor de tempo positivo onde o padrão é o tamanho da janela do gatilho da criança  | Não |
| **depende: compensado** | A compensação do gatilho da dependência. | Timespan<br/>(hh:mm:ss) |  Um valor de tempo que deve ser negativo numa auto-dependência. Se não for especificado qualquer valor, a janela é a mesma que o próprio gatilho. | Auto-Dependência: Sim<br/>Outros: Não  |

### <a name="windowstart-and-windowend-system-variables"></a>Variáveis do sistema WindowStart e WindowEnd

Pode utilizar as variáveis do sistema **WindowStart** e **WindowEnd** do gatilho da janela caindo na definição do **gasoduto** (isto é, para parte de uma consulta). Passe as variáveis do sistema como parâmetros para o seu oleoduto na definição do **gatilho.** O exemplo que se segue mostra como passar estas variáveis como parâmetros:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

Para utilizar os valores variáveis do sistema **WindowStart** e **WindowEnd** na definição do pipeline, utilize os seus parâmetros "MyWindowStart" e "MyWindowEnd", em conformidade.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Ordem de execução de janelas em cenário de enchimento
Quando há várias janelas para a execução (especialmente num cenário de enchimento), a ordem de execução para janelas é determinística, desde intervalos mais antigos a mais recentes. Atualmente, não é possível modificar este comportamento.

### <a name="existing-triggerresource-elements"></a>Elementos triggerResource existentes
Os seguintes pontos aplicam-se aos elementos **triggerResource** existentes:

* Se o valor para o elemento de **frequência** (ou tamanho da janela) do gatilho mudar, o estado das janelas que já estão processadas *não* é reposto. O gatilho continua a disparar para as janelas da última janela que executou usando o novo tamanho da janela.
* Se o valor para o elemento **finalTime** do gatilho mudar (adicionado ou atualizado), o estado das janelas que já estão processados *não* é reposto. O gatilho honra o novo valor **final do tempo.** Se o novo valor **final O tempo** for antes das janelas que já estão executadas, o gatilho para. Caso contrário, o gatilho para quando o novo valor final do **tempo** é encontrado.

### <a name="tumbling-window-trigger-dependency"></a>Dependência do gatilho da janela caindo

Se quiser certificar-se de que um gatilho da janela caindo só é executado após a execução bem sucedida de outro gatilho de janela caindo na fábrica de dados, [crie uma dependência](tumbling-window-trigger-dependency.md)do gatilho da janela. 

## <a name="sample-for-azure-powershell"></a>Amostra para Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Esta secção mostra-lhe como usar o Azure PowerShell para criar, iniciar e monitorizar um gatilho.

1. Crie um ficheiro JSON chamado **MyTrigger.json** na pasta C:\ADFv2QuickStartPSH\ com o seguinte conteúdo:

    > [!IMPORTANT]
    > Antes de guardar o ficheiro JSON, detete o valor do elemento **startTime** para o tempo atual utc. Detete o valor do elemento **endTime** para uma hora depois do tempo atual utc.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. Criar um gatilho utilizando o **cmdlet Set-AzDataFactoryV2Trigger:**

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Confirme que o estado do gatilho é **interrompido** utilizando o cmdlet **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Inicie o gatilho utilizando o cmdlet **Start-AzDataFactoryV2Trigger:**

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Confirme que o estado do gatilho é **iniciado** utilizando o cmdlet **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Obtenha o gatilho em Azure PowerShell utilizando o cmdlet **Get-AzDataFactoryV2TriggerRun.** Para obter informações sobre as correções do gatilho, execute periodicamente o seguinte comando. Atualize os valores **TriggerRunStartedAfter** e **TriggerRunStartedBefore** para corresponder aos valores na definição do gatilho:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Para monitorizar as condutas de disparo e o gasoduto corre no portal Azure, consulte o [gasoduto Monitor .](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações detalhadas sobre os gatilhos, consulte a [execução do Gasoduto e os gatilhos](concepts-pipeline-execution-triggers.md#trigger-execution).
* [Criar uma dependência de acionamento de janela em cascata](tumbling-window-trigger-dependency.md)
