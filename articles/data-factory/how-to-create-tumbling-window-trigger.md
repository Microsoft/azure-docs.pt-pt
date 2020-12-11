---
title: Criar gatilhos de janelas caindo na Fábrica de Dados Azure
description: Aprenda a criar um gatilho na Azure Data Factory que executa um oleoduto numa janela caindo.
services: data-factory
documentationcenter: ''
author: chez-charlie
ms.author: chez
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: 07af7f7f716a83ee9fa47619c1334a29786818d7
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033090"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Create a trigger that runs a pipeline on a tumbling window(Criar um acionador que execute um pipeline numa janela em cascata)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo fornece passos para criar, iniciar e monitorizar um gatilho da janela caindo. Para obter informações gerais sobre os gatilhos e os tipos [suportados, consulte a execução do Pipeline e os gatilhos](concepts-pipeline-execution-triggers.md).

Os acionadores de janela em cascata são um tipo de acionador que é acionado num intervalo de tempo periódico a partir de uma hora de início especificada, mantendo o estado. As janelas em cascata são uma série de intervalos de tempo com tamanho fixo, não sobrepostos e contínuos. Um gatilho de janela caindo tem uma relação de um para um com um oleoduto e só pode fazer referência a um oleoduto singular. O gatilho da janela de tropeçar é uma alternativa de peso mais pesado para o gatilho de horário, oferecendo um conjunto de funcionalidades para cenários complexos[(dependência de outros gatilhos de janelas em queda,](#tumbling-window-trigger-dependency) [reexecução de um trabalho falhado](tumbling-window-trigger-dependency.md#monitor-dependencies) e definir a tentativa de [revólesia do utilizador para os oleodutos).](#user-assigned-retries-of-pipelines) Para entender melhor a diferença entre o gatilho do horário e o gatilho da janela, visite [aqui.](concepts-pipeline-execution-triggers.md#trigger-type-comparison)

## <a name="data-factory-ui"></a>IU do Data Factory

1. Para criar um gatilho de janela caindo na UI da Fábrica de Dados, selecione o **separador Gatilhos** e, em seguida, selecione **New**. 
1. Depois de abrir o painel de configuração do gatilho, selecione **Tumbling Window** e, em seguida, defina as propriedades do gatilho da janela caindo. 
1. Quando tiver terminado, selecione **Guardar**.

![Criar um gatilho de janela caindo no portal Azure](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Propriedades do tipo de gatilho da janela caindo

Uma janela de tropeçar tem as seguintes propriedades do tipo gatilho:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime": <<datetime – optional>>,
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

A tabela a seguir fornece uma visão geral de alto nível dos principais elementos JSON que estão relacionados com a recorrência e agendamento de um gatilho da janela caindo:

| Elemento JSON | Descrição | Tipo | Valores permitidos | Necessário |
|:--- |:--- |:--- |:--- |:--- |
| **tipo** | O tipo do gatilho. O tipo é o valor fixo "TumblingWindowTrigger". | String | "TumblingWindowTrigger" | Sim |
| **estado de tempo de execução** | O estado atual do tempo de execução do gatilho.<br/>**Nota:** Este elemento é \<readOnly> . | String | "Iniciado", "Parado", "Deficiente" | Sim |
| **frequência** | Uma corda que representa a unidade de frequência (minutos ou horas) em que o gatilho se repete. Se os valores da data **de início do Tempo** forem mais granulares do que o valor da **frequência,** as datas **de início são** consideradas quando os limites da janela são calculados. Por exemplo, se o valor da **frequência** for de hora em hora e o valor inicial do Tempo de **Arranque** for 2017-09-01T10:10:10Z, a primeira janela é (2017-09-01T10:10:10Z, 2017-09-011:10:10Z). | String | "minuto", "hora"  | Sim |
| **intervalo** | Um valor inteiro positivo que indica o intervalo do valor **frequency**, que determina o número de vezes que o acionador é executado. Por exemplo, se o **intervalo** for 3 e a **frequência** for "hora", o gatilho repete-se a cada 3 horas. <br/>**Nota:** O intervalo mínimo da janela é de 5 minutos. | Número inteiro | Um inteiro positivo. | Sim |
| **horário de início**| A primeira ocorrência, que pode ser no passado. O primeiro intervalo de disparo é **(startTime**, intervalo **startTime**  +  ). | DateTime | Um valor de DataTime. | Sim |
| **endTime**| A última ocorrência, que pode ser no passado. | DateTime | Um valor de DataTime. | Sim |
| **atraso** | O tempo para atrasar o início do processamento de dados para a janela. O gasoduto é iniciado após o tempo de execução esperado mais a quantidade de **atraso**. O **atraso** define quanto tempo o gatilho espera para além do tempo devido antes de desencadear uma nova execução. O **atraso** não altera a janela **de inícioTime**. Por exemplo, um valor de **atraso** de 00:10:00 implica um atraso de 10 minutos. | Timespan<br/>(hh:mm:ss)  | Um valor de tempo em que o padrão é 00:00:00. | Não |
| **maxConcurrency** | O número de disparos simultâneos que são disparados para janelas que estão prontas. Por exemplo, voltar a encher as horas por hora para ontem resulta em 24 janelas. Se **maxConcurrency** = 10, os eventos de gatilho são disparados apenas para as primeiras 10 janelas (00:00-01:00 - 09:00-10:00). Depois de concluídas as primeiras 10 corridas de gasodutos acionados, o gatilho é disparado para as próximas 10 janelas (10:00-11:00 - 19:00-20:00). Continuando com este exemplo de **maxConcurrency** = 10, se houver 10 janelas prontas, existem 10 percursos totais de gasoduto. Se só houver uma janela pronta, só há 1 oleoduto. | Número inteiro | Um inteiro entre 1 e 50. | Sim |
| **retriptaPolícia: Conde** | O número de retreições antes do curso do gasoduto é marcado como "Falhado".  | Número inteiro | Um inteiro, onde o padrão é 0 (sem retracências). | Não |
| **retriptaPolícia: intervalosSegundos** | O atraso entre as tentativas de repetição especificadas em segundos. | Número inteiro | O número de segundos, onde o padrão é 30. | Não |
| **dependsOn: tipo** | O tipo de TumblingWindowTriggerReference. Necessário se uma dependência for definida. | String |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | Não |
| **dependsOn: tamanho** | O tamanho da janela de caindo de dependência. | Timespan<br/>(hh:mm:ss)  | Um valor de tempo positivo onde o padrão é o tamanho da janela do gatilho da criança  | Não |
| **dependsOn: offset** | A compensação do gatilho da dependência. | Timespan<br/>(hh:mm:ss) |  Um valor de tempo que deve ser negativo numa auto-dependência. Se nenhum valor especificado, a janela é a mesma que o gatilho em si. | Auto-dependência: Sim<br/>Outros: Não  |

> [!NOTE]
> Depois de publicado um gatilho da janela, **o intervalo** e **a frequência** não podem ser editados.

### <a name="windowstart-and-windowend-system-variables"></a>Variáveis do sistema WindowStart e WindowEnd

Pode utilizar as variáveis do sistema **WindowStart** e **WindowEnd** do gatilho da janela caindo na definição do **seu pipeline** (isto é, para uma parte de uma consulta). Passe as variáveis do sistema como parâmetros para o seu pipeline na definição de **gatilho.** O exemplo a seguir mostra como passar estas variáveis como parâmetros:

```json
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

Para utilizar os valores variáveis do sistema **WindowStart** e **WindowEnd** na definição do gasoduto, utilize os seus parâmetros "MyWindowStart" e "MyWindowEnd", em conformidade.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Ordem de execução das janelas em um cenário de enchimento

Se o startTime do gatilho estiver no passado, então com base nesta fórmula, M=(CurrentTime- TriggerStartTime)/TumblingWindowSize, o gatilho gerará cadeias de backfill {M} (passado) em paralelo, honrando a conúência do gatilho, antes de executar as futuras execuções. A ordem de execução das janelas é determinística, desde os intervalos mais antigos até aos mais recentes. Atualmente, não é possível modificar este comportamento.

### <a name="existing-triggerresource-elements"></a>Elementos existentes do TriggerResource

Os seguintes pontos aplicam-se à atualização dos elementos **existentes do TriggerResource:**

* O valor para o elemento **de frequência** (ou tamanho da janela) do gatilho juntamente com o elemento **de intervalo** não pode ser alterado uma vez que o gatilho é criado. Isto é necessário para o bom funcionamento das repetições de triggerRun e avaliações de dependência
* Se o valor para o elemento **endTime** do gatilho mudar (adicionado ou atualizado), o estado das janelas já processadas *não* é reposto. O gatilho honra o novo valor **do fim do Tempo.** Se o novo valor **endTime** for antes das janelas já executadas, o gatilho para. Caso contrário, o gatilho para quando o novo valor **endTime** é encontrado.

### <a name="user-assigned-retries-of-pipelines"></a>Utilizador atribuiu recauchutagens de gasodutos

Em caso de avarias no gasoduto, o gatilho da janela pode voltar a tentar a execução do gasoduto referenciado automaticamente, utilizando os mesmos parâmetros de entrada, sem a intervenção do utilizador. Isto pode ser especificado usando a propriedade "retrippolitidade" na definição de gatilho.

### <a name="tumbling-window-trigger-dependency"></a>Dependência do gatilho da janela caindo

Se quiser certificar-se de que um gatilho da janela de caindo só é executado após a execução bem sucedida de outro gatilho da janela caindo na fábrica de dados, [crie uma dependência do gatilho da janela caindo](tumbling-window-trigger-dependency.md).

### <a name="cancel-tumbling-window-run"></a>Cancelar a corrida da janela caindo

Pode cancelar as corridas para um gatilho de janela caindo, se a janela específica estiver em _Espera,_ _Esperando na Dependência,_ ou _Estado de Execução_

* Se a janela estiver em **funcionamento,** cancele a _Pipeline Run_ associada, e o gatilho será marcado como _cancelado_ posteriormente.
* Se a janela estiver em **Estado de Espera** ou Espera em **Dependência,** pode cancelar a janela da Monitorização:

![Cancelar um gatilho da janela caindo da página de monitorização](media/how-to-create-tumbling-window-trigger/cancel-tumbling-window-trigger.png)

Também pode repetir uma janela cancelada. A repetição tomará as _últimas_ definições publicadas do gatilho, e as dependências para a janela especificada serão _reavaliadas_ após a repetição

![Repercuta um gatilho da janela para corridas previamente canceladas](media/how-to-create-tumbling-window-trigger/rerun-tumbling-window-trigger.png)

## <a name="sample-for-azure-powershell"></a>Amostra para Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Esta secção mostra-lhe como utilizar o Azure PowerShell para criar, iniciar e monitorizar um gatilho.

1. Crie um ficheiro JSON nomeado **MyTrigger.jsna** pasta C:\ADFv2QuickStartPSH\ com o seguinte conteúdo:

    > [!IMPORTANT]
    > Antes de guardar o ficheiro JSON, desajei o valor do elemento **startTime** para o tempo UTC atual. Desajei o valor do elemento **endTime** para uma hora depois do tempo UTC atual.

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

2. Crie um gatilho utilizando o **cmdlet Set-AzDataFactoryV2Trigger:**

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Confirme se o estado do gatilho é **interrompido** utilizando o cmdlet **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Inicie o gatilho utilizando o cmdlet **Start-AzDataFactoryV2Trigger:**

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Confirme se o estado do gatilho é **iniciado** utilizando o cmdlet **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Obtenha o gatilho em Azure PowerShell utilizando o cmdlet **Get-AzDataFactoryV2TriggerRun.** Para obter informações sobre o gatilho, execute o seguinte comando periodicamente. Atualize o **TriggerRunStartedAfter** e **TriggerRunStartedBeted Para** corresponder aos valores na definição do gatilho:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Para monitorizar as correções do gatilho e o gasoduto funciona no portal Azure, consulte [o gasoduto Monitor](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Passos seguintes

* Para obter informações detalhadas sobre os gatilhos, consulte [a execução do Pipeline e os gatilhos](concepts-pipeline-execution-triggers.md#trigger-execution).
* [Criar uma dependência de acionamento de janela em cascata](tumbling-window-trigger-dependency.md)
