---
title: Criar dependências de gatilho sinuoso de janela
description: Aprenda a criar dependência de um gatilho de janela caindo na Fábrica de Dados Azure.
services: data-factory
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2019
ms.openlocfilehash: bbcbb19530aebe777a91cbe4c5487e1b50ace2e5
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559764"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Criar uma dependência de acionamento de janela em cascata
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo fornece passos para criar uma dependência de um gatilho de janela caindo. Para obter informações gerais sobre os gatilhos da Janela Tumbling, consulte [Como criar](how-to-create-tumbling-window-trigger.md)o gatilho da janela .

Para construir uma cadeia de dependência e certificar-se de que um gatilho é executado apenas após a execução bem sucedida de outro gatilho na fábrica de dados, utilize esta funcionalidade avançada para criar uma dependência de janelas caindo.

Para uma demonstração sobre como criar oleodutos dependentes na sua Fábrica de Dados Azure utilizando o gatilho da janela, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Criar uma dependência na UI da Fábrica de Dados

Para criar dependência de um gatilho, selecione **Trigger > Advanced > New**, e, em seguida, escolha o gatilho para depender com a compensação e tamanho apropriados. Selecione **Terminar** e publique as alterações da fábrica de dados para que as dependências entrem em vigor.

![Criação de Dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Criação de Dependência")

## <a name="tumbling-window-dependency-properties"></a>Propriedades de dependência de janelas caindo

Um gatilho de janela caindo com uma dependência tem as seguintes propriedades:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
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
        }
    }
}
```

A tabela seguinte fornece a lista de atributos necessários para definir uma dependência da Janela Caindo.

| **Nome da Propriedade** | **Descrição**  | **Tipo** | **Necessário** |
|---|---|---|---|
| tipo  | Todos os gatilhos existentes da janela são exibidos nesta queda para baixo. Escolha o gatilho para assumir a dependência.  | TumblingWindowTriggerDependencyReference ou SelfDependencyTumblingWindowTriggerReference | Sim |
| offset | Compensado do gatilho da dependência. Fornecer um valor no formato de tempo e compensações negativas e positivas são permitidas. Este imóvel é obrigatório se o gatilho depender de si mesmo e em todos os outros casos é opcional. A auto-dependência deve ser sempre uma compensação negativa. Se não for especificado qualquer valor, a janela é a mesma que o próprio gatilho. | Timespan<br/>(hh:mm:ss) | Auto-Dependência: Sim<br/>Outros: Não |
| size | Tamanho da janela de tropeçar da dependência. Forneça um valor de tempo positivo. Esta propriedade é opcional. | Timespan<br/>(hh:mm:ss) | No  |

> [!NOTE]
> Um gatilho da janela pode depender de um máximo de cinco outros gatilhos.

## <a name="tumbling-window-self-dependency-properties"></a>Propriedades de auto-dependência de janelas caindo

Em cenários em que o gatilho não deve passar para a janela seguinte até que a janela anterior seja concluída com sucesso, construa uma auto-dependência. Um gatilho de auto-dependência que está dependente do sucesso de corridas anteriores dentro do hr anterior terá as propriedades abaixo:

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
## <a name="usage-scenarios-and-examples"></a>Cenários e exemplos de utilização

Abaixo estão ilustrações de cenários e uso de propriedades de dependência de janelas caindo.

### <a name="dependency-offset"></a>Compensação da dependência

![Exemplo de compensação](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Exemplo de compensação")

### <a name="dependency-size"></a>Tamanho da dependência

![Exemplo de tamanho](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Exemplo de tamanho")

### <a name="self-dependency"></a>Auto-dependência

![Auto-dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Auto-dependência")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dependência de outro gatilho da janela caindo

Um trabalho diário de processamento de telemetria, dependendo de outro trabalho diário que agrega os últimos sete dias de produção e gera streams de janelas rolantes de sete dias:

![Exemplo de dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Exemplo de dependência")

### <a name="dependency-on-itself"></a>Dependência de si mesma

Um trabalho diário sem lacunas nos fluxos de saída do trabalho:

![Exemplo de auto-dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Exemplo de auto-dependência")

## <a name="monitor-dependencies"></a>Monitorizar dependências

Pode monitorizar a cadeia de dependência e as janelas correspondentes a partir da página de monitorização do gatilho. Navegar para **monitorizar > disparos**. Sob a coluna de ações, pode reproduzir o gatilho ou ver as suas dependências.

![Monitorizar execuções acionadas](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Monitorizar execuções acionadas")

Se clicar em 'Ver Dependências do Gatilho', pode ver o estado das dependências. Se um dos gatilhos de dependência falhar, deve reexecutá-lo com sucesso para que o gatilho dependente corra. Um gatilho de janela caindo vai esperar por dependências por sete dias antes de sair.

![Monitorizar dependências](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Monitorizar dependências")

Para um visual mais visual para ver o horário de dependência do gatilho, selecione a vista Gantt.

![Monitorizar dependências](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Monitorizar dependências")

## <a name="next-steps"></a>Passos seguintes

* Rever [Como criar um gatilho](how-to-create-tumbling-window-trigger.md) de janela caindo
