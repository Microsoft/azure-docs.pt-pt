---
title: incluir ficheiro
description: incluir ficheiro
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 071f2849a877f4ea1e8a84eff6ccfb8343be3ec7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734069"
---
| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Alertas métricos (clássicos) |100 regras de alerta ativo por subscrição. | Suporte de chamada |
| Alertas de métricas |5.000 regras de alerta ativo por subscrição em Azure público, Azure China 21Vianet e Azure Government nuvens. Se estiver a atingir este limite, explore se pode utilizar [alertas multi-recursos do mesmo tipo.](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)<br/>5.000 séries de tempo métricas por regra de alerta. | Chamada de apoio. |
| Alertas do registo de atividades | 100 regras de alerta ativo por subscrição (não podem ser aumentadas). | O mesmo que o padrão |
| Alertas de registo | 512 regras de alerta ativo por subscrição. 200 regras de alerta ativo por recurso. | Suporte de chamada |
| Regras de alerta e duração das regras de ação| Log search alerta 4096 caracteres<br/>Todos os outros personagens de 2048 | O mesmo que o padrão |