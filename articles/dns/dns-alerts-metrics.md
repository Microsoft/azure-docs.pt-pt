---
title: Métricas e alertas - Azure DNS
description: Com este caminho de aprendizagem, inicie com métricas e alertas De DNS Azure.
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
ms.openlocfilehash: 42acbc0d32b3ce5de4befcf112b68f611ad70542
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937443"
---
# <a name="azure-dns-metrics-and-alerts"></a>Métricas e alertas de DNS azure
O Azure DNS é um serviço de hospedagem para domínios DNS que fornece resolução de nome utilizando a infraestrutura Microsoft Azure. Este artigo descreve métricas e alertas para o serviço Azure DNS.

## <a name="azure-dns-metrics"></a>Métricas De NsD azure

O Azure DNS fornece métricas para os clientes permitirem monitorizar aspetos específicos das suas zonas de DNS alojadas no serviço. Além disso, com as métricas DNS Azure, pode configurar e receber alertas com base em condições de interesse. As métricas são fornecidas através do [serviço Azure Monitor](../azure-monitor/index.yml). O Azure DNS fornece as seguintes métricas via Monitor Azure para as suas zonas DNS:

-   Volume de Consultas
-   RecordSetCount
-   Utilização de RecordSetCapacity

Pode também ver a [definição destas métricas](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) na página de documentação do Monitor Azure.
>[!NOTE]
> Neste momento, estas métricas só estão disponíveis para zonas públicas de DNS alojadas em DNS Azure. Se tiver zonas privadas alojadas em DNS Azure, estas métricas não fornecerão dados para essas zonas. Além disso, as métricas e a funcionalidade de alerta só são suportadas na nuvem pública de Azure. O apoio às nuvens soberanas seguir-se-á mais tarde. 

O elemento mais granular para o qual se pode ver métricas é uma zona DeDNs. Não é possível ver atualmente métricas para registos individuais de recursos dentro de uma zona.

### <a name="query-volume"></a>Volume de consulta

A métrica *do Volume de Consulta* em DNS Azure mostra o volume de consultas dNS (tráfego de consulta) que é recebida pelo Azure DNS para a sua zona DNS. A unidade de medição é count e a agregação é o total de todas as consultas recebidas ao longo de um período de tempo. 

Para visualizar esta métrica, selecione a experiência do explorador de Métricas (pré-visualização) a partir do separador Monitor no portal Azure. Selecione a sua zona DNS a partir da queda do Recurso, selecione a métrica do Volume de Consulta e selecione Soma como a agregação. Abaixo a imagem mostra um exemplo.  Para obter mais informações sobre a experiência e gráfico do Metrics Explorer, consulte [o Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md).

![Volume de consulta](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Figura: Métricas de volume de consulta DNS azure*

### <a name="record-set-count"></a>Contagem de conjuntos de discos
A métrica *record set count* mostra o número de Conjuntos de Registos em DNS Azure para a sua zona DNS. Todos os Recordsets definidos na sua zona são contados. A unidade de medição é count e a agregação é o máximo de todos os Conjuntos de Registos. Para visualizar esta métrica, selecione a experiência do explorador de **Métricas (pré-visualização)** a partir do separador **Monitor** no portal Azure. Selecione a sua zona DNS a partir da queda do **Recurso,** selecione a métrica de contagem de conjuntos de **registos** e, em seguida, selecione **Max** como a **agregação**. Para obter mais informações sobre a experiência e gráfico do Metrics Explorer, consulte [o Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md). 

![Contagem de conjuntos de discos](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Figura: Métricas de contagem de recordes do DNS azure*


### <a name="record-set-capacity-utilization"></a>Utilização da capacidade do conjunto de registos
A métrica de *utilização* da capacidade do conjunto de discos em DNS Azure mostra a percentagem de utilização da sua capacidade de Recordset para uma Zona DNS. Todas as zonas de DNS em Azure DNS estão sujeitas a um limite de Recordset que define o número máximo de Conjuntos de Registos que são permitidos para a Zona (ver [limites dNS).](dns-zones-records.md#limits) Por isso, esta métrica mostra-lhe o quão perto está de atingir o limite de Recordset. Por exemplo, se tiver 500 Recordsets configurados para a sua zona DNS, e a zona tiver o limite de registo padrão de 5000, a métrica de Utilização de Capacidade seleções do RecordSetcapacity mostrará o valor de 10% (que é obtido dividindo 500 por 5000). O aparelho de medição é **Percentual** e o tipo **de agregação** é **máximo**. Para visualizar esta métrica, selecione a experiência do explorador de Métricas (pré-visualização) a partir do separador Monitor no portal Azure. Selecione a sua zona DNS a partir da queda do Recurso, selecione a métrica de utilização da capacidade do conjunto de registos e selecione Max como a agregação. Abaixo a imagem mostra um exemplo. Para obter mais informações sobre a experiência e gráfico do Metrics Explorer, consulte [o Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md). 

![Contagem de conjuntos de discos](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Figura: Métricas de utilização da capacidade do conjunto de recordes do DNS do Azure*

## <a name="alerts-in-azure-dns"></a>Alertas em DNS Azure
O Azure Monitor fornece a capacidade de alertar contra os valores métricos disponíveis. As métricas DNS estão disponíveis na nova experiência de configuração do Alerta. Conforme descrito em detalhe na documentação de alertas do [Monitor Azure,](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)pode selecionar a Zona DNS como recurso, escolher o tipo de sinal métrico e configurar a lógica de alerta e outros parâmetros, como **period** e **frequência.** Pode ainda definir um [Grupo de Ação](../azure-monitor/platform/action-groups.md) para quando a condição de alerta for satisfeita, através da qual o alerta será emitido através das ações escolhidas. Para obter mais informações sobre como configurar o alerta para as métricas do Monitor Azure, consulte [Criar, visualizar e gerir alertas utilizando o Monitor Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o Azure DNS.](dns-overview.md)
