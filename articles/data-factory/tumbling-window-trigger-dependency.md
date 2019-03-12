---
title: Criar dependências de Acionador de janela em cascata na fábrica de dados do Azure | Documentos da Microsoft
description: Saiba como criar dependência num acionador de janela em cascata no Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: f3a547f5b953262d7263d1be0897161cf4091a1d
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574394"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Criar uma dependência de acionamento de janela em cascata

Este artigo fornece passos para criar uma dependência num acionador de janela em cascata. Para obter informações gerais sobre os acionadores de janela em cascata, consulte [como criar o acionador de janela em cascata](how-to-create-tumbling-window-trigger.md).

Para criar uma cadeia de dependência e certifique-se de que o acionador é executado apenas após a execução bem-sucedida do outro acionador na fábrica de dados, utilize esta funcionalidade avançada para criar uma dependência de janela em cascata.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Criar uma dependência na IU do Data Factory

Para criar a dependência num acionador, selecione **acionador > Avançadas > New**e, em seguida, selecione o acionador a depender com o deslocamento adequado e o tamanho. Selecione **concluir** e publicar as alterações de fábrica de dados para as dependências entre em vigor.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png)

## <a name="tumbling-window-dependency-properties"></a>Propriedades de dependência de janela em cascata

Uma dependência de Acionador de janela em cascata tem as seguintes propriedades:

```json
{
    "name": "DemoTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00.000Z",
            "delay": "00:00:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": "-02:00:00",
                    "offset": "-01:00:00",
                    "referenceTrigger": {
                        "referenceName": "DemoDependency1",
                        "type": "TriggerReference"
                    }
                }
            ]
        }
    }
}
```

A tabela seguinte fornece a lista de atributos necessários para definir uma dependência de janela em cascata.

| **Nome da propriedade** | **Descrição**  | **Tipo** | **Necessário** |
|---|---|---|---|
| Acionador  | Todos os existentes em cascata os acionadores de janela são exibidos nesta lista pendente. Escolha o acionador para assumir a dependência.  | TumblingWindowTrigger | Sim |
| Desvio | Deslocamento do acionador de dependência. Forneça um valor no formato span tempo e deslocamentos negativos e positivos são permitidos. Este parâmetro é obrigatório se o acionador é dependendo em si e em todos os outros casos é opcional. Self-dependência deve ser sempre um deslocamento negativo. | Timespan | Self-dependência: Sim outro: Não |
| Tamanho da janela | Tamanho da janela em cascata dependência. Forneça um valor no formato de span de tempo. Este parâmetro é opcional. | Timespan | Não  |
|||||

## <a name="tumbling-window-self-dependency-properties"></a>Propriedades de dependência de reposição personalizada de janela em cascata

Nos cenários em que o acionador só deve avançar para a próxima janela até que a janela anterior seja concluída com êxito, crie uma dependência de self. Acionador de Self-dependência terá abaixo propriedades:

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```

Seguem-se as ilustrações dos cenários e a utilização de janela em cascata propriedades de dependência.

## <a name="dependency-offset"></a>Deslocamento de dependência

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png)

## <a name="dependency-size"></a>Tamanho de dependência

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png)

## <a name="self-dependency"></a>Self-dependência

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png)

## <a name="usage-scenarios"></a>Cenários de utilização

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dependência de outro acionador de janela em cascata

Por exemplo, uma tarefa de processamento de telemetria diário dependendo da outra tarefa diária, agregar os últimos sete dias de saída e gera os fluxos de janela sem interrupção de sete dias:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png)

### <a name="dependency-on-itself"></a>Dependência em si mesmo

Uma tarefa diária sem intervalos em fluxos de saída da tarefa:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png)

## <a name="monitor-dependencies"></a>Monitorizar dependências

Pode monitorar a cadeia de dependência e monitorização de página de execução do windows correspondentes ao acionador. Navegue para **monitorização > acionar execuções**.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png)

Clique em do espelho para ver todas a execuções de Acionador dependente do período selecionado.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png)

## <a name="next-steps"></a>Passos Seguintes

Revisão [como criar um acionador de janela em cascata](how-to-create-tumbling-window-trigger.md).