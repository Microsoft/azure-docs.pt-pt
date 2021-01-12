---
title: Variáveis do sistema na Azure Data Factory
description: Este artigo descreve variáveis do sistema suportadas pela Azure Data Factory. Pode utilizar estas variáveis em expressões ao definir entidades da Data Factory.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: fc6b2e4c944394d811abc19f70aeb34a0ae3c9a4
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127673"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variáveis do sistema suportadas pela Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve variáveis do sistema suportadas pela Azure Data Factory. Pode utilizar estas variáveis em expressões ao definir entidades da Data Factory.

## <a name="pipeline-scope"></a>Extensão do gasoduto
Estas variáveis do sistema podem ser referenciadas em qualquer lugar do gasoduto JSON.

| Nome da Variável | Descrição |
| --- | --- |
| @pipeline(). DataFactory |Nome da fábrica de dados o gasoduto está em execução |
| @pipeline(). Gasoduto |Nome do oleoduto |
| @pipeline(). RunId |ID do gasoduto específico |
| @pipeline(). TriggerType |O tipo de gatilho que invocou o gasoduto (por `ScheduleTrigger` exemplo, `BlobEventsTrigger` , ). Para obter uma lista de tipos de gatilho suportados, consulte [a execução do Pipeline e os gatilhos na Azure Data Factory](concepts-pipeline-execution-triggers.md). Um tipo de gatilho `Manual` indica que o gasoduto foi acionado manualmente. |
| @pipeline(). TriggerId|ID do gatilho que invocou o oleoduto |
| @pipeline(). Nome do gatilho|Nome do gatilho que invocou o oleoduto |
| @pipeline(). Hora do Gatilho|Tempo do gatilho que invocou o oleoduto. Este é o momento em que o gatilho **realmente** disparou para invocar o gasoduto, e pode diferir ligeiramente da hora programada do gatilho.  |

>[!NOTE]
>As variáveis do sistema de data/hora relacionadas com o gatilho (tanto em âmbitos de pipeline como de gatilho) devolvem as datas UTC no formato ISO 8601, por exemplo, `2017-06-01T22:20:00.4061448Z` .

## <a name="schedule-trigger-scope"></a>Programação de acionamento
Estas variáveis do sistema podem ser referenciadas em qualquer lugar do gatilho JSON para gatilhos do tipo [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger).

| Nome da Variável | Descrição |
| --- | --- |
| @trigger().Horário programado |Hora em que o gatilho estava programado para invocar o gasoduto. |
| @trigger().startTime |Hora em que o gatilho **disparou** para invocar o gasoduto. Isto pode diferir ligeiramente da hora programada do gatilho. |

## <a name="tumbling-window-trigger-scope"></a>Telescópio de gatilho da janela caindo
Estas variáveis do sistema podem ser referenciadas em qualquer lugar do gatilho JSON para gatilhos do tipo [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger).

| Nome da Variável | Descrição |
| --- | --- |
| @trigger().outputs.windowStartTime |Início da janela associada ao acionador. |
| @trigger().outputs.windowEndTime |Extremidade da janela associada ao acionador. |
| @trigger().Horário programado |Hora em que o gatilho estava programado para invocar o gasoduto. |
| @trigger().startTime |Hora em que o gatilho **disparou** para invocar o gasoduto. Isto pode diferir ligeiramente da hora programada do gatilho. |

## <a name="event-based-trigger-scope"></a>Âmbito de disparo baseado em eventos
Estas variáveis do sistema podem ser referenciadas em qualquer lugar do gatilho JSON para gatilhos do tipo [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger).

| Nome da Variável | Descrição |
| --- | --- |
| @triggerBody().fileName  |Nome do ficheiro cuja criação ou supressão causou o disparo.   |
| @triggerBody().Nome de pasta  |Caminho para a pasta que contém o ficheiro especificado por `@triggerBody().fileName` . O primeiro segmento do caminho da pasta é o nome do recipiente de armazenamento Azure Blob.  |
| @trigger().startTime |Hora em que o gatilho disparou para invocar o gasoduto. |

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre como estas variáveis são usadas em expressões, consulte [as funções de linguagem de expressão &](control-flow-expression-language-functions.md).
