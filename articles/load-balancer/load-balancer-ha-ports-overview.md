---
title: Descrição geral das portas de elevada disponibilidade no Azure
titlesuffix: Azure Load Balancer
description: Saiba mais sobre num Balanceador de carga interno de balanceamento de carga de portas de elevada disponibilidade.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2018
ms.author: kumud
ms.openlocfilehash: 328471292ea6cbe07e96cc18af7f9c524407de3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60930283"
---
# <a name="high-availability-ports-overview"></a>Descrição geral das portas de elevada disponibilidade

Balanceador de carga de Standard do Azure ajuda-o a fluxos TCP e UDP de balanceamento de carga em todas as portas em simultâneo quando estiver a utilizar um balanceador de carga interno. 

Uma regra de balanceamento de carga do portas elevada disponibilidade (HA) é uma variante de uma regra, configurada num interno Balanceador de carga padrão de balanceamento de carga. Pode simplificar o uso de um balanceador de carga ao fornecer uma única regra balancear a carga todos os fluxos TCP e UDP que chegam em todas as portas de um balanceador de carga de padrão interno. A decisão de balanceamento de carga é feita por fluxo. Esta ação baseia-se a seguinte ligação de cinco cadeias de identificação: endereço IP, porta de origem, endereço IP de destino, porta de destino e protocolo de origem

As regras de balanceamento de carga de portas HA ajudá-lo com cenários críticos, como elevada disponibilidade e dimensionamento para aplicações virtuais de rede (NVAs) dentro das redes virtuais. Também pode ajudar a funcionalidade quando tem de ser um grande número de portas com balanceamento de carga. 

Regras de balanceamento de carga de portas HA é configurada ao definir as portas de front-end e back-end como **0** e o protocolo para **todos os**. O recurso do Balanceador de carga interno, em seguida, faz o balanceamento de todos os fluxos do TCP e UDP, independentemente do número de porta

## <a name="why-use-ha-ports"></a>Por que usar portas HA?

### <a name="nva"></a>Aplicações virtuais de rede

Pode utilizar NVAs para ajudar a proteger sua carga de trabalho do Azure de múltiplos tipos de ameaças de segurança. Quando utilizar NVAs nestes cenários, têm de ser fiável e altamente disponíveis e devem aumentar horizontalmente para a pedido.

Pode alcançar essas metas simplesmente adicionando instâncias da NVA para o conjunto de back-end do seu Balanceador de carga interno e configurar um HA portas a regra de Balanceador de carga.

Para a NVA HA cenários, as portas HA oferecem as seguintes vantagens:
- Fornecer a ativação pós-falha rápida para instâncias de bom estado de funcionamento, por instância sondas de estado de funcionamento
- Certifique-se com o aumento na horizontal para um desempenho mais elevado *n*-instâncias ativas
- Fornecer *n*-cenários de Active Directory e ativa-passiva
- Eliminar a necessidade de soluções complexas, como nós do ZooKeeper do Apache para monitorização de aplicações

O diagrama seguinte apresenta uma implementação de rede virtual do hub-and-spoke. A imposição de túnel spokes seu tráfego de rede virtual do concentrador e através da NVA, antes de deixarem o espaço fidedigno. As NVAs estão por trás de um balanceador de carga interno padrão com uma configuração de portas HA. Todo o tráfego pode ser processado e encaminhado em conformidade. Quando configurado, como mostra o diagrama a seguir, um adicionalmente a regra de balanceamento de carga de portas HA fornece simetria de fluxo para o tráfego de entrada e saída.

<a node="diagram"></a>
![Diagrama de rede virtual hub-and-spoke, com NVAs implementada no modo de HA](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Se estiver a utilizar NVAs, certifique-se com os respetivos fornecedores de como usar da melhor maneira portas HA e para saber quais cenários são suportados.

### <a name="load-balancing-large-numbers-of-ports"></a>Balanceamento de carga grandes números de portas

Também pode utilizar portas HA para aplicações que requerem balanceamento de carga de um grande número de portas. Pode simplificar a esses cenários, utilizando um interno [Balanceador de carga Standard](load-balancer-standard-overview.md) com portas HA. Uma única regra de balanceamento de carga substitui várias balanceamento de carga regras individuais, uma para cada porta.

## <a name="region-availability"></a>Disponibilidade de região

A funcionalidade de portas HA está disponível em todas as regiões do Azure global.

## <a name="supported-configurations"></a>Configurações suportadas

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Uma configuração de portas HA de IP (não - devolução direta do servidor) única, não flutuante num Standard Balanceador de carga interno

Esta configuração é uma configuração de portas HA básica. Pode configurar um HA regra de balanceamento de carga de portas num único endereço IP de front-end ao fazer o seguinte:
1. Ao configurar o Balanceador de carga Standard, selecione o **portas HA** caixa de verificação na configuração de regra de Balanceador de carga.
2. Para **IP flutuante**, selecione **desativado**.

Esta configuração não permite quaisquer outra balanceamento de carga na configuração da regra no recurso de Balanceador de carga atual. Também permite que não existem outra interno configuração Balanceador de carga recursos para determinado conjunto de instâncias de back-end.

No entanto, pode configurar um balanceador de carga Standard público para as instâncias de back-end além desta regra de portas HA.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Uma configuração de portas HA de IP (devolução direta do servidor) única, vírgula flutuante num Standard Balanceador de carga interno

Da mesma forma pode configurar o Balanceador de carga a utilizar uma regra de balanceamento de carga com **portas HA** com um único front-end ao definir o **IP flutuante** para **ativado**. 

Ao utilizar esta configuração, pode adicionar mais flutuantes balanceamento de carga regras de IP e/ou um balanceador de carga público. No entanto, não é possível utilizar um IP de vírgula flutuante, portas HA configuração de balanceamento de carga sobre esta configuração.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Várias configurações de portas HA num Standard Balanceador de carga interno

Se o seu cenário requer que configure mais do que um HA porta front-end para o mesmo conjunto de back-end, pode fazer o seguinte: 
- Configure mais do que um front-end endereço IP privado para um único recurso de Balanceador de carga interno.
- Configure várias regras de balanceamento de carga, onde cada regra tem um endereço IP de front-end exclusivo único selecionado.
- Selecione o **portas HA** opção e, em seguida, defina **IP flutuante** para **ativado** para todas as regras de balanceamento de carga.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Um balanceador de carga interno com portas HA e um balanceador de carga público na mesma instância de back-end

Pode configurar *um* recurso de Balanceador de carga Standard público para os recursos de back-end, juntamente com uma única Standard Balanceador de carga interno com portas HA.

>[!NOTE]
>Esta capacidade está atualmente disponível através de modelos Azure Resource Manager, mas não está disponível através do portal do Azure.

## <a name="limitations"></a>Limitações

- HA configuração de portas está disponível apenas para balanceadores de carga internos. Não está disponível para balanceadores de carga público.

- A combinação de um HA portas a regra de balanceamento de carga e um portas não HA não é suportada a regra de balanceamento de carga.

- A funcionalidade de portas HA não está disponível para IPv6.

- Só quando utilizado, conforme mostrado no diagrama acima e através de portas HA regras de balanceamento de carga, a simetria de fluxo (principalmente para cenários NVA) é suportada com a instância de back-end e um único NIC (e única configuração de IP). Não é fornecido em qualquer outro cenário. Isso significa que dois ou mais recursos do Balanceador de carga e suas respectivas regras tomar decisões independentes e nunca são coordenadas. Consulte a descrição e diagrama para [aplicações virtuais de rede](#nva). Quando estiver usando um várias NICs ou sandwiching da NVA entre um balanceador de carga públicos e internos, simetria de fluxo não está disponível.  Poderá conseguir contornar esse problema origem NAT'ing a entrada de fluxo para o IP da aplicação para permitir respostas chegam na mesma NVA.  No entanto, é altamente recomendável utilizar um único NIC e utilizar a arquitetura de referência mostrada no diagrama acima.


## <a name="next-steps"></a>Passos Seguintes

- [Configurar portas HA num Standard Balanceador de carga interno](load-balancer-configure-ha-ports.md)
- [Saiba mais sobre o Balanceador de carga Standard](load-balancer-standard-overview.md)
