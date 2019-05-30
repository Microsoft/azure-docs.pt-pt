---
title: Descrição geral do Azure Monitor para contentores | Documentos da Microsoft
description: Este artigo descreve o Monitor do Azure para contentores que monitoriza a solução de informações de contentor do AKS e o valor proporciona ao monitorizar o estado de funcionamento dos seus clusters do AKS e instâncias de contentores no Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d6321564672097fbf901d1d33afac9f606fcb63a
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521840"
---
# <a name="azure-monitor-for-containers-overview"></a>Monitor do Azure para contentores-descrição geral

Monitor do Azure para contentores é uma funcionalidade concebida para monitorizar o desempenho de cargas de trabalho de contentor implementado nos clusters do Kubernetes geridos alojados no Azure Kubernetes Service (AKS) ou Azure Container Instances. Os contentores é fundamental monitorizar, especialmente quando estiver a executar um cluster de produção em escala, com várias aplicações.

Monitor do Azure para contentores dá-lhe visibilidade de desempenho por memória de coleta e métricas de processador de controladores, nós e contentores que estão disponíveis no Kubernetes por meio da API de métricas. Os registos do contentor também são recolhidos.  Depois de ativar a monitorização de clusters de Kubernetes, métricas e registos são automaticamente recolhidos para por meio de uma versão em contentores do agente do Log Analytics para Linux. As métricas são escritas para o arquivo de métricas e dados de registo são escritos para o armazenamento de registos associado com sua [do Log Analytics](../log-query/log-query-overview.md) área de trabalho. 

![Monitor do Azure para uma arquitetura de contentores](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>O que faz do Azure Monitor para os contentores oferecem?

Monitor do Azure para contentores fornece uma experiência de monitorização abrangente com diferentes recursos do Azure Monitor permitindo que compreender o desempenho e estado de funcionamento do cluster de Kubernetes e as cargas de trabalho do contentor. Com o Azure Monitor para contentores, pode:

* Identifique os contentores do AKS que estão em execução no nó e a utilização de processador e memória média. Esse conhecimento pode ajudá-lo a identificar afunilamentos de recursos.
* Identifique a utilização de processador e memória de grupos de contentor e respetivos contentores alojados no Azure Container Instances.  
* Identifique onde o contentor reside num controlador ou de um pod. Esse conhecimento pode ajudá-lo a ver do controlador ou do pod desempenho geral.
* Reveja a utilização de recursos de cargas de trabalho em execução no anfitrião que não estão relacionadas com os processos padrão que suportam o pod.
* Compreenda o comportamento do cluster sob cargas mais pesadas e médios. Esse conhecimento pode ajudá-lo a identificar necessidades de capacidade e determinar a carga máxima que o cluster pode suportar. 
* Configure alertas para notificá-lo ou guarde-a quando a utilização de CPU e memória em nós ou contentores exceder os limiares de proativamente.  

## <a name="how-do-i-access-this-feature"></a>Como aceder a esta funcionalidade?
Pode acessar o Monitor do Azure para contentores duas formas, do Azure Monitor ou diretamente a partir do cluster AKS selecionado. Azure monitor tem uma perspetiva global de todos os contentores implementados, que é monitorizado ou não, permitindo que para pesquisar e filtrar nas suas subscrições e grupos de recursos e, em seguida, faça uma busca no Monitor do Azure para contentores dos contentor selecionado.  Caso contrário, pode acessar a funcionalidade diretamente a partir de um contentor do AKS selecionado da página de AKS.  

![Descrição geral dos métodos para aceder ao Azure Monitor para contentores](./media/container-insights-overview/azmon-containers-experience.png)

Se estiver interessado na monitorização e gestão de Docker e o Windows anfitriões de contentores para ver a configuração, auditoria e utilização de recursos, consulte a [solução de monitorização de contentores](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Passos Seguintes
Para começar a monitorizar o seu cluster do AKS, reveja [como ativar o Azure Monitor para contentores](container-insights-onboard.md) para compreender os requisitos e os métodos disponíveis para ativar a monitorização.  
