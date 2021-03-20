---
title: Conceitos - Aplicações em escala nos Serviços Azure Kubernetes (AKS)
description: Saiba mais sobre o dimensionamento no Serviço Azure Kubernetes (AKS), incluindo o autoescalador horizontal, o autoescalador de clusters e o conector Azure Container Instances.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: b72ed7cefc6a16eb484e1337dbd64e5f069a2201
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94686043"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Opções de dimensionamento para aplicações no Serviço Kubernetes do Azure (AKS)

À medida que executam aplicações no Serviço Azure Kubernetes (AKS), poderá ter de aumentar ou diminuir a quantidade de recursos computacional. Como o número de casos de aplicação que precisa de ser alterado, o número de nós kubernetes subjacentes também pode ter de ser alterado. Também poderá ser necessário prever rapidamente um grande número de casos adicionais de aplicação.

Este artigo introduz os conceitos fundamentais que o ajudam a escalar aplicações em AKS:

- [Escala manual](#manually-scale-pods-or-nodes)
- [Autoescalador de cápsulas horizontais (HPA)](#horizontal-pod-autoscaler)
- [Dimensionador automático de cluster](#cluster-autoscaler)
- [Integração de Azure Container Instance (ACI) com AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Cápsulas ou nóns de escala manual

Pode escalar manualmente réplicas (cápsulas) e nós para testar como a sua aplicação responde a uma mudança nos recursos disponíveis e no estado. Os recursos de dimensionamento manual também permitem definir uma quantidade definida de recursos para usar para manter um custo fixo, como o número de nós. Para escalar manualmente, define-se a réplica ou a contagem de nós. A API de Kubernetes programa então a criação de cápsulas adicionais ou nóleiros de drenagem com base nessa réplica ou contagem de nós.

Ao escalonar os nós, a API de Kubernetes chama a API AZURE Compute relevante ligada ao tipo de computação utilizado pelo seu cluster. Por exemplo, para os clusters construídos na escala VM define a lógica para a seleção dos nós a remover é determinada pela API dos Conjuntos de Escala VM. Para saber mais sobre como os nós são selecionados para remoção em escala baixa, consulte as [FAQ VMSS](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed).

Para começar com cápsulas de escala manual e nós ver [aplicações de escala em AKS][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Dimensionador automático de pods horizontal

Kubernetes utiliza o autoescalador horizontal (HPA) para monitorizar a procura de recursos e escalar automaticamente o número de réplicas. Por predefinição, o autoescalador horizontal verifica a API métrica a cada 30 segundos para quaisquer alterações necessárias na contagem de réplicas. Quando são necessárias alterações, o número de réplicas é aumentado ou diminuído em conformidade. O autoescalador horizontal funciona com clusters AKS que implementaram o Metrics Server para Kubernetes 1.8+.

![Kubernetes pod horizontal autoscaling](media/concepts-scale/horizontal-pod-autoscaling.png)

Ao configurar o autoescala horizontal para uma determinada implantação, define-se o número mínimo e máximo de réplicas que podem ser executadas. Também define a métrica para monitorizar e basear quaisquer decisões de escala, como o uso do CPU.

Para começar com o autoescalador horizontal em AKS, consulte [as cápsulas autoescala em AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Arrefecimento de eventos de escala

Como o autoescalador horizontal verifica a API métrica a cada 30 segundos, os eventos de escala anteriores podem não ter sido concluídos com sucesso antes de ser feita outra verificação. Este comportamento pode fazer com que o autoescalador horizontal altere o número de réplicas antes que o evento de escala anterior possa receber a carga de trabalho da aplicação e as exigências do recurso se ajustem em conformidade.

Para minimizar os eventos de corrida, é definido um valor de atraso. Este valor define quanto tempo o autoescalador horizontal deve esperar depois de um evento de escala antes que outro evento de escala possa ser ativado. Este comportamento permite que a nova contagem de réplicas produza efeitos e a API métrica reflita a carga de trabalho distribuída. Não [há atrasos para eventos de escala a partir de Kubernetes 1.12](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#support-for-cooldown-delay), no entanto o atraso na escala de eventos está em incumprimento de 5 minutos.

Atualmente, não é possível sintonizar estes valores de arrefecimento a partir do padrão.

## <a name="cluster-autoscaler"></a>Dimensionador automático de cluster

Para responder às exigências do pod em mudança, a Kubernetes tem um cluster autoscaler, que ajusta o número de nós com base nos recursos de computação solicitados no conjunto de nós. Por predefinição, o autoescalador do cluster verifica o servidor API métricas a cada 10 segundos para quaisquer alterações necessárias na contagem de nós. Se o cluster determinar que é necessária uma alteração, o número de nós no seu cluster AKS é aumentado ou diminuído em conformidade. O autoescalador de cluster funciona com clusters AKS ativados por Kubernetes que executam Kubernetes 1.10.x ou superior.

![Autoescalador de cluster Kubernetes](media/concepts-scale/cluster-autoscaler.png)

O autoescalador de cluster é normalmente utilizado ao lado do autoescalador horizontal. Quando combinado, o autoescalador horizontal aumenta ou diminui o número de cápsulas com base na procura de aplicação, e o autoescalador do cluster ajusta o número de nós conforme necessário para executar essas cápsulas adicionais em conformidade.

Para começar com o cluster autoscaler em AKS, consulte [Cluster Autoscaler em AKS][aks-cluster-autoscaler].

### <a name="scale-out-events"></a>Escalar eventos

Se um nó não tiver recursos computacional suficientes para executar uma cápsula solicitada, essa cápsula não pode progredir através do processo de agendamento. A cápsula não pode arrancar a menos que existam recursos adicionais de computação dentro da piscina de nós.

Quando o autoescalador do cluster nota pods que não podem ser programados devido a restrições de recursos de piscina de nó, o número de nós dentro da piscina do nó é aumentado para fornecer os recursos adicionais de computação. Quando esses nós adicionais são implantados com sucesso e disponíveis para uso dentro da piscina de nós, as cápsulas são então programadas para funcionar sobre eles.

Se a sua aplicação precisar de escalar rapidamente, algumas cápsulas podem permanecer num estado à espera de serem agendadas até que os nós adicionais implantados pelo autoescalador do cluster possam aceitar as cápsulas programadas. Para aplicações que tenham elevadas exigências de explosão, pode escalar com nós virtuais e instâncias de contentores Azure.

### <a name="scale-in-events"></a>Escala em eventos

O autoescalador do cluster também monitoriza o estado de agendamento de pods para nós que não receberam recentemente novos pedidos de agendamento. Este cenário indica que o conjunto de nós tem mais recursos computetados do que os necessários, e o número de nós pode ser diminuído.

Um nó que passa um limiar para deixar de ser necessário por 10 minutos por defeito está programado para a eliminação. Quando esta situação ocorre, as cápsulas são programadas para funcionar em outros nós dentro da piscina do nó, e o autoescalador cluster diminui o número de nós.

As suas aplicações podem sofrer algumas perturbações, uma vez que as cápsulas são programadas em diferentes nós quando o autoescalador do cluster diminui o número de nós. Para minimizar a perturbação, evite aplicações que utilizem uma única instância de pod.

## <a name="burst-to-azure-container-instances"></a>Explosão em Instâncias de Contentores de Azure

Para escalar rapidamente o seu cluster AKS, pode integrar-se com instâncias de contentores Azure (ACI). Kubernetes tem componentes incorporados para escalar a réplica e a contagem de nós. No entanto, se a sua aplicação precisar de escalar rapidamente, o autoescalador horizontal pode agendar mais cápsulas do que os recursos de computação existentes no conjunto de nós. Se configurado, este cenário desencadearia o autoescalador do cluster para implantar nós adicionais na piscina de nós, mas pode levar alguns minutos para que esses nós provisem com sucesso e permitam que o programador Kubernetes erque as cápsulas neles.

![Kubernetes explodem escalando para ACI](media/concepts-scale/burst-scaling.png)

O ACI permite-lhe implantar rapidamente casos de contentores sem sobrecargas adicionais de infraestruturas. Quando se conecta com a AKS, a ACI torna-se uma extensão lógica segura do seu cluster AKS. O componente [de nós virtuais,][virtual-nodes-cli] baseado no [Kubelet Virtual,][virtual-kubelet]está instalado no seu cluster AKS que apresenta o ACI como um nó virtual de Kubernetes. Os Kubernetes podem então agendar cápsulas que funcionam como instâncias ACI através de nós virtuais, e não como pods nos nós VM diretamente no seu cluster AKS.

A sua aplicação não requer modificação para usar nós virtuais. As implementações podem escalar através de AKS e ACI e sem atrasos, uma vez que o autoescalador de cluster implementa novos nós no seu cluster AKS.

Os nós virtuais são implantados numa sub-rede adicional na mesma rede virtual que o seu cluster AKS. Esta configuração de rede virtual permite que o tráfego entre ACI e AKS seja seguro. Como um cluster AKS, um caso ACI é um recurso de computação segura e lógica que é isolado de outros utilizadores.

## <a name="next-steps"></a>Passos seguintes

Para começar com aplicações de escala, siga primeiro o [quickstart para criar um cluster AKS com o Azure CLI][aks-quickstart]. Em seguida, pode começar a escalar as aplicações manualmente ou automaticamente no seu cluster AKS:

- Cápsulas ou [nóns][aks-manually-scale-pods] de escala manual [][aks-manually-scale-nodes]
- Use o [autoscaler da cápsula horizontal][aks-hpa]
- Utilize o [autoescalador][aks-cluster-autoscaler] de cluster

Para obter mais informações sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

- [Aglomerados de Kubernetes / AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Acesso e identidade kubernetes / AKS][aks-concepts-identity]
- [Segurança Kubernetes / AKS][aks-concepts-security]
- [Redes virtuais Kubernetes / AKS][aks-concepts-network]
- [Armazenamento Kubernetes / AKS][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md
