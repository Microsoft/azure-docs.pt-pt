---
title: Fontes de dados de Azure Monitor pastas de trabalho | Microsoft docs
description: Simplifique relatórios complexos com pastas de trabalho Azure Monitor parametrizadas predefinidas e personalizadas criadas com base em várias fontes de dados
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 368da2baaea1be41331d77f4dae1bdd9567b8cc5
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872898"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Fontes de dados de Azure Monitor pastas de trabalho

As pastas de trabalho são compatíveis com um grande número de fontes de dados. Este artigo explicará as fontes de dados que estão disponíveis atualmente para Azure Monitor pastas de trabalho.

## <a name="logs"></a>Registos

As pastas de trabalho permitem consultar logs das seguintes fontes:

* Logs de Azure Monitor (recursos de Application Insights e espaços de trabalho de Log Analytics.)
* Dados centrados em recursos (logs de atividade)

Os autores da pasta de trabalho podem usar consultas KQL que transformam os dados de recurso subjacentes para selecionar um conjunto de resultados que pode ser visualizado como texto, gráficos ou grades.

![Captura de tela da interface de relatório de logs de pastas de trabalho](./media/workbooks-overview/logs.png)

Os autores da pasta de trabalho podem facilmente consultar em vários recursos, criando uma experiência de relatório sofisticada verdadeiramente unificada.

## <a name="metrics"></a>Métricas

Os recursos do Azure emitem [métricas](data-platform-metrics.md) que podem ser acessadas via pastas de trabalho. As métricas podem ser acessadas em pastas de trabalho por meio de um controle especializado que permite especificar os recursos de destino, as métricas desejadas e sua agregação. Esses dados podem então ser plotados em gráficos ou grades.

![Captura de tela de gráficos de métricas de pasta de trabalho de utilização da CPU](./media/workbooks-overview/metrics-graph.png)

![Captura de tela da interface de métricas da pasta de trabalho](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

As pastas de trabalho dão suporte à consulta de recursos e seus metadados usando o grafo de recursos do Azure (ARG). Essa funcionalidade é usada principalmente para criar escopos de consulta personalizados para relatórios. O escopo do recurso é expresso por meio de um subconjunto de KQL com suporte de ARG, que geralmente é suficiente para casos de uso comuns.

Para fazer com que um controle de consulta Use essa fonte de dados, use a lista suspensa tipo de consulta para escolher o grafo de recursos do Azure e selecione as assinaturas a serem direcionadas. Use o controle de consulta para adicionar o ARG KQL-subconjunto que seleciona um subconjunto de recursos interessante.


![Captura de tela da consulta KQL do grafo de recursos do Azure](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Alertas (versão prévia)

As pastas de trabalho permitem que os usuários visualizem os alertas ativos relacionados aos seus recursos. Esse recurso permite a criação de relatórios que reúnem dados de notificação (alerta) e informações de diagnóstico (métricas, logs) em um relatório. Essas informações também podem ser Unidas para criar relatórios avançados que combinam informações entre essas fontes de dados.

Para fazer com que um controle de consulta Use essa fonte de dados, use a lista suspensa tipo de consulta para escolher alertas e selecionar as assinaturas, grupos de recursos ou recursos para o destino. Use os menus suspensos de filtro de alerta para selecionar um subconjunto interessante de alertas para suas necessidades analíticas.

![Captura de tela da consulta de alertas](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Integridade da carga de trabalho (versão prévia)

Azure Monitor tem funcionalidade que monitora de forma proativa a disponibilidade e o desempenho de sistemas operacionais convidados Windows ou Linux. O Azure Monitor modela os principais componentes e suas relações, critérios de como medir a integridade desses componentes e quais componentes o alertam quando uma condição não íntegra é detectada. As pastas de trabalho permitem que os usuários usem essas informações para criar relatórios interativos sofisticados.

Para fazer com que um controle de consulta Use essa fonte de dados, use a lista suspensa **tipo de consulta** para escolher integridade da carga de trabalho e selecione assinatura, grupo de recursos ou recursos de VM para destino. Use os menus suspensos de filtro de integridade para selecionar um subconjunto interessante de incidentes de integridade para suas necessidades analíticas.

![Captura de tela da consulta de alertas](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure Resource Health 

As pastas de trabalho dão suporte à obtenção do Azure Resource Health e à combinação deles com outras fontes de dados para criar relatórios de integridade avançados e interativos

Para fazer com que um controle de consulta Use essa fonte de dados, use a lista suspensa **tipo de consulta** para escolher a integridade do Azure e selecione os recursos para direcionar. Use os menus suspensos de filtro de integridade para selecionar um subconjunto interessante de problemas de recursos para suas necessidades analíticas.

![Captura de tela da consulta de alertas](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Data Explorer do Azure (versão prévia)

As pastas de trabalho agora têm suporte para a consulta de clusters do [Azure data Explorer](https://docs.microsoft.com/azure/data-explorer/) com a poderosa linguagem de consulta [Kusto](https://docs.microsoft.com/azure/kusto/query/index) .   

![Captura de tela da janela de consulta do Kusto](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Passos seguintes

* [Comece a aprender mais](workbooks-visualizations.md) sobre pastas de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da pasta de trabalho.
