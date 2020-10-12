---
title: Observabilidade na Gestão API da Azure / Microsoft Docs
description: Visão geral de todas as opções de observabilidade na Azure API Management.
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: apimpm
ms.openlocfilehash: 1ebebed465952bbb5d3e8f82ae1c7776c441c6b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097824"
---
# <a name="observability-in-azure-api-management"></a>Observabilidade na Gestão API da Azure

A visibilidade é a capacidade de compreender o estado interno de um sistema a partir dos dados que produz e a capacidade de explorar esses dados para responder a perguntas sobre o que aconteceu e porquê. 

A Azure API Management ajuda as organizações a centralizar a gestão de todas as APIs. Uma vez que serve como um único ponto de entrada de todo o tráfego da API, é um local ideal para observar as APIs. 

## <a name="observability-tools"></a>Ferramentas de observabilidade

O quadro abaixo resume todas as ferramentas suportadas pela API Management para observar APIs, cada uma é útil para um ou mais cenários:

| Ferramenta        | Útil para    | Atraso de dados | Retenção | Amostragem | Tipo de dados | Ativado|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[Inspetor da API](api-management-howto-api-inspector.md)** | Teste e depuração | Instantâneo | Os últimos 100 vestígios | Ligado por pedido | Solicitar vestígios | Sempre
| **Analytics incorporado** | Relatórios e monitorização | Minutos | Vida útil | 100% | Relatórios e registos | Sempre |
| **[Métricas do Monitor Azure](api-management-howto-use-azure-monitor.md)** | Relatórios e monitorização | Minutos | 93 dias (upgrade para prolongar) | 100% | Métricas | Sempre |
| **[Registos do Azure Monitor](api-management-howto-use-azure-monitor.md)** | Reportagem, monitorização e depuragem | Minutos | 31 dias/5GB (upgrade para prolongar) | 100% (ajustável) | Registos | Opcional |
| **[Azure Application Insights](api-management-howto-app-insights.md)** | Reportagem, monitorização e depuragem | Segundos | 90 dias/5GB (upgrade para prolongar) | Personalizado | Troncos, métricas | Opcional |
| **[Iniciar sessão através do Azure Event Hub](api-management-howto-log-event-hubs.md)** | Cenários personalizados | Segundos | Gerido pelo utilizador | Personalizado | Personalizado | Opcional |

### <a name="self-hosted-gateway"></a>Gateway auto-hospedado

Todas as ferramentas acima mencionadas são suportadas pelo gateway gerido na nuvem. O [gateway auto-hospedado](self-hosted-gateway-overview.md) atualmente não envia registos de diagnóstico para o Azure Monitor. No entanto, é possível configurar e persistir registos localmente onde o gateway auto-hospedado é implantado. Para mais informações, consulte [as métricas e registos de nuvem configurados para obter gateway auto-hospedado](how-to-configure-cloud-metrics-logs.md) e [configurar métricas e registos locais para porta de entrada auto-hospedada.](how-to-configure-local-metrics-logs.md)

## <a name="next-steps"></a>Passos Seguintes

* [Siga os tutoriais para saber mais sobre a API Management](import-and-publish.md)
