---
title: Criar os acionadores de janela em cascata na fábrica de dados do Azure | Documentos da Microsoft
description: Saiba como criar um acionador no Azure Data Factory que executa um pipeline numa janela em cascata.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: shlo
ms.openlocfilehash: 6fbdee71ab1123c258a5191a78e38f51eb41cbab
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433234"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Criar um acionador que é executado um pipeline numa janela em cascata
Este artigo fornece passos para criar, iniciar e monitorizar um acionador de janela em cascata. Para obter informações gerais sobre os acionadores e os tipos suportados, consulte [execuções de pipelines e acionadores](concepts-pipeline-execution-triggers.md).

Os acionadores de janela em cascata são um tipo de acionador que é acionado num intervalo de tempo periódico a partir de uma hora de início especificada, mantendo o estado. As janelas em cascata são uma série de intervalos de tempo com tamanho fixo, não sobrepostos e contínuos. Um acionador de janela em cascata tem uma relação um para um com um pipeline e só pode referenciar um único pipeline.

## <a name="data-factory-ui"></a>IU do Data Factory

Para criar um acionador de janela em cascata no portal do Azure, selecione **acionador > janela em cascata > seguinte**e, em seguida, configure as propriedades que definem a janela em cascata.

![Criar um acionador de janela em cascata no portal do Azure](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Em cascata propriedades do tipo de Acionador de janela
Uma janela em cascata tem as seguintes propriedades de tipo de Acionador:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
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

A tabela seguinte fornece uma visão geral dos principais elementos JSON que estão relacionados com a periodicidade e o agendamento de um acionador de janela em cascata:

| Elemento JSON | Descrição | Type | Valores permitidos | Necessário |
|:--- |:--- |:--- |:--- |:--- |
| **tipo** | O tipo de Acionador. O tipo é o valor fixo "TumblingWindowTrigger." | String | "TumblingWindowTrigger" | Sim |
| **runtimeState** | O estado atual do acionador de tempo de execução.<br/>**Nota**: Este elemento está \<só de leitura >. | String | "Iniciado", "parada" "Desativada" | Sim |
| **frequency** | Uma cadeia de caracteres que representa a unidade de frequência (minutos ou horas) com que o acionador voltar a ocorrer. Se o **startTime** valores de data são mais granulares do que o **frequência** valor, o **startTime** datas são consideradas quando os limites da janela são computados. Por exemplo, se o **frequência** valor é de hora a hora e o **startTime** valor é 2017-09-01T10:10:10Z, a primeira janela está (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | String | "minuto", "hour"  | Sim |
| **interval** | Um valor inteiro positivo que indica o intervalo do valor **frequency**, que determina o número de vezes que o acionador é executado. Por exemplo, se o **intervalo** é 3 e o **frequência** é a "hour", o acionador voltar a ocorrer em três horas. | Número inteiro | Um número inteiro positivo. | Sim |
| **startTime**| A primeira ocorrência, que pode ser no passado. O primeiro intervalo de Acionador é (**startTime**, **startTime** + **intervalo**). | DateTime | Um valor de DateTime. | Sim |
| **endTime**| A última ocorrência, que pode ser no passado. | DateTime | Um valor de DateTime. | Sim |
| **delay** | A quantidade de tempo para atrasar o início do processamento de dados para a janela. A execução do pipeline é iniciado após o tempo de execução esperado mais a quantidade de **atraso**. O **atraso** define o tempo que o acionador aguarda posterior à hora de conclusão antes de acionar uma execução de novo. O **atraso** não altera a janela **startTime**. Por exemplo, um **atraso** valor de 00:00 10: implica um atraso de 10 minutos. | Timespan<br/>(hh:mm:ss)  | Um valor de intervalo de tempo em que a predefinição é 00:00:00. | Não |
| **maxConcurrency** | O número de execuções de Acionador simultâneas que são acionadas para o windows que estão prontos. Por exemplo, para preencher novamente hora a hora é executado para resultados de ontem no 24 windows. Se **maxConcurrency** = 10, o acionador de eventos são disparados apenas para os primeiros 10 windows (01 00:00:00 - 09:00-10:00). Depois de concluir as primeiras 10 execuções de pipeline acionadas, são acionadas execuções do acionador para os 10 em seguida windows (10:00-UTC+11:00-19: - 20 00:00). Continuar com este exemplo de **maxConcurrency** = 10, se existirem 10 windows estiver prontos, há 10 execuções de total pipeline. Se existir apenas 1 janela pronto, só existe 1 execução de pipeline. | Número inteiro | Um número inteiro entre 1 e 50. | Sim |
| **retryPolicy: Contagem** | O número de tentativas antes da execução de pipeline está marcado como "Falhado".  | Número inteiro | Um número inteiro, em que a predefinição é 0 (não existem repetições). | Não |
| **retryPolicy: intervalInSeconds** | O atraso entre tentativas de repetição especificado em segundos. | Número inteiro | O número de segundos, em que a predefinição é 30. | Não |

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart e WindowEnd variáveis do sistema

Pode utilizar o **WindowStart** e **WindowEnd** variáveis do sistema do acionador de janela em cascata de seu **pipeline** definição (ou seja, para a parte de uma consulta). Passar as variáveis do sistema como parâmetros para o pipeline a **acionador** definição. O exemplo seguinte mostra como passar essas variáveis como parâmetros:

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

Para utilizar o **WindowStart** e **WindowEnd** valores das variáveis de sistema na definição do pipeline, utilize os parâmetros "MyWindowStart" e "MyWindowEnd", em conformidade.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Ordem de execução do windows num cenário de preenchimento
Quando existem várias janelas para execução (especialmente num cenário de preenchimento), a ordem de execução para o windows é determinística, de intervalos mais antigos ao mais recente. Atualmente, não é possível modificar este comportamento.

### <a name="existing-triggerresource-elements"></a>Elementos de TriggerResource existentes
Os pontos seguintes aplicam-se existente já **TriggerResource** elementos:

* Se o valor para o **frequência** elemento (ou tamanho da janela) das alterações de Acionador, o estado do windows que já estão a ser processados é *não* repor. O acionador continua a ser disparado para os windows a partir da última janela que ele executado utilizando o novo tamanho de janela.
* Se o valor para o **endTime** é o elemento dos acionador alterações (adicionado ou atualizado), o estado do windows que já são processados *não* repor. O acionador honra a nova **endTime** valor. Se o novo **endTime** valor é antes do windows que já forem executadas, o pára de Acionador. Caso contrário, o acionador para quando o novo **endTime** valor for encontrado.

## <a name="sample-for-azure-powershell"></a>Exemplo para o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Esta secção mostra-lhe como utilizar o Azure PowerShell para criar, iniciar e monitorizar um acionador.

1. Crie um ficheiro JSON com o nome **MyTrigger.json** na pasta C:\ADFv2QuickStartPSH\ com o seguinte conteúdo:

    > [!IMPORTANT]
    > Antes de guardar o ficheiro JSON, defina o valor do **startTime** elemento para a hora UTC atual. Defina o valor do **endTime** elemento para uma hora posterior à hora UTC atual.

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

2. Criar um acionador com o **Set-AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Confirme que o estado do acionador está **parado** utilizando o **Get-AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Iniciar o acionador com o **Start-AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Confirme que o estado do acionador está **iniciado** utilizando o **Get-AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. GET, o acionador é executado no Azure PowerShell, utilizando o **Get-AzDataFactoryV2TriggerRun** cmdlet. Para obter informações sobre as execuções do acionador, execute o seguinte comando periodicamente. Atualização do **TriggerRunStartedAfter** e **TriggerRunStartedBefore** valores de acordo com os valores existentes na sua definição do acionador:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Para monitorizar o pipeline e execuções de Acionador é executado no portal do Azure, consulte [monitorizar execuções de pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Passos Seguintes
Para obter informações detalhadas sobre os acionadores, veja [execuções de pipelines e acionadores](concepts-pipeline-execution-triggers.md#triggers).
