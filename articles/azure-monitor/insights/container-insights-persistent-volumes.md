---
title: Configure a monitorização do PV com o Monitor Azure para contentores | Microsoft Docs
description: Este artigo descreve como pode configurar a monitorização dos aglomerados de Kubernetes com volumes persistentes com o Azure Monitor para contentores.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: d7da6bc88e7c8526e3940714502d3c92d2f37dd8
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704478"
---
# <a name="configure-pv-monitoring-with-azure-monitor-for-containers"></a>Configure a monitorização do PV com o Monitor Azure para contentores

A partir da versão do agente *ciprod10052020,* o Azure Monitor para contentores integrados agora suporta a monitorização do uso de PV (volume persistente).

## <a name="pv-metrics"></a>Métricas de PV

O Azure Monitor para recipientes inicia automaticamente a monitorização do PV, recolhendo as seguintes métricas em intervalos de 60sec e armazenando-os na tabela **InsightMetrics.**

|Nome da métrica |Dimensão métrica (etiquetas) |Descrição |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Espaço usado em bytes para um volume persistente específico com uma reivindicação usada por uma vagem específica. `pvCapacityBytes` é dobrado como uma dimensão no campo Tags para reduzir o custo de ingestão de dados e simplificar as consultas.|

## <a name="monitor-persistent-volumes"></a>Monitorize volumes persistentes

O Azure Monitor para recipientes inclui gráficos pré-configurados para esta métrica num livro para cada cluster. Pode encontrar as tabelas no separador Volume Persistente do livro de **dados de Workload Details** diretamente de um cluster AKS, selecionando Livros de Trabalho a partir do painel de leitura à esquerda e da lista de drop-down do **View Workbooks** no Insight. Também pode ativar um alerta recomendado para a utilização de PV, bem como consultar estas métricas em Log Analytics.  

![Exemplo de livro de trabalho do Azure Monitor PV](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre as métricas de PV recolhidas [aqui.](./container-insights-agent-config.md)
