---
title: Configure a monitorização do PV com insights do contentor | Microsoft Docs
description: Este artigo descreve como pode configurar agrupamentos de Kubernetes com volumes persistentes com insights de contentores.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 0afbeab49a6909a0011cd75a0419f7325ca68132
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713733"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Configure a monitorização do PV com insights do contentor

Começando pela versão do agente *ciprod10052020,* o agente integrado de insights de contentores suporta agora a monitorização do uso de PV (volume persistente).

## <a name="pv-metrics"></a>Métricas de PV

Os insights do recipiente iniciam automaticamente a monitorização do PV, recolhendo as seguintes métricas em intervalos de 60sec e armazenando-as na tabela **InsightMetrics.**

|Nome da métrica |Dimensão métrica (etiquetas) |Descrição |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Espaço usado em bytes para um volume persistente específico com uma reivindicação usada por uma vagem específica. `pvCapacityBytes` é dobrado como uma dimensão no campo Tags para reduzir o custo de ingestão de dados e simplificar as consultas.|

## <a name="monitor-persistent-volumes"></a>Monitorize volumes persistentes

Os insights do recipiente incluem gráficos pré-configurados para esta métrica num livro para cada cluster. Pode encontrar as tabelas no separador Volume Persistente do livro de **dados de Workload Details** diretamente de um cluster AKS, selecionando Livros de Trabalho a partir do painel de leitura à esquerda e da lista de drop-down do **View Workbooks** no Insight. Também pode ativar um alerta recomendado para a utilização de PV, bem como consultar estas métricas em Log Analytics.  

![Exemplo de livro de trabalho do Azure Monitor PV](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as métricas de PV recolhidas [aqui.](./container-insights-agent-config.md)
