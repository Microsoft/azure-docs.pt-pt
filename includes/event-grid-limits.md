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
ms.openlocfilehash: ac08a9ab6975039402df44e6a043ee81cf5b222e
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56988087"
---
Os limites seguintes aplicam-se a tópicos de sistema do Event Grid e tópicos personalizados, *não* domínios de evento.

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por subscrição do Azure | 100 |
| Subscrições de eventos por tópico | 500 |
| Publicar a taxa para um tópico personalizado (entrada) | 5000 eventos por segundo por tópico |

Os limites seguintes aplicam-se apenas aos domínios de evento.

| Recurso | Limite |
| --- | --- |
| Tópicos por domínio de eventos | 1000 durante a pré-visualização pública |
| Subscrições de eventos por tópico dentro de um domínio | 50 durante a pré-visualização pública |
| Âmbito de domínio subscrições de eventos | 50 durante a pré-visualização pública |
| Publicar a taxa para um domínio de evento (entrada) | 5000 eventos por segundo durante a pré-visualização pública |