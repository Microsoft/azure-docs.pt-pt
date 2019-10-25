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
ms.openlocfilehash: 890095718833c90a6764d0799d2081b393b1d0bd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887822"
---
Os limites a seguir se aplicam aos tópicos do sistema de grade de eventos do Azure e aos tópicos personalizados, *não* aos domínios de evento.

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por assinatura do Azure | 100 |
| Assinaturas de evento por tópico | 500 |
| Taxa de publicação para um tópico personalizado (entrada) | 5\.000 eventos por segundo por tópico |
| Solicitações de publicação | 250 por segundo |
| Tamanho do evento | Suporte para 64 KB em disponibilidade geral (GA). O suporte para 1 MB está atualmente em versão prévia. |

Os limites a seguir se aplicam somente a domínios de evento.

| Recurso | Limite |
| --- | --- |
| Tópicos por domínio de evento | 100.000 |
| Assinaturas de evento por tópico em um domínio | 500 |
| Assinaturas de evento de escopo de domínio | 50 |
| Taxa de publicação para um domínio de evento (entrada) | 5\.000 eventos por segundo |
| Solicitações de publicação | 250 por segundo |
| Domínios de evento por assinatura do Azure | 100 |