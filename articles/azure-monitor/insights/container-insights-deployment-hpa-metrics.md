---
title: Implantação & métricas HPA com Monitor Azure para contentores Microsoft Docs
description: Este artigo descreve que métricas de implantação & HPA (autoscaler da cápsula horizontal) são recolhidas com o Azure Monitor para contentores.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: ee1f0d4849a8382a898aaca84956ff78166e138f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89570532"
---
# <a name="deployment--hpa-metrics-with-azure-monitor-for-containers"></a>Implantação & métricas HPA com Monitor Azure para contentores

Começando pela versão do agente *ciprod08072020,* o monitor Azure para o agente integrado em contentores recolhe agora métricas para implementações & HPAs.

## <a name="deployment-metrics"></a>Métricas de implantação

O Monitor azul para contentores inicia automaticamente a monitorização das Implementações, recolhendo as seguintes métricas em intervalos de 60 segundos e armazenando-as na tabela **InsightMetrics:**

|Nome da métrica |Dimensão métrica (etiquetas) |Descrição |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, criaçãoTime, implantação, implantaçãoStragia, k8sNamespace, spec_replicas, status_replicas_available, status_replicas_updated (status.updatedReplicas) | Número total de cápsulas prontas direcionadas para esta implantação (status.readyReplicas). Abaixo estão as dimensões desta métrica. <ul> <li> implantação - nome da implantação </li> <li> k8sNamespace - Kubernetes espaço de nome para a implantação </li> <li> implementaçãoStrategia - Estratégia de implantação para substituir cápsulas por novas (spec.strategy.type)</li><li> creationTime - tempo de criação de implantação </li> <li> spec_replicas - Número de cápsulas desejadas (spec.replicas) </li> <li>status_replicas_available - Número total de cápsulas disponíveis (prontas para pelo menos minReadySeconds) direcionadas para esta implantação (status.availableReplicas)</li><li>status_replicas_updated - Número total de cápsulas não terminadas direcionadas para esta implantação que têm a especificação do modelo desejada (status.updatedReplicas) </li></ul>|

## <a name="hpa-metrics"></a>Métricas de HPA

O Azure Monitor para contentores inicia automaticamente a monitorização dos A HP, recolhendo as seguintes métricas em intervalos de 60 segundos e armazenando-os na tabela **InsightMetrics:**

|Nome da métrica |Dimensão métrica (etiquetas) |Descrição |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, hpa, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, targetKind, targetName | Número atual de réplicas de cápsulas geridas por este autoescalador (status.currentReplicas). Abaixo estão as dimensões desta métrica. <ul> <li> hpa - nome do HPA </li> <li> k8sNamespace - Kubernetes espaço de nome para o HPA </li> <li> lastScaleTime - Última vez o HPA escalou o número de cápsulas (status.lastScaleTime)</li><li> criaçãoTime - HPA criação timetamp </li> <li> spec_max_replicas - Limite superior para o número de cápsulas que podem ser definidas pelo autoescalador (spec.maxReplicas) </li> <li> spec_min_replicas - Limite inferior para o número de réplicas a que o autoescalador pode reduzir (spec.minReplicas) </li><li>status_desired_replicas - Número desejado de réplicas de cápsulas geridas por este autoescalador (status.desireReplicas)</li><li>targetKind - Tipo do alvo do HPA (spec.scaleTargetRef.kind) </li><li>nome do alvo - Nome do alvo do HPA (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>Gráficos de HPA de & de implantação 

O Azure Monitor para contentores inclui gráficos pré-configurados para as métricas listadas anteriormente na tabela como um livro para cada cluster. Pode encontrar as implementações & o livro de trabalho HPA **Implementações & HPA** diretamente de um cluster AKS selecionando livros de **trabalho** a partir do painel de esquerda e da lista de drop-down do **View Workbooks** no Insight.

## <a name="next-steps"></a>Passos seguintes

- Reveja [as métricas do estado de Kube em Kubernetes](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) para saber mais sobre as métricas do estado de Kube.