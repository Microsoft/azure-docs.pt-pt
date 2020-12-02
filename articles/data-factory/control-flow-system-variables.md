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
ms.openlocfilehash: 1780b4a64de349c1e272158fe6bfde9cab6f8369
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486051"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variáveis do sistema suportadas pela Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve variáveis do sistema suportadas pela Azure Data Factory. Pode utilizar estas variáveis em expressões ao definir entidades da Data Factory.

## <a name="pipeline-scope"></a>Extensão do gasoduto
Estas variáveis do sistema podem ser referenciadas em qualquer lugar do gasoduto JSON.

| Nome da Variável | Descrição |
| --- | --- |
| @pipeline(). DataFactory |Nome da fábrica de dados o gasoduto está a funcionar dentro |
| @pipeline(). Gasoduto |Nome do oleoduto |
| @pipeline(). RunId | ID do gasoduto específico |
| @pipeline(). TriggerType | Tipo do gatilho que invocou o gasoduto (Manual, Agendador) |
| @pipeline(). TriggerId| ID do gatilho que invoca o oleoduto |
| @pipeline(). Nome do gatilho| Nome do gatilho que invoca o oleoduto |
| @pipeline(). Hora do Gatilho| Hora em que o gatilho que invocou o oleoduto. O tempo de disparo é a hora do disparo real, não a hora programada. Por exemplo, `13:20:08.0149599Z` é devolvido em vez de `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Programação Detonador
Estas variáveis do sistema podem ser referenciadas em qualquer lugar do gatilho JSON se o gatilho for do tipo: "ScheduleTrigger".

| Nome da Variável | Descrição |
| --- | --- |
| @trigger().Horário programado |Hora em que o gatilho estava programado para invocar o curso do oleoduto. Por exemplo, para um gatilho que dispara a cada 5 minutos, esta variável `2017-06-01T22:20:00Z` `2017-06-01T22:25:00Z` regressaria, `2017-06-01T22:30:00Z` respectivamente.|
| @trigger().startTime |Tempo em que o gatilho **disparou** para invocar o gasoduto. Por exemplo, para um gatilho que dispara a cada 5 minutos, esta variável pode devolver algo `2017-06-01T22:20:00.4061448Z` assim, `2017-06-01T22:25:00.7958577Z` `2017-06-01T22:30:00.9935483Z` respectivamente. (Nota: A estada de tempo é por defeito no formato ISO 8601)|

## <a name="tumbling-window-trigger-scope"></a>Telescópio do gatilho da janela de tumbling
Estas variáveis do sistema podem ser referenciadas em qualquer lugar do gatilho JSON se o gatilho for do tipo: "TumblingWindowTrigger".
(Nota: A estada de tempo é por defeito no formato ISO 8601)

| Nome da Variável | Descrição |
| --- | --- |
| @trigger().outputs.windowStartTime |Início da janela quando o gatilho estava programado para invocar o gasoduto. Se o gatilho da janela caindo tiver uma frequência de "hora a hora" esta seria a hora no início da hora.|
| @trigger().outputs.windowEndTime |Fim da janela quando o gatilho estava programado para invocar o gasoduto. Se o gatilho da janela caindo tiver uma frequência de "hora a hora" esta seria a hora no final da hora.|
## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre como estas variáveis são usadas em expressões, consulte [as funções de linguagem de expressão &](control-flow-expression-language-functions.md).
