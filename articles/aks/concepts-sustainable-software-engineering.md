---
title: Conceitos - Engenharia de software sustentável nos Serviços Azure Kubernetes (AKS)
description: Saiba mais sobre engenharia de software sustentável no Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 700723041855fdae4f994480d180292fdfd12e15
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346899"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Princípios de engenharia de software sustentáveis no Serviço Azure Kubernetes (AKS)

Os princípios de engenharia de software sustentável são um conjunto de competências para ajudá-lo a definir, construir e executar aplicações sustentáveis. O objetivo geral é reduzir a sua pegada de carbono de todos os aspetos da sua aplicação. [Os Princípios da Engenharia de Software Sustentável][principles-sse] têm uma visão geral dos princípios da engenharia de software sustentável.

Uma ideia importante para entender sobre engenharia de software sustentável é que é uma mudança de prioridades e foco. Em muitos casos, o software é projetado e funcionado de uma forma que se foca no desempenho rápido e baixa latência. A engenharia de software sustentável centra-se na redução do máximo de emissões de carbono possível. Em alguns casos, a aplicação de princípios sustentáveis de engenharia de software pode dar-lhe um desempenho mais rápido ou uma menor latência, como por exemplo, reduzindo o total das viagens de rede. Noutros casos, a redução das emissões de carbono pode causar um desempenho mais lento ou um aumento da latência, como o atraso das cargas de trabalho de baixa prioridade. Antes de considerar aplicar princípios de engenharia de software sustentáveis à sua aplicação, reveja as prioridades, necessidades e trade-offs da sua aplicação.

## <a name="measure-and-optimize"></a>Medir e otimizar

Para reduzir a pegada de carbono dos seus clusters AKS, precisa entender como os recursos do seu cluster estão sendo usados. [O Azure Monitor][azure-monitor] fornece detalhes sobre a utilização de recursos do seu cluster, como a memória e o uso do CPU. Estes dados podem ajudar a informar as suas decisões para reduzir a pegada de carbono do seu cluster e observar o efeito das suas alterações. Também pode instalar a [Calculadora de Sustentabilidade][sustainability-calculator] da Microsoft para ver a pegada de carbono de todos os seus recursos Azure.

## <a name="increase-resource-utilization"></a>Aumentar a utilização de recursos

Uma abordagem para reduzir a sua pegada de carbono é reduzir a quantidade de tempo inativo para os seus recursos de computação. Reduzir o seu tempo de inatividade envolve aumentar a utilização dos seus recursos de computação. Por exemplo, se tivesse quatro nós no seu cluster, cada um com 50% de capacidade, todos os seus quatro nós têm 50% de capacidade não utilizada permanecendo inativo. Se reduzisse o seu cluster a três nós, então a mesma carga de trabalho faria com que os seus três nós funcionassem a 67% da capacidade, reduzindo a sua capacidade não utilizada para 33% em cada nó e aumentando a sua utilização.

> [!IMPORTANT]
> Ao considerar fazer alterações nos recursos do seu cluster, verifique se os [seus pools do sistema][system-pools] têm recursos suficientes para manter a estabilidade dos componentes do sistema central do seu cluster. Nunca reduza os recursos do seu cluster ao ponto de o seu cluster se tornar instável.

Depois de rever a utilização do seu cluster, considere utilizar as funcionalidades oferecidas por [várias piscinas de nó.][multiple-node-pools] Pode utilizar [o dimensionamento de nó para][node-sizing] definir piscinas de nó com CPU específicos e perfis de memória, permitindo-lhe adaptar os seus nós às suas necessidades de carga de trabalho. Dimensionar os seus nós às suas necessidades de carga de trabalho pode ajudá-lo a executar alguns nós numa utilização mais elevada. Também pode configurar como o seu cluster [escala][scale] e utilizar o [autoescalador horizontal][scale-horizontal] e o [autoescalador][scale-auto] do cluster para escalar o seu cluster automaticamente com base na sua configuração. Controlar como as escalas de cluster podem ajudá-lo a manter todos os seus nós em alta utilização, mantendo ao mesmo tempo as alterações na carga de trabalho do seu cluster. Nos casos em que uma carga de trabalho é tolerante a interrupções ou rescisões repentinas, pode utilizar [piscinas spot][spot-pools] para tirar partido da capacidade ociosa dentro do Azure. Por exemplo, as piscinas spot podem funcionar bem para trabalhos de lote ou ambientes de desenvolvimento.

O aumento da utilização também pode reduzir o excesso de nós, o que reduz a energia consumida pelas [reservas de recursos em cada nó.][resource-reservations]

Reveja também o CPU e *os pedidos* de memória e *limites* nos manifestos de Kubernetes das suas aplicações. À medida que baixa esses valores para a memória e CPU, mais memória e CPU estão disponíveis para o cluster para executar outras cargas de trabalho. À medida que corre mais cargas de trabalho com CPU e memória mais baixas, o seu cluster torna-se mais densamente alocado, o que aumenta a sua utilização. Ao reduzir o CPU e a memória para as suas aplicações, o comportamento das suas aplicações pode tornar-se degradado ou instável se definir estes valores demasiado baixos. Antes de alterar o CPU e *os pedidos* e limites de memória, considere realizar *alguns testes* de benchmarking para entender se estes valores são definidos adequadamente. Além disso, nunca reduza estes valores ao ponto de a sua aplicação ficar instável.

## <a name="reduce-network-travel"></a>Reduzir as viagens de rede

A redução dos pedidos e respostas à distância de e para o seu cluster tem de viajar geralmente reduz o consumo de eletricidade através de dispositivos de networking e reduz as emissões de carbono. Depois de rever o tráfego da sua rede, considere criar clusters [em regiões][regions] mais próximas da fonte do tráfego da sua rede. Também pode usar [o Azure Traffic Manager][azure-traffic-manager] para ajudar no encaminhamento do tráfego para os grupos de colocação de aglomerados e proximidades mais [próximos][proiximity-placement-groups] para ajudar a reduzir a distância entre os recursos do Azure.

> [!IMPORTANT]
> Ao considerar fazer alterações na rede do seu cluster, nunca reduza as viagens de rede ao custo de satisfazer os requisitos de carga de trabalho. Por exemplo, a utilização [de zonas de disponibilidade][availability-zones] causa mais viagens de rede no seu cluster, mas a utilização dessa funcionalidade pode ser necessária para lidar com os requisitos de carga de trabalho.

## <a name="demand-shaping"></a>Procura de formação

Sempre que possível, considere transferir a procura dos recursos do seu cluster para tempos ou regiões onde possa utilizar o excesso de capacidade. Por exemplo, considere alterar o tempo ou a região para um trabalho de lote para executar ou utilizar [piscinas spot][spot-pools]. Considere também refactorar a sua aplicação para usar uma fila para adiar a execução de cargas de trabalho que não necessitam de processamento imediato.

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
[azure-monitor]: ../azure-monitor/insights/container-insights-overview.md
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