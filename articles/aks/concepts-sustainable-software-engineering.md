---
title: Conceitos - Engenharia de software sustentável nos Serviços Azure Kubernetes (AKS)
description: Saiba mais sobre engenharia de software sustentável no Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: c43c65dfa2f3930510bd59aaa24c798525bd691b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011496"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Princípios de engenharia de software sustentáveis no Serviço Azure Kubernetes (AKS)

Os princípios de engenharia de software sustentável são um conjunto de competências para ajudá-lo a definir, construir e executar aplicações sustentáveis. O objetivo geral é reduzir a sua pegada de carbono de todos os aspetos da sua aplicação. [Os Princípios da Engenharia de Software Sustentável][principles-sse] têm uma visão geral dos princípios da engenharia de software sustentável.

A engenharia de software sustentável é uma mudança de prioridades e foco. Em muitos casos, a forma como a maioria do software é projetado e executado destaca o rápido desempenho e baixa latência. Entretanto, a engenharia de software sustentável centra-se na redução do máximo de emissões de carbono possível. Considere:

* A aplicação de princípios de engenharia de software sustentáveis pode dar-lhe um desempenho mais rápido ou uma menor latência, como por exemplo, reduzindo o total das viagens de rede. 
* A redução das emissões de carbono pode causar um desempenho mais lento ou um aumento da latência, como o atraso das cargas de trabalho de baixa prioridade. 

Antes de aplicar princípios de engenharia de software sustentáveis à sua aplicação, reveja as prioridades, necessidades e compensações da sua aplicação.

## <a name="measure-and-optimize"></a>Medir e otimizar

Para reduzir a pegada de carbono dos seus clusters AKS, precisa entender como os recursos do seu cluster estão sendo usados. [O Azure Monitor][azure-monitor] fornece detalhes sobre a utilização de recursos do seu cluster, como a memória e o uso do CPU. Estes dados informam a sua decisão de reduzir a pegada de carbono do seu cluster e observam o efeito das suas alterações. 

Também pode instalar a [Calculadora de Sustentabilidade][sustainability-calculator] da Microsoft para ver a pegada de carbono de todos os seus recursos Azure.

## <a name="increase-resource-utilization"></a>Aumentar a utilização de recursos

Uma abordagem para baixar a sua pegada de carbono é reduzir o seu tempo de marcha lenta. Reduzir o seu tempo de inatividade envolve aumentar a utilização dos seus recursos de computação. Por exemplo:
1. Tinhas quatro nós no teu agrupamento, cada um a correr a 50% da capacidade. Então, todos os seus quatro nós têm 50% de capacidade não lavada permanecendo inativo. 
1. Reduziu o seu cluster a três nós, cada um com 67% de capacidade com a mesma carga de trabalho. Teria diminuído com sucesso a sua capacidade não utilizada para 33% em cada nó e aumentado a sua utilização.

> [!IMPORTANT]
> Ao considerar alterar os recursos do seu cluster, verifique se os [seus pools do sistema][system-pools] têm recursos suficientes para manter a estabilidade dos componentes do sistema principal do seu cluster. **Nunca** reduza os recursos do seu cluster ao ponto de o seu cluster se tornar instável.

Depois de rever a utilização do seu cluster, considere usar as funcionalidades oferecidas por [várias piscinas de nó:][multiple-node-pools] 

* Tamanho do nó

    Use [o tamanho do nó][node-sizing] para definir piscinas de nó com cpu específico e perfis de memória, permitindo-lhe adaptar os seus nós às suas necessidades de carga de trabalho. Ao dimensionar os seus nós às suas necessidades de carga de trabalho, pode executar alguns nós com uma utilização mais elevada. 

* Dimensionamento do cluster

    Configure como o seu cluster [escala][scale]. Utilize o [autoescalador de cápsulas horizontais][scale-horizontal] e o [autoescalador][scale-auto] do cluster para escalar o seu cluster automaticamente com base na sua configuração. Controle como o seu cluster escala para manter todos os seus nós em alta utilização enquanto permanece sincronizado com alterações na carga de trabalho do seu cluster. 

* Piscinas spot

    Nos casos em que uma carga de trabalho é tolerante a interrupções ou rescisões repentinas, pode utilizar [piscinas spot][spot-pools]. As piscinas spot aproveitam a capacidade inativa dentro do Azure. Por exemplo, as piscinas spot podem funcionar bem para trabalhos de lote ou ambientes de desenvolvimento.

> [!NOTE]
>O aumento da utilização também pode reduzir o excesso de nós, o que reduz a energia consumida pelas [reservas de recursos em cada nó.][resource-reservations]

Por fim, reveja o CPU e *os pedidos* de memória e *limites* nos manifestos de Kubernetes das suas aplicações. 
* À medida que baixa a memória e os valores do CPU, mais memória e CPU estão disponíveis para o cluster para executar outras cargas de trabalho. 
* À medida que corre mais cargas de trabalho com CPU e memória mais baixas, o seu cluster torna-se mais densamente alocado, o que aumenta a sua utilização. 

Ao reduzir o CPU e a memória para as suas aplicações, o comportamento das suas aplicações pode tornar-se degradado ou instável se definir CPU e valores de memória demasiado baixos. Antes de alterar o CPU e os *pedidos* e limites de memória, escame *alguns testes* de benchmarking para verificar se os valores são definidos adequadamente. Nunca reduza estes valores ao ponto de instabilidade da aplicação.

## <a name="reduce-network-travel"></a>Reduzir as viagens de rede

Ao reduzir os pedidos e respostas da distância de e para o seu cluster, pode reduzir as emissões de carbono e o consumo de eletricidade através de dispositivos de networking. Depois de rever o tráfego da sua rede, considere criar clusters [em regiões][regions] mais próximas da fonte do tráfego da sua rede. Você pode usar [O Gestor de Tráfego Azure][azure-traffic-manager] para encaminhar o tráfego para os grupos de colocação de aglomerados e proximidades mais [próximos][proiximity-placement-groups] e reduzir a distância entre os recursos de Azure.

> [!IMPORTANT]
> Ao considerar fazer alterações na rede do seu cluster, nunca reduza as viagens de rede ao custo de satisfazer os requisitos de carga de trabalho. Por exemplo, ao usar [zonas de disponibilidade][availability-zones] causa mais viagens de rede no seu cluster, as zonas de disponibilidade podem ser necessárias para lidar com os requisitos de carga de trabalho.

## <a name="demand-shaping"></a>Procura de formação

Sempre que possível, considere transferir a procura dos recursos do seu cluster para tempos ou regiões onde possa utilizar o excesso de capacidade. Por exemplo, considere:
* Alterar o tempo ou a região para um trabalho de lote a executar.
* Utilizando [piscinas spot][spot-pools]. 
* Refactorar a sua aplicação para usar uma fila para adiar cargas de trabalho que não precisam de processamento imediato.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as características da AKS mencionadas neste artigo:

* [Múltiplas piscinas de nó][multiple-node-pools]
* [Tamanho do nó][node-sizing]
* [Escalar um aglomerado][scale]
* [Dimensionador automático de pods horizontal][scale-horizontal]
* [Dimensionador automático de cluster][scale-auto]
* [Piscinas spot][spot-pools]
* [Piscinas de sistema][system-pools]
* [Reservas de recursos][resource-reservations]
* [Grupos de colocação de proximidade][proiximity-placement-groups]
* [Zonas de Disponibilidade][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: https://docs.microsoft.com/learn/modules/sustainable-software-engineering-overview/