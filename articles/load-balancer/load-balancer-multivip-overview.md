---
title: Vários front-ends para Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Visão geral de vários front-ends no Azure Load Balancer
services: load-balancer
documentationcenter: na
author: chkuhtz
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: chkuhtz
ms.openlocfilehash: b109e87a8fcbef0bfca356c83716509ebc6cecd4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884216"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Vários front-ends para Azure Load Balancer

Azure Load Balancer permite balancear a carga de serviços em várias portas, vários endereços IP ou ambos. Você pode usar definições de balanceador de carga públicas e internas para balancear a carga de fluxos em um conjunto de VMs.

Este artigo descreve os conceitos básicos dessa capacidade, conceitos importantes e restrições. Se você pretende apenas expor serviços em um endereço IP, você pode encontrar instruções simplificadas para configurações [públicas](load-balancer-get-started-internet-portal.md) ou [internas](load-balancer-get-started-ilb-arm-portal.md) do Load Balancer. A adição de vários front-ends é incremental para uma única configuração de front-end. Usando os conceitos deste artigo, você pode expandir uma configuração simplificada a qualquer momento.

Quando você define um Azure Load Balancer, um frontend e uma configuração de pool de back-end são conectados com regras. A investigação de integridade referenciada pela regra é usada para determinar como novos fluxos são enviados a um nó no pool de back-end. O front-end (também conhecido como VIP) é definido por uma tupla de 3, composta por um endereço IP (público ou interno), um protocolo de transporte (UDP ou TCP) e um número de porta da regra de balanceamento de carga. O pool de back-end é uma coleção de configurações de IP de máquina virtual (parte do recurso NIC) que referenciam o pool de back-end Load Balancer.

A tabela a seguir contém algumas configurações de front-end de exemplo:

| Front-end | Endereço IP | protocol | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

A tabela mostra quatro front-ends diferentes. Os front-ends #1, #2 e #3 são um único front-end com várias regras. O mesmo endereço IP é usado, mas a porta ou o protocolo é diferente para cada front-end. Os front-ends #1 e #4 são um exemplo de vários front-ends, onde o mesmo protocolo e porta de front-end são reutilizados em vários front-ends.

Azure Load Balancer fornece flexibilidade para definir as regras de balanceamento de carga. Uma regra declara como um endereço e uma porta no front-end são mapeados para o endereço de destino e a porta no back-end. Se as portas de back-end são ou não reutilizadas entre as regras depende do tipo da regra. Cada tipo de regra tem requisitos específicos que podem afetar a configuração do host e o design da investigação. Há dois tipos de regras:

1. A regra padrão sem reutilização de porta de back-end
2. A regra IP flutuante em que as portas de back-end são reutilizadas

Azure Load Balancer permite misturar ambos os tipos de regra na mesma configuração de balanceador de carga. O balanceador de carga pode usá-los simultaneamente para determinada VM ou qualquer combinação, desde que você obedeça às restrições da regra. O tipo de regra escolhido depende dos requisitos do seu aplicativo e da complexidade do suporte a essa configuração. Você deve avaliar quais tipos de regra são melhores para seu cenário.

Exploraremos esses cenários ainda mais começando com o comportamento padrão.

## <a name="rule-type-1-no-backend-port-reuse"></a>#1 do tipo de regra: Nenhuma reutilização de porta de back-end

![Ilustração de vários front-end com front-end verde e roxo](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

Nesse cenário, os front-ends são configurados da seguinte maneira:

| Front-end | Endereço IP | protocol | port |
| --- | --- | --- | --- |
| ![front-end verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![front-end roxo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

O DIP é o destino do fluxo de entrada. No pool de back-end, cada VM expõe o serviço desejado em uma porta exclusiva em um DIP. Esse serviço é associado ao front-end por meio de uma definição de regra.

Definimos duas regras:

| Regra | Front-end do mapa | Para o pool de back-end |
| --- | --- | --- |
| 1 |![front-end verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

O mapeamento completo no Azure Load Balancer agora é o seguinte:

| Regra | Endereço IP de front-end | protocol | port | Destino | port |
| --- | --- | --- | --- | --- | --- |
| ![regra verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Endereço IP DIP |80 |
| ![regra roxa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Endereço IP DIP |81 |

Cada regra deve produzir um fluxo com uma combinação exclusiva de endereço IP de destino e porta de destino. Ao variar a porta de destino do fluxo, várias regras podem entregar fluxos para o mesmo DIP em portas diferentes.

As investigações de integridade são sempre direcionadas para o DIP de uma VM. Você deve garantir que sua investigação reflita a integridade da VM.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Tipo de regra #2: reutilização de porta de back-end usando IP flutuante

Azure Load Balancer fornece a flexibilidade para reutilizar a porta de front-end em vários front-ends, independentemente do tipo de regra usado. Além disso, alguns cenários de aplicativo preferem ou exigem que a mesma porta seja usada por várias instâncias de aplicativo em uma única VM no pool de back-end. Exemplos comuns de reutilização de porta incluem clustering para alta disponibilidade, soluções de virtualização de rede e exposição de vários pontos de extremidade TLS sem nova criptografia.

Se você quiser reutilizar a porta de back-end em várias regras, deverá habilitar o IP flutuante na definição de regra.

"IP flutuante" é a terminologia do Azure para uma parte do que é conhecido como DSR (retorno de servidor direto). O DSR consiste em duas partes: uma topologia de fluxo e um esquema de mapeamento de endereço IP. Em um nível de plataforma, Azure Load Balancer sempre funciona em uma topologia de fluxo DSR, independentemente de o IP flutuante estar habilitado ou não. Isso significa que a parte de saída de um fluxo é sempre reescrita corretamente para fluir diretamente de volta para a origem.

Com o tipo de regra padrão, o Azure expõe um esquema de mapeamento de endereço IP de balanceamento de carga tradicional para facilitar o uso. Habilitar o IP flutuante altera o esquema de mapeamento de endereço IP para permitir flexibilidade adicional, conforme explicado abaixo.

O diagrama a seguir ilustra essa configuração:

![Ilustração de vários front-end com front-end verde e roxo com DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Para esse cenário, cada VM no pool de back-end tem três interfaces de rede:

* DIP: uma NIC virtual associada à VM (configuração de IP do recurso NIC do Azure)
* Frontend 1: uma interface de loopback no sistema operacional convidado que está configurado com o endereço IP do front-end 1
* Front-end 2: uma interface de loopback no sistema operacional convidado que está configurado com o endereço IP do front-end 2

> [!IMPORTANT]
> A configuração das interfaces de loopback é executada no sistema operacional convidado. Essa configuração não é realizada nem gerenciada pelo Azure. Sem essa configuração, as regras não funcionarão. As definições de investigação de integridade usam o DIP da VM em vez da interface de loopback que representa o front-end DSR. Portanto, o serviço deve fornecer respostas de investigação em uma porta DIP que reflita o status do serviço oferecido na interface de loopback que representa o front-end DSR.

Vamos supor a mesma configuração de front-end que no cenário anterior:

| Front-end | Endereço IP | protocol | port |
| --- | --- | --- | --- |
| ![front-end verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![front-end roxo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Definimos duas regras:

| Regra | Front-end | Mapear para pool de back-end |
| --- | --- | --- |
| 1 |![rule](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (em VM1 e VM2) |
| 2 |![rule](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Front-End2:80 (em VM1 e VM2) |

A tabela a seguir mostra o mapeamento completo no balanceador de carga:

| Regra | Endereço IP de front-end | protocol | port | Destino | port |
| --- | --- | --- | --- | --- | --- |
| ![regra verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |igual ao frontend (65.52.0.1) |igual ao front-end (80) |
| ![regra roxa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |igual ao frontend (65.52.0.2) |igual ao front-end (80) |

O destino do fluxo de entrada é o endereço IP de front-end na interface de loopback na VM. Cada regra deve produzir um fluxo com uma combinação exclusiva de endereço IP de destino e porta de destino. Ao variar o endereço IP de destino do fluxo, a reutilização de porta é possível na mesma VM. Seu serviço é exposto ao balanceador de carga ligando-o ao endereço IP do front-end e à porta da respectiva interface de loopback.

Observe que este exemplo não altera a porta de destino. Embora esse seja um cenário de IP flutuante, o Azure Load Balancer também dá suporte à definição de uma regra para regravar a porta de destino do back-end e torná-la diferente da porta de destino do front-end.

O tipo de regra IP flutuante é a base de vários padrões de configuração do balanceador de carga. Um exemplo que está disponível no momento é a configuração do [AlwaysOn do SQL com vários ouvintes](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) . Ao longo do tempo, documentaremos mais desses cenários.

## <a name="limitations"></a>Limitações

* Há suporte para várias configurações de front-end apenas com VMs IaaS.
* Com a regra de IP flutuante, seu aplicativo deve usar a configuração de IP primário para fluxos SNAT de saída. Se seu aplicativo se associar ao endereço IP de front-end configurado na interface de loopback no SO convidado, o SNAT de saída do Azure não estará disponível para regravar o fluxo de saída e o fluxo falhará.  Examine os [cenários de saída](load-balancer-outbound-connections.md).
* Os endereços IP públicos têm um efeito na cobrança. Para obter mais informações, consulte [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Os limites de assinatura se aplicam. Para obter mais informações, consulte [limites de serviço](../azure-subscription-service-limits.md#networking-limits) para obter detalhes.

## <a name="next-steps"></a>Passos Seguintes

- Examine [as conexões de saída](load-balancer-outbound-connections.md) para entender o impacto de vários front-ends no comportamento de conexão de saída.
