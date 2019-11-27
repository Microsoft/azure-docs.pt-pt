---
title: Métricas e alertas-DNS do Azure
description: Com este roteiro de aprendizagem, comece com as métricas e os alertas de DNS do Azure.
services: dns
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: allensu
ms.openlocfilehash: dc4d7de3d235fcdaf4a7f681065ba6e2857eb2ce
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212404"
---
# <a name="azure-dns-metrics-and-alerts"></a>Métricas e alertas de DNS do Azure
O DNS do Azure é um serviço de hospedagem para domínios DNS que fornece resolução de nomes usando a infraestrutura de Microsoft Azure. Este artigo descreve as métricas e os alertas para o serviço DNS do Azure.

## <a name="azure-dns-metrics"></a>Métricas de DNS do Azure

O DNS do Azure fornece métricas para os clientes permitirem que monitorem aspectos específicos de suas zonas DNS hospedadas no serviço. Além disso, com as métricas de DNS do Azure, você pode configurar e receber alertas com base em condições de interesse. As métricas são fornecidas por meio do [serviço de Azure monitor](../azure-monitor/index.yml). O DNS do Azure fornece as seguintes métricas por meio de Azure Monitor para suas zonas DNS:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Você também pode ver a [definição dessas métricas](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) na página de documentação Azure monitor.
>[!NOTE]
> Neste momento, essas métricas só estão disponíveis para zonas DNS públicas hospedadas no DNS do Azure. Se você tiver zonas privadas hospedadas no DNS do Azure, essas métricas não fornecerão dados para essas zonas. Além disso, o recurso de métricas e alertas só tem suporte na nuvem pública do Azure. O suporte para nuvens soberanas será seguido em um momento posterior. 

O elemento mais granular para o qual você pode ver as métricas é uma zona DNS. No momento, não é possível ver métricas para registros de recursos individuais em uma zona.

### <a name="query-volume"></a>Volume de consulta

A métrica *volume de consulta* no DNS do Azure mostra o volume de consultas DNS (tráfego de consulta) que é recebido pelo DNS do Azure para sua zona DNS. A unidade de medida é a contagem e a agregação é o total de todas as consultas recebidas durante um período de tempo. 

Para exibir essa métrica, selecione experiência do Gerenciador de métricas (versão prévia) na guia monitorar na portal do Azure. Selecione a zona DNS na lista suspensa recurso, selecione a métrica volume de consulta e selecione soma como a agregação. A captura de tela abaixo mostra um exemplo.  Para obter mais informações sobre a experiência de Metrics Explorer e o gráfico, consulte [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md).

![Volume de consulta](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Figura: métricas de volume de consulta de DNS do Azure*

### <a name="record-set-count"></a>Contagem de conjuntos de registros
A métrica *contagem de conjunto de registros* mostra o número de conjuntos de registros no DNS do Azure para sua zona DNS. Todos os conjuntos de registros definidos em sua zona são contados. A unidade de medida é a contagem e a agregação é o máximo de todos os conjuntos de registros. Para exibir essa métrica, selecione experiência do Gerenciador de **métricas (versão prévia)** na guia **monitorar** na portal do Azure. Selecione a zona DNS na lista suspensa **recurso** , selecione a métrica **contagem de conjunto de registros** e, em seguida, selecione **máximo** como a **agregação**. Para obter mais informações sobre a experiência de Metrics Explorer e o gráfico, consulte [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md). 

![Contagem de conjuntos de registros](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Figura: métricas de contagem do conjunto de registros DNS do Azure*


### <a name="record-set-capacity-utilization"></a>Utilização da capacidade do conjunto de registros
A métrica de *utilização da capacidade do conjunto de registros* no DNS do Azure mostra a porcentagem de utilização da capacidade do conjunto de registros para uma zona DNS. Cada zona DNS no DNS do Azure está sujeita a um limite de conjunto de registros que define o número máximo de conjuntos de registros permitidos para a zona (consulte [limites de DNS](dns-zones-records.md#limits)). Portanto, essa métrica mostra o quão perto você está atingindo o limite do conjunto de registros. Por exemplo, se você tiver 500 conjuntos de registros configurados para sua zona DNS e a zona tiver o limite de conjunto de registros padrão de 5000, a métrica RecordSetCapacityUtilization mostrará o valor de 10% (que é obtido dividindo 500 por 5000). A unidade de medida é **percentual** e o tipo de **agregação** é **máximo**. Para exibir essa métrica, selecione experiência do Gerenciador de métricas (versão prévia) na guia monitorar na portal do Azure. Selecione a zona DNS na lista suspensa recurso, selecione a métrica de utilização da capacidade do conjunto de registros e selecione máximo como a agregação. A captura de tela abaixo mostra um exemplo. Para obter mais informações sobre a experiência de Metrics Explorer e o gráfico, consulte [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md). 

![Contagem de conjuntos de registros](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Figura: as métricas de utilização da capacidade do conjunto de registros DNS do Azure*

## <a name="alerts-in-azure-dns"></a>Alertas no DNS do Azure
Azure Monitor fornece a capacidade de alertar em relação aos valores de métrica disponíveis. As métricas de DNS estão disponíveis na nova experiência de configuração de alerta. Conforme descrito em detalhes na [documentação de alertas de Azure monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md), você pode selecionar a zona DNS como o recurso, escolher o tipo de sinal de métrica e configurar a lógica de alerta e outros parâmetros, como **período** e **frequência**. Você pode definir ainda mais um [grupo de ações](../azure-monitor/platform/action-groups.md) para quando a condição de alerta for atendida, na qual o alerta será entregue por meio das ações escolhidas. Para obter mais informações sobre como configurar alertas para métricas de Azure Monitor, consulte [criar, exibir e gerenciar alertas usando Azure monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [DNS do Azure](dns-overview.md).
