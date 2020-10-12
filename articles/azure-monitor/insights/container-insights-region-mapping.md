---
title: Monitor Azure para mapeamentos da região de contentores
description: Descreve os mapeamentos da região suportados entre o Azure Monitor para contentores, Log Analytics Workspace e métricas personalizadas.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 9e3b8635c70dfdf33b0a062be80c948cd77923cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272910"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapeamentos da região apoiados pelo Azure Monitor para contentores

 Ao permitir o Azure Monitor para contentores, apenas certas regiões são suportadas para ligar um espaço de trabalho Log Analytics e um cluster AKS, e recolher métricas personalizadas submetidas ao Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapeamentos suportados por log Analytics

As regiões AKS apoiadas estão listadas em [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service) O espaço de trabalho Log Analytics deve estar na mesma região, com exceção das regiões enumeradas no quadro seguinte. Assista [a notas de lançamento da AKS](https://github.com/Azure/AKS/releases) para atualizações.


|**Região do Cluster AKS** | **Log Analytics Região do espaço de trabalho** |
|-----------------------|------------------------------------|
|**África** | |
|Sul-AfricaNorth |Europa Ocidental |
|Sul-AfricaWest |Europa Ocidental |
|**Austrália** | |
|AustráliaCentral2 |AustráliaCentral |
|**Brasil** | |
|BrazilSouth | Sul-Centro |
|**Canadá** ||
|CanadáEast |Centro do Canadá |
|**Europa** | |
|FranceSouth |FranceCentral |
|UkWest |UKSouth |
|**Índia** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japão** | |
|Japão Oeste |JapãoEast |
|**Coreia** | |
|Coreias |KoreaCentral |
|**EUA** | |
|WestCentralus<sup>1</sup>|Leste<sup>1</sup>|


<sup>1</sup> Devido a restrições de capacidade, a região não está disponível na criação de novos recursos. Isto inclui um espaço de trabalho Log Analytics. No entanto, os recursos pré-existentes ligados na região devem continuar a funcionar.

## <a name="custom-metrics-supported-regions"></a>Métricas personalizadas suportadas regiões

As métricas de recolha dos nós e cápsulas de agrupamentos Azure Kubernetes Services (AKS) são suportadas para publicação como métricas personalizadas apenas nas [seguintes regiões de Azure.](../platform/metrics-custom-overview.md#supported-regions)

## <a name="next-steps"></a>Passos seguintes

Para começar a monitorizar o seu cluster AKS, [reveja como permitir que o Monitor Azure para os contentores](container-insights-onboard.md) compreenda os requisitos e métodos disponíveis para permitir a monitorização.  
