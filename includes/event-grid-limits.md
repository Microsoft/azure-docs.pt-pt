---
title: incluir ficheiro
description: incluir ficheiro
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 05/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e7a0424e4454639211c6494aab0700e75269361
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83721225"
---
Os seguintes limites aplicam-se aos tópicos do sistema Azure Event Grid e tópicos personalizados, *e não* domínios de eventos.

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por subscrição do Azure | 100 |
| Assinaturas de eventos por tópico | 500 |
| Taxa de publicação para um tópico personalizado (ingresso) | 5.000 eventos por segundo por tópico |
| Tamanho do evento | 1 MB. As operações são carregadas em incrementos de 64 KB. Assim, eventos acima de 64 KB incorrerão em acusações de operações como se fossem múltiplos eventos. Por exemplo, um evento de 130 KB incorreria em operações como se fossem 3 eventos separados.  |
| Tópicos por domínio de evento | 100 000 |
| Assinaturas de eventos por tópico dentro de um domínio | 500 |
| Assinaturas de eventos de âmbito de domínio | 50 |
| Taxa de publicação para um domínio de evento (ingress) | 5.000 eventos por segundo |
| Domínios de eventos por subscrição azure | 100 |
| Conexões de ponto final privados por tópico ou domínio | 64 | 
| Regras ip firewall por tópico ou domínio | 16 | 