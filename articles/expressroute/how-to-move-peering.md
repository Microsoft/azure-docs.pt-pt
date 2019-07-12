---
title: Mover um peering público para a Microsoft - peering de ExpressRoute do Azure | Documentos da Microsoft
description: Este artigo mostra-lhe os passos para mover o peering público para a Microsoft peering no ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 5581e2a5c2fe2ee5e154870f7ffc2ab1c3c0f3b4
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592156"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Mover um peering público para o peering da Microsoft

Este artigo ajuda-o a mover uma configuração do peering público para peering sem períodos de indisponibilidade da Microsoft. ExpressRoute suporta a utilização de peering da Microsoft com filtros de rota para serviços do Azure PaaS, como o armazenamento do Azure e a Base de Dados SQL do Azure. Agora, precisa apenas de um domínio de encaminhamento para aceder aos serviços do Microsoft PaaS e SaaS. Pode utilizar filtros de rota para seletivamente anunciar os prefixos do serviço PaaS para as regiões do Azure que pretende consumir.

Peering público do Azure tem 1 endereço de IP do NAT associado a cada sessão BGP. Peering da Microsoft permite-lhe configurar o seu próprio alocações de NAT, bem como utilizar filtros de rota de anúncios de prefixo seletiva. Peering público é um serviço unidirecional com o qual conectividade é sempre iniciado a partir da WAN para serviços do Microsoft Azure. Serviços do Microsoft Azure não será capazes de iniciar as ligações na sua rede através deste domínio de encaminhamento.

Depois de ativar o peering público, pode ligar a todos os serviços do Azure. Não podemos permitem-lhe escolher seletivamente os serviços para os quais podemos anunciar rotas. Enquanto o peering da Microsoft é uma conetividade bidirecional onde ligação pode ser iniciada a partir do serviço do Microsoft Azure, juntamente com sua WAN. Para obter mais informações sobre domínios de encaminhamento e o peering, veja [ExpressRoute circuitos e domínios de encaminhamento](expressroute-circuit-peerings.md).

## <a name="before"></a>Antes de começar

Para ligar ao peering da Microsoft, terá de configurar e gerir o NAT. O fornecedor de conectividade pode configurar e gerir o NAT como um serviço gerido. Se estiver a planear aceder ao Azure PaaS e serviços de SaaS do Azure em peering da Microsoft, é importante para o tamanho do conjunto de IP do NAT corretamente. Para obter mais informações sobre a NAT para o ExpressRoute, consulte a [requisitos do NAT para peering da Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering). Quando se liga à Microsoft através do Azure ExpressRoute(Microsoft peering), tem várias ligações à Microsoft. Uma ligação é a ligação à Internet existente e a outra é através do ExpressRoute. Algum tráfego para a Microsoft poderá passar pela Internet, mas voltar através do ExpressRoute ou vice-versa.

![Conectividade bidirecional](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> O conjunto IP do NAT anunciado para a Microsoft não deve ser anunciado à Internet. Tal irá interromper a conectividade a outros serviços Microsoft.

Consulte a [encaminhamento assimétrico com vários caminhos de rede](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) para limitações de encaminhamento assimétrico antes de configurar o peering da Microsoft.

* Se estiver a utilizar o peering público e atualmente tem regras de rede de IP para endereços IP públicos, que são utilizadas para acesso [armazenamento do Azure](../storage/common/storage-network-security.md) ou [base de dados do Azure SQL](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), terá de certificar-se de que o conjunto de IP do NAT configurado com a Microsoft peering está incluído na lista de endereços IP públicos para a conta de armazenamento do Azure ou SQL do Azure.<br>
* Para mover para o peering sem períodos de indisponibilidade da Microsoft, utilize os passos neste artigo na ordem em que eles são apresentados.

## <a name="create"></a>1. Criar o peering da Microsoft

Se o peering da Microsoft não tiver sido criada, utilize qualquer um dos artigos seguintes para criar o peering da Microsoft. Se as ofertas de fornecedor de conectividade gerenciadas de camada 3 serviços, pode pedir o fornecedor de conectividade para ativar o peering da Microsoft para o seu circuito.

Se a camada 3 gerida por si as seguintes informações são necessárias antes de continuar:

* Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.<br>
* Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.<br>
* Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Para obter ligações primária e secundária tem de utilizar o mesmo ID de VLAN.<br>
* Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.<br>
* Prefixos anunciados: Tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão BGP. São aceites apenas prefixos de endereços IP públicos. Se pretender enviar um conjunto de prefixos, pode enviar uma lista separada por vírgulas. Estes prefixos têm de ser registados para si num RIR/TIR.<br>
* Nome do registo de encaminhamento: Pode especificar o RIR / IRR em relação aos quais o número e os prefixos são registados.

* **Opcional** -cliente ASN: Se estiver a anunciar prefixos que não estão registados para o peering número, pode especificar o número as no qual estão registados.<br>
* **Opcional** -hash MD5 uma se optar por utilizar um.

Pode encontrar instruções detalhadas para ativar o peering da Microsoft nos seguintes artigos:

* [Criar o peering da Microsoft através do portal do Azure](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Criar o peering da Microsoft com o Azure Powershell](expressroute-howto-routing-arm.md#msft)<br>
* [Criar o peering da Microsoft através da CLI do Azure](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Validar Microsoft peering está ativado

Certifique-se de que o peering da Microsoft está ativado e os prefixos públicos anunciados estão no estado configurado.

* [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [CLI do Azure](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Configurar e associar um filtro de rota ao circuito

Por predefinição, peering da Microsoft novas não anunciar prefixos de qualquer até que um filtro de rota é anexado ao circuito. Quando cria uma regra de filtro de rota, pode especificar a lista de comunidades de serviços para regiões do Azure que pretende consumir serviços PaaS do Azure. Isso fornece a flexibilidade para filtrar as rotas de acordo com seus requisitos, conforme mostrado na captura de ecrã seguinte:

![Intercalar o peering público](./media/how-to-move-peering/routefilter.jpg)

Configure filtros de rota usando qualquer um dos seguintes artigos:

* [Configurar filtros de rota para peering da Microsoft através do portal do Azure](how-to-routefilter-portal.md)<br>
* [Configurar filtros de rota para peering da Microsoft com o Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Configurar filtros de rota para peering da Microsoft com a CLI do Azure](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Eliminar o peering público

Depois de verificar que o peering da Microsoft está configurado e os prefixos que deseja consumir corretamente são publicados no peering da Microsoft, em seguida, pode eliminar o peering público. Para eliminar o peering público, utilize qualquer um dos seguintes artigos:

* [Eliminar um peering público do Azure através do portal do Azure](expressroute-howto-routing-portal-resource-manager.md#deletepublic)<br>
* [Eliminar o peering público do Azure com o Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)<br>
* [Eliminar o peering público do Azure com a CLI](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Peerings de vista
  
Pode ver uma lista de todos os circuitos do ExpressRoute e peerings no portal do Azure. Para obter mais informações, consulte [os detalhes do peering Microsoft vista](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
