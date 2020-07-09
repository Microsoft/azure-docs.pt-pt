---
title: Monitor Azure para mapeamentos da região de contentores
description: Este artigo descreve os mapeamentos da região suportados entre o Azure Monitor para contentores, Log Analytics Workspace e métricas personalizadas.
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: references_regions
ms.openlocfilehash: 3e8ead78c5e0e534e07c1e2ab0e25eb3f5a90c38
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84194991"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapeamentos da região apoiados pelo Azure Monitor para contentores

 Ao permitir o Azure Monitor para contentores, apenas certas regiões são suportadas para ligar um espaço de trabalho Log Analytics e um cluster AKS, e recolher métricas personalizadas submetidas ao Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapeamentos suportados por log Analytics

Os recursos de cluster AKS ou log analytics podem residir em outras regiões, e a tabela seguinte mostra os nossos mapeamentos.

|**Região do Cluster AKS** | **Log Analytics Região do espaço de trabalho** |
|-----------------------|------------------------------------|
|**África** | |
|Sul-AfricaNorth |Europa Ocidental |
|Sul-AfricaWest |Europa Ocidental |
|**Austrália** | |
|AustráliaEast |AustráliaEast |
|AustráliaCentral |AustráliaCentral |
|AustráliaCentral2 |AustráliaCentral |
|AustráliaEast |AustráliaEast |
|**Ásia-Pacífico** | |
|Ásia Oriental |Ásia Oriental |
|Sudeste da Ásia |Sudeste da Ásia |
|**Brasil** | |
|BrazilSouth | Sul-Centro |
|**Canadá** ||
|Centro do Canadá |Centro do Canadá |
|CanadáEast |Centro do Canadá |
|**Europa** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UkWest |UKSouth |
|Europa Ocidental |Europa Ocidental |
|**Índia** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japão** | |
|JapãoEast |JapãoEast |
|Japão Oeste |JapãoEast |
|**Coreia** | |
|KoreaCentral |KoreaCentral |
|Coreias |KoreaCentral |
|**EUA** | |
|Central |Central|
|Rio Eastus |Rio Eastus |
|Leste |Leste |
|Westus |Westus |
|Westus2 |Westus2 |
|WestCentralus<sup>1</sup>|Leste<sup>1</sup>|
|US Gov - Virginia |US Gov - Virginia |

<sup>1</sup> Devido a restrições de capacidade, a região não está disponível na criação de novos recursos. Isto inclui um espaço de trabalho Log Analytics. No entanto, os recursos pré-existentes ligados na região devem continuar a funcionar.

## <a name="custom-metrics-supported-regions"></a>Métricas personalizadas suportadas regiões

As métricas de recolha dos nós e cápsulas de agrupamentos Azure Kubernetes Services (AKS) são suportadas para publicação como métricas personalizadas apenas nas [seguintes regiões de Azure.](../platform/metrics-custom-overview.md#supported-regions)

## <a name="next-steps"></a>Próximos passos

Para começar a monitorizar o seu cluster AKS, [reveja como permitir que o Monitor Azure para os contentores](container-insights-onboard.md) compreenda os requisitos e métodos disponíveis para permitir a monitorização.  
