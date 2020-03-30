---
title: Conceitos - Aplicações de escala nos Serviços Azure Kubernetes (AKS)
description: Aprenda sobre a escala no Serviço Azure Kubernetes (AKS), incluindo autoscaler de cápsula horizontal, autoscaler cluster e o conector De instâncias de contentores Azure.
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: 396e5bc31723768ada334dd5043bca724af5e84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595863"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Opções de dimensionamento para aplicações no Serviço Kubernetes do Azure (AKS)

À medida que executa aplicações no Serviço Azure Kubernetes (AKS), poderá ter de aumentar ou diminuir a quantidade de recursos computacionais. Como o número de casos de aplicação que precisa de ser alterado, o número de nós kubernetes subjacentes também pode ter de mudar. Também poderá ter de fornecer rapidamente um grande número de casos adicionais de candidatura.

Este artigo introduz os conceitos fundamentais que o ajudam a escalar aplicações no AKS:

- [Escala manual](#manually-scale-pods-or-nodes)
- [Autoscaler de cápsula horizontal (HPA)](#horizontal-pod-autoscaler)
- [Autoscaler cluster](#cluster-autoscaler)
- [Integração da Instância de Contentores Azure (ACI) com a AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Pods ou nósos de escala manual

Pode escalar manualmente réplicas (cápsulas) e nós para testar como a sua aplicação responde a uma mudança de recursos disponíveis e estado. Os recursos de escala manual também permitem definir uma quantidade fixa de recursos para usar para manter um custo fixo, como o número de nós. Para escalar manualmente, define a réplica ou a contagem do nó. A API kubernetes então programa a criação de cápsulas adicionais ou a drenagem de nódosos com base nessa réplica ou contagem de nó.

Ao escalonar os nós, a API kubernetes chama a API azure compute relevante ligada ao tipo de computação utilizado pelo seu cluster. Por exemplo, para os clusters construídos em escala VM define a lógica para selecionar quais os nós a remover é determinada pela API de Conjuntos de Escala VM. Para saber mais sobre como os nós são selecionados para remoção na escala para baixo, consulte o [VMSS FAQ](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed).

Para começar com cápsulas de escala manual e nótores ver [aplicações Scale em AKS][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Autoscaler de pod horizontal

Kubernetes utiliza o autoscaler horizontal da cápsula (HPA) para monitorizar a procura de recursos e escalar automaticamente o número de réplicas. Por predefinição, o autoescalador horizontal da cápsula verifica o API métrico a cada 30 segundos para quaisquer alterações necessárias na contagem de réplicas. Quando são necessárias alterações, o número de réplicas é aumentado ou diminuído em conformidade. O autoscaler horizontal da cápsula funciona com clusters AKS que implantaram o Servidor de Métricas para Kubernetes 1.8+.

![Autoscalcificação horizontal Kubernetes](media/concepts-scale/horizontal-pod-autoscaling.png)

Quando configurar o autoescalador horizontal para uma determinada implantação, define o número mínimo e máximo de réplicas que podem ser executadas. Também define a métrica para monitorizar e basear quaisquer decisões de escala, como o uso de CPU.

Para começar com o autoscaler horizontal em AKS, consulte [as cápsulas de escala automática em AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Arrefecimento de eventos de escala

À medida que o autoescalador horizontal da cápsula verifica o API métrico a cada 30 segundos, os eventos de escala anteriores podem não ter sido concluídos com sucesso antes de ser feito outro controlo. Este comportamento pode fazer com que o autoscaler horizontal da cápsula altere o número de réplicas antes que o evento de escala anterior possa receber carga de trabalho de aplicação e as exigências dos recursos se ajustarem em conformidade.

Para minimizar estes eventos de corrida, os valores de arrefecimento ou atraso são definidos. Estes valores definem quanto tempo o autoscaler horizontal da cápsula deve esperar após um evento de escala antes que outro evento de escala possa ser desencadeado. Este comportamento permite que a nova contagem de réplicas produza efeito e a API métricas reflita a carga de trabalho distribuída. Por padrão, o atraso na escala de eventos é de 3 minutos, e o atraso na escala para baixo eventos é de 5 minutos

Atualmente, não é possível afinar estes valores de arrefecimento a partir do padrão.

## <a name="cluster-autoscaler"></a>Autoscaler cluster

Para responder às exigências da pod em mudança, a Kubernetes tem um autoscaler cluster, que ajusta o número de nós com base nos recursos de computação solicitados na piscina do nó. Por predefinição, o autoescalador do cluster verifica o servidor API métrico a cada 10 segundos para quaisquer alterações necessárias na contagem de nós. Se a escala automática do cluster determinar que é necessária uma alteração, o número de nós no seu cluster AKS é aumentado ou diminuído em conformidade. O autoscaler cluster funciona com clusters AKS ativados por RBAC que executam Kubernetes 1.10.x ou superiores.

![Autoscaler de cluster Kubernetes](media/concepts-scale/cluster-autoscaler.png)

O autoscaler cluster é normalmente utilizado ao lado do autoescalador horizontal da cápsula. Quando combinado, o autoescalador horizontal aumenta ou diminui o número de cápsulas com base na procura de aplicação, e o autoescalador do cluster ajusta o número de nós conforme necessário para executar essas cápsulas adicionais em conformidade.

Para começar com o autoscaler cluster em AKS, consulte [Cluster Autoscaler em AKS][aks-cluster-autoscaler].

### <a name="scale-up-events"></a>Escalar eventos

Se um nó não tiver recursos de computação suficientes para executar uma cápsula solicitada, essa cápsula não pode progredir através do processo de agendamento. A cápsula não pode começar a menos que recursos de computação adicionais estejam disponíveis dentro da piscina do nó.

Quando o autoscaler do cluster nota cápsulas que não podem ser programadas devido a restrições de recursos de piscina de nós, o número de nós dentro da piscina do nó é aumentado para fornecer os recursos de computação adicionais. Quando esses nós adicionais forem implantados com sucesso e disponíveis para uso dentro da piscina do nó, as cápsulas estão então programadas para funcionar neles.

Se a sua aplicação precisar de escala rápida, algumas cápsulas podem permanecer em estado à espera de serem programadas até que os nós adicionais implantados pelo autoscaler do cluster possam aceitar as cápsulas programadas. Para aplicações com elevadas exigências de explosão, pode escalar com nós virtuais e instâncias de contentores Azure.

### <a name="scale-down-events"></a>Escala de eventos

O autoscaler do cluster também monitoriza o estado de agendamento do pod para nós que não receberam recentemente novos pedidos de agendamento. Este cenário indica que o conjunto do nó tem mais recursos computacionais do que os necessários, e o número de nós pode ser diminuído.

Um nó que passa um limiar por deixar de ser necessário por 10 minutos por defeito está agendado para a eliminação. Quando esta situação ocorre, as cápsulas estão programadas para correr em outros nós dentro da piscina do nó, e o autoescalador do cluster diminui o número de nós.

As suas aplicações podem sofrer algumas perturbações, uma vez que as cápsulas são programadas em diferentes nós quando o autoescalador de cluster diminui o número de nós. Para minimizar a perturbação, evite aplicações que utilizem uma única instância de pod.

## <a name="burst-to-azure-container-instances"></a>Explosão para instâncias de contentores de Azure

Para escalar rapidamente o seu cluster AKS, pode integrar-se com instâncias de contentores Azure (ACI). Kubernetes tem componentes incorporados para escalar a réplica e a contagem de nó. No entanto, se a sua aplicação precisar de uma escala rápida, o autoscaler horizontal pode agendar mais cápsulas do que pode ser fornecida pelos recursos computacionais existentes na piscina do nó. Se configurado, este cenário desencadearia então o autoescalador do cluster para implantar nós adicionais na piscina do nó, mas pode levar alguns minutos para que esses nós oprovisionem com sucesso e permitam que o programador kubernetes execute cápsulas neles.

![Kubernetes rebentou escalando para ACI](media/concepts-scale/burst-scaling.png)

O ACI permite-lhe implementar rapidamente instâncias de contentores sem despesas adicionais de infraestruturas. Quando se conecta com AKS, o ACI torna-se uma extensão lógica segura do seu cluster AKS. O componente [de nós virtuais,][virtual-nodes-cli] que é baseado em [Virtual Kubelet,][virtual-kubelet]está instalado no seu cluster AKS que apresenta ACI como um nó virtual de Kubernetes. Os Kubernetes podem então agendar cápsulas que funcionam como instâncias ACI através de nós virtuais, e não como casulos em nós VM diretamente no seu cluster AKS. Os nódosos virtuais estão atualmente em pré-visualização no AKS.

A sua aplicação não necessita de modificações para utilizar nódosos virtuais. As implementações podem escalar através de AKS e ACI e sem demora, uma vez que o cluster autoscaler implementa novos nós no seu cluster AKS.

Os nós virtuais são implantados para uma subrede adicional na mesma rede virtual que o seu cluster AKS. Esta configuração de rede virtual permite que o tráfego entre ACI e AKS seja protegido. Como um cluster AKS, uma instância DeCI é um recurso de computação lógica e seguro que é isolado de outros utilizadores.

## <a name="next-steps"></a>Passos seguintes

Para começar com aplicações de escala, siga primeiro o [quickstart para criar um cluster AKS com o Azure CLI][aks-quickstart]. Em seguida, pode começar a escalar aplicações manual ou automaticamente no seu cluster AKS:

- Pods [pods][aks-manually-scale-pods] ou [nósos][aks-manually-scale-nodes] de escala manual
- Utilize o [autoscaler horizontal da cápsula][aks-hpa]
- Utilize o [autoscaler][aks-cluster-autoscaler] do cluster

Para obter mais informações sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

- [Aglomerados kubernetes / AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Kubernetes / ACESSO aks e identidade][aks-concepts-identity]
- [Kubernetes / Segurança AKS][aks-concepts-security]
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
[aks-cluster-autoscaler]: autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md