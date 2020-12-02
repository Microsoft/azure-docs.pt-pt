---
title: Analisar padrões de utilização da CDN do Azure
description: Este artigo descreve os diferentes tipos de relatórios de análise disponíveis para os produtos Azure CDN.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2020
ms.author: allensu
ms.openlocfilehash: 2c593cda7761ce1defa6bdb31e0dbb528b9e5eca
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483993"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analisar padrões de utilização da CDN do Azure

Depois de ativar a CDN para a sua aplicação, pode monitorizar a utilização do CDN, verificar a saúde da sua entrega e resolver potenciais problemas. A Azure CDN fornece estas capacidades das seguintes formas: 

## <a name="raw-logs-for-azure-cdn-from-microsoft"></a>Registos em bruto para Azure CDN da Microsoft
Com um perfil padrão da Microsoft, pode ativar registos em bruto e selecionar para transmitir registos para:

* Storage do Azure
* Hubs de Eventos
* Azure Log Analytics

Com o Azure Log Analytics pode visualizar métricas de monitorização e configurar alertas. 

Para obter mais informações, consulte [os registos crus Azure CDN HTTP](monitoring-and-access-log.md).


## <a name="core-analytics-via-azure-diagnostic-logs"></a>Análise do núcleo através de registos de diagnóstico Azure

A análise do núcleo está disponível para pontos finais cdN para todos os níveis de preços. Os registos de diagnóstico azure permitem que a análise do núcleo seja exportada para armazenamento Azure, centros de eventos ou registos do Azure Monitor. Os registos do Azure Monitor oferecem uma solução com gráficos que são configuráveis e personalizáveis. Para obter mais informações sobre os registos de diagnóstico [Azure, consulte os registos de diagnóstico do Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Relatórios centrais de Verizon

**O Azure CDN Standard de Verizon** ou **Azure CDN Premium dos** perfis Verizon fornece relatórios principais. Pode ver relatórios principais no portal suplementar de Verizon. Os relatórios centrais de Verizon são acessíveis através da opção **Manage** a partir do portal Azure e oferecem diferentes tipos de gráficos e vistas. Para obter mais informações, consulte [relatórios core de Verizon.](cdn-analyze-usage-patterns.md)

## <a name="verizon-custom-reports"></a>Relatórios personalizados de Verizon

**O Azure CDN Standard da Verizon** ou **Azure CDN Premium dos** perfis Verizon fornece relatórios personalizados. Pode ver relatórios personalizados no portal suplementar Verizon. Os relatórios personalizados da Verizon são acessíveis através da opção **Gerir** a partir do portal Azure. 

Os relatórios personalizados mostram o número de acessos ou dados transferidos para cada borda CNAME. Os dados são agrupados pelo código de resposta HTTP ou estatuto de cache durante o período de tempo. Para mais informações, consulte [Relatórios Personalizados da Verizon.](cdn-verizon-custom-reports.md)

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium dos relatórios da Verizon

Com **o Azure CDN Premium da Verizon,** também pode aceder aos seguintes relatórios:
   * [Relatórios HTTP Avançados](cdn-advanced-http-reports.md)
   * [Estatísticas em tempo real](cdn-real-time-stats.md)
   * [Desempenho do nó de borda Azure CDN](cdn-edge-performance.md)

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu sobre as diferentes opções para relatórios de análise para a Azure CDN.

Para obter mais informações sobre a Azure CDN e os outros serviços Azure mencionados neste artigo, consulte:

* [O que é o Azure CDN?](cdn-overview.md)
* [Logs crus Azure CDN HTTP](monitoring-and-access-log.md)
