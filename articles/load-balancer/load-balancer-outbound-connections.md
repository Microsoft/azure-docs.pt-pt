---
title: Esquilibrador de carga de azure de saída
description: Descreve como o Azure Load Balancer é usado como um proxy para a conectividade de saída da Internet
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 185bb47677e978a3098f39024995da6399f90658
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241774"
---
# <a name="outbound-proxy-azure-load-balancer"></a>Esquilibrador de carga de azure de saída

Um equilibrador de carga Azure pode ser usado como um proxy para a conectividade de saída da Internet. O equilibrador de carga fornece a conectividade de saída para as instâncias de backend. 

Esta configuração utiliza **tradução de endereço de rede de origem (SNAT)** . O SNAT reescreve o endereço IP do backend para o endereço IP público do seu equilibrador de carga. 

O SNAT permite **mascarar IP** da instância backend. Esta mascarada impede que fontes externas tenham um endereço direto para as instâncias de backend. 

A partilha de um endereço IP entre instâncias de backend reduz o custo dos IPs públicos estáticos e suporta cenários como a simplificação de IP permite listas com tráfego de IPs públicos conhecidos. 

## <a name="sharing-ports-across-resources"></a><a name ="snat"></a> Partilha de portos através de recursos

Se os recursos de backend de um balanceador de carga não tiverem endereços IP públicos de nível de instância (ILPIP), eles estabelecem conectividade de saída através do IP frontal do esquilibrista de carga pública.

As portas são usadas para gerar identificadores únicos usados para manter fluxos distintos. A internet usa um cinco tuple para fornecer esta distinção.

O cinco tuple consiste em:

* IP de destino
* Porta de destino
* IP de origem
* Fonte portuária e protocolo para fornecer esta distinção.

Se uma porta for utilizada para ligações de entrada, terá um **ouvinte** para pedidos de ligação de entrada nessa porta e não pode ser utilizada para ligações de saída. 

Para estabelecer uma ligação de saída, deve ser utilizado um **porto efémero** para fornecer ao destino um porto para comunicar e manter um fluxo de tráfego distinto. 

Cada endereço IP tem 65.535 portas. As primeiras portas 1024 são reservadas como **portas de sistema.** Cada porta pode ser utilizada para ligações de entrada ou saída para TCP e UDP. 

Dos restantes portos, a Azure dá 64.000 para utilização como **portos efémeros.** Quando um endereço IP é adicionado como uma configuração IP frontend, estas portas efémeras podem ser usadas para SNAT.

Através de regras de saída, estas portas SNAT podem ser distribuídas para backend instances para permitir-lhes partilhar o IP(s) público do balançador de carga para ligações de saída.

A ligação em rede no anfitrião para cada instância de backend fará SNAT para pacotes que fazem parte de uma ligação de saída. O anfitrião reescreve o IP de origem a um dos IPs públicos. O hospedeiro reescreve a porta de origem de cada pacote de saída para uma das portas SNAT.

## <a name="exhausting-ports"></a><a name="scenarios"></a> Portas exaustivas

Todas as ligações ao mesmo destino IP e porto de destino usarão uma porta SNAT. Esta ligação mantém um fluxo de **tráfego** distinto da instância de backend ou **cliente** para um **servidor** . Este processo dá ao servidor uma porta distinta para abordar o tráfego. Sem este processo, a máquina cliente desconhece de que fluxo um pacote faz parte.

Imagine ter vários navegadores https://www.microsoft.com indo, que é:

* Destino IP = 23.53.254.142
* Porto de Destino = 443
* Protocolo = TCP

Sem diferentes portas de destino para o tráfego de retorno (a porta SNAT utilizada para estabelecer a ligação), o cliente não terá forma de separar um resultado de consulta de outro.

As ligações de saída podem rebentar. Uma instância de backend pode ser atribuída portos insuficientes. Sem **a reutilização da ligação** ativada, o risco de **exaustão portuária** SNAT é aumentado.

Novas ligações de saída para um IP de destino falharão quando ocorrer a exaustão do porto. As ligações terão sucesso quando uma porta estiver disponível. Este esgotamento ocorre quando as 64.000 portas de um endereço IP são espalhadas finamente em muitas instâncias de backend. Para obter orientações sobre a mitigação da exaustão do porto SNAT, consulte o [guia de resolução de problemas](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection).  

Para as ligações TCP, o equilibrador de carga utilizará uma única porta SNAT para cada destino IP e porta. Este multiuso permite múltiplas ligações ao mesmo destino IP com a mesma porta SNAT. Este multiuso é limitado se a ligação não for para diferentes portas de destino.

Para ligações UDP, o balanceador de carga utiliza um algoritmo **NAT cone restrito por porta,** que consome uma porta SNAT por destino IP qualquer que seja a porta de destino. 

Uma porta é reutilizada para um número ilimitado de ligações. A porta só é reutilizada se o destino IP ou porta for diferente.

## <a name="port-allocation"></a><a name="preallocatedports"></a> Dotação portuária

Cada IP público atribuído como um IP frontal do seu balanceador de carga recebe 64.000 portas SNAT para os seus membros de pool backend. Os portos não podem ser partilhados com membros da piscina de backend. Uma gama de portas SNAT só pode ser utilizada por uma única instância de backend para garantir que os pacotes de devolução são encaminhados corretamente. 

Recomenda-se que utilize uma regra de saída explícita para configurar a atribuição de porta SNAT. Esta regra maximizará o número de portas SNAT que cada instância de backend tem disponível para ligações de saída. 

Se utilizar a atribuição automática de SNAT de saída através de uma regra de equilíbrio de carga, a tabela de atribuição definirá a sua atribuição portuária.

A tabela a seguir <a name="snatporttable"></a> mostra as pré-aallocations da porta SNAT para níveis de tamanhos de piscina de backend:

| Tamanho da piscina (instâncias VM) | Portas SNAT pré-locadas por configuração IP |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

>[!NOTE]
> Se tiver uma piscina de backend com um tamanho máximo de 10, cada instância pode ter 64.000/10 = 6.400 portas se definir uma regra de saída explícita. De acordo com a tabela acima, cada um terá apenas 1.024 se escolher a atribuição automática.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Regras de saída e Nat de rede virtual

As regras de saída do Azure Load Balancer e o Virtual Network NAT são opções disponíveis para saída de uma rede virtual.

Para obter mais informações sobre as regras de saída, consulte [as regras de saída.](outbound-rules.md)

Para obter mais informações sobre a Rede Virtual Azure NAT, consulte [o que é Azure Virtual Network NAT](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Restrições

*   As portas serão libertadas após 15 segundos se um **TCP RST** for recebido ou enviado
*   As portas serão libertadas após 240 segundos se um **FINACK** for recebido ou enviado
*   Quando uma ligação é inativa sem que sejam enviados novos pacotes, as portas serão libertadas após 4 a 120 minutos.
  * Este limiar pode ser configurado através de regras de saída.
*   Cada endereço IP fornece 64.000 portas que podem ser usadas para SNAT.
*   Cada porta pode ser utilizada tanto para ligações TCP como UDP para um endereço IP de destino
  * É necessária uma porta UDP SNAT se a porta de destino é única ou não. Para cada ligação UDP a um IP de destino, é utilizada uma porta UDP SNAT.
  * Uma porta TCP SNAT pode ser usada para múltiplas ligações ao mesmo destino IP, desde que as portas de destino sejam diferentes.
*   A exaustão do SNAT ocorre quando uma instância de backend se esgota de determinadas portas SNAT. Um equilibrador de carga ainda pode ter portas SNAT não uusadas. Se as portas SNAT utilizadas por uma instância de backend excederem as portas SNAT dadas, não será possível estabelecer novas ligações de saída.

## <a name="next-steps"></a>Passos seguintes

*   [Falhas de ligação de saída de resolução de problemas por causa da exaustão do SNAT](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)
*   [Reveja as métricas SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) e familiarize-se com a forma correta de filtrar, dividir e vê-las.

