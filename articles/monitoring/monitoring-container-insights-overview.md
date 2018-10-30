---
title: Descrição geral do Azure Monitor para contentores (pré-visualização) | Documentos da Microsoft
description: Este artigo descreve o Monitor do Azure para contentores que monitoriza a solução de informações de contentor do AKS e o valor proporciona ao monitorizar o estado de funcionamento dos seus clusters do AKS e instâncias de contentores no Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: magoedte
ms.openlocfilehash: 13fde8a4ec4ecf43b9997d5d4d181a232505626f
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50213003"
---
# <a name="azure-monitor-for-containers-preview-overview"></a>Monitor do Azure para contentores (pré-visualização)-Descrição geral

Monitor do Azure para contentores é uma funcionalidade concebida para monitorizar o desempenho de cargas de trabalho de contentor implementadas em clusters do Kubernetes alojados no Azure Kubernetes Service (AKS) geridos. Os contentores é fundamental monitorizar, especialmente quando estiver a executar um cluster de produção em escala, com várias aplicações.

Monitor do Azure para contentores dá-lhe visibilidade de desempenho por memória de coleta e métricas de processador de controladores, nós e contentores que estão disponíveis no Kubernetes por meio da API de métricas. Os registos do contentor também são recolhidos.  Depois de ativar a monitorização de clusters de Kubernetes, estas métricas e registos são automaticamente recolhidos para por meio de uma versão em contentores do agente do Log Analytics para Linux e armazenados no seu [do Log Analytics](../log-analytics/log-analytics-overview.md) área de trabalho. 
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>O que faz do Azure Monitor para os contentores oferecem?

Monitor do Azure para contentores inclui várias vistas predefinidas que mostram as cargas de trabalho de contentor que residem e o que afeta o estado de funcionamento do desempenho do cluster de Kubernetes monitorizado para que possa:  

* Identifique os contentores do AKS que estão em execução no nó e a utilização de processador e memória média. Esse conhecimento pode ajudá-lo a identificar afunilamentos de recursos.
* Identifique onde o contentor reside num controlador ou de um pod. Esse conhecimento pode ajudá-lo a ver do controlador ou do pod desempenho geral. 
* Reveja a utilização de recursos de cargas de trabalho em execução no anfitrião que não estão relacionadas com os processos padrão que suportam o pod.
* Compreenda o comportamento do cluster sob cargas mais pesadas e médios. Esse conhecimento pode ajudá-lo a identificar necessidades de capacidade e determinar a carga máxima que o cluster pode suportar. 

## <a name="how-do-i-access-this-feature"></a>Como aceder a esta funcionalidade?
Pode acessar o Monitor do Azure para contentores duas formas, do Azure Monitor ou diretamente a partir do cluster AKS selecionado. Azure monitor tem uma perspetiva global de todos os contentores implementados, que é monitorizado e que não são, permitindo que para pesquisar e filtrar nas suas subscrições e grupos de recursos e depois pormenorize Monitor do Azure para contentores do selecionado contentor.  Caso contrário, basta pode acessar a funcionalidade diretamente a partir de um contentor do AKS selecionado da página de AKS.  

![Descrição geral dos métodos para aceder ao Azure Monitor para contentores](./media/monitoring-container-insights-overview/azmon-containers-views.png)

Se estiver interessado na monitorização e gestão de Docker e o Windows anfitriões de contentores para ver a configuração, auditoria e utilização de recursos, consulte a [solução de monitorização de contentores](../log-analytics/log-analytics-containers.md).

## <a name="next-steps"></a>Passos Seguintes
Para começar a monitorizar o seu cluster do AKS, reveja [como para integrar o Azure Monitor para contentores](monitoring-container-insights-onboard.md) para compreender os requisitos e os métodos disponíveis para ativar a monitorização.  