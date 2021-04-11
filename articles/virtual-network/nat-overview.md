---
title: O que é Azure Virtual Network NAT?
titlesuffix: Azure Virtual Network
description: Visão geral das funcionalidades, recursos, arquitetura e implementação da Rede Virtual NAT. Saiba como funciona o NAT de Rede Virtual e como utilizar os recursos de gateway NAT na nuvem.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: allensu
ms.openlocfilehash: 3a4e9649d921c6af34d99ab290adb96be3ad1282
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058532"
---
# <a name="what-is-virtual-network-nat"></a>O que é Virtual Network NAT?

O VIRTUAL Network NAT (tradução de endereços de rede) simplifica a conectividade de saída da Internet para redes virtuais. Quando configurados numa sub-rede, toda a conectividade de saída utiliza os seus endereços IP públicos estáticos especificados.  A conectividade de saída é possível sem o balanceador de carga ou endereços IP públicos diretamente ligados a máquinas virtuais. O NAT é totalmente gerido e altamente resistente.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP (PIP) and an IP prefix." width="256" title="Rede Virtual NAT">
</p>



*Figura: Virtual Network NAT*

## <a name="static-ip-addresses-for-outbound-only"></a>Endereços IP estáticos apenas para saída

A conectividade de saída pode ser definida para cada sub-rede com NAT.  Várias sub-redes dentro da mesma rede virtual podem ter DIFERENTES NATs. Uma sub-rede é configurada especificando qual recurso de gateway NAT a utilizar. Todos os fluxos de saída da UDP e TCP de qualquer instância de máquina virtual utilizarão o NAT. 

O NAT é compatível com os recursos de endereços IP públicos standard SKU ou recursos prefixos IP públicos ou uma combinação de ambos.  Pode utilizar um prefixo IP público diretamente ou distribuir os endereços IP públicos do prefixo através de vários recursos de gateway NAT. A NAT irá preparar todo o tráfego para o alcance dos endereços IP do prefixo.  Qualquer filtragem IP das suas implementações é agora fácil.

Todo o tráfego de saída da sub-rede é processado automaticamente pela NAT sem qualquer configuração do cliente.  As rotas definidas pelo utilizador não são necessárias. A NAT tem precedência sobre outros cenários de saída e substitui o destino padrão da Internet de uma sub-rede.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>SNAT a pedido com vários endereços IP para escala

O NAT utiliza a "tradução de endereços de rede portuária" (PNAT ou PAT) e é recomendado para a maioria das cargas de trabalho. Cargas de trabalho dinâmicas ou divergentes podem ser facilmente acomodadas com alocação de fluxo de saída a pedido. Evita-se um planeamento extensivo, uma pré-atribuição e, em última análise, uma sobreprovisão dos recursos de saída. Os recursos portuários SNAT são partilhados e disponíveis em todas as sub-redes utilizando um recurso específico de gateway NAT e são fornecidos quando necessário.

Um endereço IP público anexado ao NAT fornece até 64.000 fluxos simultâneos para UDP e TCP, respectivamente. Pode começar com um único endereço IP e escalar até 16 endereços IP utilizando endereços IP públicos ou prefixos IP públicos ou ambos.  Um recurso de gateway NAT utilizará todos os endereços IP associados ao recurso para ligações de saída de todas as sub-redes configuradas com o mesmo recurso de gateway NAT.

O NAT permite que os fluxos sejam criados da rede virtual para a Internet. O tráfego de devolução da Internet só é permitido em resposta a um fluxo ativo.

Ao contrário do que acontece com o SNAT de saída do balanceador de carga, o NAT não tem restrições sobre as quais o IP privado de uma instância de máquina virtual pode fazer ligações de saída.  As configurações IP primárias e secundárias podem criar ligação à Internet de saída com o NAT.

## <a name="coexistence-of-inbound-and-outbound"></a>Coexistência de entrada e saída

A NAT é compatível com os seguintes recursos SKU padrão:

- Balanceador de carga
- Endereço IP público
- Prefixo de IP público

Quando utilizados em conjunto com o NAT, estes recursos fornecem conectividade à Internet de entrada para as suas sub-redes. A NAT fornece toda a conectividade de saída da Internet a partir das suas sub-redes.

As funcionalidades NAT e Standard SKU compatíveis estão cientes da direção em que o fluxo foi iniciado. Cenários de entrada e saída podem coexistir. Estes cenários receberão as traduções corretas de endereço de rede porque estas funcionalidades estão cientes da direção do fluxo. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="512" title="Direção de fluxo NAT de rede virtual">
</p>

*Figura: Direção de fluxo NAT de rede virtual*

## <a name="fully-managed-highly-resilient"></a>Totalmente gerido, altamente resistente

O NAT está totalmente dimensionado desde o início. Não é necessária uma operação de rampa ou de escala.  A Azure gere o funcionamento da NAT para si.  A NAT tem sempre vários domínios de avaria e pode sofrer múltiplas falhas sem falha de serviço.

## <a name="tcp-reset-for-unrecognized-flows"></a>Reposição de TCP para fluxos não reconhecidos

O lado privado da NAT envia pacotes de reset TCP para tentativas de comunicação numa ligação TCP que não existe. Um exemplo são as ligações que atingiram o tempo limite. O próximo pacote recebido devolverá um Reset TCP ao endereço IP privado para sinalizar e forçar o encerramento da ligação.

O lado público da NAT não gera pacotes de reset TCP ou qualquer outro tráfego.  Apenas é emitido o tráfego produzido pela rede virtual do cliente.

## <a name="configurable-tcp-idle-timeout"></a>Tempo limite de marcha lenta ção do TCP configurável

É utilizado um tempo limite de 4 minutos de TCP padrão e pode ser aumentado até 120 minutos. Qualquer atividade num fluxo também pode reiniciar o temporizador inativo, incluindo os keepalives TCP.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Isolamento regional ou de zona com zonas de disponibilidade

O NAT é regional por defeito. Ao criar [cenários de zonas de disponibilidade,](../availability-zones/az-overview.md) o NAT pode ser isolado numa zona específica (implantação zonal).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="512" title="Rede Virtual NAT com zonas de disponibilidade">
</p>

*Figura: Rede Virtual NAT com zonas de disponibilidade*

## <a name="multi-dimensional-metrics-for-observability"></a>Métricas multidimensionais para a observabilidade

Pode monitorizar o funcionamento do seu NAT através de métricas multidimensionais expostas no Azure Monitor. Estas métricas podem ser usadas para observar o uso e para a resolução de problemas.  Os recursos de gateway DA NAT expõem as seguintes métricas:
- Bytes
- Pacotes
- Pacotes derrubados
- Total de ligações SNAT
- Transições do estado de ligação SNAT por intervalo.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>SLA

Na disponibilidade geral, a trajetória de dados do NAT está disponível pelo menos 99,9%.

## <a name="pricing"></a>Preços

Para obter detalhes sobre os preços, consulte [os preços da Rede Virtual.](https://azure.microsoft.com/pricing/details/virtual-network)

## <a name="availability"></a>Disponibilidade

A Rede Virtual NAT e o recurso de gateway NAT estão disponíveis em todas as regiões de todas as regiões de [nuvens](https://azure.microsoft.com/global-infrastructure/regions/)Azure.

## <a name="suggestions"></a>Sugestões

Queremos saber como podemos melhorar o serviço. Propor e votar o que devemos construir a seguir no [UserVoice para o NAT.](https://aka.ms/natuservoice)

## <a name="limitations"></a>Limitações

* O NAT é compatível com o IP público SKU padrão, prefixo IP público e recursos do balançador de carga. Os recursos básicos, tais como o balanceador de carga básica, e quaisquer produtos derivados deles não são compatíveis com o NAT.  Os recursos básicos devem ser colocados numa sub-rede não configurada com o NAT.
* A família iPv4 é apoiada.  A NAT não interage com a família IPv6.  O NAT não pode ser implantado numa sub-rede com um prefixo IPv6.
* A NAT não pode abranger várias redes virtuais.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o recurso nat gateway.](./nat-gateway-resource.md)
* [Diga-nos o que construir a seguir para o NAT de Rede Virtual no UserVoice](https://aka.ms/natuservoice).
