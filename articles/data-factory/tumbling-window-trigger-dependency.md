---
title: Criar dependências de gatilho de janelas caindo
description: Aprenda a criar dependência de um gatilho de janela caindo na Azure Data Factory.
services: data-factory
ms.author: chez
author: chez-charlie
manager: weetok
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/03/2020
ms.openlocfilehash: 4a99865e13e029dcea478cf6085d71c465918b14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89421854"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Criar uma dependência de acionamento de janela em cascata
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo fornece passos para criar uma dependência de um gatilho de janela caindo. Para obter informações gerais sobre os gatilhos da Janela Tumbling, consulte [Como criar o gatilho da janela .](how-to-create-tumbling-window-trigger.md)

Para construir uma cadeia de dependência e certificar-se de que um gatilho é executado apenas após a execução bem sucedida de outro gatilho na fábrica de dados, utilize esta funcionalidade avançada para criar uma dependência de janelas caindo.

Para uma demonstração de como criar oleodutos dependentes na sua Fábrica de Dados Azure utilizando o gatilho da janela caindo, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Criar uma dependência na UI da Fábrica de Dados

Para criar dependência de um gatilho, selecione **Trigger > Advanced > New**, e, em seguida, escolha o gatilho para depender do offset e do tamanho apropriados. **Selecione Terminar** e publicar as alterações na fábrica de dados para que as dependências entrem em vigor.

![Criação de Dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency-01.png "Criação de Dependência")

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

A tabela seguinte fornece a lista de atributos necessários para definir uma dependência da Janela Tumbling.

| **Nome da propriedade** | **Descrição**  | **Tipo** | **Necessário** |
|---|---|---|---|
| tipo  | Todos os gatilhos da janela de caindo existentes são apresentados nesta queda. Escolha o gatilho para assumir a dependência.  | TumblingWindowTriggerDependencyReference ou SelfDependencyTumblingWindowTriggerReference | Sim |
| offset | Compensação do gatilho da dependência. Fornecer um valor no formato de tempo e tanto compensações negativas como positivas são permitidas. Esta propriedade é obrigatória se o gatilho depender de si mesmo e em todos os outros casos é opcional. A auto-dependência deve ser sempre uma compensação negativa. Se nenhum valor especificado, a janela é a mesma que o gatilho em si. | Timespan<br/>(hh:mm:ss) | Auto-dependência: Sim<br/>Outros: Não |
| size | Tamanho da janela de caindo de dependência. Fornecer um valor de tempos positivo. Esta propriedade é opcional. | Timespan<br/>(hh:mm:ss) | Não  |

> [!NOTE]
> Um gatilho da janela caindo pode depender de um máximo de cinco outros gatilhos.

## <a name="tumbling-window-self-dependency-properties"></a>Propriedades de auto-dependência da janela caindo

Em cenários em que o gatilho não deve seguir para a janela seguinte até que a janela anterior esteja concluída com sucesso, construa uma auto-dependência. Um gatilho de auto-dependência que depende do sucesso de execuções anteriores de si mesmo dentro da hora anterior terá as propriedades indicadas no seguinte código.

> [!NOTE]
> Se o seu gasoduto acionado depender da saída de gasodutos em janelas previamente acionadas, recomendamos que utilize apenas a auto-dependência do gatilho da janela. Para limitar as execuções paralelas do gatilho, desloe o gatilho máximo.

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

### <a name="dependency-offset"></a>Compensação de dependência

![Exemplo de offset](media/tumbling-window-trigger-dependency/tumbling-window-dependency-02.png "Exemplo de offset")

### <a name="dependency-size"></a>Tamanho da dependência

![Exemplo de tamanho](media/tumbling-window-trigger-dependency/tumbling-window-dependency-03.png "Exemplo de tamanho")

### <a name="self-dependency"></a>Auto-dependência

![Auto-dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency-04.png "Auto-dependência")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dependência de outro gatilho da janela caindo

Um trabalho diário de processamento de telemetria dependendo de outro trabalho diário agregando a produção dos últimos sete dias e gera fluxos de janelas rolantes de sete dias:

![Exemplo de dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency-05.png "Exemplo de dependência")

### <a name="dependency-on-itself"></a>Dependência de si mesma

Um trabalho diário sem lacunas nos fluxos de saída do trabalho:

![Exemplo de auto-dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency-06.png "Exemplo de auto-dependência")

## <a name="monitor-dependencies"></a>Monitorizar dependências

Pode monitorizar a cadeia de dependência e as janelas correspondentes a partir da página de monitorização do gatilho. Navegue para  **monitorizar > Trigger Runs**. Se um gatilho da janela de ingrção tiver dependências, o Nome do Gatilho terá uma hiperligação para a visão de monitorização da dependência.  

![Monitorizar execuções acionadas](media/tumbling-window-trigger-dependency/tumbling-window-dependency-07.png "O gatilho do monitor corre - inteiro para a vista de dependência da janela caindo")

Clique no nome do gatilho para ver as dependências do gatilho. O painel da direita mostra informações detalhadas sobre o gatilho, tais como RunID, tempo de janela, estado, e assim por diante.

![Vista da lista de dependências do monitor](media/tumbling-window-trigger-dependency/tumbling-window-dependency-08.png "Vista da lista de dependências do monitor")

Pode ver o estado das dependências e janelas para cada gatilho dependente. Se um dos gatilhos de dependência falhar, deve reenca encontrá-lo com sucesso para que o gatilho dependente possa funcionar.

Um gatilho da janela caindo vai esperar de dependências por _sete dias_ antes de sair. Após sete dias, o gatilho falhará.

Para uma visualização mais visual do calendário de dependência do gatilho, selecione a vista Gantt.

![Gráfico de gantt de dependências de monitorização](media/tumbling-window-trigger-dependency/tumbling-window-dependency-09.png "Monitor dependências Gantt chart vista")

As caixas transparentes mostram as janelas de dependência de cada gatilho dependente do fluxo, enquanto caixas coloridas sólidas acima mostram corridas individuais de janelas. Aqui ficam algumas dicas para interpretar a vista do gráfico de Gantt:

* Caixa transparente torna azul quando as janelas dependentes estão em estado pendente ou em funcionamento
* Depois de todas as janelas ter sucesso para um gatilho dependente, a caixa transparente vai ficar verde
* A caixa transparente torna-se vermelha quando alguma janela dependente falha. Procure uma caixa vermelha sólida para identificar a janela de falha

Para refazer uma janela na vista do gráfico de Gantt, selecione a caixa de cores sólida para a janela, e um painel de ação aparecerá com detalhes e opções de repetição

## <a name="next-steps"></a>Passos seguintes

* Rever [Como criar um gatilho de janela caindo](how-to-create-tumbling-window-trigger.md)
