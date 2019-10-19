---
title: Visão geral de Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve Azure Monitor para contêineres que monitora a solução de informações de contêiner AKS e o valor que ele fornece monitorando a integridade dos clusters do AKS e das instâncias de contêiner no Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/02/2019
ms.openlocfilehash: 8a6a9862679508971edd23b2d4220446c2692d72
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555373"
---
# <a name="azure-monitor-for-containers-overview"></a>Visão geral de Azure Monitor para contêineres

O Azure Monitor para contêineres é um recurso projetado para monitorar o desempenho de cargas de trabalho de contêiner implantadas em instâncias de contêiner do Azure ou clusters kubernetes gerenciados hospedados no AKS (serviço kubernetes do Azure). O monitoramento de seus contêineres é essencial, especialmente quando você está executando um cluster de produção, em escala, com vários aplicativos.

Azure Monitor para contêineres oferece visibilidade de desempenho coletando métricas de memória e processador de controladores, nós e contêineres que estão disponíveis no kubernetes por meio da API de métricas. Os registos do contentor também são recolhidos.  Depois de habilitar o monitoramento de clusters do kubernetes, as métricas e os logs são coletados automaticamente para você por meio de uma versão em contêiner do agente de Log Analytics para Linux. As métricas são gravadas no repositório de métricas e os dados de log são gravados no repositório de logs associado ao seu espaço de trabalho [log Analytics](../log-query/log-query-overview.md) . 

![Azure Monitor para arquitetura de contêineres](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>O que o Azure Monitor para contêineres fornece?

O Azure Monitor para contêineres fornece uma experiência de monitoramento abrangente usando diferentes recursos do Azure Monitor, permitindo que você entenda o desempenho e a integridade do cluster kubernetes e das cargas de trabalho do contêiner. Com Azure Monitor para contêineres, você pode:

* Identifique os contêineres do AKS que estão em execução no nó e sua utilização média de processador e memória. Esse conhecimento pode ajudá-lo a identificar gargalos de recursos.
* Identifique a utilização de memória e processador de grupos de contêineres e seus contêineres hospedados em instâncias de contêiner do Azure.  
* Identifique onde o contêiner reside em um controlador ou um pod. Esse conhecimento pode ajudá-lo a exibir o desempenho geral do controlador ou do pod.
* Examine a utilização de recursos de cargas de trabalho em execução no host que não estão relacionadas aos processos padrão que dão suporte ao Pod.
* Entenda o comportamento do cluster em cargas médias e mais pesadas. Esse conhecimento pode ajudá-lo a identificar as necessidades de capacidade e determinar a carga máxima que o cluster pode sustentar. 
* Configure alertas para notificá-lo proativamente ou registre-o quando a utilização de CPU e memória em nós ou contêineres exceder seus limites.
* Integre com o [Prometheus](https://prometheus.io/docs/introduction/overview/) para exibir as métricas de aplicativo e carga de trabalho coletadas de nós e kubernetes usando [consultas](container-insights-log-search.md) para criar alertas personalizados, painéis e executar análise detalhada.

    >[!NOTE]
    >O suporte para Prometheus é um recurso em visualização pública no momento.
    >

* Monitore cargas de trabalho de contêiner [implantadas no AKs-Engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

Confira o vídeo a seguir fornecendo um aprofundamento no nível intermediário para ajudá-lo a saber mais sobre como monitorar o cluster AKS com Azure Monitor para contêineres.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Como fazer acessar esse recurso?

Você pode acessar Azure Monitor para contêineres de duas maneiras, de Azure Monitor ou diretamente do cluster AKS selecionado. Em Azure Monitor, você tem uma perspectiva global de todos os contêineres implantados, que são monitorados e que não estão, permitindo Pesquisar e filtrar entre suas assinaturas e grupos de recursos e, em seguida, analisar Azure Monitor para contêineres do contêiner selecionado.  Caso contrário, você pode acessar o recurso diretamente de um contêiner AKS selecionado na página AKS.  

![Visão geral dos métodos para acessar Azure Monitor para contêineres](./media/container-insights-overview/azmon-containers-experience.png)

Se você estiver interessado em monitorar e gerenciar seus hosts de contêiner do Docker e do Windows em execução fora do AKS para exibir a configuração, a auditoria e a utilização de recursos, consulte a [solução de monitoramento de contêiner](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Passos seguintes

Para começar a monitorar o cluster do AKS, examine [como habilitar o Azure monitor para contêineres](container-insights-onboard.md) para entender os requisitos e os métodos disponíveis para habilitar o monitoramento.  
