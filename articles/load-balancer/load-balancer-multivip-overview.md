---
title: Vários front-ends de Balanceador de carga do Azure
titlesuffix: Azure Load Balancer
description: Descrição geral dos vários front-ends no balanceador de carga do Azure
services: load-balancer
documentationcenter: na
author: chkuhtz
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: chkuhtz
ms.openlocfilehash: b9a140314b8eba6386c37bdbcf2bb3de58589335
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60594144"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Vários front-ends de Balanceador de carga do Azure

O Balanceador de carga do Azure permite-lhe carregar saldo de serviços em várias portas, vários endereços IP ou ambos. Pode utilizar as definições do Balanceador de carga públicos e internos para fluxos de balanceamento de carga num conjunto de VMs.

Este artigo descreve os conceitos básicos desta capacidade, conceitos importantes e restrições. Se só pretende expor serviços num endereço IP, pode encontrar instruções simplificadas para [pública](load-balancer-get-started-internet-portal.md) ou [interno](load-balancer-get-started-ilb-arm-portal.md) configurações de Balanceador de carga. Adicionar vários front-ends é incremental para uma configuração de front-end único. Usando os conceitos deste artigo, pode expandir uma configuração simplificada em qualquer altura.

Quando define um balanceador de carga do Azure, um front-end e uma configuração de conjuntos de back-end estão ligados com regras. A sonda de estado de funcionamento referenciada pela regra é utilizada para determinar como os novos fluxos são enviadas para um nó no conjunto de back-end. O front-end (também conhecido como VIP) é definido por uma tupla de 3, composta por um endereço IP (interno ou público), um protocolo de transporte (UDP ou TCP) e um número de porta da regra de balanceamento de carga. O conjunto de back-end é uma coleção de configurações de IP da Máquina Virtual (parte do recurso NIC) que referenciam o conjunto de back-end de Balanceador de carga.

A tabela seguinte contém algumas configurações de front-end de exemplo:

| Front-end | Endereço IP | protocol | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

A tabela mostra quatro front-ends de diferentes. Front-ends n. º 1, #2 e 3 de # são um front-end único com várias regras. O mesmo endereço IP é utilizado, mas a protocolo ou a porta é diferente para cada front-end. Front-ends n. º 1 e 4 de # são um exemplo de vários front-ends, em que o mesmo protocolo front-end e a porta são reutilizados em vários front-ends.

O Balanceador de carga do Azure fornece flexibilidade na definição de regras de balanceamento de carga. Uma regra declara como um endereço e porta no front-end é mapeado para o endereço de destino e a porta back-end. Se pretende ou não as portas de back-end são reutilizadas em regras dependem do tipo da regra. Cada tipo de regra tem requisitos específicos que podem afetar o design de configuração e a sonda do anfitrião. Existem dois tipos de regras:

1. A regra predefinida com nenhuma reutilização de porta de back-end
2. A regra de IP flutuante em que as portas de back-end são reutilizadas

O Balanceador de carga do Azure permite-lhe combinar ambos os tipos de regra na mesma configuração de Balanceador de carga. O Balanceador de carga pode utilizá-los em simultâneo para uma determinada VM ou qualquer combinação, desde que concordar com as restrições da regra. Que tipo de regra que escolher depende dos requisitos da sua aplicação e a complexidade do suporte a essa configuração. Deve avaliar quais tipos de regra são recomendados para o seu cenário.

Vamos explorar esses cenários ainda mais ao começar com o comportamento padrão.

## <a name="rule-type-1-no-backend-port-reuse"></a>Tipo de regra #1: Nenhuma reutilização de porta de back-end

![Vários front-end ilustração, com verde e roxa front-end](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

Neste cenário, os front-ends são configuradas da seguinte forma:

| Front-end | Endereço IP | protocol | port |
| --- | --- | --- | --- |
| ![front-end verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![front-end púrpura](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

A QUEDA é o destino do fluxo de entrada. No agrupamento de back-end, cada VM expõe o serviço desejado numa porta única num DIP. Este serviço está associado com o front-end através de uma definição de regra.

Definimos duas regras:

| Regra | Mapear o front-end | Para o conjunto back-end |
| --- | --- | --- |
| 1 |![front-end verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

O mapeamento completo no balanceador de carga do Azure é agora o seguinte:

| Regra | Endereço IP de front-end | protocol | port | Destino | port |
| --- | --- | --- | --- | --- | --- |
| ![regra verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Endereço IP |80 |
| ![regra púrpura](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Endereço IP |81 |

Cada regra deve produzir um fluxo com uma combinação única de endereço IP de destino e porta de destino. Variando a porta de destino do fluxo, várias regras podem entregar fluxos para o DIP mesmo nas portas diferentes.

Sondas de estado de funcionamento sejam sempre direcionadas para o DIP de uma VM. Tem de se certificar de que que a sua pesquisa reflete o estado de funcionamento da VM.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>#2 do tipo de regra: reutilização de porta de back-end com o IP flutuante

O Balanceador de carga do Azure fornece a flexibilidade de reutilizar a porta de front-end em vários front-ends, independentemente do tipo de regra utilizado. Além disso, alguns cenários de aplicações preferem ou exigem a mesma porta a ser utilizada por várias instâncias de aplicações numa única VM no conjunto de back-end. Exemplos comuns de reutilização de porta de incluir o cluster para elevada disponibilidade, aplicações virtuais e expor vários pontos de extremidade do TLS sem nova criptografia de rede.

Se quiser reutilizar a porta de back-end em várias regras, tem de ativar o IP flutuante na definição da regra.

"IP flutuante" é a terminologia do Azure durante uma parte do que é conhecido como direto servidor devolver (DSR). DSR consiste em duas partes: esquema de mapeamento de endereços de uma topologia de fluxo e um IP. Num nível de plataforma, o Balanceador de carga do Azure sempre opera numa topologia de fluxo DSR, independentemente se o IP flutuante está ativado ou não. Isso significa que a parte de saída de um fluxo sempre corretamente é reescrita para fluxo diretamente de volta para a origem.

Com o tipo de regra padrão, o Azure expõe uma esquema de mapeamento de endereço IP para facilidade de utilização de balanceamento de carga tradicional. Ativar o IP flutuante muda o esquema de mapeamento de endereço IP para permitir flexibilidade adicional, conforme explicado abaixo.

O diagrama seguinte ilustra esta configuração:

![Vários front-end ilustração, com verde e roxa front-end com DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Para este cenário, todas as VMS num conjunto de back-end tem três interfaces de rede:

* DIP: NIC Virtual associado à VM (configuração de IP do recurso NIC do Azure)
* Front-end 1: uma interface de loopback dentro do sistema operacional que está configurada com endereço IP de front-end 1 convidado
* Front-end 2: uma interface de loopback dentro do sistema operacional que está configurada com endereço IP de front-end 2 convidado

> [!IMPORTANT]
> A configuração das interfaces de loopback é efetuada no SO convidado. Esta configuração não é realizada ou gerenciada pelo Azure. Sem esta configuração, as regras não irão funcionar. Definições de sonda de estado de funcionamento, utilize o DIP de VM, em vez da interface de loopback representando o front-end de DSR. Por conseguinte, o serviço tem de fornecer respostas de sonda numa porta DIP que refletem o estado do serviço oferecido na interface de loopback representando o front-end de DSR.

Vamos supor que a mesma configuração de front-end, como no cenário anterior:

| Front-end | Endereço IP | protocol | port |
| --- | --- | --- | --- |
| ![front-end verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![front-end púrpura](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Definimos duas regras:

| Regra | Front-end | Mapear para o conjunto back-end |
| --- | --- | --- |
| 1 |![Regra](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (em VM1 e VM2) |
| 2 |![Regra](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (em VM1 e VM2) |

A tabela seguinte mostra o mapeamento completo no balanceador de carga:

| Regra | Endereço IP de front-end | protocol | port | Destino | port |
| --- | --- | --- | --- | --- | --- |
| ![regra verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |mesmo que o front-end (65.52.0.1) |mesmo que o front-end (80) |
| ![regra púrpura](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |mesmo que o front-end (65.52.0.2) |mesmo que o front-end (80) |

O destino do fluxo de entrada é o endereço IP de front-end na interface de loopback na VM. Cada regra deve produzir um fluxo com uma combinação única de endereço IP de destino e porta de destino. Variando o endereço IP de destino do fluxo, a reutilização de porta é possível na mesma VM. O serviço está exposto ao balanceador de carga vinculando-o para o endereço IP do front-end e a porta da interface de loopback respectivos.

Tenha em atenção que este exemplo não altera a porta de destino. Mesmo que este é um cenário de IP flutuante, Balanceador de carga do Azure também suporta a definição de uma regra para reescrever a porta de back-end de destino e para que seja diferente da porta de destino do front-end.

O tipo de regra de IP flutuante é a base dos vários padrões de configuração de Balanceador de carga. Um exemplo que está atualmente disponível é o [AlwaysOn de SQL com várias escutas](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) configuração. Ao longo do tempo, iremos irá documentar mais estes cenários.

## <a name="limitations"></a>Limitações

* Várias configurações de front-end só são suportadas com VMs de IaaS.
* Com a regra de IP flutuante, a aplicação tem de utilizar a configuração de IP primária para fluxos de saída. Se seu aplicativo liga para o endereço IP de front-end configurado no loopback interface no SO convidado, Azure do SNAT não está disponível para reescrever o fluxo de saída e o fluxo falha.
* Endereços IP públicos têm um efeito na faturação. Para obter mais informações, consulte [preços de endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Limites de subscrição se aplicam. Para obter mais informações, consulte [limites de serviço](../azure-subscription-service-limits.md#networking-limits) para obter detalhes.

## <a name="next-steps"></a>Passos Seguintes

- Revisão [ligações de saída](load-balancer-outbound-connections.md) para compreender o impacto de vários front-ends no comportamento de ligação de saída.
