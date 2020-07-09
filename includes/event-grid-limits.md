---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 05/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e7a0424e4454639211c6494aab0700e75269361
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83721225"
---
Os seguintes limites aplicam-se aos tópicos do sistema Azure Event Grid e tópicos personalizados, *não* aos domínios de eventos.

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por subscrição do Azure | 100 |
| Assinaturas de eventos por tópico | 500 |
| Taxa de publicação para um tópico personalizado (entrada) | 5.000 eventos por segundo por tópico |
| Tamanho do evento | 1 MB. No entanto, as operações são cobradas em incrementos de 64 KB. Assim, eventos acima de 64 KB incorrerão em cargas de operações como se fossem múltiplos eventos. Por exemplo, um evento de 130 KB incorreria em operações como se fossem 3 eventos separados.  |
| Tópicos por domínio de evento | 100.000 |
| Subscrições de eventos por tópico dentro de um domínio | 500 |
| Subscrições de eventos de âmbito de domínio | 50 |
| Taxa de publicação para um domínio de evento (entrada) | 5.000 eventos por segundo |
| Domínios de eventos por subscrição de Azure | 100 |
| Ligações privadas de ponto final por tópico ou domínio | 64 | 
| Regras de Firewall IP por tópico ou domínio | 16 | 