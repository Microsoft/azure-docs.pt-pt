---
title: Firewall de aplicações web do Azure, monitorização e registo
description: Aprenda a firewall de aplicações web (WAF) com FrontDoor monitorização e registo
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: e4ba6cca679ce4910ea941d9578939721514b2ec
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478965"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Firewall de aplicações web do Azure, monitorização e registo 

Registo e monitorização de firewall (WAF) de aplicações web do Azure são fornecidos através do registo e integração com o Azure Monitor e o Azure Monitor registos.

## <a name="azure-monitor"></a>Azure Monitor

É integrado a WAF com o log de FrontDoor [do Azure Monitor](../azure-monitor/overview.md). O Azure Monitor permite-lhe controlar as informações de diagnóstico, incluindo WAF alertas e registos. Pode configurar a monitorização da WAF dentro do recurso de porta de entrada no portal no **diagnóstico** separador ou através do Azure Monitor diretamente do serviço.

No portal do Azure, vá para o tipo de recurso de porta de entrada. Partir **monitorização**/**métricas** separador à esquerda, pode adicionar **WebApplicationFirewallRequestCount** para controlar o número de pedidos que correspondam a regras de WAF. Filtros personalizados podem ser criados com base nos tipos de ação e nomes de regras.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Os registos e diagnósticos

WAF com a porta da frente fornece relatórios detalhados sobre cada ameaça detetada. Os registos estão integrados no Diagnóstico do Azure e os registos e alertas são registados no formato json. Estes registos podem ser integrados no [registos do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog regista todos os pedidos que são reencaminhados para o back-ends de cliente. FrontdoorWebApplicationFirewallLog regista qualquer solicitação que corresponda a uma regra de WAF.

A consulta de exemplo seguinte obtém os registos da WAF em pedidos bloqueados:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

A consulta de exemplo seguinte obtém AccessLogs entradas:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [desde início](front-door-overview.md).

