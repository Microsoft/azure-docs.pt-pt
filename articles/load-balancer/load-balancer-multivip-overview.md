---
title: Frentemúltiplas - Equilíbrio de Carga Azure
description: Com este caminho de aprendizagem, começar com uma visão geral de várias frentes no Azure Load Balancer
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
ms.openlocfilehash: f911b36d4f38d9b769cf34e4e2326ed1cb52da80
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022816"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Múltiplas extremidades dianteiras para o Equilíbrio de Carga Azure

O Azure Load Balancer permite-lhe carregar serviços de equilíbrio em várias portas, múltiplos endereços IP, ou ambos. Pode utilizar definições públicas e internas de equilíbrio de carga para carregar fluxos de equilíbrio através de um conjunto de VMs.

Este artigo descreve os fundamentos desta capacidade, conceitos importantes e constrangimentos. Se pretender apenas expor serviços num endereço IP, pode encontrar instruções simplificadas para configurações de equilíbrio de carga [pública](load-balancer-get-started-internet-portal.md) ou [interna.](load-balancer-get-started-ilb-arm-portal.md) Adicionar várias extremidades dianteiras é incremental a uma única configuração frontal. Utilizando os conceitos deste artigo, pode expandir uma configuração simplificada a qualquer momento.

Quando define um Balancer de Carga Azure, uma configuração frontal e uma piscina de backend estão ligados às regras. A sonda de saúde referenciada pela regra é usada para determinar como novos fluxos são enviados para um nó na piscina de backend. O frontend (aka VIP) é definido por um endereço IP de 3 tuple composto por um endereço IP (público ou interno), um protocolo de transporte (UDP ou TCP) e um número de porta da regra de equilíbrio de carga. A piscina de backend é uma coleção de configurações IP de máquina virtual (parte do recurso NIC) que referenciam o pool de backend load Balancer.

A tabela seguinte contém algumas configurações frontais exemplo:

| Front-end | Endereço IP | protocolo | porta |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

A mesa mostra quatro frentes diferentes. Frontends #1, #2 e #3 são um único frontend com várias regras. O mesmo endereço IP é utilizado, mas a porta ou protocolo é diferente para cada frente. Frontends #1 e #4 são um exemplo de múltiplas frentes, onde o mesmo protocolo frontend e porta são reutilizados em várias frentes.

O Azure Load Balancer proporciona flexibilidade na definição das regras de equilíbrio da carga. Uma regra declara como um endereço e porta na extremidade frontal é mapeado para o endereço de destino e porta no backend. Se as portas de backend são ou não reutilizadas através de regras depende do tipo da regra. Cada tipo de regra tem requisitos específicos que podem afetar a configuração do hospedeiro e o design da sonda. Existem dois tipos de regras:

1. A regra padrão sem reutilização da porta de backend
2. A regra de IP flutuante onde as portas de backend são reutilizadas

O Equilíbrio de Carga Azure permite-lhe misturar ambos os tipos de regras na mesma configuração do equilíbrio de carga. O equilibrador de carga pode usá-los simultaneamente para um dado VM, ou qualquer combinação, desde que cumpra os constrangimentos da regra. Que tipo de regra escolhe depende dos requisitos da sua aplicação e da complexidade de suportar essa configuração. Deve avaliar quais os tipos de regras melhores para o seu cenário.

Exploramos ainda mais estes cenários começando pelo comportamento padrão.

## <a name="rule-type-1-no-backend-port-reuse"></a>Tipo de regra #1: Não há reutilização da porta backend

![Ilustração frontal múltipla com frontend verde e roxo](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

Neste cenário, as extremidades dianteiras são configuradas da seguinte forma:

| Front-end | Endereço IP | protocolo | porta |
| --- | --- | --- | --- |
| ![frontend verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![frontend roxo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

O DIP é o destino do fluxo de entrada. Na piscina de backend, cada VM expõe o serviço desejado em uma porta única em um DIP. Este serviço está associado ao frontend através de uma definição de regra.

Definimos duas regras:

| Regra | Extremidade frontal do mapa | Para apoiar a piscina |
| --- | --- | --- |
| 1 |![frontend verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

O mapeamento completo no Equilíbrio de Carga Azure é agora o seguinte:

| Regra | Endereço IP de front-end | protocolo | porta | Destino | porta |
| --- | --- | --- | --- | --- | --- |
| ![regra verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Endereço IP DIP |80 |
| ![regra roxa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Endereço IP DIP |81 |

Cada regra deve produzir um fluxo com uma combinação única de endereço IP de destino e porta de destino. Ao variar a porta de destino do fluxo, várias regras podem fornecer fluxos para o mesmo DIP em diferentes portas.

As sondas de saúde são sempre direcionadas para o MERGULHO de um VM. Deve assegurar-se de que a sua sonda reflete a saúde do VM.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Tipo de regra #2: reutilização da porta de backend utilizando IP flutuante

O Azure Load Balancer proporciona a flexibilidade para reutilizar a porta fronte em várias extremidades frontais, independentemente do tipo de regra utilizado. Além disso, alguns cenários de aplicação preferem ou exigem que a mesma porta seja utilizada por múltiplas instâncias de aplicação num único VM na piscina de backend. Exemplos comuns de reutilização da porta incluem agrupamento para alta disponibilidade, aparelhos virtuais de rede e expondo vários pontos finais TLS sem reencriptação.

Se pretender reutilizar a porta de backend através de várias regras, deve ativar o IP flutuante na definição de regra.

"FLOATING IP" é a terminologia de Azure para uma parte do que é conhecido como Retorno do Servidor Direto (DSR). A DSR é constituída por duas partes: uma topologia de fluxo e um esquema de mapeamento de endereçoip. A nível da plataforma, o Azure Load Balancer opera sempre numa topologia de fluxo DSR, independentemente de o IP flutuante estar ou não ativado. Isto significa que a parte de saída de um fluxo é sempre corretamente reescrita para fluir diretamente de volta à origem.

Com o tipo de regra padrão, o Azure expõe um sistema tradicional de mapeamento de endereçoIP de equilíbrio de carga para facilitar a utilização. Permitir alterações de IP flutuantes o sistema de mapeamento de endereços IP para permitir uma flexibilidade adicional, como explicado abaixo.

O diagrama seguinte ilustra esta configuração:

![Ilustração frontal múltipla com frontend verde e roxo com DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Para este cenário, cada VM no pool backend tem três interfaces de rede:

* DIP: um NIC virtual associado ao VM (configuração IP do recurso NIC do Azure)
* Frontend 1: uma interface de backback dentro do OS do hóspede que é configurada com endereço IP do Frontend 1
* Frontend 2: uma interface de backback dentro do OS do hóspede que é configurada com endereço IP do Frontend 2

Para cada VM na piscina de backend, execute os seguintes comandos num Aviso de Comando do Windows.

Para obter a lista de nomes de interface que tem no seu VM, escreva este comando:

    netsh interface show interface 

Para o VM NIC (gerido pelo Azure), escreva este comando:

    netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled
   (substituir o nome da interface com o nome desta interface)

Para cada interface de backback adicionada, repita estes comandos:

    netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled 
   (substituir o nome da interface com o nome desta interface de backback)
     
    netsh interface ipv4 set interface “interfacename” weakhostsend=enabled 
   (substituir o nome da interface com o nome desta interface de backback)

> [!IMPORTANT]
> A configuração das interfaces de backback é realizada dentro do OS do hóspede. Esta configuração não é executada ou gerida pelo Azure. Sem esta configuração, as regras não funcionarão. As definições de sonda de saúde utilizam o DIP do VM em vez da interface de backback que representa o DSR Frontend. Por isso, o seu serviço deve fornecer respostas de sonda numa porta DIP que reflitam o estado do serviço oferecido na interface de backback que representa o DSR Frontend.


Vamos assumir a mesma configuração frontal que no cenário anterior:

| Front-end | Endereço IP | protocolo | porta |
| --- | --- | --- | --- |
| ![frontend verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![frontend roxo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Definimos duas regras:

| Regra | Front-end | Mapa para piscina de backend |
| --- | --- | --- |
| 1 |![regra](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (em VM1 e VM2) |
| 2 |![regra](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (em VM1 e VM2) |

A tabela que se segue mostra o mapeamento completo no equilibrador de carga:

| Regra | Endereço IP de front-end | protocolo | porta | Destino | porta |
| --- | --- | --- | --- | --- | --- |
| ![regra verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |o mesmo que o frontend (65.52.0.1) |o mesmo que o frontend (80) |
| ![regra roxa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |o mesmo que o frontend (65.52.0.2) |o mesmo que o frontend (80) |

O destino do fluxo de entrada é o endereço IP frontal na interface de loopback no VM. Cada regra deve produzir um fluxo com uma combinação única de endereço IP de destino e porta de destino. Ao variar o endereço IP de destino do fluxo, a reutilização da porta é possível no mesmo VM. O seu serviço está exposto ao equilibrista de carga ligando-o ao endereço IP da extremidade frontal e à porta da respetiva interface de backback.

Note que este exemplo não altera a porta de destino. Apesar de se tratar de um cenário IP flutuante, o Azure Load Balancer também suporta a definição de uma regra para reescrever a porta de destino de backend e torná-la diferente da porta de destino frontend.

O tipo de regra IP flutuante é a base de vários padrões de configuração do equilíbrio de carga. Um exemplo que está atualmente disponível é o [SQL AlwaysOn com a](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) configuração multiple Listeners. Com o tempo, documentaremos mais destes cenários.

## <a name="limitations"></a>Limitações

* Várias configurações frontais são suportadas apenas com VMs IaaS.
* Com a regra IP flutuante, a sua aplicação deve utilizar a configuração IP primária para fluxos de SNAT de saída. Se a sua aplicação se ligar ao endereço IP frontal configurado na interface de backback no OS de saída do hóspede, o SNAT de saída do Azure não está disponível para reescrever o fluxo de saída e o fluxo falha.  Reveja [os cenários de saída.](load-balancer-outbound-connections.md)
* Os endereços IP públicos têm um efeito na faturação. Para mais informações, consulte os preços do [Endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Aplicam-se os limites de subscrição. Para mais informações, consulte [os limites de serviço](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) para mais detalhes.

## <a name="next-steps"></a>Próximos passos

- Reveja [as ligações de saída](load-balancer-outbound-connections.md) para entender o impacto de várias extremidades frontais no comportamento de ligação de saída.
