---
title: Visão geral das portas de alta disponibilidade em Azure
titleSuffix: Azure Load Balancer
description: Saiba mais sobre o equilíbrio das portas de alta disponibilidade num equilibrador interno de carga.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: 6f089af71e4d32023e9cebd6613872f7db0eed7a
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694964"
---
# <a name="high-availability-ports-overview"></a>Visão geral das portas de alta disponibilidade

O Azure Standard Load Balancer ajuda-o a equilibrar **todos os** fluxos de protocolo em **todas as** portas simultaneamente quando estiver a utilizar um Equilibrador de Carga interno através de portas HA.

As portas de alta disponibilidade (HA) são um tipo de regra de equilíbrio de carga que proporciona uma maneira fácil de equilibrar **todos os** fluxos que chegam a **todas as** portas de um Balanceador de Carga Padrão interno. A decisão de equilibrar a carga é tomada por fluxo. Esta ação baseia-se na seguinte ligação de cinco tuples: endereço IP de origem, porta de origem, endereço IP de destino, porta de destino e protocolo

As regras de equilíbrio de carga das portas HA ajudam-no com cenários críticos, tais como alta disponibilidade e escala para aparelhos virtuais de rede (NVAs) dentro de redes virtuais. A funcionalidade também pode ajudar quando um grande número de portas deve ser equilibrada em carga. 

As regras de equilíbrio de carga das portas HA são configuradas quando define as portas frontais e traseiras para **0** e o protocolo para **Todos**. O recurso do balançador de carga interno equilibra todos os fluxos de TCP e UDP, independentemente do número da porta

## <a name="why-use-ha-ports"></a>Por que usar portas HA?

### <a name="network-virtual-appliances"></a><a name="nva"></a>Aparelhos virtuais de rede

Pode utilizar NVAs para ajudar a proteger a sua carga de trabalho Azure de vários tipos de ameaças à segurança. Quando se utilizam NVAs nestes cenários, devem ser fiáveis e altamente disponíveis, e devem aumentar a procura.

Você pode alcançar estes objetivos simplesmente adicionando instâncias NVA ao pool back-end do seu equilibficador de carga interno e configurando uma regra de equilíbrio de portas HA.

Para cenários NVA HA, as portas HA oferecem as seguintes vantagens:
- Fornecer falha rápida a casos saudáveis, com sondas de saúde por instância
- Garantir um desempenho mais elevado com a escala para *n*-instâncias ativas
- Fornecer *cenários n*-ativos e activos-passivos
- Eliminar a necessidade de soluções complexas, como os nómada Apache ZooKeeper para monitorização de aparelhos

O diagrama seguinte apresenta uma implementação de rede virtual hub-and-spoke. Os porta-vozes forçam o seu tráfego para a rede virtual do centro e através da NVA, antes de deixar o espaço de confiança. Os NVAs estão por trás de um Balanceador de Carga Padrão interno com uma configuração de portas HA. Todo o tráfego pode ser processado e encaminhado em conformidade. Quando configurado como mostrado no diagrama seguinte, uma regra de equilíbrio de carga dos portos HA fornece adicionalmente simetria de fluxo para o tráfego de entradas e saídas.

<a node="diagram"></a>
![Diagrama de rede virtual hub-and-spoke, com NVAs implantados no modo HA](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Se estiver a utilizar NVAs, confirme com os seus fornecedores como utilizar melhor as portas HA e saber quais os cenários suportados.

### <a name="load-balancing-large-numbers-of-ports"></a>Um grande número de portos equilibrados

Também pode utilizar portas HA para aplicações que requerem equilíbrio de carga de um grande número de portas. Pode simplificar estes cenários utilizando um Balanceador de [Carga Padrão](./load-balancer-overview.md) interno com portas HA. Uma única regra de equilíbrio de carga substitui várias regras individuais de equilíbrio de carga, uma para cada porta.

## <a name="region-availability"></a>Disponibilidade de região

A funcionalidade de portas HA está disponível em todas as regiões globais de Azure.

## <a name="supported-configurations"></a>Configurações suportadas

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Uma configuração de portas HA de IP única e não flutuante (retorno não-directo do servidor) num Balanceador de Carga Padrão interno

Esta configuração é uma configuração básica das portas HA. Pode configurar uma regra de equilíbrio de carga das portas HA num único endereço IP frontal, fazendo o seguinte:
1. Ao configurar o Balanceador de Carga Padrão, selecione a caixa de verificação das **portas HA** na configuração da regra do balançador de carga.
2. Para **IP flutuante**, selecione **Disabled**.

Esta configuração não permite qualquer outra configuração de regra de equilíbrio de carga no recurso atual do balançador de carga. Também não permite nenhuma outra configuração interna do recurso do balanceador de carga para o conjunto dado de instâncias de back-end.

No entanto, pode configurar um Balancer de Carga Standard público para as instâncias de back-end para além desta regra das portas HA.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Uma configuração de portas HA de IP única e flutuante num balanceador de carga padrão interno

Pode igualmente configurar o seu equilibrador de carga para utilizar uma regra de equilíbrio de carga com **a porta HA** com uma única extremidade frontal, definindo o IP **flutuante** para **Ativado**. 

Ao utilizar esta configuração, pode adicionar mais regras flutuantes de equilíbrio de carga IP e/ou um equilibrador de carga pública. No entanto, não é possível utilizar uma configuração de equilíbrio de carga IP não flutuante, nas portas HA em cima desta configuração.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Múltiplas configurações de portas HA num balanceador de carga padrão interno

Se o seu cenário exigir que configuure mais do que uma extremidade frontal da porta HA para a mesma piscina traseira, pode fazer o seguinte: 
- Configure mais de um endereço IP privado frontal para um único recurso interno do Balancer de Carga Padrão.
- Configure várias regras de equilíbrio de carga, onde cada regra tem um único endereço IP frontal único selecionado.
- Selecione a opção **portas HA** e, em seguida, desempate **o IP flutuante** para **Ativar** todas as regras de equilíbrio de carga.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Um equilibrador de carga interno com portas HA e um equilibrador de carga pública na mesma instância de back-end

Pode configurar *um* recurso público Standard Load Balancer para os recursos de backend, juntamente com um único Balancer de Carga Padrão interno com portas HA.

## <a name="limitations"></a>Limitações

- As regras de equilíbrio de carga das portas HA estão disponíveis apenas para o Balanceador de Carga Padrão interno.
- A combinação de uma regra de equilíbrio de carga das portas HA e uma regra de equilíbrio de carga das portas não HA que indiquem a mesma configuração de ipconfiguration de backend **não** é suportada numa única configuração IP frontend, a menos que ambos tenham IP flutuante ativado.
- Os fragmentos de IP existentes serão reencaminhados pelas regras de equilíbrio de carga dos portos ha para o mesmo destino que o primeiro pacote.  A fragmentação ip de um pacote UDP ou TCP não é suportada.
- A simetria de fluxo (principalmente para cenários de NVA) é suportada com instância de backend e uma única configuração NIC (e única configuração IP) apenas quando usada como mostrado no diagrama acima e usando regras de equilíbrio de carga das portas HA. Não é fornecido em nenhum outro cenário. Isto significa que dois ou mais recursos do Balancer de Carga e respetivas regras tomam decisões independentes e nunca são coordenados. Consulte a descrição e o diagrama dos [aparelhos virtuais da rede](#nva). Quando estiver a utilizar vários NICs ou a fazer sanduiches no NVA entre um Balanceador de Carga público e interno, a simetria de fluxo não está disponível.  Poderá trabalhar em torno disto através da fonte NAT'ing o fluxo de entrada para o IP do aparelho para permitir que as respostas cheguem no mesmo NVA.  No entanto, recomendamos vivamente a utilização de um único NIC e a utilização da arquitetura de referência mostrada no diagrama acima.


## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre o Balanceador de Carga Padrão](load-balancer-overview.md)
