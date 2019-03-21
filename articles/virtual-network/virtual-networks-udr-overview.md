---
title: Encaminhamento de tráfego de rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba como é que o Azure encaminha o tráfego da rede virtual e como pode personalizar o encaminhamento do Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2017
ms.author: jdial
ms.openlocfilehash: 90ca35ec899d71578a7da4061ca7842d13769072
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58123577"
---
# <a name="virtual-network-traffic-routing"></a>Encaminhamento de tráfego da rede virtual

Saiba como é que o Azure encaminha o tráfego entre recursos do Azure, do local e da Internet. O Azure cria automaticamente uma tabela de rotas para cada sub-rede dentro de uma rede virtual do Azure e adiciona as rotas predefinidas do sistema à tabela. Para saber mais sobre as redes virtuais e as sub-redes, veja [Descrição geral da rede virtual](virtual-networks-overview.md). Pode substituir algumas das rotas do sistema do Azure por [rotas personalizadas](#custom-routes) e adicionar rotas personalizadas adicionais às tabelas de rotas. O Azure encaminha o tráfego de saída de uma sub-rede com base nas rotas definidas na tabela de rotas dessa sub-rede.

## <a name="system-routes"></a>Rotas de sistema

O Azure cria automaticamente as rotas do sistema e atribui-as a cada sub-rede numa rede virtual. Não pode criar rotas do sistema nem pode removê-las, mas pode substituir algumas destas por [rotas personalizadas](#custom-routes). O Azure cria rotas do sistema predefinidas para cada sub-rede e adiciona [rotas predefinidas opcionais](#optional-default-routes) a sub-redes específicas ou a todas as sub-redes, quando utiliza capacidades do Azure específicas.

### <a name="default"></a>Predefinição

Cada rota contém um prefixo de endereço e o tipo de salto seguinte. Quando o tráfego que sai de uma sub-rede é enviado para um endereço IP dentro do prefixo do endereço de uma rota, a rota que contém o prefixo é aquela que o Azure vai utilizar. Saiba mais sobre [como o Azure seleciona uma rota](#how-azure-selects-a-route) quando várias rotas contêm os mesmos prefixos ou prefixos sobrepostos. Sempre que é criada uma rede virtual, o Azure cria automaticamente as rotas do sistema predefinidas seguintes para cada sub-rede na rede virtual:


|Origem |Prefixos de endereço                                        |Tipo de salto seguinte  |
|-------|---------                                               |---------      |
|Predefinição|Exclusivos da rede virtual                           |Rede virtual|
|Predefinição|0.0.0.0/0                                               |Internet       |
|Predefinição|10.0.0.0/8                                              |Nenhuma           |
|Predefinição|172.16.0.0/12                                           |Nenhuma           |
|Predefinição|192.168.0.0/16                                          |Nenhuma           |
|Predefinição|100.64.0.0/10                                           |Nenhuma           |

Os tipos de salto seguintes listados na tabela anterior representam a forma como o Azure encaminha o tráfego destinado ao prefixo de endereço listado. As explicações para os tipos de salto seguintes são as seguintes:

- **Rede virtual**: Encaminha o tráfego entre intervalos de endereços dentro do [espaço de endereços](manage-virtual-network.md#add-or-remove-an-address-range) de uma rede virtual. O Azure cria uma rota com um prefixo de endereço que corresponde a cada intervalo de endereços definido dentro do espaço de endereços de uma rede virtual. Se o espaço de endereços da rede virtual tiver vários intervalos de endereços definidos, o Azure cria uma rota individual para cada intervalo. O Azure utiliza as rotas criadas para cada intervalo de endereços para encaminhar automaticamente o tráfego entre as sub-redes. Não precisa de definir gateways para que o Azure encaminhe o tráfego entre sub-redes. Embora as redes virtuais contenham sub-redes e cada sub-rede tenha um intervalo de endereços definido, o Azure *não* cria rotas predefinidas para os intervalos de endereços das sub-redes, pois estes estão entro de um intervalo de endereços do espaço de endereços de uma rede virtual.

- **Internet**: Encaminha o tráfego especificado pelo prefixo de endereço para a Internet. A rota predefinida do sistema especifica o prefixo de endereço 0.0.0.0/0. Se não substituir as rotas predefinidas do Azure, o Azure encaminha o tráfego de qualquer endereço não especificado por um intervalo de endereços dentro de uma rede virtual para a Internet, com uma exceção. Se o endereço de destino for para um dos serviços do Azure, o Azure encaminha o tráfego diretamente para o serviço através da rede principal do Azure, em vez de o encaminhar para a Internet. O tráfego entre os serviços do Azure não atravessa a Internet, independentemente da região do Azure na qual a rede virtual existe ou da região em que uma instância do serviço Azure está implementada. Pode substituir a rota do sistema predefinida do Azure para o prefixo de endereço 0.0.0.0/0 por uma [rota personalizada](#custom-routes).

- **Nenhum**: O tráfego encaminhado para o **None** tipo de próximo salto é removido, em vez de encaminhado fora da sub-rede. O Azure cria automaticamente rotas predefinidas para os seguintes prefixos de endereço:
  - **10.0.0.0/8 172.16.0.0/12 e 192.168.0.0/16**: Reservado para utilização privada em RFC 1918.
  - **100.64.0.0/10**: Reservado em RFC 6598.

    Se atribuir um dos intervalos de endereços anteriores dentro do espaço de endereços de uma rede virtual, o Azure altera automaticamente o tipo de salto seguinte da rota de **Nenhum** para **Rede virtual**. Se atribuir um intervalo de endereços ao espaço de endereços de uma rede virtual que inclui um dos quatro prefixos de endereços reservados, mas não é igual a estes, o Azure remove a rota para o prefixo e adiciona uma rota ao prefixo de endereço que adicionou, tendo **Rede virtual** como o tipo de salto seguinte.

### <a name="optional-default-routes"></a>Rotas predefinidas opcionais

O Azure adiciona rotas de sistema predefinidas adicionais para diferentes capacidades do Azure, mas apenas se as ativar. Consoante a capacidade, o Azure adiciona as rotas predefinidas opcionais a sub-redes específicas ou a todas as sub-redes dentro da rede virtual. As rotas do sistema adicionais e os tipos de salto seguinte que o Azure pode adicionar se ativar as diferentes capacidades são:

|Origem                 |Prefixos de endereço                       |Tipo de salto seguinte|Sub-rede numa rede virtual à qual a rota é adicionada|
|-----                  |----                                   |---------                    |--------|
|Predefinição                |Recursos exclusivos para a rede virtual, por exemplo: 10.1.0.0/16|VNet peering                 |Todos|
|Gateway de rede virtual|Prefixos anunciados no local através do BGP ou configurados no gateway de rede local     |Gateway de rede virtual      |Todos|
|Predefinição                |Vários                               |VirtualNetworkServiceEndpoint|Apenas a sub-rede na qual está ativado um ponto final de serviço.|

- **Peering de rede virtual (VNet)**: Quando cria uma peering entre duas redes virtuais de rede virtual, é adicionada uma rota para cada intervalo de endereços dentro do espaço de endereço de cada rede virtual que é criado um peering para. Saiba mais sobre o [peering de rede virtual](virtual-network-peering-overview.md).  
- **Gateway de rede virtual**: Uma ou mais rotas com *gateway de rede Virtual* listado como o tipo de próximo salto são adicionados quando um gateway de rede virtual é adicionado a uma rede virtual. A origem também é *gateway de rede virtual*, porque o gateway adiciona as rotas à sub-rede. Se o seu gateway de rede no local trocar de rotas de protocolo [BGP](#border-gateway-protocol) (Border Gateway Protocol) com um gateway de rede virtual do Azure, é adicionada uma rota a cada rota propagada a partir do gateway de rede no local. Recomenda-se que resuma as rotas no local para os intervalos de endereços maiores possíveis, para que sejam propagadas para um gateway de rede virtual do Azure o menor número possível de rotas. Existem limites ao número de rotas que pode propagar para um gateway de rede virtual do Azure. Para obter mais detalhes, veja [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).
- **VirtualNetworkServiceEndpoint**: Os endereços IP públicos para determinados serviços são adicionados à tabela de rotas pelo Azure ao ativar a um ponto final de serviço para o serviço. Os pontos finais de serviço são ativados para sub-redes individuais dentro de uma rede virtual, pelo que a rota só é adicionada à tabela de rotas de uma sub-rede para a qual o ponto final esteja ativado. Os endereços IP públicos dos serviços do Azure mudam periodicamente. O Azure gere os endereços na tabela de rotas automaticamente quando os mesmos são modificados. Saiba mais sobre os [pontos finais de serviço da rede virtual](virtual-network-service-endpoints-overview.md) e os serviços para os quais pode criar pontos finais de serviço. 

> [!NOTE]
> Os tipos de salto seguinte **VNet peering** e **VirtualNetworkServiceEndpoint** só são adicionados a tabelas de rotas de sub-redes que estejam dentro das redes virtuais criadas através do modelo de implementação Azure Resource Manager. Os tipos de salto seguinte não são associados às tabelas de rotas que estejam associadas a sub-redes da rede virtual criada através do modelo de implementação clássica. Saiba mais sobre os [modelos de implementação](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure.

## <a name="custom-routes"></a>Rotas personalizadas

Pode criar rotas personalizadas mediante a criação de rotas [definidas pelo utilizador](#user-defined) ou da troca de rotas do [BGP](#border-gateway-protocol) (Border Gateway Protocol) entre o seu gateway de rede no local e um gateway de rede virtual do Azure. 
 
### <a name="user-defined"></a>Definidas pelo utilizador

Pode criar rotas personalizadas, ou definidas pelo utilizador, no Azure para substituir as rotas do sistema predefinidas do Azure ou para adicionar mais rotas à tabela de rotas de uma sub-rede. No Azure, crie uma tabela de rotas e associe-a a nenhuma ou a mais sub-redes da rede virtual. Cada sub-rede pode ter zero ou uma tabela de rotas associada a si. Para saber mais sobre o número máximo de rotas que pode adicionar a uma tabela de rotas e o número máximo de tabelas de rotas definidas pelo utilizador que pode criar por subscrição do Azure, veja [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Se criar uma tabela de rotas e a associar a uma sub-rede, as rota são combinadas ou substituem as rotas predefinidas que o Azure adiciona por predefinição a uma sub-rede.

Ao criar uma rota definida pelo utilizador, pode especificar os tipos de próximo salto abaixo:

- **Aplicação virtual**: Uma aplicação virtual é uma máquina virtual que normalmente executa uma aplicação de rede, como uma firewall. Para saber mais sobre diversas aplicações de rede virtual pré-configuradas que pode implementar numa rede virtual, veja o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Quando cria uma rota com o tipo de salto seguinte **aplicação virtual**, também tem de especificar um endereço IP de próximo salto. O endereço IP pode ser:

  - O [endereço IP privado](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) de uma interface de rede ligada a uma máquina virtual. Qualquer interface de rede ligada a uma máquina virtual que reencaminhe o tráfego de rede para um endereço que não o da mesma tem de ter a opção do Azure *Ativar reencaminhamento de IP* ativada. A definição desativa a verificação por parte do Azure da origem e do destino de uma interface de rede. Saiba mais sobre como [ativar o reencaminhamento de IP em interfaces de rede](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Embora *Ativar o reencaminhamento de IPs* seja uma definição do Azure, também poderá ser necessário ativar o reencaminhamento de IP dentro do sistema operativo da máquina virtual, de modo a que a aplicação reencaminhe o tráfego entre as interfaces de rede dos endereços IP privados atribuídos ao Azure. Se a aplicação tem de encaminhar o tráfego para um endereço IP público, tem de suportar o tráfego com proxy, ou traduzir com um endereço de rede o endereço IP privado do endereço IP privado da origem para o seu próprio endereço IP privado, o qual o Azure, em seguida, traduz com um endereço de rede para um endereço IP público, antes de enviar o tráfego para a Internet. Para determinar as definições necessárias na máquina virtual, veja a documentação relativa ao seu sistema operativo ou à sua aplicação de rede. Para compreender as ligações de saída no Azure, veja [Compreender as ligações de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

    > [!NOTE]
    > Implemente uma aplicação virtual numa sub-rede diferente daquela em que estão implementados os recursos que são encaminhados através da mesma. Implementar a aplicação virtual na mesma sub-rede e, depois, aplicar uma tabela de rotas à sub-rede que encaminha o tráfego através dessa aplicação virtual, pode resultar em ciclos de encaminhamento, nos quais o tráfego nunca sai da sub-rede.

  - O endereço IP privado de um [Balanceador de carga interno](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure. Muitas vezes, são utilizados balanceadores de carga como parte de uma [estratégia de elevada disponibilidade para aplicações de redes virtuais](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).

    Pode definir uma rota que tem 0.0.0.0/0 como o prefixo de endereço e um tipo de próximo salto de aplicação virtual, permitindo que a aplicação inspecione o tráfego e determine se este deve ser encaminhado ou ignorado. Se quiser criar uma rota definida pelo utilizador que contenha o prefixo de endereço 0.0.0.0/0, leia [prefixo de endereço 0.0.0.0/0](#default-route) primeiro.

- **Gateway de rede virtual**: Especifica quando pretende que o tráfego destinado a prefixos de endereço específico encaminhados para um gateway de rede virtual. O gateway de rede virtual tem de ser criado com o tipo **VPN**. Não é possível especificar um gateway de rede virtual criado como o tipo **ExpressRoute** numa rota definida pelo utilizador porque o com o ExpressRoute, tem de utilizar o BGP para rotas personalizadas. Pode definir uma rota que direciona o tráfego destinado ao prefixo de endereço 0.0.0.0/0 para um gateway de rede virtual [baseado numa rota](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#vpntype). No local, poderá ter um dispositivo que inspeciona o tráfego e determina se este deve ser reencaminhado ou ignorado. Se quiser criar uma rota definida pelo utilizador para o prefixo de endereço 0.0.0.0/0, leia [prefixo de endereço 0.0.0.0/0](#default-route) primeiro. Em vez de configurar uma rota definida pelo utilizador para o prefixo de endereço 0.0.0.0/0, pode anunciar uma rota com o prefixo 0.0.0.0/0 através do BGP, se tiver [ativado o BGP num gateway de rede virtual VPN](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- **Nenhum**: Especifica quando deseja que a diminuir o tráfego para um prefixo de endereço, em vez de reencaminhar o tráfego para um destino. Se ainda não tiver configurado uma capacidade, o Azure poderá apresentar *Nenhum* em algumas das rotas do sistema opcionais. Por exemplo, se vir *Nenhum* apresentado como o **Endereço IP do próximo salto** com o **Tipo de próximo salto** de *Gateway de rede virtual* ou *Aplicação Virtual*, poderá dever-se ao facto de o dispositivo não estar em execução ou totalmente configurado. O Azure cria [rotas do sistema predefinidas](#default) para prefixos de endereço reservado com **Nenhum** como o tipo de próximo salto.
- **Rede virtual**: Especifica quando pretende substituir o encaminhamento predefinido dentro de uma rede virtual. Veja [Exemplo de encaminhamento](#routing-example) para obter um exemplo de como pode ser útil criar uma rota com o tipo de salto **Rede virtual**.
- **Internet**: Especifica quando quiser encaminhar explicitamente o tráfego destinado a um prefixo de endereço para a Internet ou se pretender que o tráfego destinado aos serviços do Azure com endereços IP públicos mantidas ao nível da rede principal do Azure.

Não pode especificar **VNet peering** nem **VirtualNetworkServiceEndpoint** como o tipo de próximo salto em rotas definidas pelo utilizador. As rotas que tenham o tipo de próximo salto **VNet peering** ou **VirtualNetworkServiceEndpoint** só são criadas pelo Azure, quando configura um peering de rede virtual ou um ponto final de serviço.

**Tipos de próximo salto transversais às ferramentas do Azure**

O nome apresentado e referenciado para os tipos de próximo salto são diferentes no portal do Azure e nas ferramentas de linha de comandos e nos modelos de implementação do Azure Resource Manager e clássica. A tabela seguinte mostra os nomes utilizados para fazer referência a cada tipo de próximo salto com as diferentes ferramentas e [modelos de implementação](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json):

|Tipo de salto seguinte                   |CLI do Azure e PowerShell (Resource Manager) |CLI clássica do Azure e PowerShell (clássico)|
|-------------                   |---------                                       |-----|
|Gateway de rede virtual         |VirtualNetworkGateway                           |VPNGateway|
|Rede virtual                 |VNetLocal                                       |VNETLocal (não disponível na CLI clássica no modo asm)|
|Internet                        |Internet                                        |Internet (não disponível na CLI clássica no modo asm)|
|Aplicação virtual               |VirtualAppliance                                |VirtualAppliance|
|Nenhuma                            |Nenhuma                                            |Null (não disponível na CLI clássica no modo asm)|
|Peering de rede virtual         |VNet peering                                    |Não aplicável|
|Ponto final do serviço de rede virtual|VirtualNetworkServiceEndpoint                   |Não aplicável|

### <a name="border-gateway-protocol"></a>BGP (Border Gateway Protocol)

Os gateways de rede no local podem utilizar o BGP (Border Gateway Protocol) para trocar de rotas com gateways de rede virtual do Azure. A utilização do BGP com um gateway de rede virtual do Azure depende do tipo que selecionou quando criou o gateway. Se tiver selecionado o tipo:

- **ExpressRoute**: Tem de utilizar o BGP para anunciar rotas no local, o router de periferia da Microsoft. Não é possível criar rotas definidas pelo utilizador para forçar o tráfego para o gateway de rede virtual do ExpressRoute, se implementar um gateway de rede virtual implementado com o tipo: ExpressRoute. Pode utilizar rotas definidas pelo utilizador para forçar o tráfego do Express Route para, por exemplo, um Dispositivo Virtual de Rede.
- **VPN**: Opcionalmente, pode utilizar o BGP. Para obter detalhes, veja [BGP com ligações VPN de site a site](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Quando utiliza o BGP para trocar rotas com o Azure, é adicionada uma rota separada à tabela de rotas de todas as sub-redes numa rede virtual para cada prefixo anunciado. A rota é adicionada com *Gateway de rede virtual* listado como a origem e o tipo de próximo salto. 

ER e propagação de rotas do Gateway de VPN podem ser desativadas numa sub-rede através de uma propriedade numa tabela de rotas. Quando trocar as rotas com o Azure com o BGP, as rotas não são adicionadas à tabela de rotas de todas as sub-redes com a propagação do BGP desativada. A conectividade com ligações VPN é conseguida através de [rotas personalizadas](#custom-routes) com um tipo de salto seguinte de *Gateway de rede virtual*. Para obter detalhes, veja [Como desativar a propagação de rotas do BGP](manage-route-table.md#create-a-route-table).

## <a name="how-azure-selects-a-route"></a>Como o Azure seleciona uma rota

Quando o tráfego de saída é enviado a partir de uma sub-rede, o Azure seleciona uma rota com base no endereço IP de destino, através da utilização do algoritmo de correspondência de prefixo mais longo. Por exemplo, uma tabela de rotas tem duas rotas: Uma rota especifica o prefixo de endereço 10.0.0.0/24, enquanto a outra Especifica o prefixo de endereço 10.0.0.0/16. O Azure encaminha o tráfego destinado a 10.0.0.5, para o tipo de próximo salto especificado na rota com o prefixo de endereço 10.0.0.0/24, porque 10.0.0.0/24 é um prefixo mais longo do que 10.0.0.0/16, apesar de 10.0.0.5 estar dentro de ambos os prefixos de endereço. O Azure encaminha o tráfego destino a 10.0.1.5, para o tipo de próximo salto especificado na rota com o prefixo de endereço 10.0.0.0/16, porque 10.0.1.5n não está incluído no prefixo de endereço 10.0.0.0/24, pelo que a rota com o prefixo de endereço 10.0.0.0/16 é a que tem o prefixo mais longo que tem correspondência.

Se várias rotas tiverem o mesmo prefixo de endereço, o Azure seleciona o tipo de rota com base na prioridade seguinte:

1. Rota definida pelo utilizador
2. Rota BGP
3. Rota de sistema

> [!NOTE]
> As rotas de sistema para o tráfego de rede virtual, peerings de rede virtual ou pontos finais do serviço de rede virtual são rotas preferenciais, mesmo que as rotas BGP sejam mais específicas.

Por exemplo, uma tabela de rotas contém as rotas seguintes:


|Origem   |Prefixos de endereço  |Tipo de salto seguinte           |
|---------|---------         |-------                 |
|Predefinição  | 0.0.0.0/0        |Internet                |
|Utilizador     | 0.0.0.0/0        |Gateway de rede virtual |

Quando o tráfego se destina a um endereço IP fora dos prefixos de endereço de outras rotas na tabela de rotas, o Azure seleciona a rota com a origem **Utilizador**, porque as rotas definidas pelo utilizador têm prioridade mais alta do que as rotas do sistema predefinidas.

Veja [Exemplo de encaminhamento](#routing-example) para obter uma tabela de encaminhamento completa com uma explicação das rotas na mesma.

## <a name="default-route"></a>Prefixo de endereço 0.0.0.0/0

Um rota que tenha o prefixo de endereço 0.0.0.0/0 diz ao Azure como encaminhar o tráfego destinado a um endereço IP que está fora do prefixo de endereço de outra rota qualquer na tabela de rotas de uma sub-rede. Quando é criada uma sub-rede, o Azure cria uma [rota](#default) predefinida para o prefixo de endereço 0.0.0.0/0, com o tipo de próximo seguinte **Internet**. Se não substituir esta rota, o Azure encaminha para a Internet todo o tráfego destinado aos endereços IP não incluídos no prefixo de endereço de nenhuma outra rota. A exceção é que o tráfego para os endereços IP públicos dos serviços do Azure permanece na rede principal do Azure e não é encaminhado para a Internet. Se substituir esta rota por uma rota [personalizada](#custom-routes), o tráfego destinado aos endereços fora dos prefixos de endereço de outra rede qualquer na tabela de rotas é enviado para uma aplicação de rede virtual ou para um gateway de rede virtual, dependendo do que especificar nessa rota personalizada.

Quando substitui o prefixo de endereço 0.0.0.0/0, para além do tráfego de saída da sub-rede fluir através do gateway de rede virtual ou da aplicação virtual, ocorrem as seguintes alterações ao encaminhamento predefinido do Azure: 

- O Azure envia o tráfego ao tipo de próximo salto especificado na rota, incluindo o tráfego destinado aos endereços IP públicos dos serviços do Azure. Se o tipo de próximo salto da rota com o prefixo de endereço 0.0.0.0/0 for **Internet**, o tráfego da sub-rede destinado aos endereços IP públicos dos serviços do Azure nunca sai da rede principal do Azure, independentemente da região do Azure em que a rede virtual ou o recurso do serviço do Azure reside. Contudo, se criar uma rota definida pelo utilizador ou do BGP com o tipo de próximo salto **gateway de rede virtual** ou **aplicação virtual**, todo o tráfego, incluindo o tráfego enviado para os endereços IP públicos dos serviços do Azure para os quais não ativou os [pontos finais de serviço](virtual-network-service-endpoints-overview.md), é enviado para o tipo de próximo salto especificado na rota. Se tiver ativado um ponto final de serviço para um serviço, o tráfego para o mesmo não é encaminhado para o tipo de próximo salto numa rota com o prefixo de endereço 0.0.0.0/0, porque os prefixos de endereço do serviço são especificados na rota que o Azure cria quando é ativado o ponto final do serviço e porque são mais longos do que 0.0.0.0/0.
- Deixa de poder aceder diretamente a recursos na sub-rede a partir da Internet. Pode aceder indiretamente aos recursos na sub-rede a partir da Internet se o tráfego de entrada for transmitido para o dispositivo especificado pelo tipo de próximo salto de uma rota com o prefixo de endereço 0.0.0.0/0 antes de chegar ao recurso da rede virtual. Se a rota contiver os valores seguintes para o tipo de próximo salto:
    - **Aplicação virtual**: A aplicação tem de:
        - Ser acessível a partir da Internet
        - Ter um endereço IP público atribuído à mesma
        - Não ter uma regra de grupo de segurança de rede associada à mesma que impeça a comunicação com o dispositivo
        - Não negar a comunicação
        - Ser capaz de utilizar a tradução e o reencaminhamento de endereços de rede ou o proxy de tráfego no recurso de destino na sub-rede, bem como de devolvê-lo à Internet. 
    - **Gateway de rede virtual**: Se o gateway for um gateway de rede virtual, um dispositivo ligado à Internet no local pode traduzir de endereço de rede do ExpressRoute e a partir de agora ou o proxy de tráfego para o recurso de destino na sub-rede, através do ExpressRoute [peering privado ](../expressroute/expressroute-circuit-peerings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#privatepeering). 

Se a rede virtual esiver ligada a um gateway VPN do Azure, não associe nenhuma tabela de rota à [sub-rede do gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) que incluia uma rota com um destino 0.0.0.0/0. Se o fizer, poderá impedir que o gateway funcione corretamente. Para obter detalhes, veja a questão *Por que motivo determinadas portas são abertas no meu gateway de VPN?* nas [FAQ do Gateway de VPN](../vpn-gateway/vpn-gateway-vpn-faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gatewayports).

Veja [DMZ between Azure and your on-premises datacenter](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid?toc=%2fazure%2fvirtual-network%2ftoc.json) (DMZ entre o Azure e o seu datacenter no local) e [DMZ between Azure and the Internet](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2fazure%2fvirtual-network%2ftoc.json) (DMZ entre o Azure e a Internet) para obter detalhes da implementação ao utilizar gateways de rede virtual e aplicações virtuais entre a Internet e o Azure.

## <a name="routing-example"></a>Exemplo de encaminhamento

Para ilustrar os conceitos deste artigo, as secções que se seguem descrevem:
- Um cenário, com os requisitos
- As rotas personalizadas necessárias para cumprir os requisitos
- A tabela de rotas que existe para uma sub-rede e que inclui as rotas predefinidas e personalizadas necessárias para cumprir os requisitos

> [!NOTE]
> Este exemplo não tem como objetivo ser uma prática recomendada ou uma melhor prática de implementação. É disponibilidade para ilustrar os conceitos deste artigo.

### <a name="requirements"></a>Requisitos

1. Implemente duas redes virtuais na mesma região do Azure e ative os recursos para comunicarem entre as redes virtuais.
2. Ative uma rede no local para comunicar em segurança com ambas as redes virtuais através de um túnel de VPN na Internet. *Em alternativa, pode ser utilizada uma ligação do ExpressRoute, mas neste exemplo é utilizada uma ligação VPN.*
3. Para uma sub-rede numa rede virtual:
 
    - Force todo o tráfego de saída da sub-rede, exceto para o Armazenamento do Azure e dentro da sub-rede, para fluir através de uma aplicação de rede virtual, para fins de inspeção e registo.
    - Não inspecione o tráfego entre os endereços IP privados dentro da sub-rede; permita que o tráfego flua diretamente entre todos os recursos. 
    - Ignore todo o tráfego de saída destinado à outra rede virtual.
    - Permita que o tráfego de saída para o Armazenamento do Azure flua diretamente para o armazenamento sem forçá-lo através de uma aplicação virtual de rede.

4. Permita todo o tráfego entre todas as outras sub-redes e redes virtuais.

### <a name="implementation"></a>Implementação

A imagem abaixo mostra uma implementação com o modelo de implementação do Azure Resource Manager que cumpre os requisitos anteriores:

![Diagrama de rede](./media/virtual-networks-udr-overview/routing-example.png)

As setas mostram o fluxo do tráfego. 

### <a name="route-tables"></a>Tabelas de rotas

#### <a name="subnet1"></a>Subnet1

A tabela de rotas de *Subnet1* na imagem contém as rotas seguintes:

|ID  |Origem |Estado  |Prefixos de endereço    |Tipo de salto seguinte          |Endereço IP do próximo salto|Nome da rota definida pelo utilizador| 
|----|-------|-------|------              |-------                |--------           |--------      |
|1   |Predefinição|Inválido|10.0.0.0/16         |Rede virtual        |                   |              |
|2   |Utilizador   |Ativa |10.0.0.0/16         |Aplicação virtual      |10.0.100.4         |Within-VNet1  |
|3   |Utilizador   |Ativa |10.0.0.0/24         |Rede virtual        |                   |Within-Subnet1|
|4   |Predefinição|Inválido|10.1.0.0/16         |VNet peering           |                   |              |
|5   |Predefinição|Inválido|10.2.0.0/16         |VNet peering           |                   |              |
|6   |Utilizador   |Ativa |10.1.0.0/16         |Nenhuma                   |                   |ToVNet2-1-Drop|
|7   |Utilizador   |Ativa |10.2.0.0/16         |Nenhuma                   |                   |ToVNet2-2-Drop|
|8   |Predefinição|Inválido|10.10.0.0/16        |Gateway de rede virtual|[X.X.X.X]          |              |
|9   |Utilizador   |Ativa |10.10.0.0/16        |Aplicação virtual      |10.0.100.4         |To-On-Prem    |
|10  |Predefinição|Ativa |[X.X.X.X]           |VirtualNetworkServiceEndpoint    |         |              |
|11  |Predefinição|Inválido|0.0.0.0/0           |Internet               |                   |              |
|12  |Utilizador   |Ativa |0.0.0.0/0           |Aplicação virtual      |10.0.100.4         |Default-NVA   |

Segue-se uma explicação de cada ID de rota:

1. O Azure adicionou automaticamente esta rota a todas as sub-redes em *Virtual-network-1*, porque 10.0.0.0/16 é o único intervalo de endereços definido no espaço de endereços da rede virtual. Se a rota definida pelo utilizador na rota ID2 não tivesse sido criada, o tráfego enviado para qualquer endereço entre 10.0.0.1 e 10.0.255.254 seria encaminhado dentro da rede virtual, porque o prefixo é mais longo do que 0.0.0.0/0 e não está dentro dos prefixos de endereço de nenhuma outra rota. O Azure alterou automaticamente o estado de *Ativa* para *Inválida* quando ID2, uma rota definida pelo utilizador, foi adicionada, uma vez que tem o mesmo prefixo da rota predefinida e as rotas definidas pelo utilizador substituem as predefinidas. O estado desta rota ainda é *Ativa* em *Subnet2*, porque a tabela de rotas em que a rota definida pelo utilizador ID2 se encontra não está associada a *Subnet2*.
2. O Azure adicionou esta rota quando foi associada uma rota definida pelo utilizador para o prefixo de endereço 10.0.0.0/16 à sub-rede *Subnet1* na rede virtual *Virtual-network-1*. A rota definida pelo utilizador especifica 10.0.100.4 como o endereço IP da aplicação virtual, porque este é o endereço IP privado atribuído à máquina virtual da aplicação virtual. A tabela de rotas na qual esta rota está incluída não está associada a *Subnet2*, pelo que não aparece na tabela de rotas para *Subnet2*. Esta rota substitui a rota predefinida para o prefixo 10.0.0.0/16 (ID1), que encaminhou automaticamente o tráfego dirigido a 10.0.0.1 e 10.0.255.254 dentro da rede virtual através do tipo de próximo salto da rede virtual. Esta rota existe para satisfazer o [requisito](#requirements) 3, para forçar todo o tráfego de saída através de uma aplicação virtual.
3. O Azure adicionou esta rota quando foi associada uma rota definida pelo utilizador para o prefixo de endereço 10.0.0.0/24 à sub-rede *Subnet1*. O tráfego destinado a endereços entre 10.0.0.1 e 10.0.0.254 permanece dentro da sub-rede, em vez de ser encaminhado para a aplicação virtual especificada na regra anterior (ID2), porque tem um prefixo mais longo do que a rota ID2. Esta rota não foi associada a *Subnet2*, pelo que não aparece na tabela de rotas para *Subnet2*. Esta rota substitui eficazmente a rota ID2 para o tráfego em *Subnet1*. Esta rota existe para satisfazer o [requisito](#requirements) 3.
4. O Azure adicionou automaticamente as rotas nos IDs 4 e 5 a todas as sub-redes em *Virtual-network-1*, quando foi feito o peering da rede virtual com *Virtual-network-2.* *Virtual-network-2* tem dois intervalos de endereços no seu espaço de endereços: 10.1.0.0/16 e 10.2.0.0/16, para que o Azure adicionou uma rota para cada intervalo. Se as rotas definidas pelo utilizador nas rotas IDs 6 e 7 não tivessem sido criadas, o tráfego enviado para qualquer endereço entre 10.1.0.1-10.1.255.254 e 10.2.0.1-10.2.255.254 seria encaminhado para a rede virtual em modo de peering, porque o prefixo é mais longo do que 0.0.0.0/0 e não está dentro dos prefixos de endereço de nenhuma outra rota. O Azure alterou automaticamente o estado de *Ativa* para *Inválida* quando as rotas nos IDs 6 e 7 foram adicionadas, uma vez que tem os mesmos prefixos das rotas nos IDs 4 e 5 e as rotas definidas pelo utilizador substituem as predefinidas. O estado das rotas nos IDs 4 e 5 ainda têm o estado *Ativa* para *Subnet2*, porque a tabela de rotas na qual as rotas definidas pelo utilizador nestes IDs se encontram não está associada a *Subnet2*. Foi criado um peering de rede virtual para cumprir o [requisito](#requirements) 1.
5. Explicação igual à do ID4.
6. O Azure adicionou esta rota e a rota no ID7 quando as rotas definidas pelo utilizador para os prefixos de endereço 10.1.0.0/16 e 10.2.0.0/16 foram associadas à sub-rede *Subnet1*. O Azure ignora o tráfego destinado a endereços entre 10.1.0.1-10.1.255.254 e 10.2.0.1-10.2.255.254, que não é encaminhado para a rede virtual em modo de peering, porque as rotas definidas pelo utilizador substituem as rotas predefinidas. As rotas não foram associadas a *Subnet2*, pelo que não aparecem na tabela de rotas para *Subnet2*. As rotas substituem as rotas com os ID4 e ID5 para o tráfego que sai de *Subnet1*. As rotas com os ID6 e ID7 existem para satisfazer o [requisito](#requirements) 3 para ignorar o tráfego destinado à outra rede virtual.
7. Explicação igual à do ID6.
8. O Azure adicionou automaticamente esta rota a todas as sub-redes em *Virtual-network-1*, quando foi criado um gateway de rede virtual do tipo VPN dentro da rede virtual. O Azure adicionou o endereço IP público do gateway de rede virtual à tabela de rotas. O tráfego enviado para qualquer endereço entre 10.10.0.1 e 10.10.255.254 é encaminhado para o gateway de rede virtual. O prefixo é mais longo do que 0.0.0.0/0 e não está dentro dos prefixos de endereço de nenhuma das outras rotas. Foi criado um gateway de rede virtual para cumprir o [requisito](#requirements) 2.
9. O Azure adicionou esta rota quando foi adicionada uma rota definida pelo utilizador para o prefixo de endereço 10.10.0.0/16 à tabela de rotas associada a *Subnet1*. Esta rota substitui o ID8. A rota envia todo o tráfego destinado À rede no local para um NVA para inspeção, em vez de o encaminhar diretamente para o local. Esta rota foi criada para satisfazer o [requisito](#requirements) 3.
10. O Azure adicionou automaticamente esta rota à sub-rede quando foi ativado um ponto final de serviço para um serviço do Azure na sub-rede. O Azure encaminha o tráfego da sub-rede para um endereço IP público do serviço através da rede da infraestrutura do Azure. O prefixo é mais longo do que 0.0.0.0/0 e não está dentro dos prefixos de endereço de nenhuma das outras rotas. Foi criado um ponto final do serviço para cumprir o [requisito](#requirements) 3, de modo a permitir que o tráfego destinado ao Armazenamento do Azure flua diretamente para o mesmo.
11. O Azure adicionou automaticamente esta rota à rota de tabelas de todas as sub-redes em *Virtual-network-1* e em *Virtual-network-2.* O prefixo de endereço 0.0.0.0/0 é o prefixo mais curto. Qualquer tráfego enviado para endereços dentro de prefixos mais longos é encaminhado com base noutras rotas. Por predefinição, o Azure encaminha para a Internet todo o tráfego destinado a endereços que não os especificados numa das outras rotas. O Azure alterou automaticamente o estado de *Ativa* para *Inválida* na sub-rede *Subnet1* quando foi adicionada uma rota definida pelo utilizador para o prefixo de endereços 0.0.0.0/0 (ID12) à sub-rede. O estado desta rota ainda é *Ativa* em todas as outras sub-redes dentro de ambas as redes virtuais, porque a rota não está associada a nenhuma outra sub-rede dentro de outras redes virtuais.
12. O Azure adicionou esta rota quando foi associada uma rota definida pelo utilizador para o prefixo de endereço 0.0.0.0/0 à sub-rede *Subnet1*. A rota definida pelo utilizador especifica 10.0.100.4 como o endereço IP da aplicação virtual. Esta rota não está associada a *Subnet2*, pelo que não aparece na tabela de rotas para *Subnet2*. Todo o tráfego destinado a qualquer endereço que não esteja incluído nos prefixos de endereço de uma das outras rotas é enviado para a aplicação virtual. Adicionar esta rota fez com que o estado da rota predefinida para o prefixo de endereço 0.0.0.0/0 (ID11) passasse de *Ativa* para *Inválida* em *Subnet1*, porque as rotas definidas pelo utilizador substituem as rotas predefinidas. Esta rota existe para satisfazer o [requisito](#requirements) 3.

#### <a name="subnet2"></a>Subnet2

A tabela de rotas de *Subnet2* na imagem contém as rotas seguintes:

|Origem  |Estado  |Prefixos de endereço    |Tipo de salto seguinte             |Endereço IP do próximo salto|
|------- |-------|------              |-------                   |--------           
|Predefinição |Ativa |10.0.0.0/16         |Rede virtual           |                   |
|Predefinição |Ativa |10.1.0.0/16         |VNet peering              |                   |
|Predefinição |Ativa |10.2.0.0/16         |VNet peering              |                   |
|Predefinição |Ativa |10.10.0.0/16        |Gateway de rede virtual   |[X.X.X.X]          |
|Predefinição |Ativa |0.0.0.0/0           |Internet                  |                   |
|Predefinição |Ativa |10.0.0.0/8          |Nenhuma                      |                   |
|Predefinição |Ativa |100.64.0.0/10       |Nenhuma                      |                   |
|Predefinição |Ativa |172.16.0.0/12       |Nenhuma                      |                   |
|Predefinição |Ativa |192.168.0.0/16      |Nenhuma                      |                   |

A tabela de rotas para *Subnet2* contém todas as rotas predefinidas criadas pelo Azure e as rotas opcionais de peering de VNet e de gateway de rede virtual. O Azure adicionou as rotas opcionais a todas as sub-redes na rede virtual quando o gateway e o peering foram adicionados à rede virtual. O Azure removeu as rotas para os prefixos de endereços 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 e 100.64.0.0/10 da tabela de rotas *Subnet1* quando a rota definida pelo utilizador para o prefixo 0.0.0.0/0 foi adicionada a *Subnet1*.  

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma tabela de rotas definidas pelo utilizador com rotas e uma aplicação de rede virtual](tutorial-create-route-table-portal.md)
- [Configurar o BGP para um Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Utilizar o BGP com o ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#route-aggregation-and-prefix-limits)
- [Ver todas as rotas de uma sub-rede](diagnose-network-routing-problem.md). As tabelas de rotas definidas pelo utilizador só lhe mostram este tipo de rotas e não as rotas predefinidas nem do BGP relativas às sub-redes. Ver todas as rotas mostra as rotas predefinidas, do BGP e definidas pelo utilizador relativas à sub-rede na qual a interface de rede se encontra.
- [Determinar o tipo de próximo salto](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) entre uma máquina virtual e um endereço IP de destino. A funcionalidade de próximo salto do Observador de Rede do Azure permite-lhe ver se o tráfego está a sair de uma sub-rede e a ser encaminhado para onde é suposto.
