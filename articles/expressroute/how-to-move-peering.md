---
title: 'Azure ExpressRoute: Mova um público para espreitar a Microsoft'
description: Este artigo mostra-lhe os passos para mover o seu público para a Microsoft espreitando no ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: fc5740fef87497d881f689f888e446dbf4d100e1
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84019456"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Move a public peering to Microsoft peering (Mover um peering público para o peering da Microsoft)

Este artigo ajuda-o a mover uma configuração de espreitar publicamente para a Microsoft a espreitar sem tempo de inatividade. ExpressRoute suporta a utilização de peering da Microsoft com filtros de rota para serviços do Azure PaaS, como o armazenamento do Azure e a Base de Dados SQL do Azure. Agora, precisa apenas de um domínio de encaminhamento para aceder aos serviços do Microsoft PaaS e SaaS. Pode utilizar filtros de rota para seletivamente anunciar os prefixos do serviço PaaS para as regiões do Azure que pretende consumir.

O público azure tem 1 endereço IP NAT associado a cada sessão de BGP. O peering da Microsoft permite-lhe configurar as suas próprias alocações NAT, bem como utilizar filtros de rotas para anúncios de prefixo seletivo. O Public Peering é um serviço unidirecional que utiliza o qual a Conectividade é sempre iniciada desde os seus serviços WAN até microsoft Azure. Os serviços do Microsoft Azure não poderão iniciar ligações na sua rede através deste domínio de encaminhamento.

Uma vez que o público esteja ativado, você pode ligar a todos os serviços Azure. Não lhe permitimos escolher seletivamente serviços para os quais anunciamos rotas. Embora o peering da Microsoft seja uma conectividade bidirecional onde a ligação pode ser iniciada a partir do serviço Microsoft Azure juntamente com o seu WAN. Para obter mais informações sobre domínios de encaminhamento e observação, consulte [os circuitos ExpressRoute e os domínios de encaminhamento](expressroute-circuit-peerings.md).

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Para se ligar ao peering da Microsoft, precisa de configurar e gerir o NAT. O seu fornecedor de conectividade pode configurar e gerir o NAT como um serviço gerido. Se está a planear aceder aos serviços Azure PaaS e Azure SaaS na Microsoft, é importante dimensionar corretamente o pool IP NAT. Para obter mais informações sobre o NAT para o ExpressRoute, consulte os [requisitos naNAT para o peering](expressroute-nat.md#nat-requirements-for-microsoft-peering)da Microsoft . Quando se conecta à Microsoft através do Azure ExpressRoute (peering microsoft), tem várias ligações à Microsoft. Uma ligação é a ligação à Internet existente e a outra é através do ExpressRoute. Algum tráfego para a Microsoft poderá passar pela Internet, mas voltar através do ExpressRoute ou vice-versa.

![Conectividade bidirecional](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> O conjunto IP do NAT anunciado para a Microsoft não deve ser anunciado à Internet. Tal irá interromper a conectividade a outros serviços Microsoft.

Consulte o [encaminhamento assimétrico com vários caminhos](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) de rede para ressalvas de encaminhamento assimétrico antes de configurar o peering da Microsoft.

* Se estiver a utilizar o peering público e tiver atualmente regras de rede IP para endereços IP públicos que são utilizados para aceder ao [Azure Storage](../storage/common/storage-network-security.md) ou [à Azure SQL Database,](../azure-sql/database/vnet-service-endpoint-rule-overview.md)tem de se certificar de que o pool IP NAT configurado com o peering da Microsoft está incluído na lista de endereços IP públicos para a conta de armazenamento Azure ou na conta Azure SQL.<br>
* Para se deslocar à Microsoft sem tempo de inatividade, utilize os passos deste artigo na ordem que são apresentados.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. Criar o peering da Microsoft

Se o peering da Microsoft não tiver sido criado, utilize qualquer um dos seguintes artigos para criar o peering da Microsoft. Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode pedir ao fornecedor de conectividade que permita à Microsoft espreitar o seu circuito.

Se a camada 3 for gerida por si, são necessárias as seguintes informações antes de proceder:

* Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. A partir desta sub-rede, atribuirá o primeiro endereço IP utilizável ao seu router, uma vez que a Microsoft utiliza o segundo IP utilizável para o seu router.<br>
* Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. A partir desta sub-rede, atribuirá o primeiro endereço IP utilizável ao seu router, uma vez que a Microsoft utiliza o segundo IP utilizável para o seu router.<br>
* Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Para as ligações Primária seleções e secundárias, deve utilizar o mesmo ID VLAN.<br>
* Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.<br>
* Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Se planeia enviar um conjunto de prefixos, pode enviar uma lista separada de vírpostas. Estes prefixos têm de ser registados para si num RIR/TIR.<br>
* Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.

* **Opcional** - Cliente ASN: Se estiver a publicitar prefixos que não estejam registados no número AS de peering, pode especificar o número AS a que estão registados.<br>
* **Opcional** - Um haxixe MD5 se optar por utilizar um.

Instruções detalhadas para permitir o epeering da Microsoft podem ser encontradas nos seguintes artigos:

* [Criar o peering da Microsoft usando o portal Azure](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Criar o peering da Microsoft usando o Azure Powershell](expressroute-howto-routing-arm.md#msft)<br>
* [Criar o peering da Microsoft usando o Azure CLI](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. Validar o peering da Microsoft está ativado

Verifique se o peering da Microsoft está ativado e que os prefixos públicos anunciados estão no estado configurado.

* [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [CLI do Azure](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. Configure e fixe um filtro de rota no circuito

Por predefinição, os novos pares da Microsoft não anunciam quaisquer prefixos até que um filtro de rota esteja ligado ao circuito. Ao criar uma regra de filtro de rota, pode especificar a lista de comunidades de serviços para regiões Azure que pretende consumir para os serviços Azure PaaS. Isto proporciona-lhe a flexibilidade para filtrar as rotas de acordo com a sua exigência, como mostra a seguinte imagem:

![Fundir o público](./media/how-to-move-peering/routefilter.jpg)

Configure filtros de rotas utilizando qualquer um dos seguintes artigos:

* [Configure filtros de rotas para a Microsoft a espreitar usando o portal Azure](how-to-routefilter-portal.md)<br>
* [Configure filtros de rotas para a Microsoft a espreitar usando o Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Configure filtros de rotas para a Microsoft a espreitar usando o Azure CLI](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. Eliminar o público a espreitar

Depois de verificar se o peering da Microsoft está configurado e os prefixos que pretende consumir são corretamente anunciados no microsoft peering, pode então eliminar o público. Para eliminar o público, utilize qualquer um dos seguintes artigos:

* [Eliminar o público azure usando o Azure PowerShell](about-public-peering.md#powershell)
* [Eliminar o público azure usando o CLI](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. Ver espreitar
  
Pode ver uma lista de todos os circuitos e observações da ExpressRoute no portal Azure. Para mais informações, consulte ver detalhes de [espreitar a Microsoft](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre o ExpressRoute, consulte o [ExpressRoute FAQ](expressroute-faqs.md).
