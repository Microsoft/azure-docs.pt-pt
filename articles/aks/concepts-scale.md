---
title: Conceitos – dimensionar aplicativos nos serviços Kubernetess do Azure (AKS)
description: Saiba mais sobre o dimensionamento no AKS (serviço de kubernetes do Azure), incluindo o conector automático de Pod horizontal, o dimensionamento automático de cluster e o Azure container instances Connector.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: aaa279596532d3a1d47a974b48a45bd67101fa95
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768627"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Opções de dimensionamento para aplicativos no serviço kubernetes do Azure (AKS)

Ao executar aplicativos no AKS (serviço kubernetes do Azure), talvez seja necessário aumentar ou diminuir a quantidade de recursos de computação. Como o número de instâncias de aplicativo que você precisa alterar, o número de nós kubernetes subjacentes também pode precisar ser alterado. Você também pode precisar provisionar rapidamente um grande número de instâncias de aplicativo adicionais.

Este artigo apresenta os principais conceitos que ajudam a dimensionar aplicativos no AKS:

- [Dimensionar manualmente](#manually-scale-pods-or-nodes)
- [HPA (autodimensionamento de Pod horizontal)](#horizontal-pod-autoscaler)
- [Autoescalar do cluster](#cluster-autoscaler)
- [Integração de ACI (instância de contêiner do Azure) com AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Dimensionar manualmente os pods ou nós

Você pode dimensionar manualmente as réplicas (PODS) e os nós para testar como seu aplicativo responde a uma alteração nos recursos e no estado disponíveis. O dimensionamento manual de recursos também permite que você defina uma quantidade definida de recursos a serem usados para manter um custo fixo, como o número de nós. Para dimensionar manualmente, você define a réplica ou a contagem de nós. A API kubernetes, em seguida, agenda a criação de pods adicionais ou a descarga de nós com base nessa réplica ou contagem de nós.

Ao reduzir os nós verticalmente, a API kubernetes chama a API de computação do Azure relevante vinculada ao tipo de computação usado pelo cluster. Por exemplo, para clusters criados em escala de VM define a lógica para selecionar quais nós remover é determinada pela API dos conjuntos de dimensionamento de VM. Para saber mais sobre como os nós são selecionados para remoção na redução vertical, consulte as [perguntas frequentes do VMSS](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed).

Para começar a dimensionar manualmente os pods e os nós, consulte [dimensionar aplicativos em AKs][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Autoescalar do pod horizontal

O kubernetes usa o HPA (dimensionamento automático de Pod horizontal) para monitorar a demanda de recursos e dimensionar automaticamente o número de réplicas. Por padrão, a escala automática de Pod horizontal verifica a API de métrica a cada 30 segundos em busca de quaisquer alterações necessárias na contagem de réplicas. Quando as alterações são necessárias, o número de réplicas aumenta ou diminui adequadamente. O autodimensionador de Pod horizontal funciona com clusters AKS que implantaram o servidor de métricas para kubernetes 1.8 +.

![Dimensionamento automático de Pod horizontal kubernetes](media/concepts-scale/horizontal-pod-autoscaling.png)

Ao configurar a escala automática de Pod horizontal para uma determinada implantação, você define o número mínimo e máximo de réplicas que podem ser executadas. Você também define a métrica para monitorar e basear as decisões de dimensionamento, como o uso da CPU.

Para começar a usar o dimensionamento automático de Pod horizontal em AKS, confira autoescala [pods em AKs][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Cooldown de eventos de dimensionamento

Como a escala automática de Pod horizontal verifica a API de métrica a cada 30 segundos, os eventos de escala anterior podem não ter sido concluídos com êxito antes que outra verificação seja feita. Esse comportamento pode fazer com que a escala automática de Pod horizontal altere o número de réplicas antes que o evento de escala anterior pudesse receber a carga de trabalho do aplicativo e que as demandas de recursos se ajustem de acordo.

Para minimizar esses eventos de corrida, cooldown ou valores de atraso são definidos. Esses valores definem por quanto tempo o dimensionador horizontal Pod deve aguardar após um evento de escala antes que outro evento de escala possa ser disparado. Esse comportamento permite que a nova contagem de réplica entre em vigor e a API de métricas reflita a carga de trabalho distribuída. Por padrão, o atraso em eventos de expansão é de 3 minutos e o atraso em eventos de redução vertical é de 5 minutos

No momento, não é possível ajustar esses valores de cooldown do padrão.

## <a name="cluster-autoscaler"></a>Autoescalar do cluster

Para responder à alteração das demandas de Pod, o kubernetes tem um conjunto de dimensionamento automática de cluster, que ajusta o número de nós com base nos recursos de computação solicitados no pool de nós. Por padrão, o dimensionador automática do cluster verifica o servidor de API de métrica a cada 10 segundos para todas as alterações necessárias na contagem de nós. Se o dimensionamento automático do cluster determinar que uma alteração é necessária, o número de nós no cluster AKS será aumentado ou diminuído adequadamente. O dimensionador de clusters em autoescalar funciona com clusters AKS habilitados para RBAC que executam o kubernetes 1.10. x ou superior.

![Autoescalar do cluster kubernetes](media/concepts-scale/cluster-autoscaler.png)

O dimensionamento em escala geral do cluster normalmente é usado junto com o dimensionamento horizontal do pod. Quando combinado, o pod de dimensionamento horizontal aumenta ou diminui o número de pods com base na demanda do aplicativo e o dimensionamento automática do cluster ajusta o número de nós conforme necessário para executar os pods adicionais de forma adequada.

Para começar a usar o cluster de dimensionamento em AKS, consulte o [dimensionamento de cluster em AKs][aks-cluster-autoscaler].

### <a name="scale-up-events"></a>Escalar verticalmente os eventos

Se um nó não tiver recursos de computação suficientes para executar um pod solicitado, esse Pod não poderá progredir pelo processo de agendamento. O Pod não pode iniciar, a menos que recursos de computação adicionais estejam disponíveis no pool de nós.

Quando o dimensionador automática do cluster observa pods que não pode ser agendado devido a restrições de recursos do pool de nós, o número de nós dentro do pool de nós é aumentado para fornecer os recursos de computação adicionais. Quando esses nós adicionais são implantados com êxito e estão disponíveis para uso dentro do pool de nós, os pods são agendados para serem executados neles.

Se o seu aplicativo precisar ser dimensionado rapidamente, alguns pods podem permanecer em um estado aguardando para ser agendado até que os nós adicionais implantados pelo dimensionamento rápido do cluster possam aceitar os pods agendados. Para aplicativos que têm altas demandas de intermitência, você pode dimensionar com nós virtuais e instâncias de contêiner do Azure.

### <a name="scale-down-events"></a>Reduzir eventos de redução

O autoescalar do cluster também monitora o status de agendamento do pod para nós que não receberam novas solicitações de agendamento recentemente. Esse cenário indica que o pool de nós tem mais recursos de computação que os necessários e o número de nós pode ser reduzido.

Um nó que passa um limite para não ser mais necessário por 10 minutos por padrão é agendado para exclusão. Quando essa situação ocorre, os pods são agendados para serem executados em outros nós no pool de nós, e o dimensionamento automática do cluster diminui o número de nós.

Seus aplicativos podem experimentar algumas interrupções, pois os pods são agendados em nós diferentes quando o dimensionamento automática do cluster diminui o número de nós. Para minimizar a interrupção, evite aplicativos que usam uma única instância de Pod.

## <a name="burst-to-azure-container-instances"></a>Disparo para instâncias de contêiner do Azure

Para dimensionar rapidamente seu cluster AKS, você pode integrar com ACI (instâncias de contêiner do Azure). O kubernetes tem componentes internos para dimensionar a réplica e a contagem de nós. No entanto, se o seu aplicativo precisar ser dimensionado rapidamente, o pod de dimensionamento horizontal pode agendar mais pods do que pode ser fornecido pelos recursos de computação existentes no pool de nós. Se configurado, esse cenário acionaria o dimensionador automático do cluster para implantar nós adicionais no pool de nós, mas pode levar alguns minutos para que esses nós provisionem com êxito e permitam que o Agendador kubernetes execute pods neles.

![Escala de intermitência kubernetes para ACI](media/concepts-scale/burst-scaling.png)

O ACI permite que você implante rapidamente instâncias de contêiner sem sobrecarga adicional de infraestrutura. Quando você se conecta com o AKS, o ACI se torna uma extensão lógica segura do seu cluster AKS. O componente de [nós virtuais][virtual-nodes-cli] , que se baseia em [Kubelet virtual][virtual-kubelet], é instalado em seu cluster AKs que apresenta ACI como um nó de kubernetes virtual. Kubernetes pode então agendar pods que executam como instâncias de ACI por meio de nós virtuais, não como pods nos nós de VM diretamente no cluster AKS. Nós virtuais estão atualmente em visualização no AKS.

Seu aplicativo não requer nenhuma modificação para usar nós virtuais. As implantações podem ser dimensionadas entre AKS e ACI e sem atraso, pois o dimensionamento rápido de cluster implanta novos nós em seu cluster AKS.

Os nós virtuais são implantados em uma sub-rede adicional na mesma rede virtual que o cluster AKS. Essa configuração de rede virtual permite que o tráfego entre ACI e AKS seja protegido. Como um cluster AKS, uma instância de ACI é um recurso de computação lógico seguro que é isolado de outros usuários.

## <a name="next-steps"></a>Passos seguintes

Para começar a dimensionar aplicativos, primeiro siga o guia de [início rápido para criar um cluster AKs com o CLI do Azure][aks-quickstart]. Em seguida, você pode começar a dimensionar manualmente ou automaticamente os aplicativos em seu cluster AKS:

- Dimensionar manualmente os [pods][aks-manually-scale-pods] ou [nós][aks-manually-scale-nodes]
- Usar o [dimensionamento de escalabilidade horizontal][aks-hpa]
- Usar o [dimensionamento de cluster][aks-cluster-autoscaler]

Para obter mais informações sobre os principais conceitos de kubernetes e AKS, consulte os seguintes artigos:

- [Clusters e cargas de trabalho do kubernetes/AKS][aks-concepts-clusters-workloads]
- [Acesso e identidade de kubernetes/AKS][aks-concepts-identity]
- [Segurança do kubernetes/AKS][aks-concepts-security]
- [Redes virtuais kubernetes/AKS][aks-concepts-network]
- [Armazenamento kubernetes/AKS][aks-concepts-storage]

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