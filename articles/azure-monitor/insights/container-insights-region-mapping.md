---
title: O Azure Monitor para mapeamentos de região de contentores
description: Este artigo descreve os mapeamentos de região suportados entre o Azure Monitor para contentores, área de trabalho do Log Analytics e métricas personalizadas.
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 481a2a400be4e983e0a2337a200324061494efa1
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518082"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapeamentos de região suportados pelo Azure Monitor para contentores

 Ao ativar o Azure Monitor para contentores, apenas determinadas regiões são suportadas para ligar uma área de trabalho do Log Analytics e um cluster do AKS e recolher métricas personalizadas submetido para o Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapeamentos de suporte de área de trabalho do log Analytics

Os recursos de cluster do AKS ou a área de trabalho do Log Analytics pode residir em outras regiões, e a tabela seguinte mostra nossos mapeamentos.

|**Região de Cluster do AKS** | **Região da área de trabalho de análise de registo** |
|-----------------------|------------------------------------|
|**África** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Austrália** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Ásia-Pacífico** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**Brasil** | |
|BrazilSouth | SouthCentralUS |
|**Canadá** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**Europa** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|WestEurope |WestEurope |
|**Índia** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japão** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**Coreia** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**EUA** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|

<sup>1</sup> devido a restrições de capacidade, a região não está disponível durante a criação de novos recursos. Isto inclui uma área de trabalho do Log Analytics. No entanto, os recursos ligados preexistentes na região devem continuar a funcionar.

## <a name="custom-metrics-supported-regions"></a>Métricas personalizadas de regiões suportadas

Recolher métricas de serviços do Azure Kubernetes (AKS) clusters de nós e pods são suportados para publicação como métricas personalizadas apenas nas seguintes [regiões do Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Passos Seguintes

Para começar a monitorizar o seu cluster do AKS, reveja [como ativar o Azure Monitor para contentores](container-insights-onboard.md) para compreender os requisitos e os métodos disponíveis para ativar a monitorização.  