---
title: Vários frontends - Azure Load Balancer
description: Com este caminho de aprendizagem, começa com uma visão geral de vários frontends no Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5c2072d13cab9839a276c0437747d7075918e78a
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696885"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Vários frontends para Azure Load Balancer

O Azure Load Balancer permite-lhe carregar serviços de equilíbrio em várias portas, vários endereços IP ou ambos. Pode utilizar definições de balançadores de carga públicas e internas para carregar fluxos de equilíbrio através de um conjunto de VMs.

Este artigo descreve os fundamentos desta capacidade, conceitos importantes e constrangimentos. Se pretender expor os serviços num endereço IP, pode encontrar instruções simplificadas para configurações de balançadores de carga [públicos](./quickstart-load-balancer-standard-public-portal.md) ou [internos.](./quickstart-load-balancer-standard-internal-portal.md) Adicionar vários frontends é incremental a uma única configuração frontal. Utilizando os conceitos deste artigo, pode expandir uma configuração simplificada a qualquer momento.

Quando define um Balançador de Carga Azure, uma configuração frontal e uma configuração de piscina de backend estão ligadas às regras. A sonda de saúde referenciada pela regra é usada para determinar como novos fluxos são enviados para um nó na piscina de backend. O frontend (aka VIP) é definido por um endereço IP composto por um endereço IP (público ou interno), um protocolo de transporte (UDP ou TCP), e um número de porta a partir da regra de equilíbrio de carga. O pool backend é uma coleção de configurações IP da Máquina Virtual (parte do recurso NIC) que referem o pool de backend balancer de carga.

A tabela a seguir contém algumas configurações de frontend de exemplo:

| Front-end | Endereço IP | protocolo | porta |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

A mesa mostra quatro frontends diferentes. Frontends #1, #2 e #3 são um único frontend com várias regras. O mesmo endereço IP é utilizado mas a porta ou protocolo é diferente para cada frontend. Os frontends #1 e #4 são um exemplo de múltiplos frontends, onde o mesmo protocolo frontal e porta são reutilizados em vários frontends.

O Azure Load Balancer proporciona flexibilidade na definição das regras de equilíbrio de carga. Uma regra declara como um endereço e porta na parte frontal é mapeado para o endereço de destino e porto no backend. Se as portas de backend são ou não reutilizadas através das regras depende do tipo da regra. Cada tipo de regra tem requisitos específicos que podem afetar a configuração do hospedeiro e o design da sonda. Existem dois tipos de regras:

1. A regra padrão sem reutilização da porta de backend
2. A regra IP flutuante onde as portas de backend são reutilizadas

O Azure Load Balancer permite misturar ambos os tipos de regras na mesma configuração do balançador de carga. O equilibrador de carga pode usá-los simultaneamente para um determinado VM, ou qualquer combinação, desde que cumpra os constrangimentos da regra. O tipo de regra que escolher depende dos requisitos da sua aplicação e da complexidade de suportar essa configuração. Deve avaliar quais os tipos de regras que são melhores para o seu cenário.

Exploramos ainda mais estes cenários, começando pelo comportamento padrão.

## <a name="rule-type-1-no-backend-port-reuse"></a>Tipo de regra #1: Sem reutilização da porta de backend

![Ilustração frontal múltipla com frontend verde e roxo](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

Neste cenário, os frontends são configurados da seguinte forma:

| Front-end | Endereço IP | protocolo | porta |
| --- | --- | --- | --- |
| ![frontend verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![frontend roxo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

O DIP é o destino do fluxo de entrada. Na piscina de backend, cada VM expõe o serviço desejado numa porta única num DIP. Este serviço está associado ao frontend através de uma definição de regra.

Definimos duas regras:

| Regra | Frontend do mapa | Para a piscina de backend |
| --- | --- | --- |
| 1 |![frontend verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![backend verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![backend verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![backend roxo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![backend roxo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

O mapeamento completo em Azure Load Balancer é agora o seguinte:

| Regra | Endereço IP frontend | protocolo | porta | Destino | porta |
| --- | --- | --- | --- | --- | --- |
| ![regra verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Endereço IP DIP |80 |
| ![regra roxa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Endereço IP DIP |81 |

Cada regra deve produzir um fluxo com uma combinação única de endereço IP de destino e porta de destino. Ao variar a porta de destino do fluxo, várias regras podem fornecer fluxos para o mesmo DIP em diferentes portas.

As sondas de saúde são sempre direcionadas para o DIP de um VM. Deve assegurar-se de que a sua sonda reflete a saúde do VM.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Tipo de regra #2: reutilização da porta de backend utilizando IP flutuante

O Azure Load Balancer proporciona a flexibilidade para reutilizar a porta frontal através de vários frontends, independentemente do tipo de regra utilizado. Além disso, alguns cenários de aplicação preferem ou exigem que a mesma porta seja usada por múltiplas instâncias de aplicação num único VM na piscina de backend. Exemplos comuns de reutilização portuária incluem agrupamentos para alta disponibilidade, aparelhos virtuais de rede e exposição de vários pontos finais TLS sem reen encriptação.

Se pretender reutilizar a porta de backend através de várias regras, deve ativar o IP flutuante na definição de regra.

"IP flutuante" é a terminologia de Azure para uma parte do que é conhecido como Retorno do Servidor Direto (DSR). A DSR é constituída por duas partes: uma topologia de fluxo e um esquema de mapeamento de endereços IP. A nível da plataforma, o Azure Load Balancer opera sempre numa topologia de fluxo DSR, independentemente de o IP flutuante estar ou não ativado. Isto significa que a parte de saída de um fluxo é sempre corretamente reescrita para fluir diretamente de volta à origem.

Com o tipo de regra padrão, o Azure expõe um esquema tradicional de mapeamento de endereço IP de equilíbrio de carga para facilitar a utilização. Permitir que o IP flutuante altera o sistema de mapeamento de endereços IP para permitir uma flexibilidade adicional, conforme explicado abaixo.

O diagrama a seguir ilustra esta configuração:

![Ilustração frontal múltipla com frontend verde e roxo com DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Para este cenário, cada VM na piscina de backend tem três interfaces de rede:

* DIP: um NIC Virtual associado ao VM (configuração IP do recurso NIC da Azure)
* Frontend 1: uma interface loopback dentro do so convidado que está configurado com endereço IP do Frontend 1
* Frontend 2: uma interface loopback dentro do so convidado que está configurado com endereço IP de Frontend 2

Para cada VM na piscina de backend, executar os seguintes comandos num Pedido de Comando do Windows.

Para obter a lista de nomes de interface que tem no seu VM, digite este comando:

```console
netsh interface show interface 
```

Para o VM NIC (Azure gerido), digite este comando:

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled
```

(substituir o nome de interface pelo nome desta interface)

Para cada interface de backback que adicionou, repita estes comandos:

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled 
```

(substituir o nome de interface pelo nome desta interface loopback)

```console
netsh interface ipv4 set interface “interfacename” weakhostsend=enabled 
```

(substituir o nome de interface pelo nome desta interface loopback)

> [!IMPORTANT]
> A configuração das interfaces loopback é realizada dentro do so convidado. Esta configuração não é executada ou gerida pela Azure. Sem esta configuração, as regras não funcionarão. As definições da sonda de saúde utilizam o DIP do VM em vez da interface loopback que representa o DSR Frontend. Por isso, o seu serviço deve fornecer respostas de sonda numa porta DIP que reflitam o estado do serviço oferecido na interface loopback que representa o DSR Frontend.


Vamos assumir a mesma configuração frontal que no cenário anterior:

| Front-end | Endereço IP | protocolo | porta |
| --- | --- | --- | --- |
| ![frontend verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![frontend roxo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Definimos duas regras:

| Regra | Front-end | Mapa para piscina de backend |
| --- | --- | --- |
| 1 |![regra verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![backend verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (em VM1 e VM2) |
| 2 |![regra roxa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![backend roxo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (em VM1 e VM2) |

A tabela a seguir mostra o mapeamento completo no equilibrador de carga:

| Regra | Endereço IP frontend | protocolo | porta | Destino | porta |
| --- | --- | --- | --- | --- | --- |
| ![regra verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |o mesmo que frontend (65.52.0.1) |o mesmo que frontend (80) |
| ![regra roxa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |o mesmo que frontend (65.52.0.2) |o mesmo que frontend (80) |

O destino do fluxo de entrada é o endereço IP frontend na interface loopback no VM. Cada regra deve produzir um fluxo com uma combinação única de endereço IP de destino e porta de destino. Ao variar o endereço IP de destino do fluxo, a reutilização portuária é possível no mesmo VM. O seu serviço está exposto ao equilibrador de carga ligando-o ao endereço IP e porta da respetiva interface loopback.

Note que este exemplo não altera a porta de destino. Apesar de este ser um cenário ip flutuante, o Azure Load Balancer também suporta definir uma regra para reescrever a porta de destino backend e torná-la diferente da porta de destino frontend.

O tipo de regra IP flutuante é a base de vários padrões de configuração do balançador de carga. Um exemplo que está atualmente disponível é a [configuração SQL AlwaysOn com Múltiplos Ouvintes.](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) Com o tempo, documentaremos mais destes cenários.

## <a name="limitations"></a>Limitações

* Várias configurações frontend são suportadas apenas com IaaS VMs.
* Com a regra IP flutuante, a sua aplicação deve utilizar a configuração IP primária para fluxos SNAT de saída. Se a sua aplicação se ligar ao endereço IP frontal configurado na interface loopback no sistema operativo convidado, o SNAT de saída do Azure não está disponível para reescrever o fluxo de saída e o fluxo falha.  Rever [cenários de saída.](load-balancer-outbound-connections.md)
* O IP flutuante não é atualmente suportado em configurações ip secundárias para cenários internos de equilíbrio de carga.
* Os endereços IP públicos têm um efeito na faturação. Para mais informações, consulte [os preços do endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Aplicam-se limites de subscrição. Para mais informações, consulte [os limites de Serviço](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) para mais detalhes.

## <a name="next-steps"></a>Passos seguintes

- [Reveja as ligações de saída](load-balancer-outbound-connections.md) para entender o impacto de múltiplos frontends no comportamento de ligação de saída.