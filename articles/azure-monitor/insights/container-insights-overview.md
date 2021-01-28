---
title: Visão geral do Monitor Azure para contentores | Microsoft Docs
description: Este artigo descreve o Azure Monitor para recipientes que monitorizam a solução AKS Container Insights e o valor que fornece através da monitorização da saúde dos seus agrupamentos AKS e Instâncias de Contentores em Azure.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: a21a0e506082b58473870baed014ede16665e837
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943808"
---
# <a name="azure-monitor-for-containers-overview"></a>Descrição geral do Azure Monitor para contentores

O Azure Monitor para contentores é uma característica concebida para monitorizar o desempenho das cargas de trabalho dos contentores implantadas para:

- Agrupamentos geridos de Kubernetes hospedados no [Serviço Azure Kubernetes (AKS)](../../aks/intro-kubernetes.md)
- Clusters Kubernetes auto-geridos hospedados em Azure usando [motor AKS](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Clusters Kubernetes auto-geridos hospedados em [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) ou no local
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)
- [Azure Arc ativou Kubernetes](../../azure-arc/kubernetes/overview.md) (pré-visualização)

O Azure Monitor para contentores suporta clusters que executam o sistema operativo Linux e Windows Server 2019. Os tempos de funcionação do contentor que suporta são Docker, Moby, e qualquer tempo de execução compatível com CRI como CRI-O e ContainerD.

Monitorizar os seus contentores é fundamental, especialmente quando se está a executar um cluster de produção, em escala, com múltiplas aplicações.

O Azure Monitor para recipientes dá-lhe visibilidade de desempenho recolhendo métricas de memória e processador de controladores, nós e recipientes disponíveis em Kubernetes através da API métricas. Os registos do contentor também são recolhidos.  Depois de ativar a monitorização dos clusters kubernetes, as métricas e os registos são automaticamente recolhidos para si através de uma versão contentorizada do agente Log Analytics para o Linux. As métricas são escritas para a loja de métricas e os dados de registo são escritos na loja de registos associados ao seu espaço de trabalho [Log Analytics.](../log-query/log-query-overview.md)

![Monitor Azure para arquitetura de contentores](./media/container-insights-overview/azmon-containers-architecture-01.png)

## <a name="what-does-azure-monitor-for-containers-provide"></a>O que o Azure Monitor fornece para os contentores?

O Azure Monitor para contentores proporciona uma experiência de monitorização abrangente utilizando diferentes características do Azure Monitor. Estas funcionalidades permitem-lhe compreender o desempenho e a saúde do seu cluster Kubernetes que executa o sistema operativo Linux e Windows Server 2019 e as cargas de trabalho dos contentores. Com o Azure Monitor para contentores, pode:

* Identifique os recipientes AKS que estão a funcionar no nó e o seu processador médio e utilização da memória. Este conhecimento pode ajudá-lo a identificar estrangulamentos de recursos.
* Identifique o processador e a utilização da memória dos grupos de contentores e dos seus contentores alojados em Instâncias de Contentores Azure.
* Identifique onde o recipiente reside num controlador ou numa cápsula. Este conhecimento pode ajudá-lo a ver o desempenho geral do controlador ou da cápsula.
* Reveja a utilização de recursos de cargas de trabalho em execução no hospedeiro que não estejam relacionadas com os processos padrão que suportam a cápsula.
* Entenda o comportamento do cluster sob cargas médias e mais pesadas. Este conhecimento pode ajudá-lo a identificar necessidades de capacidade e determinar a carga máxima que o cluster pode suportar.
* Configure alertas para notificá-lo proativamente ou registrá-lo quando a CPU e a utilização da memória em nós ou contentores excederem os seus limiares, ou quando ocorre uma mudança de estado de saúde no cluster na infraestrutura ou nos nós de saúde rollup.
* Integre com [a Prometheus](https://prometheus.io/docs/introduction/overview/) para visualizar as métricas de aplicação e carga de trabalho que recolhe de nós e Kubernetes usando [consultas](container-insights-log-search.md) para criar alertas personalizados, dashboards e análise detalhada detalhada.
* Monitorar as cargas de trabalho dos contentores implantados no local do [motor AKS](https://github.com/Azure/aks-engine) e [no motor AKS em Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
* Monitorar as cargas de trabalho dos contentores [implantadas no Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >Suporte para Azure Red Hat OpenShift é uma funcionalidade em pré-visualização pública neste momento.
    >

* Monitorar as cargas de trabalho [dos contentores implantadas no Arco Azure ativaram kubernetes (pré-visualização)](../../azure-arc/kubernetes/overview.md).

As principais diferenças na monitorização de um cluster do Windows Server em comparação com um cluster Linux são as seguintes:

- O Windows não tem uma métrica RSS de memória e, como resultado, não está disponível para nó e recipientes windows. A métrica [working set](/windows/win32/memory/working-set) está disponível.
- A informação sobre a capacidade de armazenamento do disco não está disponível para os nós Windows.
- Apenas ambientes de pod são monitorizados, não ambientes Docker.
- Com o lançamento da pré-visualização, um máximo de 30 contentores do Windows Server são suportados. Esta limitação não se aplica aos contentores Linux.

Confira o seguinte vídeo que proporciona um mergulho profundo de nível intermédio para ajudá-lo a aprender sobre a monitorização do seu cluster AKS com o Azure Monitor para recipientes.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Como acesso a esta funcionalidade?

Pode aceder ao Azure Monitor para obter recipientes de duas maneiras, a partir do Azure Monitor ou diretamente do cluster AKS selecionado. A partir do Azure Monitor, tem uma perspetiva global de todos os recipientes implantados, que são monitorizados e que não são, permitindo-lhe pesquisar e filtrar através das suas assinaturas e grupos de recursos, e depois perfurar no Azure Monitor para obter recipientes a partir do recipiente selecionado.  Caso contrário, pode aceder diretamente à funcionalidade a partir de um recipiente AKS selecionado a partir da página AKS.

![Panorâmica dos métodos de acesso ao Monitor Azure para contentores](./media/container-insights-overview/azmon-containers-experience.png)

Se estiver interessado em monitorizar e gerir os seus anfitriões de contentores Docker e Windows que estão fora da AKS para visualizar a configuração, auditoria e utilização de recursos, consulte a [solução de Monitorização](./containers.md)do Contentor .

## <a name="next-steps"></a>Próximos passos

Para começar a monitorizar o seu cluster Kubernetes, [reveja como permitir que o Monitor Azure para os recipientes](container-insights-onboard.md) compreendam os requisitos e métodos disponíveis para permitir a monitorização.

