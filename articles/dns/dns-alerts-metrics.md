---
title: Alertas e métricas DNS do Azure | Documentos da Microsoft
description: Saiba mais sobre o DNS do Azure métricas e alertas.
services: dns
documentationcenter: na
author: vhorne
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: victorh
ms.openlocfilehash: a9d8bc172eb5f5e0e119a0bde56fb167f7a0c2b2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699143"
---
# <a name="azure-dns-metrics-and-alerts"></a>Métricas DNS do Azure e alertas
O DNS do Azure é um serviço de alojamento dos domínios DNS que fornece resolução de nomes através da infraestrutura do Microsoft Azure. Este artigo descreve as métricas e alertas para o serviço de DNS do Azure.

## <a name="azure-dns-metrics"></a>Métricas DNS do Azure

O DNS do Azure fornece métricas para os clientes para ativá-las monitorizar aspetos específicos das suas zonas DNS alojadas no serviço. Além disso, com a métrica de DNS do Azure, pode configurar e receber alertas com base nas condições de interesse. As métricas são fornecidas através da [serviço do Azure Monitor](../azure-monitor/index.yml). O DNS do Azure fornece as métricas seguintes através do Azure Monitor para suas zonas DNS:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Também pode ver o [definição destas métricas](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) na página de documentação do Azure Monitor.
>[!NOTE]
> Neste momento, estas métricas só estão disponíveis para as zonas de DNS público alojadas no DNS do Azure. Se tiver as zonas privadas do alojada no DNS do Azure, estas métricas não fornecerá dados para essas zonas. Além disso, a métricas e a funcionalidade alerta só é suportado na cloud pública do Azure. Suporte de clouds soberanas seguirão posteriormente. 

O elemento mais granular, que pode ver as métricas de é uma zona DNS. Atualmente não é possível ver as métricas para os registos de recursos individuais dentro de uma zona.

### <a name="query-volume"></a>Volume de consultas

O *Volume de consultas* métrica no DNS do Azure mostra o volume de consultas DNS (tráfego de consulta) que é recebido pelo DNS do Azure para a sua zona DNS. A unidade de medida é a contagem e a agregação é o total de todas as consultas recebidas durante um período de tempo. 

Para ver esta métrica, selecione a experiência do Explorador de métricas (pré-visualização) do separador Monitor no portal do Azure. Selecione a sua zona DNS na lista suspensa de recursos, selecione a métrica de Volume de consulta e selecione a soma como a agregação. Captura de ecrã abaixo mostra um exemplo.  Para obter mais informações sobre o Explorador de métricas experiência e criação de gráficos, veja [Explorador de métricas do Azure Monitor](../azure-monitor/platform/metrics-charts.md).

![Volume de consultas](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Figura: Métricas de Volume de consultas de DNS do Azure*

### <a name="record-set-count"></a>Contagem de conjunto de registros
O *contagem de conjunto de registros* métrica mostra o número de conjuntos de registros no DNS do Azure para a sua zona DNS. Todos os conjuntos de registros definidos na sua zona são contabilizados. A unidade de medida é a contagem e a agregação é o número máximo de todos os conjuntos de registros. Para ver esta métrica, selecione **métricas (pré-visualização)** experiência explorer a partir do **Monitor** separador no portal do Azure. Selecione a sua zona DNS do **Resource** pendente, selecione o **contagem de conjunto de registros** métrica e, em seguida, selecione **máx.** como o **agregação** . Para obter mais informações sobre o Explorador de métricas experiência e criação de gráficos, veja [Explorador de métricas do Azure Monitor](../azure-monitor/platform/metrics-charts.md). 

![Contagem de conjunto de registros](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Figura: Métricas de contagem de conjunto de registros de DNS do Azure*


### <a name="record-set-capacity-utilization"></a>Utilização da capacidade do conjunto de registos
O *utilização da capacidade de definir registo* métrica no DNS do Azure mostra a percentagem de utilização da sua capacidade de conjunto de registros para uma zona DNS. Cada zona DNS no DNS do Azure está sujeito a um limite de conjunto de registros que define o número máximo de conjuntos de registros que têm permissão para a zona (veja [limites DNS](dns-zones-records.md#limits)). Por conseguinte, esta métrica mostra-lhe como fechar está a atingir o limite de conjunto de registros. Por exemplo, se tiver 500 conjuntos de registros configurados para a sua zona DNS e a zona tem o limite predefinido do conjunto de registros de 5000, a métrica de RecordSetCapacityUtilization irá mostrar o valor de 10% (o que é obtida pela divisória 500 por 5000). É a unidade de medida **percentagem** e o **agregação** é do tipo **máximo**. Para ver esta métrica, selecione a experiência do Explorador de métricas (pré-visualização) do separador Monitor no portal do Azure. Selecione a sua zona DNS na lista suspensa de recursos, selecione a métrica de utilização de capacidade do conjunto de registo e selecionar o máximo como a agregação. Captura de ecrã abaixo mostra um exemplo. Para obter mais informações sobre o Explorador de métricas experiência e criação de gráficos, veja [Explorador de métricas do Azure Monitor](../azure-monitor/platform/metrics-charts.md). 

![Contagem de conjunto de registros](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Figura: Métricas de conjunto de utilização de capacidade do registo DNS do Azure*

## <a name="alerts-in-azure-dns"></a>Alertas no DNS do Azure
O Azure Monitor fornece a capacidade de alerta em relação aos valores de métrica disponíveis. As métricas DNS estão disponíveis na nova experiência de configuração de alerta. Conforme descrito em detalhe no [documentação de alertas do Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md), selecione a zona DNS que o recurso, escolha o tipo de sinal de métrica e configurar a lógica de alerta e outros parâmetros, tais como **período**e **frequência**. Pode definir mais aprofundadamente uma [grupo de ação](../azure-monitor/platform/action-groups.md) para quando for cumprida a condição do alerta, por meio das quais o alerta será entregue por meio de ações escolhidas. Para obter mais informações sobre como configurar alertas de métricas do Azure Monitor, consulte [criar, ver e gerir alertas ao utilizar o Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [DNS do Azure](dns-overview.md).
