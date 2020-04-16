---
title: Variáveis do sistema na Fábrica de Dados Azure
description: Este artigo descreve variáveis do sistema suportadas pela Azure Data Factory. Pode utilizar estas variáveis em expressões ao definir entidades da Fábrica de Dados.
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
ms.openlocfilehash: 2690ded0ac45719cb1082c85ab535c91ad491172
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417968"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variáveis do sistema suportadas pela Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve variáveis do sistema suportadas pela Azure Data Factory. Pode utilizar estas variáveis em expressões ao definir entidades da Fábrica de Dados.

## <a name="pipeline-scope"></a>Âmbito do gasoduto
Estas variáveis do sistema podem ser referenciadas em qualquer lugar do pipeline JSON.

| Nome da Variável | Descrição |
| --- | --- |
| @pipeline(). DataFactory |Nome da fábrica de dados que o gasoduto está a funcionar dentro |
| @pipeline(). Gasoduto |Nome do oleoduto |
| @pipeline(). Runid | ID da execução específica do gasoduto |
| @pipeline(). TriggerType | Tipo do gatilho que invocou o gasoduto (Manual, Agendador) |
| @pipeline(). Desencadeador| Identificação do gatilho que invoca o oleoduto |
| @pipeline(). Nome do gatilho| Nome do gatilho que invoca o oleoduto |
| @pipeline(). Hora do gatilho| Hora do gatilho que invocou o oleoduto. O tempo do gatilho é o tempo de disparo, não a hora programada. Por exemplo, `13:20:08.0149599Z` é devolvido em vez de`13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Agendar âmbito do gatilho
Estas variáveis do sistema podem ser referenciadas em qualquer lugar do gatilho JSON se o gatilho for de tipo: "ScheduleTrigger".

| Nome da Variável | Descrição |
| --- | --- |
| @trigger().horário programado |Hora do gatilho para invocar a execução do oleoduto. Por exemplo, para um gatilho que dispara a `2017-06-01T22:20:00Z`cada `2017-06-01T22:25:00Z` `2017-06-01T22:30:00Z` 5 min, esta variável voltaria, respectivamente.|
| @trigger().inícioTime |Está na altura de o **gatilho** disparar para invocar a execução do oleoduto. Por exemplo, para um gatilho que dispara a cada 5 `2017-06-01T22:20:00.4061448Z` `2017-06-01T22:25:00.7958577Z`min, esta variável pode devolver algo assim, `2017-06-01T22:30:00.9935483Z` respectivamente. (Nota: A marca de tempo é por defeito no formato ISO 8601)|

## <a name="tumbling-window-trigger-scope"></a>Scope do gatilho da janela caindo
Estas variáveis do sistema podem ser referenciadas em qualquer lugar do gatilho JSON se o gatilho for de tipo: "TumblingWindowTrigger".
(Nota: A marca de tempo é por defeito no formato ISO 8601)

| Nome da Variável | Descrição |
| --- | --- |
| @trigger(.outputs.windowStartTime |Início da janela quando o gatilho estava programado para invocar a execução do gasoduto. Se o gatilho da janela de tropeçar tiver uma frequência de "hora a hora" esta seria a hora no início da hora.|
| @trigger(.outputs.windowEndTime |Fim da janela quando o gatilho estava programado para invocar a execução do oleoduto. Se o gatilho da janela de tropeçar tiver uma frequência de "hora a hora" esta seria a hora no final da hora.|
## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre como estas variáveis são usadas em expressões, consulte a [linguagem de expressão & funções.](control-flow-expression-language-functions.md)
