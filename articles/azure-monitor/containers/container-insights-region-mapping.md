---
title: Mapeamentos da região de insights de contentores
description: Descreve os mapeamentos da região suportados entre insights de contentores, log analytics workspace, e métricas personalizadas.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: f9e910b1352109608becb82609e85e26d27d2cd1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728880"
---
# <a name="region-mappings-supported-by-container-insights"></a>Mapeamentos da região suportados por insights de contentores

 Ao permitir insights de contentores, apenas certas regiões são suportadas para ligar um espaço de trabalho Log Analytics e um cluster AKS, e recolher métricas personalizadas submetidas ao Azure Monitor.

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

As métricas de recolha dos nós e cápsulas de agrupamentos Azure Kubernetes Services (AKS) são suportadas para publicação como métricas personalizadas apenas nas [seguintes regiões de Azure.](../essentials/metrics-custom-overview.md#supported-regions)

## <a name="next-steps"></a>Passos seguintes

Para começar a monitorizar o seu cluster AKS, [reveja como permitir que os insights do Recipiente](container-insights-onboard.md) compreendam os requisitos e métodos disponíveis para permitir a monitorização.  
