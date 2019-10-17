---
title: Azure Monitor para mapeamentos de região de contêineres
description: Este artigo descreve os mapeamentos de região com suporte entre Azure Monitor para contêineres, espaço de trabalho Log Analytics e métricas personalizadas.
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ca77277c8d8d405b8fe81f612a8d7600d3c937ab
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388190"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapeamentos de região com suporte pelo Azure Monitor para contêineres

 Ao habilitar Azure Monitor para contêineres, somente determinadas regiões têm suporte para vincular um espaço de trabalho Log Analytics e um cluster AKS e coletar métricas personalizadas enviadas para Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapeamentos com suporte do Log Analytics Workspace

Os recursos de cluster AKS ou Log Analytics espaço de trabalho podem residir em outras regiões e a tabela a seguir mostra nossos mapeamentos.

|**Região de cluster AKS** | **Região do espaço de trabalho Log Analytics** |
|-----------------------|------------------------------------|
|**Oriental** | |
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
|WestCentralUS<sup>1</sup>|Lesteus<sup>1</sup>|
|Gov dos E.U.A. Virginia |Gov dos E.U.A. Virginia |

<sup>1</sup> devido a restrições de capacidade, a região não está disponível durante a criação de novos recursos. Isso inclui um espaço de trabalho Log Analytics. No entanto, os recursos vinculados preexistentes na região devem continuar funcionando.

## <a name="custom-metrics-supported-regions"></a>Regiões com suporte para métricas personalizadas

A coleta de métricas dos nós de clusters AKS (serviços Kubernetess do Azure) e pods tem suporte para publicação como métricas personalizadas somente nas seguintes [regiões do Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Passos seguintes

Para começar a monitorar o cluster do AKS, examine [como habilitar o Azure monitor para contêineres](container-insights-onboard.md) para entender os requisitos e os métodos disponíveis para habilitar o monitoramento.  
