---
title: Variáveis do sistema no Azure Data Factory
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
ms.openlocfilehash: 0a5237336530d30c3801b13b910171e236e87a23
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679282"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variáveis do sistema com suporte pelo Azure Data Factory
Este artigo descreve as variáveis de sistema com suporte pelo Azure Data Factory. Você pode usar essas variáveis em expressões ao definir Data Factory entidades.

## <a name="pipeline-scope"></a>Escopo do pipeline
Essas variáveis do sistema podem ser referenciadas em qualquer lugar no JSON do pipeline.

| Nome da variável | Descrição |
| --- | --- |
| @pipeline(). DataFactory |Nome do data factory em que a execução do pipeline está sendo executada |
| @pipeline(). Tubula |Nome do pipeline |
| @pipeline(). RunId | ID da execução de pipeline específica |
| @pipeline(). TriggerType | Tipo do gatilho que invocou o pipeline (manual, Agendador) |
| @pipeline(). TriggerId| ID do gatilho que invoca o pipeline |
| @pipeline(). TriggerName| Nome do gatilho que invoca o pipeline |
| @pipeline(). Gatilhotime| Hora em que o gatilho que invocou o pipeline. O tempo de disparo é o tempo real acionado, não a hora agendada. Por exemplo, `13:20:08.0149599Z` é retornado em vez de `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Planejar o escopo do gatilho
Essas variáveis do sistema podem ser referenciadas em qualquer lugar no gatilho JSON se o gatilho for do tipo: "ScheduleTrigger".

| Nome da variável | Descrição |
| --- | --- |
| @trigger(). scheduletime |Hora em que o gatilho foi agendado para invocar a execução do pipeline. Por exemplo, para um gatilho que é acionado a cada 5 minutos, essa variável retornaria `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:30:00Z` respectivamente.|
| @trigger(). StartTime |Hora em que o gatilho **realmente** foi acionado para invocar a execução do pipeline. Por exemplo, para um gatilho que é acionado a cada 5 minutos, essa variável pode retornar algo como este `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z``2017-06-01T22:30:00.9935483Z`, respectivamente. (Observação: o carimbo de data/hora é por padrão no formato ISO 8601)|

## <a name="tumbling-window-trigger-scope"></a>Escopo de gatilho de janela em cascata
Essas variáveis do sistema podem ser referenciadas em qualquer lugar no gatilho JSON se o gatilho for do tipo: "TumblingWindowTrigger".
(Observação: o carimbo de data/hora é por padrão no formato ISO 8601)

| Nome da variável | Descrição |
| --- | --- |
| @trigger(). Outputs. windowStartTime |Início da janela quando o gatilho foi agendado para invocar a execução do pipeline. Se o gatilho da janela em cascata tiver uma frequência de "por hora", essa será a hora no início da hora.|
| @trigger(). Outputs. windowEndTime |Fim da janela quando o gatilho foi agendado para invocar a execução do pipeline. Se o gatilho da janela em cascata tiver uma frequência de "por hora", essa será a hora no final da hora.|
## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre como essas variáveis são usadas em expressões, consulte [funções de & de linguagem de expressão](control-flow-expression-language-functions.md).
