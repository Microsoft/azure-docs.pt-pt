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
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "66376914"
---
Os limites a seguir se aplicam aos tópicos do sistema de grade de eventos do Azure e aos tópicos personalizados, *não* aos domínios de evento.

| Resource | Limite |
| --- | --- |
| Tópicos personalizados por assinatura do Azure | 100 |
| Assinaturas de evento por tópico | 500 |
| Taxa de publicação para um tópico personalizado (entrada) | 5\.000 eventos por segundo por tópico |
| Solicitações de publicação | 250 por segundo |
| Tamanho do evento | Suporte para 64 KB em disponibilidade geral (GA). O suporte para 1 MB está atualmente em versão prévia. |

Os limites a seguir se aplicam somente a domínios de evento.

| Resource | Limite |
| --- | --- |
| Tópicos por domínio de evento | 1\.000 durante a visualização pública |
| Assinaturas de evento por tópico em um domínio | 50 durante a visualização pública |
| Assinaturas de evento de escopo de domínio | 50 durante a visualização pública |
| Taxa de publicação para um domínio de evento (entrada) | 5\.000 eventos por segundo durante a visualização pública |
| Solicitações de publicação | 250 por segundo |