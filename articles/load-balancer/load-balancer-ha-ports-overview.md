---
title: Visão geral de portas de alta disponibilidade em Azure
titleSuffix: Azure Load Balancer
description: Saiba mais sobre as portas de alta disponibilidade que equilibram o equilíbrio de carga num equilibrador de carga interna.
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
ms.openlocfilehash: d3bd1156de4aed7d1ea5c530605697f2dc80d63c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80476988"
---
# <a name="high-availability-ports-overview"></a>Visão geral das portas de alta disponibilidade

O Equilíbrio de Carga Padrão Azure ajuda-o a equilibrar os fluxos de TCP e UDP em todas as portas simultaneamente quando estiver a utilizar um equilibrador de carga interna. 

Uma regra de equilíbrio de carga das portas (HA) de alta disponibilidade (HA) é uma variante de uma regra de equilíbrio de carga, configurada num balanceor de carga padrão interno. Pode simplificar a utilização de um equilibrante de carga, fornecendo uma regra única para equilibrar todos os fluxos de TCP e UDP que chegam a todas as portas de um Balancer de Carga Padrão interno. A decisão de equilíbrio de carga é tomada por fluxo. Esta ação baseia-se na seguinte ligação de cinco tuple: endereço IP de origem, porta de origem, endereço IP de destino, porta de destino e protocolo

As portas HA que equilibram a carga ajudam-no com cenários críticos, tais como alta disponibilidade e escala para aparelhos virtuais de rede (NVAs) dentro de redes virtuais. A funcionalidade também pode ajudar quando um grande número de portas deve ser equilibrada em carga. 

As regras de equilíbrio de carga das portas HA são configuradas quando define as portas dianteiras e traseiras para **0** e o protocolo para **Todos**. O recurso interno do equilíbrio de carga equilibra todos os fluxos de TCP e UDP, independentemente do número de porta

## <a name="why-use-ha-ports"></a>Por que usar portas HA?

### <a name="network-virtual-appliances"></a><a name="nva"></a>Aplicações virtuais de rede

Pode utilizar NVAs para ajudar a proteger a sua carga de trabalho Azure de vários tipos de ameaças à segurança. Quando se utilizam NVAs nestes cenários, devem ser fiáveis e altamente disponíveis, e devem ser dimensionados para a procura.

Você pode alcançar estes objetivos simplesmente adicionando instâncias de NVA ao conjunto de back-end do seu equilibrador de carga interna e configurando uma regra de equilíbrio de carga de portas HA.

Para os cenários da NVA HA, os portos ha oferecem as seguintes vantagens:
- Fornecer falhas rápidas a casos saudáveis, com sondas de saúde por exemplo
- Garantir um desempenho mais elevado com escala para *n-active*casos
- Fornecer *cenários n-activos*e activos-passivos
- Eliminar a necessidade de soluções complexas, como os nós do Apache ZooKeeper para monitorizar os aparelhos

O diagrama seguinte apresenta uma implantação de rede virtual de hub e spoke. Os porta-vozes forçam o seu tráfego para a rede virtual do centro e através da NVA, antes de deixar o espaço de confiança. Os NVAs estão por trás de um Balancer de Carga Padrão interno com uma configuração de portas HA. Todo o tráfego pode ser processado e encaminhado em conformidade. Quando configurado como mostra no diagrama seguinte, uma regra de equilíbrio de carga ha portas adiciona ainda a simetria de fluxo para ingresse e esgress tráfego.

<a node="diagram"></a>
![Diagrama da rede virtual hub-and-spoke, com NVAs implantados no modo HA](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Se estiver a utilizar NVAs, confirme com os seus fornecedores como utilizar melhor as portas HA e para saber quais os cenários suportados.

### <a name="load-balancing-large-numbers-of-ports"></a>Equilibrando um grande número de portas

Também pode utilizar portas HA para aplicações que requerem o equilíbrio de carga de um grande número de portas. Pode simplificar estes cenários utilizando um [Balancer de Carga Padrão](load-balancer-standard-overview.md) interno com portas HA. Uma regra única de equilíbrio de carga substitui várias regras individuais de equilíbrio de carga, uma para cada porta.

## <a name="region-availability"></a>Disponibilidade de região

A funcionalidade de portais HA está disponível em todas as regiões globais do Azure.

## <a name="supported-configurations"></a>Configurações suportadas

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Uma configuração única e não flutuante das portas HA (retorno do servidor não-directo) num balancedor de carga padrão interno

Esta configuração é uma configuração básica das portas HA. Pode configurar uma regra de equilíbrio de carga das portas HA num único endereço IP frontal, fazendo o seguinte:
1. Ao configurar o Balancer de Carga Padrão, selecione a caixa de verificação das **portas HA** na configuração da regra do Balancer de Carga.
2. Para **o IP flutuante,** selecione **Disabled**.

Esta configuração não permite qualquer outra configuração de regra de equilíbrio de carga no recurso de equilíbrio de carga atual. Também não permite nenhuma outra configuração interna de recursos de equilíbrio de carga para o conjunto dado de instâncias de back-end.

No entanto, pode configurar um Balancer de Carga Padrão público para as instâncias de back-end, para além desta regra das portas HA.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Uma configuração única e flutuante de portas HA (Retorno do Servidor Direto) num balancedor de carga padrão interno

Pode configurar o seu equilibrador de carga para utilizar uma regra de equilíbrio de carga com a **porta HA** com uma única extremidade frontal, definindo o **IP flutuante** **ativado**. 

Ao utilizar esta configuração, pode adicionar mais regras flutuantes de equilíbrio de carga IP e/ou um equilibrador de carga pública. No entanto, não é possível utilizar uma configuração de equilíbrio de carga de portas HA não flutuante seletiva em cima desta configuração.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Múltiplas configurações de portas HA num balancedor de carga padrão interno

Se o seu cenário exigir que configure mais do que uma extremidade frontal ha porta para a mesma piscina traseira, pode fazer o seguinte: 
- Configure mais de um endereço IP privado frontal para um único recurso interno do Balancer de Carga Padrão.
- Configure várias regras de equilíbrio de carga, onde cada regra tem um único endereço IP frontal único selecionado.
- Selecione a opção **portas HA** e, em seguida, coloque **o IP flutuante** para **ativado** para todas as regras de equilíbrio de carga.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Um equilibrador de carga interno com portas HA e um equilibrador de carga pública na mesma instância de back-end

Pode configurar *um* recurso público do Standard Load Balancer para os recursos de back-end, juntamente com um único Equilibrante de Carga Padrão interno com portas HA.

>[!NOTE]
>Esta capacidade está atualmente disponível através dos modelos do Gestor de Recursos do Azure, mas não está disponível através do portal Azure.

## <a name="limitations"></a>Limitações

- As regras de equilíbrio de carga das portas HA estão disponíveis apenas para o Equilibrador de Carga Padrão interno.
- A combinação de uma regra de equilíbrio de carga das portas HA e de uma regra de equilíbrio de carga das portas não-HA que indique mato de backend não é suportada.
- Os fragmentos ip existentes serão encaminhados pelas regras de equilíbrio de carga da HA Ports para o mesmo destino que o primeiro pacote.  Não é suportado a fragmentação de IP de um pacote UDP ou TCP.
- A simetria do fluxo (principalmente para cenários NVA) é suportada com instância de backend e uma única NIC (e configuração IP única) apenas quando utilizada como indicado no diagrama acima e utilizando regras de equilíbrio de carga ha ports. Não é fornecido em nenhum outro cenário. Isto significa que dois ou mais recursos da Load Balancer e respetivas regras tomam decisões independentes e nunca são coordenados. Consulte a descrição e o diagrama para [os aparelhos virtuais da rede](#nva). Quando estiver a utilizar vários NICs ou a sandes de NVA entre um equilibrista de carga público e interno, a simetria do fluxo não está disponível.  Poderá contornar esta situação através da fonte NAT'ing, o fluxo de entrada para o IP do aparelho para permitir que as respostas cheguem ao mesmo NVA.  No entanto, recomendamos vivamente a utilização de um único NIC e utilizando a arquitetura de referência mostrada no diagrama acima.


## <a name="next-steps"></a>Passos seguintes

- [Configure as portas HA num balancedor de carga padrão interno](load-balancer-configure-ha-ports.md)
- [Saiba mais sobre o Balancer de Carga Padrão](load-balancer-standard-overview.md)
