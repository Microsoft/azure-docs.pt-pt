---
title: Descrição geral do Azure Monitor para contentores | Documentos da Microsoft
description: Este artigo descreve o Monitor do Azure para contentores que monitoriza a solução de informações de contentor do AKS e o valor proporciona ao monitorizar o estado de funcionamento dos seus clusters do AKS e instâncias de contentores no Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385538"
---
# <a name="azure-monitor-for-containers-overview"></a>Monitor do Azure para contentores-descrição geral

O Monitor Azure para contentores é uma característica concebida para monitorizar o desempenho das cargas de trabalho dos contentores implantadas para:

- Aglomerados Kubernetes geridos hospedados no [Serviço Azure Kubernetes (AKS)](../../aks/intro-kubernetes.md)
- Aglomerados Kubernetes autogeridos hospedados em Azure usando [motor AKS](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Aglomerados Kubernetes autogeridos hospedados em [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) ou no local
- [OpenShift do chapéu vermelho azure](../../openshift/intro-openshift.md)

O Monitor Azure para contentores suporta clusters que executam o sistema operativo Linux e Windows Server 2019. 

Os contentores é fundamental monitorizar, especialmente quando estiver a executar um cluster de produção em escala, com várias aplicações.

Monitor do Azure para contentores dá-lhe visibilidade de desempenho por memória de coleta e métricas de processador de controladores, nós e contentores que estão disponíveis no Kubernetes por meio da API de métricas. Os registos do contentor também são recolhidos.  Depois de ativar a monitorização dos clusters, métricas e registos kubernetes são automaticamente recolhidos para si através de uma versão contentorizada do agente Log Analytics para o Linux. As métricas são escritas para a loja de métricas e os dados de registo são escritos na loja de registos associado supérns com o seu espaço de trabalho [Log Analytics.](../log-query/log-query-overview.md) 

![Monitor Azure para arquitetura de contentores](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>O que faz do Azure Monitor para os contentores oferecem?

O Monitor Azure para contentores proporciona uma experiência de monitorização abrangente utilizando diferentes características do Monitor Azure. Estas funcionalidades permitem compreender o desempenho e a saúde do seu cluster Kubernetes que executa o sistema operativo Linux e Windows Server 2019 e as cargas de trabalho do contentor. Com o Monitor Azure para recipientes pode:

* Identifique os contentores do AKS que estão em execução no nó e a utilização de processador e memória média. Esse conhecimento pode ajudá-lo a identificar afunilamentos de recursos.
* Identifique a utilização de processador e memória de grupos de contentor e respetivos contentores alojados no Azure Container Instances.  
* Identifique onde o recipiente reside num controlador ou numa cápsula. Esse conhecimento pode ajudá-lo a ver do controlador ou do pod desempenho geral. 
* Reveja a utilização de recursos de cargas de trabalho em execução no anfitrião que não estão relacionadas com os processos padrão que suportam o pod.
* Compreenda o comportamento do cluster sob cargas mais pesadas e médios. Esse conhecimento pode ajudá-lo a identificar necessidades de capacidade e determinar a carga máxima que o cluster pode suportar. 
* Configure alertas para notificá-lo proativamente ou registrá-lo quando a CPU e a utilização da memória em nós ou contentores excederem os seus limiares, ou quando uma mudança de estado de saúde ocorre no cluster na infraestrutura ou nódoa a saúde.
* Integre com [a Prometheus](https://prometheus.io/docs/introduction/overview/) para visualizar as métricas de aplicação e carga de trabalho que recolhe a partir de nós e Kubernetes usando [consultas](container-insights-log-search.md) para criar alertas personalizados, dashboards e análise detalhada.
* Monitorize as cargas de trabalho dos contentores [implantados no motor AKS](https://github.com/Azure/aks-engine) no local e [no motor AKS em Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
* Monitor de cargas de contentores [implantadas para O Turno aberto](../../openshift/intro-openshift.md)do chapéu vermelho Azure .

    >[!NOTE]
    >O suporte para o Azure Red Hat OpenShift é uma característica na pré-estreia pública neste momento.
    >

As principais diferenças na monitorização de um cluster do Windows Server em comparação com um cluster Linux são as seguintes:

- A métrica RSS da memória não está disponível para nó e recipientes windows.
- A informação sobre a capacidade de armazenamento de discos não está disponível para os nódos windows.
- Os registos de contentores não estão disponíveis para contentores em funcionamento nos nós do Windows.
- O suporte à funcionalidade Live Data (pré-visualização) está disponível, com exceção dos registos de contentores do Windows.
- Apenas ambientes de pod são monitorizados, não ambientes Docker.
- Com o lançamento da pré-visualização, são suportados no máximo 30 recipientes Do Windows Server. Esta limitação não se aplica aos contentores Linux. 

Confira o seguinte vídeo que fornece um mergulho profundo de nível intermédio para ajudá-lo a aprender sobre monitorizar o seu cluster AKS com o Monitor Azure para obter recipientes.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Como aceder a esta funcionalidade?

Pode acessar o Monitor do Azure para contentores duas formas, do Azure Monitor ou diretamente a partir do cluster AKS selecionado. Do Monitor Azure, tem uma perspetiva global de todos os contentores implantados, que são monitorizados e que não são, permitindo-lhe pesquisar e filtrar através das suas assinaturas e grupos de recursos, e depois perfurar no Monitor Azure para obter recipientes a partir do recipiente selecionado.  Caso contrário, pode aceder diretamente à funcionalidade a partir de um recipiente AKS selecionado a partir da página AKS.  

![Descrição geral dos métodos para aceder ao Azure Monitor para contentores](./media/container-insights-overview/azmon-containers-experience.png)

Se estiver interessado em monitorizar e gerir os seus anfitriões de contentores Docker e Windows que correm fora do AKS para visualizar a configuração, auditoria e utilização de recursos, consulte a [solução de Monitorização de Contentores](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Passos seguintes

Para começar a monitorizar o seu cluster Kubernetes, reveja [como permitir que o Monitor Azure para os contentores](container-insights-onboard.md) compreenda os requisitos e métodos disponíveis para permitir a monitorização. 
