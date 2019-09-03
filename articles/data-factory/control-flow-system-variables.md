---
title: Variáveis do sistema em Azure Data Factory | Microsoft Docs
description: Este artigo descreve as variáveis de sistema com suporte pelo Azure Data Factory. Você pode usar essas variáveis em expressões ao definir Data Factory entidades.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: d932164f972169c93e368a41c09df039196341b5
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141643"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variáveis do sistema com suporte pelo Azure Data Factory
Este artigo descreve as variáveis de sistema com suporte pelo Azure Data Factory. Você pode usar essas variáveis em expressões ao definir Data Factory entidades.

## <a name="pipeline-scope"></a>Escopo do pipeline
Essas variáveis do sistema podem ser referenciadas em qualquer lugar no JSON do pipeline.

| Nome da variável | Descrição |
| --- | --- |
| @pipeline().DataFactory |Nome do data factory em que a execução do pipeline está sendo executada |
| @pipeline().Pipeline |Nome do pipeline |
| @pipeline().RunId | ID da execução de pipeline específica |
| @pipeline().TriggerType | Tipo do gatilho que invocou o pipeline (manual, Agendador) |
| @pipeline().TriggerId| ID do gatilho que invoca o pipeline |
| @pipeline().TriggerName| Nome do gatilho que invoca o pipeline |
| @pipeline().TriggerTime| Hora em que o gatilho que invocou o pipeline. O tempo de disparo é o tempo real acionado, não a hora agendada. Por exemplo, `13:20:08.0149599Z` é retornado em vez de`13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Planejar o escopo do gatilho
Essas variáveis de sistema podem ser referenciadas em qualquer lugar no gatilho JSON se o gatilho for do tipo: "ScheduleTrigger".

| Nome da variável | Descrição |
| --- | --- |
| @trigger().scheduledTime |Hora em que o gatilho foi agendado para invocar a execução do pipeline. Por exemplo, para um gatilho que é acionado a cada 5 minutos, essa `2017-06-01T22:20:00Z`variável `2017-06-01T22:25:00Z`retornaria,, `2017-06-01T22:29:00Z` respectivamente.|
| @trigger().startTime |Hora em que o gatilho **realmente** foi acionado para invocar a execução do pipeline. Por exemplo, para um gatilho que é acionado a cada 5 minutos, essa variável pode retornar `2017-06-01T22:20:00.4061448Z`algo `2017-06-01T22:25:00.7958577Z`assim `2017-06-01T22:29:00.9935483Z` ,, respectivamente. (Nota: O carimbo de data/hora é por padrão no formato ISO 8601)|

## <a name="tumbling-window-trigger-scope"></a>Escopo de gatilho de janela em cascata
Essas variáveis de sistema podem ser referenciadas em qualquer lugar no gatilho JSON se o gatilho for do tipo: "TumblingWindowTrigger".
(Nota: O carimbo de data/hora é por padrão no formato ISO 8601)

| Nome da variável | Descrição |
| --- | --- |
| @trigger().outputs.windowStartTime |Início da janela quando o gatilho foi agendado para invocar a execução do pipeline. Se o gatilho da janela em cascata tiver uma frequência de "por hora", essa será a hora no início da hora.|
| @trigger().outputs.windowEndTime |Fim da janela quando o gatilho foi agendado para invocar a execução do pipeline. Se o gatilho da janela em cascata tiver uma frequência de "por hora", essa será a hora no final da hora.|
## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre como essas variáveis são usadas em expressões, consulte [funções de & de linguagem de expressão](control-flow-expression-language-functions.md).
