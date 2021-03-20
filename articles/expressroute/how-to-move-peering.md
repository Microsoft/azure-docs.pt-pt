---
title: 'Azure ExpressRoute: Mover um público a espreitar para a Microsoft a espreitar'
description: Este artigo mostra-lhe os passos para mover o seu público olhando para a Microsoft olhando no ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/12/2019
ms.author: duau
ms.openlocfilehash: 608d6c87442821e904fde16c6b75841fe792e3f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92206293"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Move a public peering to Microsoft peering (Mover um peering público para o peering da Microsoft)

Este artigo ajuda-o a mover uma configuração de espreitar o público para a Microsoft a espreitar sem tempo de inatividade. ExpressRoute suporta a utilização de peering da Microsoft com filtros de rota para serviços do Azure PaaS, como o armazenamento do Azure e a Base de Dados SQL do Azure. Agora, precisa apenas de um domínio de encaminhamento para aceder aos serviços do Microsoft PaaS e SaaS. Pode utilizar filtros de rota para seletivamente anunciar os prefixos do serviço PaaS para as regiões do Azure que pretende consumir.

O Azure public peering tem 1 endereço IP NAT associado a cada sessão de BGP. O estominho da Microsoft permite-lhe configurar as suas próprias alocações DE NAT, bem como utilizar filtros de rotas para anúncios de prefixos seletivos. O Public Peering é um serviço unidirecional que utiliza o qual a Conectividade é sempre iniciada dos seus serviços WAN para Microsoft Azure. Os serviços da Microsoft Azure não serão capazes de iniciar ligações na sua rede através deste domínio de encaminhamento.

Uma vez que o espreitamento público esteja ativado, pode ligar-se a todos os serviços Azure. Não lhe permitimos escolher seletivamente serviços para os quais anunciamos rotas. Enquanto o espreitamento da Microsoft é uma conectividade bidisl onde a ligação pode ser iniciada a partir do serviço Microsoft Azure juntamente com o seu WAN. Para obter mais informações sobre domínios de encaminhamento e observação, consulte [circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Para se ligar ao olho da Microsoft, é necessário configurar e gerir o NAT. O seu fornecedor de conectividade pode configurar e gerir o NAT como um serviço gerido. Se está a planear aceder aos serviços Azure PaaS e Azure SaaS na Microsoft, é importante dimensionar corretamente o pool NAT IP. Para obter mais informações sobre o NAT para ExpressRoute, consulte os [requisitos NAT para o espremia da Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering). Quando se conecta à Microsoft através do Azure ExpressRoute (microsoft peering), tem várias ligações à Microsoft. Uma ligação é a ligação à Internet existente e a outra é através do ExpressRoute. Algum tráfego para a Microsoft poderá passar pela Internet, mas voltar através do ExpressRoute ou vice-versa.

![Conectividade bidirecional](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> O conjunto IP do NAT anunciado para a Microsoft não deve ser anunciado à Internet. Tal irá interromper a conectividade a outros serviços Microsoft.

Consulte [o encaminhamento assimétrico com vários caminhos](./expressroute-asymmetric-routing.md) de rede para ressalvas de encaminhamento assimétrico antes de configurar o olhar da Microsoft.

* Se estiver a utilizar o espreitamento público e tiver atualmente regras de rede IP para endereços IP públicos que são utilizados para aceder ao [Azure Storage](../storage/common/storage-network-security.md) ou [à Base de Dados Azure SQL,](../azure-sql/database/vnet-service-endpoint-rule-overview.md)tem de se certificar de que o pool NAT IP configurado com o estojo da Microsoft está incluído na lista de endereços IP públicos para a conta de armazenamento Azure ou a conta Azure SQL.<br>
* Para se deslocar para a Microsoft sem tempo de inatividade, utilize os passos deste artigo na ordem em que são apresentados.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. Criar olhando a Microsoft

Se o espreguio da Microsoft não tiver sido criado, utilize qualquer um dos seguintes artigos para criar o espreitamento da Microsoft. Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode pedir ao fornecedor de conectividade para ativar o persimento da Microsoft para o seu circuito.

Se a camada 3 for gerida por si, são necessárias as seguintes informações antes de proceder:

* Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. A partir desta sub-rede irá atribuir o primeiro endereço IP utilizável ao seu router, uma vez que a Microsoft utiliza o segundo IP utilizável para o seu router.<br>
* Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. A partir desta sub-rede irá atribuir o primeiro endereço IP utilizável ao seu router, uma vez que a Microsoft utiliza o segundo IP utilizável para o seu router.<br>
* Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Para ligações primárias e secundárias, deve utilizar o mesmo ID VLAN.<br>
* Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.<br>
* Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Se pretender enviar um conjunto de prefixos, pode enviar uma lista separada por vírgula. Estes prefixos têm de ser registados para si num RIR/TIR.<br>
* Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.

* **Opcional** - Cliente ASN: Se estiver a publicitar prefixos que não estejam registados no número AS que está a espreitar, pode especificar o número AS ao qual estão registados.<br>
* **Opcional** - Um haxixe MD5 se optar por utilizar um.

Instruções detalhadas para permitir o estomamento da Microsoft podem ser encontradas nos seguintes artigos:

* [Crie o espreitamento da Microsoft usando o portal Azure](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Crie o espreitamento da Microsoft usando a Azure Powershell](expressroute-howto-routing-arm.md#msft)<br>
* [Crie o espreitamento da Microsoft usando o Azure CLI](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. Validar o peering da Microsoft está ativado

Verifique se o espreitamento da Microsoft está ativado e que os prefixos públicos anunciados estão no estado configurado.

* [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [CLI do Azure](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. Configurar e fixar um filtro de rota ao circuito

Por predefinição, os novos olhos da Microsoft não anunciam nenhum prefixo até que um filtro de rota seja ligado ao circuito. Ao criar uma regra de filtro de rota, pode especificar a lista de comunidades de serviços para as regiões de Azure que pretende consumir para os serviços Azure PaaS. Isto proporciona-lhe a flexibilidade para filtrar as rotas de acordo com o seu requisito, como mostra a seguinte imagem:

![Fusão do olhar público](./media/how-to-move-peering/routefilter.jpg)

Configure os filtros de rota utilizando qualquer um dos seguintes artigos:

* [Configure filtros de rota para a Microsoft que está a espreitar usando o portal Azure](how-to-routefilter-portal.md)<br>
* [Configure filtros de rota para a Microsoft que está a espreitar usando o Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Configure filtros de rota para a Microsoft que está a espreitar usando o Azure CLI](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. Eliminar o olhar público

Depois de verificar se o espreitamento da Microsoft está configurado e os prefixos que deseja consumir são corretamente publicitados no espreitamento da Microsoft, pode então eliminar o espreitamento público. Para eliminar o espreguio público, utilize qualquer um dos seguintes artigos:

* [Eliminar a azure espreitá-lo usando a Azure PowerShell](about-public-peering.md#powershell)
* [Excluir o olhar público do Azure usando o CLI](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. Ver os seus olhos
  
Pode ver uma lista de todos os circuitos ExpressRoute e espreitamentos no portal Azure. Para obter mais informações, consulte [os detalhes de espreitar a Microsoft.](expressroute-howto-routing-portal-resource-manager.md#getmsft)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o ExpressRoute, consulte as [FAQ ExpressRoute.](expressroute-faqs.md)