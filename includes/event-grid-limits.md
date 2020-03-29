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
ms.openlocfilehash: ee80c04a6365c2cf337c4033a90df8d2993c299d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76845962"
---
Os seguintes limites aplicam-se aos tópicos do sistema Azure Event Grid e tópicos personalizados, *e não* domínios de eventos.

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por subscrição do Azure | 100 |
| Assinaturas de eventos por tópico | 500 |
| Taxa de publicação para um tópico personalizado (ingresso) | 5.000 eventos por segundo por tópico |
| Publicar pedidos | 250 por segundo |
| Tamanho do evento | 1 MB (carregado como múltiplos eventos de 64 KB) |

Os seguintes limites aplicam-se apenas aos domínios dos eventos.

| Recurso | Limite |
| --- | --- |
| Tópicos por domínio de evento | 100 000 |
| Assinaturas de eventos por tópico dentro de um domínio | 500 |
| Assinaturas de eventos de âmbito de domínio | 50 |
| Taxa de publicação para um domínio de evento (ingress) | 5.000 eventos por segundo |
| Publicar pedidos | 250 por segundo |
| Domínios de eventos por subscrição azure | 100 |