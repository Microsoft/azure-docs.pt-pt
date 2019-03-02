---
title: Conceitos - aplicações à escala dos serviços de Kubernetes no Azure (AKS)
description: Saiba mais sobre dimensionamento no Azure Kubernetes Service (AKS), incluindo o dimensionamento automático de horizontal de pods, dimensionamento automático de cluster e o conector do Azure Container Instances.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: c7019eac4edc530de5ef64ba9eb32e8e4994e75b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245204"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Opções de dimensionamento para aplicações no Azure Kubernetes Service (AKS)

Como executar aplicações no Azure Kubernetes Service (AKS), terá de aumentar ou diminuir a quantidade de recursos de computação. Como o número de instâncias da aplicação tem de alterar o número de Kubernetes subjacente, nós também poderão ter de alterar. Também poderá aprovisionar rapidamente um grande número de instâncias de aplicações adicionais.

Este artigo apresenta as principais conceitos que o ajudam a dimensionar aplicações no AKS:

- [Dimensionar Manualmente](#manually-scale-pods-or-nodes)
- [Horizontal de pods dimensionamento automático (HPA)](#horizontal-pod-autoscaler)
- [Dimensionamento automático de cluster](#cluster-autoscaler)
- [Integração de instância de contentor (ACI) do Azure com o AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Dimensionar Manualmente pods ou nós

Pode dimensionar manualmente réplicas (pods) e os nós para testar como seu aplicativo responde a uma alteração no Estado e de recursos disponíveis. Dimensionar manualmente os recursos também permite-lhe definir uma quantidade de conjunto de recursos para utilizar para manter um custo fixo, como o número de nós. Para dimensionar manualmente, define a réplica ou contagem de nós e as agendas de API do Kubernetes, criar os pods adicionais ou drenagem de nós.

Para obter uma introdução Dimensionar Manualmente pods e nós [dimensionar aplicações no AKS][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Dimensionamento automático de horizontal de pods

Kubernetes utiliza o dimensionamento horizontal de pods automático (HPA) para a procura de recursos de monitorizar e Dimensionar automaticamente o número de réplicas. Por predefinição, o dimensionamento automático horizontal de pods verifica a API de métricas a cada 30 segundos para todas as alterações necessárias na contagem de réplicas. Quando são necessárias alterações, o número de réplicas aumenta ou diminui em conformidade. Dimensionamento automático de horizontal de pods funciona com clusters do AKS que tenham implementado o servidor de métricas para Kubernetes 1.8 +.

![Dimensionamento automático horizontal de pods do Kubernetes](media/concepts-scale/horizontal-pod-autoscaling.png)

Quando configurar o dimensionamento automático horizontal de pods para uma determinada implementação, define o número mínimo e máximo de réplicas que podem ser executadas. Também definir a métrica para monitorizar e deve basear tomar qualquer decisão de dimensionamento, como a utilização da CPU.

Para começar a utilizar com o dimensionamento automático horizontal de pods no AKS, veja [pods de dimensionamento automático no AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Arrefecimento do dimensionamento de eventos

Como o dimensionamento automático horizontal de pods verifica a API de métricas a cada 30 segundos, eventos de dimensionamento anterior podem não foram concluídas com êxito antes de outra verificação é feita. Esse comportamento pode fazer com que o dimensionamento automático horizontal de pods alterar o número de réplicas antes do evento de dimensionamento anterior foi capaz de receber a carga de trabalho de aplicação e as demandas de recursos para ajustar de forma apropriada.

Para minimizar esses eventos de corrida, os valores de arrefecimento ou atraso podem ser definidos. Esses valores definem quanto o dimensionamento automático horizontal de pods têm de aguardar após um evento de dimensionamento antes de outro evento de dimensionamento pode ser acionado. Este comportamento permite que a nova réplica contar entrem em vigor e a API de métricas refletem a carga de trabalho distribuída. Por predefinição, o atraso em escala eventos é de 3 minutos e o atraso em escala para baixo de eventos é de 5 minutos

Terá de ajustar esses valores de arrefecimento. Os valores de arrefecimento predefinidos podem dar a impressão de que o dimensionamento automático horizontal de pods não é dimensionar a contagem de réplica forma suficientemente rápida. Por exemplo, para aumentar o mais rapidamente o número de réplicas em utilização, reduzir a `--horizontal-pod-autoscaler-upscale-delay` quando cria seu horizontal de pods as definições de dimensionamento automático utilizando `kubectl`.

## <a name="cluster-autoscaler"></a>Dimensionamento automático de cluster

Para responder às mudanças na procura de pod, Kubernetes, tem um cluster dimensionamento automático que ajusta o número de nós com base no pedido de recursos de computação no conjunto de nós. O dimensionamento automático de cluster utiliza os parâmetros de arranque para coisas como intervalos de tempo entre eventos de dimensionamento e limiares de recursos. Se o dimensionamento automático de cluster determina que uma alteração é necessária, o número de nós no cluster do AKS aumenta ou diminui em conformidade. Executam clusters do AKS que utilizam o dimensionamento automático de cluster em conjuntos de dimensionamento de máquina virtual para gerir o dimensionamento de cópia de segurança e reduzir verticalmente eventos de nós do AKS.

![Dimensionamento automático de cluster do Kubernetes](media/concepts-scale/cluster-autoscaler.png)

Dimensionamento automático do cluster é normalmente utilizado em conjunto com o dimensionamento automático horizontal de pods. Quando combinadas, o dimensionamento automático horizontal de pods aumenta ou diminui o número de pods com base na demanda de aplicativo, e o dimensionamento automático de cluster ajusta o número de nós, conforme necessário para executar os pods adicionais em conformidade.

Para começar a utilizar com o dimensionamento automático de cluster no AKS, veja [dimensionamento automático de Cluster no AKS][aks-cluster-autoscaler].

### <a name="scale-up-events"></a>Aumentar verticalmente eventos

Se um nó não tem recursos de computação suficiente para executar um pod pedido, esse pod não é possível percorre o processo de agendamento. O pod não é possível iniciar a menos que os recursos de computação adicionais estão disponíveis dentro do conjunto de nó.

Quando o dimensionamento automático de cluster avisos pods que não não possível agendar devido a restrições de recursos do conjunto de nó, o número de nós dentro do conjunto de nó é aumentado para fornecer os recursos de computação adicionais. Quando esses nós adicionais estão disponíveis para utilização dentro do conjunto de nós e implementado com êxito, os pods, em seguida, são agendados para serem executadas nos mesmos.

Se a sua aplicação precisa para dimensionar rapidamente, alguns pods podem permanecer num Estado aguardar para ser agendados até que os nós adicionais implementados pelo dimensionamento automático do cluster podem aceitar os pods agendados. Para aplicações que têm a rajadas alta demanda, pode dimensionar connosco virtuais e o Azure Container Instances.

### <a name="scale-down-events"></a>Reduzir verticalmente eventos

O dimensionamento automático de cluster também monitoriza o estado de nós que não tenham recebido recentemente novas solicitações de agendamento de agendamento de pod. Este cenário indica que o conjunto de nós tem mais recursos de computação do que são necessárias e que o número de nós pode ser reduzido.

Um nó que passa um limiar para já não está a ser necessário durante 10 minutos por predefinição está agendado para eliminação. Quando essa situação ocorre, pods são agendadas para serem executadas nos outros nós dentro do conjunto de nó e o dimensionamento automático de cluster diminui o número de nós.

As aplicações podem ter alguma interrupção como pods são agendadas em nós diferentes quando o dimensionamento automático de cluster diminui o número de nós. Para minimizar a interrupção, evite aplicativos que usam uma instância de único pod.

## <a name="burst-to-azure-container-instances"></a>Períodos de rajada de instâncias de contentor do Azure

Para dimensionar rapidamente o seu cluster do AKS, pode integrar com o Azure Container Instances (ACI). Kubernetes tem componentes internos para dimensionar a contagem de nó e de réplica. No entanto, se a sua aplicação precisa para dimensionar rapidamente, o dimensionamento automático horizontal de pods pode agendar pods mais do que podem ser fornecidas pelos recursos de computação existente no conjunto de nós. Se configurado, este cenário, em seguida, irá acionar o dimensionamento automático de cluster para implementar a outros nós do conjunto de nós, mas poderá demorar alguns minutos para que esses nós aprovisionar e permitir que o agendador de Kubernetes executar os pods neles com êxito.

![Kubernetes períodos de rajada de dimensionamento no ACI](media/concepts-scale/burst-scaling.png)

ACI permite-lhe implementar rapidamente as instâncias de contentor sem sobrecarga de infraestrutura adicional. Quando se liga-se com o AKS, ACI torna-se uma extensão protegida e lógica do seu cluster do AKS. O Virtual Kubelet componente está instalado no seu cluster do AKS que apresenta o ACI como um nó virtual de Kubernetes. Kubernetes, em seguida, pode agendar pods que executam como instâncias ACI através de nós virtuais, e não como pods em nós VM diretamente no cluster do AKS.

Seu aplicativo não requer nenhuma modificação para utilizar nós virtuais. Implementações podem ser dimensionado em AKS e ACI e sem atrasos como cluster de dimensionamento automático implementa novos nós no cluster do AKS.

Nós virtuais são implementados a uma sub-rede adicional na mesma rede virtual que o cluster do AKS. Esta configuração de rede virtual permite que o tráfego entre o ACI e o AKS a ser protegido. Como um cluster do AKS, uma instância do ACI é um recurso de computação seguro e lógica que está isolado de outros utilizadores.

## <a name="next-steps"></a>Passos Seguintes

Para começar a utilizar com o dimensionamento de aplicações, siga os [início rápido para criar um cluster do AKS com a CLI do Azure][aks-quickstart]. Em seguida, pode iniciar manualmente ou automaticamente dimensionar aplicações no cluster do AKS:

- Dimensionar manualmente [pods] [ aks-manually-scale-pods] ou [nós][aks-manually-scale-nodes]
- Utilize o [dimensionamento automático de horizontal de pods][aks-hpa]
- Utilize o [dimensionamento automático do cluster][aks-cluster-autoscaler]

Para obter mais informações sobre principais Kubernetes e conceitos do AKS, consulte os artigos seguintes:

- [Kubernetes / clusters do AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Kubernetes / AKS acesso e identidade][aks-concepts-identity]
- [Kubernetes / segurança AKS][aks-concepts-security]
- [Kubernetes / redes virtuais do AKS][aks-concepts-network]
- [Kubernetes / armazenamento AKS][aks-concepts-storage]

<!-- LINKS - external -->

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
