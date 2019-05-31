---
title: incluir ficheiro
description: incluir ficheiro
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 050d3314345e64e3d69a07367a0e9acc318fa106
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66271539"
---
| Resource | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Definições de dimensionamento automático |100 por região por subscrição. | Mesmo que o padrão. |
| Alertas de métricas (clássico) |100 active regras de alerta por subscrição. | Contacte o suporte. |
| Alertas de métricas |1000 active regras de alerta por subscrição (em clouds públicas) e 100 regras de alertas ativas por subscrição do Azure China e Azure Government. | Contacte o suporte. |
| Alertas do registo de atividades | 100 active regras de alerta por subscrição. | Mesmo que o padrão. |
| Alertas de registo | 512 | Contacte o suporte. |
| Grupos de ação |2.000 grupos de ação por subscrição. | Contacte o suporte. |

**Limites do grupo específico de ação**

| Resource | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Push da aplicação do Azure | 10 ações de aplicação do azure por grupo de ação. | Contacte o suporte. |
| Email | ações num grupo de ação de e-mail de 1.000. Consulte também os [informações de limitação de taxas](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Contacte o suporte. |
| ITSM | 10 ações do ITSM num grupo de ação. | Contacte o suporte. | 
| Aplicação lógica | 10 ações de aplicação lógica num grupo de ação. | Contacte o suporte. |
| Runbook | 10 ações de runbook num grupo de ação. | Contacte o suporte. |
| SMS | 10 ações de SMS num grupo de ação. Consulte também os [informações de limitação de taxas](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Contacte o suporte. |
| Voz | 10 de voz ações num grupo de ação. Consulte também os [informações de limitação de taxas](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Contacte o suporte. |
| Webhook | 10 ações de webhook num grupo de ação.  Número máximo de chamadas de webhook é 1500 por minuto para cada subscrição. Outros limites estão disponíveis em [informações específicas da ação](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Contacte o suporte. |
