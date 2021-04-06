---
title: Utilize análises API em Azure API Management | Microsoft Docs
description: Utilize a análise na Azure API Management para ajudá-lo a compreender e categorizar o uso das suas APIs e desempenho de API.
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: ca7bd70bbf99a6d0079717a7a02328b11528d2e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96841512"
---
# <a name="get-api-analytics-in-azure-api-management"></a>Obtenha análises API na Azure API Management

A Azure API Management fornece análises incorporadas para as suas APIs. Analise a utilização e desempenho das APIs no seu caso de Gestão de API em várias dimensões, incluindo:

* Hora
* Geografia
* APIs
* Operações da API
* Produtos
* Subscrições
* Utilizadores
* Pedidos

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="Analítica da linha do tempo no portal":::

Utilize análises para monitorização de alto nível e resolução de problemas das suas APIs. Para funcionalidades de monitorização adicionais, incluindo métricas próximas em tempo real e registos de recursos para diagnósticos e auditorias, consulte [Tutorial: Monitor publicado APIs](api-management-howto-use-azure-monitor.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>Analytics - portal

Utilize o portal Azure para rever os dados de análise num ápice para a sua instância de Gestão de API.

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API. 
1. No menu da esquerda, em **Monitorização,** selecione **Analytics**.

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="Selecione análise para instância de Gestão de API no portal":::  
1. Selecione um intervalo de tempo para dados ou introduza um intervalo de tempo personalizado.
1. Selecione uma categoria de relatório para dados de análise, tais como **Timeline,** **Geografia,** e assim por diante.
1. Opcionalmente, filtrar o relatório por uma ou mais categorias adicionais.

## <a name="analytics---rest-api"></a>Analytics - REST API

Utilizar operações [de Relatórios](/rest/api/apimanagement/2019-12-01/reports) na API Management REST API para recuperar e filtrar dados de análise para o seu caso de Gestão de API.

Registos de relatórios de retorno de operações disponíveis por API, geografia, operações de API, produto, pedido, subscrição, tempo ou utilizador.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução às funcionalidades do Azure Monitor na Gestão da API, consulte [Tutorial: Monitor publicado APIs](api-management-howto-use-azure-monitor.md)
* Para obter registos e monitorização http detalhados, consulte [as suas APIs com Azure API Management, Event Hubs e Moesif](api-management-log-to-eventhub-sample.md).
* Saiba como integrar a [Azure API Management com Azure Application Insights](api-management-howto-app-insights.md).