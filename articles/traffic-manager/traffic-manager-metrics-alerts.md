---
title: Métricas e alertas no Gerenciador de tráfego do Azure
description: Neste artigo, Aprenda as métricas e os alertas disponíveis para o Gerenciador de tráfego no Azure.
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: 7e374e11053f0a1baabed72fdb240b34e2107b2a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038072"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Métricas e alertas do Gerenciador de tráfego

O Gerenciador de tráfego fornece balanceamento de carga baseado em DNS que inclui vários métodos de roteamento e opções de monitoramento de ponto de extremidade. Este artigo descreve as métricas e os alertas associados que estão disponíveis para os clientes. 

## <a name="metrics-available-in-traffic-manager"></a>Métricas disponíveis no Gerenciador de tráfego 

O Gerenciador de tráfego fornece as seguintes métricas por perfil que os clientes podem usar para entender o uso do Gerenciador de tráfego e o status de seus pontos de extremidade sob esse perfil.  

### <a name="queries-by-endpoint-returned"></a>Consultas por ponto de extremidade retornadas
Use [essa métrica](../azure-monitor/platform/metrics-supported.md) para exibir o número de consultas processadas por um perfil do Gerenciador de tráfego durante um período especificado. Você também pode exibir as mesmas informações em uma granularidade de nível de ponto de extremidade que ajuda você a entender quantas vezes um ponto de extremidade foi retornado nas respostas de consulta do Gerenciador de tráfego.

No exemplo a seguir, a Figura 1 exibe todas as respostas de consulta que o perfil do Gerenciador de tráfego retorna. 

  
![Exibição agregada de todas as consultas](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Figura 1: exibição agregada com todas as consultas*
  
A Figura 2 exibe as mesmas informações, no entanto, é dividida por pontos de extremidade. Como resultado, você pode ver o volume de respostas de consulta no qual um ponto de extremidade específico foi retornado.

![Métricas do Gerenciador de tráfego-exibição dividida de volume de consulta por ponto de extremidade](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Figura 2: exibição dividida com o volume de consulta mostrado por ponto de extremidade retornado*

## <a name="endpoint-status-by-endpoint"></a>Status do ponto de extremidade por ponto de extremidade
Use [essa métrica](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) para entender o status de integridade dos pontos de extremidade no perfil. São necessários dois valores:
 - Use **1** se o ponto de extremidade estiver ativo.
 - Use **0** se o ponto de extremidade estiver inativo.

Essa métrica pode ser mostrada como um valor de agregação que representa o status de todas as métricas (Figura 3) ou pode ser dividida (consulte a Figura 4) para mostrar o status de pontos de extremidade específicos. Se o primeiro, se o nível de agregação for selecionado como **Méd**, o valor dessa métrica será a média aritmética do status de todos os pontos de extremidade. Por exemplo, se um perfil tiver dois pontos de extremidade e apenas um estiver íntegro, essa métrica terá um valor de **0,50** , como mostrado na Figura 3. 


![Métricas do Gerenciador de tráfego-exibição composta de status do ponto de extremidade](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Figura 3: exibição composta da métrica de status do ponto de extremidade – agregação "média" selecionada*


![Métricas do Gerenciador de tráfego-exibição dividida de status do ponto de extremidade](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Figura 4: exibição dividida de métricas de status do ponto de extremidade*

Você pode consumir essas métricas por meio do portal do [serviço Azure monitor](../azure-monitor/platform/metrics-supported.md), [API REST](https://docs.microsoft.com/rest/api/monitor/), [CLI do Azure](https://docs.microsoft.com/cli/azure/monitor)e [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights), ou por meio da seção métricas da experiência do portal do Gerenciador de tráfego.

## <a name="alerts-on-traffic-manager-metrics"></a>Alertas sobre métricas do Gerenciador de tráfego
Além de processar e exibir as métricas do Gerenciador de tráfego, Azure Monitor permite que os clientes configurem e recebam alertas associados a essas métricas. Você pode escolher quais condições precisam ser atendidas nessas métricas para que um alerta ocorra, com que frequência essas condições precisam ser monitoradas e como os alertas devem ser enviados a você. Para obter mais informações, consulte a [documentação de alertas do Azure monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [serviço de Azure monitor](../azure-monitor/platform/metrics-supported.md)
- Saiba como [criar um gráfico usando Azure monitor](../azure-monitor/platform/metrics-getting-started.md#create-your-first-metric-chart)
