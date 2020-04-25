---
title: incluir ficheiro
description: incluir ficheiro
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 5992726893b722b0aa46c976a0167793f5ee6bb4
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131682"
---
Os seguintes limites aplicam-se aos tópicos do sistema Azure Event Grid e tópicos personalizados, *e não* domínios de eventos.

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por subscrição do Azure | 100 |
| Assinaturas de eventos por tópico | 500 |
| Taxa de publicação para um tópico personalizado (ingresso) | 5.000 eventos por segundo por tópico |
| Publicar pedidos | 250 por segundo |
| Tamanho do evento | 1 MB. As operações são carregadas em incrementos de 64 KB. Assim, eventos acima de 64 KB incorrerão em acusações de operações como se fossem múltiplos eventos. Por exemplo, um evento de 130 KB incorreria em operações como se fossem 3 eventos separados.  |

Os seguintes limites aplicam-se apenas aos domínios dos eventos.

| Recurso | Limite |
| --- | --- |
| Tópicos por domínio de evento | 100 000 |
| Assinaturas de eventos por tópico dentro de um domínio | 500 |
| Assinaturas de eventos de âmbito de domínio | 50 |
| Taxa de publicação para um domínio de evento (ingress) | 5.000 eventos por segundo |
| Publicar pedidos | 250 por segundo |
| Domínios de eventos por subscrição azure | 100 |