---
title: incluir ficheiro
description: incluir ficheiro
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 04/30/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 443db1b4609e62fb7c57de417e42a2b4d0737ada
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554034"
---
Os limites seguintes aplicam-se a tópicos de sistema do Azure Event Grid e tópicos personalizados, *não* domínios de evento.

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por subscrição do Azure | 100 |
| Subscrições de eventos por tópico | 500 |
| Publicar a taxa para um tópico personalizado (entrada) | 5000 eventos por segundo por tópico |

Os limites seguintes aplicam-se apenas os domínios de evento.

| Recurso | Limite |
| --- | --- |
| Tópicos por domínio de eventos | 1000 durante a pré-visualização pública |
| Subscrições de eventos por tópico dentro de um domínio | 50 durante a pré-visualização pública |
| Subscrições de eventos do âmbito de domínio | 50 durante a pré-visualização pública |
| Publicar a taxa de para um domínio de evento (entrada) | 5000 eventos por segundo durante a pré-visualização pública |