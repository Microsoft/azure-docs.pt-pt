---
title: 'Azure ExpressRoute: Azure for Network Engineers'
description: Esta página explica aos engenheiros de rede tradicionais como as redes funcionam em Azure.
documentationcenter: na
services: networking
author: osamaz
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: osamaz
ms.openlocfilehash: 9a22e58e4407897fb9418cae0ba9f32408cda8e1
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234210"
---
# <a name="azure-for-network-engineers"></a>Azure para engenheiros de rede
Como engenheiro de rede convencional, lidou com ativos físicos tais como routers, interruptores, cabos, firewalls para construir infraestruturas. Numa camada lógica, configuraram a LAN virtual (VLAN), o Protocolo de Árvores De Abrangamento (STP), os protocolos de encaminhamento (RIP, OSPF, BGP). Geriu a sua rede utilizando ferramentas de gestão e CLI. A ligação em rede na nuvem é diferente onde os pontos finais da rede são lógicos e a utilização de protocolos de encaminhamento é mínima. Trabalhará com a Azure Resource Manager API, Azure CLI e PowerShell para configurar e gerir ativos em Azure. Você começará sua jornada de rede na nuvem, compreendendo inquilinos básicos da rede Azure. 
## <a name="virtual-network"></a>Rede virtual
Quando desenhamos uma rede de baixo para cima, recolhemos algumas informações básicas. Estas informações podem ser um número de anfitriões, dispositivos de rede, número de sub-redes, encaminhamento entre sub-redes, domínios de isolamento, tais como VLANs. Esta informação ajuda a dimensionar a rede e dispositivos de segurança, bem como a criação da arquitetura para apoiar aplicações e serviços.

Quando planeia implementar as suas aplicações e serviços em Azure, começará por criar uma fronteira lógica em Azure, que se chama rede virtual. Esta rede virtual é semelhante a um limite de rede física. Como é uma rede virtual, não precisa de equipamento físico, mas ainda tem de planear para as entidades lógicas, tais como endereços IP, sub-redes IP, encaminhamento e políticas.

Quando cria uma rede virtual em Azure, é pré-configurada com uma gama IP (10.0.0.0/16). Esta gama não é fixa, pode definir o seu próprio alcance IP. Pode definir os intervalos de endereços IPv4 e IPv6. As gamas IP definidas para a rede virtual não são publicitadas na Internet. Pode criar várias sub-redes a partir do seu intervalo de IP. Estas sub-redes serão utilizadas para atribuir endereços IP a interfaces de rede virtuais (vNICs). Os primeiros quatro endereços IP de cada sub-rede são reservados e não podem ser utilizados para alocação de IP. Não há nenhum conceito de VLANs numa nuvem pública. No entanto, pode criar isolamento dentro de uma rede virtual com base nas suas sub-redes definidas.

Pode criar uma grande sub-rede que engloba todo o espaço de endereço de rede virtual ou optar por criar várias sub-redes. No entanto, se estiver a utilizar um gateway de rede virtual, o Azure requer que crie uma sub-rede com o nome "gateway subnet". O Azure utilizará esta sub-rede para atribuir endereços IP a gateways de rede virtuais. 

## <a name="ip-allocation"></a>Atribuição de IP

Quando atribui um endereço IP a um anfitrião, atribui IP a um cartão de interface de rede (NIC). Pode atribuir dois tipos de endereços IP a um NIC em Azure:

- Endereços IP públicos - Usado para comunicar entrada e saída (sem tradução de endereço de rede (NAT)) com a Internet e outros recursos Azure não ligados a uma rede virtual. A atribuição de endereços IP públicos a uma NIC é opcional. Os endereços IP públicos pertencem ao espaço de endereços IP da Microsoft.
- Endereços IP privados - Utilizados para comunicação dentro de uma rede virtual, a sua rede no local e a Internet (com NAT). O espaço de endereço IP que define na rede virtual é considerado privado mesmo que configuure o seu espaço de endereço IP público. A Microsoft não publicita este espaço para a Internet. Tem de atribuir, pelo menos, um endereço IP privado a uma VM.

Tal como acontece com os anfitriões ou dispositivos físicos, existem duas formas de alocar um endereço IP a um recurso - dinâmico ou estático. No Azure, o método de atribuição predefinido é dinâmico, quando um endereço IP é atribuído quando cria uma máquina virtual (VM) ou inicia um VM parado. O endereço IP é libertado quando para ou elimina a VM. Para garantir que o endereço IP da VM fica o mesmo, pode definir o método de alocação explicitamente como estático. Neste caso, é atribuído imediatamente um endereço IP. Só é libertado quando elimina a VM ou altera o método de alocação para dinâmico. 

Os endereços IP privados são atribuídos a partir das sub-redes definidas numa rede virtual. Para um VM, você escolhe uma sub-rede para a atribuição de IP. Se um VM contiver vários NICs, pode escolher uma sub-rede diferente para cada NIC.

## <a name="routing"></a>Encaminhamento
Ao criar uma rede virtual, o Azure cria uma tabela de encaminhamento para a sua rede. Esta tabela de encaminhamento contém seguintes tipos de rotas.
- Rotas de sistema
- Rotas predefinidos da sub-rede
- Rotas de outras redes virtuais
- Rotas BGP
- Rotas de ponto final de serviço
- Rotas definidas pelo utilizador (UDR)

Quando cria uma rede virtual pela primeira vez sem definir quaisquer sub-redes, o Azure cria entradas de encaminhamento na tabela de encaminhamento. Estas rotas são chamadas rotas do sistema. As rotas do sistema são definidas neste local. Não pode modificar estas rotas. No entanto, é possível anular as rotas dos sistemas configurando as UDRs.

Quando cria uma ou várias sub-redes dentro de uma rede virtual, o Azure cria entradas predefinidas na tabela de encaminhamento para permitir a comunicação entre estas sub-redes dentro de uma rede virtual. Estas rotas podem ser modificadas utilizando rotas estáticas, que são Rotas Definidas pelo Utilizador (UDR) em Azure.

Quando cria uma rede virtual que espreita entre duas redes virtuais, é adicionada uma rota para cada intervalo de endereços dentro do espaço de endereço de cada rede virtual para a qual é criado um espreitante.

Se a sua rede de gateways no local trocar rotas de protocolo de gateway de fronteira (BGP) com um gateway de rede virtual Azure, é adicionada uma rota para cada rota propagada a partir do gateway de rede no local. Estas rotas aparecem na tabela de encaminhamento como rotas BGP.

Os endereços IP públicos para determinados serviços são adicionados à tabela de rotas pela Azure quando ativar um ponto final de serviço para o serviço. Os pontos finais de serviço são ativados para sub-redes individuais dentro de uma rede virtual. Quando ativa um ponto final de serviço, a rota só é adicionada à tabela de rotas da sub-rede que pertence a este serviço. O Azure gere os endereços na tabela de rotas automaticamente quando os mesmos são modificados.

As rotas definidas pelo utilizador também são chamadas de rotas personalizadas. Cria uDR em Azure para anular as rotas do sistema padrão da Azure ou para adicionar rotas adicionais à tabela de rotas de uma sub-rede. Em Azure, você cria uma tabela de rotas e, em seguida, associa a tabela de rotas a sub-redes de rede virtual.

Quando tem entradas concorrentes numa tabela de roteamento, o Azure seleciona o próximo salto com base no prefixo mais longo semelhante aos routers tradicionais. No entanto, se houver várias próximas entradas de lúpulo com o mesmo prefixo de endereço, então a Azure seleciona as rotas seguindo a ordem.
1. Rotas definidas pelo utilizador (UDR)
1. Rotas BGP
1. Rotas de sistema

## <a name="security"></a>Segurança

Pode filtrar o tráfego de rede de e para recursos numa rede virtual utilizando grupos de segurança de rede. Também pode utilizar aparelhos virtuais de rede (NVA) como a Azure Firewall ou firewalls de outros fornecedores. Pode controlar como a Azure encaminha o tráfego a partir de sub-redes. Também pode limitar quem na sua organização pode trabalhar com recursos em redes virtuais.

Os grupo de segurança de rede (NSG) contêm uma lista de regras da Lista de Controlo de Acesso (ACL) que permitem ou negam o tráfego de rede para sub-redes, NICs ou ambas. Os NSGs podem ser associados a sub-redes ou a NICs individuais ligadas a sub-redes. Quando um NSG é associado a uma sub-rede, as regras da ACL são aplicadas a todas as VM nessa sub-rede. Além disso, o tráfego para uma NIC individual pode ser restringido ao associar um NSG diretamente à NIC.

Os NSGs contêm dois conjuntos de regras: de entrada e de saída. A prioridade para uma regra tem de ser exclusiva dentro de cada conjunto. Cada regra tem propriedades de protocolo, de intervalos de portas de origem e destino, de prefixos de endereços, de direção do tráfego, de prioridade e de tipo de acesso. Todos os NSGs contêm um conjunto de regras predefinidas. As regras predefinidas não podem ser eliminadas, mas como lhes é atribuída a prioridade mais baixa, podem ser substituídas pelas regras que criar.

## <a name="verification"></a>Verificação
### <a name="routes-in-virtual-network"></a>Rotas em rede virtual
A combinação de rotas que cria, as rotas padrão do Azure e quaisquer rotas propagadas a partir da sua rede no local através de um gateway Azure VPN (se a sua rede virtual estiver ligada à sua rede no local) através do protocolo gateway fronteiriço (BGP), são as rotas eficazes para todas as interfaces de rede numa sub-rede. Você pode ver estas rotas eficazes navegando para NIC via Portal, PowerShell ou CLI.
### <a name="network-security-groups"></a>Grupos de Segurança de Rede
As regras de segurança eficazes aplicadas a uma interface de rede são uma agregação das regras existentes no NSG associada a uma interface de rede, e a sub-rede em que a interface de rede está. Pode ver todas as regras de segurança eficazes dos NSGs que são aplicados nas interfaces de rede dos seus VM navegando para o NIC via Portal, PowerShell ou CLI.

## <a name="next-steps"></a>Passos seguintes

Conheça a [rede virtual.][VNet]

Saiba mais sobre [o encaminhamento de rede virtual][vnet-routing].

Conheça os [grupos de segurança][network-security]da rede.

<!--Link References-->
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[vnet-routing]: ../virtual-network/virtual-networks-udr-overview.md
[network-security]: ../virtual-network/network-security-groups-overview.md