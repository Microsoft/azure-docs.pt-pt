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
ms.openlocfilehash: 3f94481e6a8550479788d92c744327e1dc3b58c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66376914"
---
Os limites seguintes aplicam-se a tópicos de sistema do Azure Event Grid e tópicos personalizados, *não* domínios de evento.

| Resource | Limite |
| --- | --- |
| Tópicos personalizados por subscrição do Azure | 100 |
| Subscrições de eventos por tópico | 500 |
| Publicar a taxa para um tópico personalizado (entrada) | 5000 eventos por segundo por tópico |
| Publicar pedidos | 250 por segundo |
| Tamanho do evento | Disponibilidade de suporte para 64 KB em geral (GA). Suporte para 1 MB está atualmente em pré-visualização. |

Os limites seguintes aplicam-se apenas os domínios de evento.

| Resource | Limite |
| --- | --- |
| Tópicos por domínio de eventos | 1000 durante a pré-visualização pública |
| Subscrições de eventos por tópico dentro de um domínio | 50 durante a pré-visualização pública |
| Subscrições de eventos do âmbito de domínio | 50 durante a pré-visualização pública |
| Publicar a taxa de para um domínio de evento (entrada) | 5000 eventos por segundo durante a pré-visualização pública |
| Publicar pedidos | 250 por segundo |