---
title: Variáveis de sistema no Azure Data Factory | Documentos da Microsoft
description: Este artigo descreve as variáveis de sistema suportadas pelo Azure Data Factory. Pode utilizar estas variáveis em expressões de quando definir entidades do Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 183c6aea5aaf84c9d0ed635cac55074a5256851e
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872547"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variáveis de sistema suportadas pelo Azure Data Factory
Este artigo descreve as variáveis de sistema suportadas pelo Azure Data Factory. Pode utilizar estas variáveis em expressões de quando definir entidades do Data Factory.

## <a name="pipeline-scope"></a>Âmbito de pipeline
Estas variáveis de sistema podem ser referenciadas em qualquer lugar no JSON do pipeline.

| Nome da variável | Descrição |
| --- | --- |
| @pipeline().DataFactory |Nome da execução do pipeline da fábrica de dados está a ser executada |
| @pipeline().Pipeline |Nome do pipeline |
| @pipeline().RunId | ID de execução do pipeline específico |
| @pipeline().TriggerType | Tipo de Acionador que é invocado o pipeline (Manual, Scheduler) |
| @pipeline().TriggerId| ID do acionador que invoca o pipeline |
| @pipeline().TriggerName| Nome do acionador que invoca o pipeline |
| @pipeline().TriggerTime| Hora em que o acionador que é invocado o pipeline. O tempo de Acionador é a hora de acionamento real, não da data agendada. Por exemplo, `13:20:08.0149599Z` é devolvido em vez de `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Âmbito de Acionador de agenda
Estas variáveis de sistema podem ser referenciadas em qualquer lugar no acionador de JSON se o acionador é do tipo: "ScheduleTrigger."

| Nome da variável | Descrição |
| --- | --- |
| @trigger().scheduledTime |Hora quando o acionador foi agendado para invocar a execução do pipeline. Por exemplo, para um acionador que é acionado cada 5 min, esta variável retornaria `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z` , respetivamente.|
| @trigger().startTime |Hora em que o acionador **, na verdade,** acionadas para invocar a execução do pipeline. Por exemplo, para um acionador que é acionado cada 5 min, esta variável pode devolver mais ou menos assim `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z` , respetivamente. (Nota: O carimbo de hora está por predefinição no formato ISO 8601)|

## <a name="tumbling-window-trigger-scope"></a>Âmbito de Acionador de janela em cascata
Estas variáveis de sistema podem ser referenciadas em qualquer lugar no acionador de JSON se o acionador é do tipo: "TumblingWindowTrigger."

| Nome da variável | Descrição |
| --- | --- |
| @trigger().outputs.windowStartTime |Início da janela quando o acionador foi agendado para invocar a execução de pipeline. Se o acionador de janela em cascata tem uma frequência de "hora a hora" isso seria o tempo no início da hora.|
| @trigger().outputs.windowEndTime |Fim da janela quando o acionador foi agendado para invocar a execução do pipeline. Se o acionador de janela em cascata tem uma frequência de "hora a hora" isso seria o tempo no fim da hora.|
## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre como estas variáveis são utilizadas em expressões, consulte [linguagem de expressão & funções](control-flow-expression-language-functions.md).
