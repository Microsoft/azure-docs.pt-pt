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
ms.openlocfilehash: 0c3cdc21425e0634a725000efb27b3cde0ccd718
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97844725"
---
| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Alertas métricos (clássicos) |100 regras de alerta ativo por subscrição. | Suporte de chamada |
| Alertas de métricas |5.000 regras de alerta ativo por subscrição em Azure público, Azure China 21Vianet e Azure Government nuvens. Se estiver a atingir este limite, explore se pode utilizar [alertas multi-recursos do mesmo tipo.](../articles/azure-monitor/platform/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)<br/>5.000 séries de tempo métricas por regra de alerta. | Chamada de apoio. |
| Alertas do registo de atividades | 100 regras de alerta ativo por subscrição (não podem ser aumentadas). | O mesmo que o padrão |
| Alertas de registo | 512 regras de alerta ativo por subscrição. 200 regras de alerta ativo por recurso. | Suporte de chamada |
| Regras de alerta e duração das regras de ação| Log search alerta 4096 caracteres<br/>Todos os outros personagens de 2048 | O mesmo que o padrão |
