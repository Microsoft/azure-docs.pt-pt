---
title: Monitor Azure para mapeamento da região de contentores
description: Este artigo descreve os mapeamentos da região suportados entre o Monitor Azure para contentores, log analytics workspace e métricas personalizadas.
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403429"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapeamentos da região apoiados pelo Azure Monitor para contentores

 Ao permitir o Monitor Azure para contentores, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e um cluster AKS, e recolher métricas personalizadas submetidas ao Monitor Azure.

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics workspace supported mapeamentos

Os recursos do cluster AKS ou log Analytics podem residir noutras regiões, e a tabela que se segue mostra os nossos mapeamentos.

|**Região do Aglomerado AKS** | **Região do Espaço de Trabalho de Log Analytics** |
|-----------------------|------------------------------------|
|**África** | |
|África do SulNorte |Europa Ocidental |
|Sudoeste da África do Sul |Europa Ocidental |
|**Austrália** | |
|AustráliaEast |AustráliaEast |
|AustráliaCentral |AustráliaCentral |
|AustráliaCentral2 |AustráliaCentral |
|AustráliaEast |AustráliaEast |
|**Ásia-Pacífico** | |
|Ásia Oriental |Ásia Oriental |
|Sudeste asiático |Sudeste asiático |
|**Brasil** | |
|BrasilSul | Sul-CentralUS |
|**Canadá** ||
|CanadáCentral |CanadáCentral |
|CanadáEast |CanadáCentral |
|**Europa** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|Norte da Europa |Norte da Europa |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|Europa Ocidental |Europa Ocidental |
|**Índia** | |
|Índia Central |Índia Central |
|Sul da Índia |Índia Central |
|Índia Ocidental |Índia Central |
|**Japão** | |
|JapãoEast |JapãoEast |
|JapãoWest |JapãoEast |
|**Coreia** | |
|KoreaCentral |KoreaCentral |
|Coreia do Sul |KoreaCentral |
|**EUA** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|
|US Gov - Virginia |US Gov - Virginia |

<sup>1</sup> Devido às restrições de capacidade, a região não está disponível na criação de novos recursos. Isto inclui um espaço de trabalho log Analytics. No entanto, os recursos pré-existentes ligados na região devem continuar a funcionar.

## <a name="custom-metrics-supported-regions"></a>Métricas personalizadas regiões apoiadas

A recolha de métricas dos clusters Azure Kubernetes Services (AKS) os nós e os pods são suportados para publicação como métricas personalizadas apenas nas [seguintes regiões de Azure.](../platform/metrics-custom-overview.md#supported-regions)

## <a name="next-steps"></a>Passos seguintes

Para começar a monitorizar o seu cluster AKS, reveja [como permitir que o Monitor Azure para os contentores](container-insights-onboard.md) compreenda os requisitos e métodos disponíveis para permitir a monitorização.  
