---
title: Configure a monitorização do PV com insights do contentor | Microsoft Docs
description: Este artigo descreve como pode configurar agrupamentos de Kubernetes com volumes persistentes com insights de contentores.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 7c6ddd62bf06d313987289e444962378cea43fc8
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627902"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Configure a monitorização do PV com insights do contentor

A partir da versão do agente *ciprod10052020,* o Azure Monitor para contentores integrados agora suporta a monitorização do uso de PV (volume persistente). Com a versão do agente *ciprod01112021,* o agente suporta a monitorização do inventário de PV, incluindo informações sobre o estado, classe de armazenamento, tipo, modos de acesso e outros detalhes.
## <a name="pv-metrics"></a>Métricas de PV

Os insights do recipiente iniciam automaticamente a monitorização da utilização do PV, recolhendo as seguintes métricas em intervalos de 60 -seg e armazenando-as na tabela **InsightMetrics.**

| Nome da métrica | Dimensão métrica (etiquetas) | Descrição métrica |
|-----|-----------|----------|
| `pvUsedBytes`| podUID, podName, pvcName, pvcNamespace, capacityBytes, clusterId, clusterName| Espaço usado em bytes para um volume persistente específico com uma reivindicação usada por uma vagem específica. `capacityBytes` é dobrado como uma dimensão no campo Tags para reduzir o custo de ingestão de dados e simplificar as consultas.|

Saiba mais sobre a configuração das métricas de PV recolhidas [aqui.](./container-insights-agent-config.md)

## <a name="pv-inventory"></a>Inventário de PV

O Azure Monitor para contentores inicia automaticamente a monitorização dos PVs, recolhendo as seguintes informações em intervalos de 60 segundos e armazenando-os na tabela **KubePVInventory.**

|Dados |Origem de dados| Tipo de Dados| Campos|
|-----|-----------|----------|-------|
|Inventário de volumes persistentes num cluster Kubernetes |Kube API |`KubePVInventory` |    PVName, PVCapacityBytes, PVCName, PVCNamespace, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, TimeGenerated, ClusterId, ClusterName, _ResourceId |

## <a name="monitor-persistent-volumes"></a>Monitorize volumes persistentes

O Azure Monitor para contentores inclui gráficos pré-configurados para esta métrica de utilização e informações de inventário em modelos de livros para cada cluster. Também pode ativar um alerta recomendado para a utilização de PV e consultar estas métricas no Log Analytics.  

### <a name="workload-details-workbook"></a>Livro de trabalho de detalhes de carga de trabalho

Pode encontrar gráficos de utilização para cargas de trabalho específicas no separador Volume Persistente do livro **de dados de Dados** de Carga de Trabalho diretamente de um cluster AKS, selecionando livros de trabalho a partir do painel de trabalhos à esquerda, a partir da lista de drop-down dos Livros de **Visualização** no painel Insights ou do **separador Relatórios (pré-visualização)** no painel insights.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Exemplo de livro de trabalho do Azure Monitor PV":::

### <a name="persistent-volume-details-workbook"></a>Livro de detalhes de volume persistente

Pode encontrar uma visão geral do inventário de volume persistente no livro de detalhes de **volume persistente** diretamente de um cluster AKS selecionando Livros de Trabalho a partir do painel de leitura à esquerda, a partir da lista de drop-down dos livros de visualização no painel insights, ou do **separador Relatórios (pré-visualização)** no painel Insights. 


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Azure Monitor PV detalha exemplo de livro":::

### <a name="persistent-volume-usage-recommended-alert"></a>Alerta recomendado de utilização do volume persistente
Pode ativar um alerta recomendado para alertá-lo quando o uso médio de PV para uma cápsula é superior a 80%. Saiba mais sobre o alerta [aqui](./container-insights-metric-alerts.md) e como anular o limiar padrão [aqui](./container-insights-metric-alerts.md#configure-alertable-metrics-in-configmaps).
## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as métricas de PV recolhidas [aqui.](./container-insights-agent-config.md)