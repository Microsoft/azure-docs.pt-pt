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
ms.openlocfilehash: 220759e8ed31c091887bd55f8d12aa4cc03a065f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590286"
---
Os seguintes limites aplicam-se aos tópicos do sistema Azure Event Grid e tópicos personalizados, *e não* domínios de eventos.

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por subscrição do Azure | 100 |
| Assinaturas de eventos por tópico | 500 |
| Taxa de publicação para um tópico personalizado (ingresso) | 5.000 eventos por segundo por tópico |
| Publicar pedidos | 250 por segundo |
| Tamanho do evento | 1 MB. As operações são carregadas em incrementos de 64 KB. Assim, eventos acima de 64 KB incorrerão em acusações de operações como se fossem múltiplos eventos. Por exemplo, um evento de 130 KB incorreria em operações como se fossem 3 eventos separados.  |
| Tópicos por domínio de evento | 100 000 |
| Assinaturas de eventos por tópico dentro de um domínio | 500 |
| Assinaturas de eventos de âmbito de domínio | 50 |
| Taxa de publicação para um domínio de evento (ingress) | 5.000 eventos por segundo |
| Publicar pedidos para um domínio de evento | 250 por segundo |
| Domínios de eventos por subscrição azure | 100 |
| Conexões de ponto final privados por tópico ou domínio | 64 | 
| Regras ip firewall por tópico ou domínio | 16 | 