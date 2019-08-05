---
title: Criar gatilhos de janela em cascata no Azure Data Factory | Microsoft Docs
description: Saiba como criar um gatilho no Azure Data Factory que executa um pipeline em uma janela do em cascata.
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
ms.openlocfilehash: 0f78136edf58e76ed478bef9c255791d256c34a5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678485"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Criar um gatilho que executa um pipeline em uma janela do em cascata
Este artigo fornece etapas para criar, iniciar e monitorar um gatilho de janela em cascata. Para obter informações gerais sobre gatilhos e os tipos com suporte, consulte [execução de pipeline e gatilhos](concepts-pipeline-execution-triggers.md).

Os acionadores de janela em cascata são um tipo de acionador que é acionado num intervalo de tempo periódico a partir de uma hora de início especificada, mantendo o estado. As janelas em cascata são uma série de intervalos de tempo com tamanho fixo, não sobrepostos e contínuos. Um gatilho de janela em cascata tem uma relação um-para-um com um pipeline e só pode fazer referência a um pipeline singular.

## <a name="data-factory-ui"></a>IU do Data Factory

Para criar um gatilho de janela do em cascata no portal do Azure, selecione **gatilho > janela do em cascata > avançar**e, em seguida, configure as propriedades que definem a janela em cascata.

![Criar um gatilho de janela em cascata no portal do Azure](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Propriedades do tipo de gatilho da janela em cascata
Uma janela em cascata tem as seguintes propriedades de tipo de gatilho:

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
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
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

A tabela a seguir fornece uma visão geral de alto nível dos principais elementos JSON relacionados à recorrência e ao agendamento de um gatilho de janela em cascata:

| Elemento JSON | Descrição | Type | Valores permitidos | Obrigatória |
|:--- |:--- |:--- |:--- |:--- |
| **type** | O tipo do gatilho. O tipo é o valor fixo "TumblingWindowTrigger". | Cadeia | "TumblingWindowTrigger" | Sim |
| **runtimeState** | O estado atual do tempo de execução do gatilho.<br/>**Nota**: Esse elemento é \<ReadOnly >. | Cadeia | "Iniciado," "interrompido," "desabilitado" | Sim |
| **frequency** | Uma cadeia de caracteres que representa a unidade de frequência (minutos ou horas) na qual o gatilho se repete. Se os valores de data de iníciotime forem mais granulares do que o valor de **frequência** , as datas de **iníciotime** serão consideradas quando os limites da janela forem computados. Por exemplo, se o valor de **Frequency** for por hora e o valor de **StartTime** for 2017-09-01T10:10:10z, a primeira janela será (2017-09-01T10:10:10z, 2017-09-01T11:10:10z). | Cadeia | "minuto", "hora"  | Sim |
| **interval** | Um valor inteiro positivo que indica o intervalo do valor **frequency**, que determina o número de vezes que o acionador é executado. Por exemplo, se o **intervalo** for 3 e a **frequência** for "hora", o gatilho se repetirá a cada 3 horas. | Número inteiro | Um inteiro positivo. | Sim |
| **startTime**| A primeira ocorrência, que pode estar no passado. O primeiro intervalo de gatilho é(StartTime + , StartTime**Interval**). | DateTime | Um valor DateTime. | Sim |
| **endTime**| A última ocorrência, que pode estar no passado. | DateTime | Um valor DateTime. | Sim |
| **delay** | A quantidade de tempo para atrasar o início do processamento de dados para a janela. A execução do pipeline é iniciada após o tempo de execução esperado mais a quantidade de **atraso**. O **atraso** define por quanto tempo o gatilho espera após o disparo de uma nova execução. O **atraso** não altera a janela **StartTime**. Por exemplo, um valor de **atraso** de 00:10:00 implica um atraso de 10 minutos. | Período de tempo<br/>(hh: mm: SS)  | Um valor TimeSpan em que o padrão é 00:00:00. | Não |
| **maxConcurrency** | O número de execuções de gatilho simultâneas que são acionadas para o Windows que estão prontas. Por exemplo, para fazer o preenchimento das execuções por hora dos resultados ontem em 24 janelas. Se **maxConcurrency** = 10, os eventos de gatilho serão acionados somente para as primeiras 10 janelas (00:00-01:00-09:00-10:00). Depois que as 10 primeiras execuções de pipeline disparadas forem concluídas, as execuções de gatilho serão acionadas para as 10 próximas janelas (10:00-11:00-19:00-20:00). Continuando com este exemplo de **maxConcurrency** = 10, se houver 10 janelas prontas, haverá 10 execuções de pipeline totais. Se houver apenas 1 janela pronta, haverá apenas 1 execução de pipeline. | Número inteiro | Um inteiro entre 1 e 50. | Sim |
| **retryPolicy: Count** | O número de repetições antes de a execução do pipeline ser marcada como "falha".  | Número inteiro | Um inteiro, onde o padrão é 0 (sem repetições). | Não |
| **retryPolicy: intervalInSeconds** | O atraso entre as tentativas de repetição especificadas em segundos. | Número inteiro | O número de segundos, em que o padrão é 30. | Não |
| **depende: tipo** | O tipo de TumblingWindowTriggerReference. Necessário se uma dependência for definida. | Cadeia |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | Não |
| **depende: tamanho** | O tamanho da janela de dependência em cascata. | Período de tempo<br/>(hh: mm: SS)  | Um valor de TimeSpan positivo em que o padrão é o tamanho da janela do gatilho filho  | Não |
| **depende: deslocamento** | O deslocamento do gatilho de dependência. | Período de tempo<br/>(hh: mm: SS) |  Um valor TimeSpan que deve ser negativo em uma autodependência. Se nenhum valor for especificado, a janela será igual ao próprio gatilho. | Dependência automática: Sim<br/>Outros Não  |

### <a name="windowstart-and-windowend-system-variables"></a>Variáveis de sistema WindowStart e WindowEnd

Você pode usar as variáveis de sistema **WindowStart** e **WindowEnd** do gatilho de janela em cascata na definição de **pipeline** (ou seja, para parte de uma consulta). Passe as variáveis do sistema como parâmetros para seu pipeline na definição do **gatilho** . O exemplo a seguir mostra como passar essas variáveis como parâmetros:

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

Para usar os valores de variável de sistema **WindowStart** e **WindowEnd** na definição de pipeline, use os parâmetros "MyWindowStart" e "MyWindowEnd", de acordo.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Ordem de execução de janelas em um cenário de aterramento
Quando há várias janelas para execução (especialmente em um cenário de aterramento), a ordem de execução para o Windows é determinística, de intervalos mais antigos para os mais recentes. Atualmente, não é possível modificar este comportamento.

### <a name="existing-triggerresource-elements"></a>Elementos TriggerResource existentes
Os seguintes pontos se aplicam a elementos **TriggerResource** existentes:

* Se o valor do elemento **Frequency** (ou tamanho da janela) do gatilho for alterado, o estado das janelas que já foram processadas *não* será redefinido. O gatilho continua a ser acionado para as janelas da última janela executada usando o novo tamanho de janela.
* Se o valor do elemento **EndTime** do gatilho for alterado (adicionado ou atualizado), o estado das janelas que já foram processadas *não* será redefinido. O gatilho honra o novo valor **EndTime** . Se o novo valor **EndTime** for anterior às janelas que já foram executadas, o gatilho será interrompido. Caso contrário, o gatilho será interrompido quando o novo valor **EndTime** for encontrado.

### <a name="tumbling-window-trigger-dependency"></a>Dependência de gatilho de janela em cascata

Se você quiser garantir que um gatilho de janela em cascata seja executado somente após a execução bem-sucedida de outro gatilho de janela em cascata na data factory, [crie uma dependência de gatilho de janela em cascata](tumbling-window-trigger-dependency.md). 

## <a name="sample-for-azure-powershell"></a>Exemplo de Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Esta seção mostra como usar Azure PowerShell para criar, iniciar e monitorar um gatilho.

1. Crie um arquivo JSON chamado **mytrigger. JSON** na pasta C:\ADFv2QuickStartPSH\ com o seguinte conteúdo:

    > [!IMPORTANT]
    > Antes de salvar o arquivo JSON, defina o valor do elemento **StartTime** como a hora UTC atual. Defina o valor do elemento **EndTime** para uma hora após a hora UTC atual.

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

2. Crie um gatilho usando o cmdlet **set-AzDataFactoryV2Trigger** :

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Confirme se o status do gatilho é **interrompido** usando o cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Inicie o gatilho usando o cmdlet **Start-AzDataFactoryV2Trigger** :

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Confirme se o status do gatilho é **iniciado** usando o cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Obtenha as execuções do gatilho em Azure PowerShell usando o cmdlet **Get-AzDataFactoryV2TriggerRun** . Para obter informações sobre as execuções do gatilho, execute o comando a seguir periodicamente. Atualize os valores de **TriggerRunStartedAfter** e **TriggerRunStartedBefore** para corresponder aos valores em sua definição de gatilho:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Para monitorar execuções de gatilho e execuções de pipeline no portal do Azure, consulte [monitorar execuções de pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações detalhadas sobre gatilhos, consulte [execução de pipeline e gatilhos](concepts-pipeline-execution-triggers.md#triggers).
* [Criar uma dependência de gatilho de janela em cascata](tumbling-window-trigger-dependency.md)