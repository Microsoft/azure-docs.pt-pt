---
title: Descrição geral do Azure Monitor para contentores | Documentos da Microsoft
description: Este artigo descreve o Monitor do Azure para contentores que monitoriza a solução de informações de contentor do AKS e o valor proporciona ao monitorizar o estado de funcionamento dos seus clusters do AKS e instâncias de contentores no Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/18/2019
ms.openlocfilehash: 8267f8148269f8b1a0717435e57614f09c229de1
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841409"
---
# <a name="azure-monitor-for-containers-overview"></a>Monitor do Azure para contentores-descrição geral

O Azure Monitor para contêineres é um recurso projetado para monitorar o desempenho de cargas de trabalho de contêiner implantadas em:

- Clusters Managed Kubernetes hospedados no serviço de kubernetes do Azure (AKS)
- Azure Container Instances
- Clusters kubernetes autogerenciados hospedados no Azure Stack ou no local
- Azure Red Hat OpenShift

Os contentores é fundamental monitorizar, especialmente quando estiver a executar um cluster de produção em escala, com várias aplicações.

Monitor do Azure para contentores dá-lhe visibilidade de desempenho por memória de coleta e métricas de processador de controladores, nós e contentores que estão disponíveis no Kubernetes por meio da API de métricas. Os registos do contentor também são recolhidos.  Depois de habilitar o monitoramento de clusters do kubernetes, as métricas e os logs são coletados automaticamente para você por meio de uma versão em contêiner do agente de Log Analytics para Linux. As métricas são gravadas no repositório de métricas e os dados de log são gravados no repositório de logs associado ao seu espaço de trabalho [log Analytics](../log-query/log-query-overview.md) . 

![Azure Monitor para arquitetura de contêineres](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>O que faz do Azure Monitor para os contentores oferecem?

O Azure Monitor para contêineres fornece uma experiência de monitoramento abrangente usando diferentes recursos do Azure Monitor, permitindo que você entenda o desempenho e a integridade do cluster kubernetes e das cargas de trabalho do contêiner. Com Azure Monitor para contêineres, você pode:

* Identifique os contentores do AKS que estão em execução no nó e a utilização de processador e memória média. Esse conhecimento pode ajudá-lo a identificar afunilamentos de recursos.
* Identifique a utilização de processador e memória de grupos de contentor e respetivos contentores alojados no Azure Container Instances.  
* Identifique onde o contêiner reside em um controlador ou um pod. Esse conhecimento pode ajudá-lo a ver do controlador ou do pod desempenho geral. 
* Reveja a utilização de recursos de cargas de trabalho em execução no anfitrião que não estão relacionadas com os processos padrão que suportam o pod.
* Compreenda o comportamento do cluster sob cargas mais pesadas e médios. Esse conhecimento pode ajudá-lo a identificar necessidades de capacidade e determinar a carga máxima que o cluster pode suportar. 
* Configure alertas para notificá-lo proativamente ou registre-o quando a utilização de CPU e memória em nós ou contêineres exceder seus limites ou quando uma alteração de estado de integridade ocorrer no cluster no acúmulo de integridade de infraestrutura ou de nós.
* Integre com o [Prometheus](https://prometheus.io/docs/introduction/overview/) para exibir as métricas de aplicativo e carga de trabalho coletadas de nós e kubernetes usando [consultas](container-insights-log-search.md) para criar alertas personalizados, painéis e executar análise detalhada.

    >[!NOTE]
    >O suporte para Prometheus é um recurso em visualização pública no momento.
    >

* Monitore cargas de trabalho de contêiner [implantadas](https://github.com/microsoft/OMS-docker/tree/aks-engine) no mecanismo do AKS no local e no [mecanismo de AKs no Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
* Monitore cargas [de trabalho de contêiner implantadas no Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >O suporte para o Azure Red Hat OpenShift é um recurso em visualização pública no momento.
    >

Confira o vídeo a seguir fornecendo um aprofundamento no nível intermediário para ajudá-lo a saber mais sobre como monitorar o cluster AKS com Azure Monitor para contêineres.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Como aceder a esta funcionalidade?

Pode acessar o Monitor do Azure para contentores duas formas, do Azure Monitor ou diretamente a partir do cluster AKS selecionado. Em Azure Monitor, você tem uma perspectiva global de todos os contêineres implantados, que são monitorados e que não estão, permitindo Pesquisar e filtrar entre suas assinaturas e grupos de recursos e, em seguida, analisar Azure Monitor para contêineres do contêiner selecionado.  Caso contrário, você pode acessar o recurso diretamente de um contêiner AKS selecionado na página AKS.  

![Descrição geral dos métodos para aceder ao Azure Monitor para contentores](./media/container-insights-overview/azmon-containers-experience.png)

Se você estiver interessado em monitorar e gerenciar seus hosts de contêiner do Docker e do Windows em execução fora do AKS para exibir a configuração, a auditoria e a utilização de recursos, consulte a [solução de monitoramento de contêiner](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Passos seguintes

Para começar a monitorar o cluster do kubernetes, examine [como habilitar o Azure monitor para contêineres](container-insights-onboard.md) para entender os requisitos e os métodos disponíveis para habilitar o monitoramento. 
