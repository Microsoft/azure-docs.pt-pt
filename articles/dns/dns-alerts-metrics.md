---
title: Métricas e alertas - Azure DNS
description: Com este caminho de aprendizagem, começa com métricas e alertas dns do Azure.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: rohink
ms.openlocfilehash: 391109727877544a4e94bae376ecef4d33a13cfb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575279"
---
# <a name="azure-dns-metrics-and-alerts"></a>Métricas e alertas de DNS do Azure
O Azure DNS é um serviço de hospedagem para domínios DNS que fornece resolução de nomes utilizando a infraestrutura Microsoft Azure. Este artigo descreve métricas e alertas para o serviço Azure DNS.

## <a name="azure-dns-metrics"></a>Métricas de DNS de Azure

O Azure DNS fornece métricas para que os clientes lhes permitam monitorizar aspetos específicos das suas zonas de DNS hospedadas no serviço. Além disso, com métricas Azure DNS, pode configurar e receber alertas com base em condições de interesse. As métricas são fornecidas através do [serviço Azure Monitor](../azure-monitor/index.yml). O Azure DNS fornece as seguintes métricas via Azure Monitor para as suas zonas DNS:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilização

Também pode ver a [definição destas métricas](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkdnszones) na página de documentação do Monitor Azure.
>[!NOTE]
> Neste momento, estas métricas só estão disponíveis para as zonas de DNS públicos acolhidas no DNS do Azure. Se tiver Zonas Privadas hospedadas no Azure DNS, estas métricas não fornecerão dados para essas zonas. Além disso, as métricas e a funcionalidade de alerta só são suportadas na nuvem pública de Azure. O apoio às nuvens soberanas seguir-se-á mais tarde. 

O elemento mais granular que se pode ver é uma zona de DNS. Não é possível ver métricas para registos individuais de recursos dentro de uma zona.

### <a name="query-volume"></a>Volume de consulta

A métrica *de Volume de Consulta* em Azure DNS mostra o volume de consultas DNS (tráfego de consulta) que é recebido pelo Azure DNS para a sua zona de DNS. A unidade de medição é o Conde e a agregação é o total de todas as consultas recebidas ao longo de um período de tempo. 

Para visualizar esta métrica, selecione Experiência de explorador métrica (pré-visualização) a partir do separador Monitor no portal Azure. Selecione a sua zona DNS a partir da redução de recursos, selecione a métrica de Volume de Consulta e selecione Sum como agregação. A imagem abaixo mostra um exemplo.  Para obter mais informações sobre a experiência e o charting do Metrics Explorer, consulte [o Azure Monitor Metrics Explorer](../azure-monitor/essentials/metrics-charts.md).

![Volume de consulta](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Figura: Métricas de volume de consulta Azure DNS*

### <a name="record-set-count"></a>Contagem de recordes
A métrica *Record set Count* mostra o número de Recordsets no Azure DNS para a sua zona de DNS. Todos os Registos definidos na sua zona são contados. A unidade de medição é o Conde e a agregação é o máximo de todos os Recordsets. Para visualizar esta métrica, selecione Experiência de explorador **métrica (pré-visualização)** a partir do **separador Monitor** no portal Azure. Selecione a sua zona DNS a partir da redução de **recursos,** selecione a métrica **contagem de registos** e, em seguida, selecione **Max** como **a agregação**. Para obter mais informações sobre a experiência e o charting do Metrics Explorer, consulte [o Azure Monitor Metrics Explorer](../azure-monitor/essentials/metrics-charts.md). 

![Contagem de recordes](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Figura: Azure DNS Record set Count Metrics*


### <a name="record-set-capacity-utilization"></a>Record Fixo Utilização da Capacidade
A métrica *de utilização da capacidade de fixação de registos* em Azure DNS mostra a percentagem de utilização da sua capacidade de Recordset para uma Zona DE DNS. Todas as zonas DNS em Azure DNS estão sujeitas a um limite de Recordset que define o número máximo de Conjuntos de Registos que são permitidos para a Zona (ver [limites DNS).](dns-zones-records.md#limits) Por isso, esta métrica mostra-lhe o quão perto está de atingir o limite de Recordset. Por exemplo, se tiver 500 Conjuntos de Registos configurados para a sua zona de DNS, e a zona tiver o limite de Registos padrão de 5000, a métrica de RegistosSetCapacityUtilization mostrará o valor de 10% (que é obtido dividindo 500 por 5000). A unidade de medição é **percentagem** e o tipo **de agregação** é **máximo**. Para visualizar esta métrica, selecione Experiência de explorador métrica (pré-visualização) a partir do separador Monitor no portal Azure. Selecione a sua zona DNS a partir da redução de recursos, selecione a métrica de utilização da capacidade de registo e selecione Max como a agregação. A imagem abaixo mostra um exemplo. Para obter mais informações sobre a experiência e o charting do Metrics Explorer, consulte [o Azure Monitor Metrics Explorer](../azure-monitor/essentials/metrics-charts.md). 

![Screenshot que whows um exemplo de como ver métricas.](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Figura: Azure DNS Record set Capacidade Utilização métricas*

## <a name="alerts-in-azure-dns"></a>Alertas em DNS Azure
O Azure Monitor fornece a capacidade de alertar contra os valores métricos disponíveis. As métricas DNS estão disponíveis na nova experiência de configuração de Alerta. Conforme descrito em detalhe na documentação de alertas do [Monitor Azure,](../azure-monitor/alerts/alerts-metric.md)pode selecionar a Zona DNS como recurso, escolher o tipo de sinal métrico e configurar a lógica de alerta e outros parâmetros como **Period** e **Frequência**. Pode ainda definir um [Grupo de Ação](../azure-monitor/alerts/action-groups.md) para quando a condição de alerta for cumprida, pelo que o alerta será entregue através das ações escolhidas. Para obter mais informações sobre como configurar alertas para métricas do Monitor Azure, consulte [Criar, ver e gerir alertas usando o Azure Monitor](../azure-monitor/alerts/alerts-metric.md). 

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o Azure DNS](dns-overview.md).