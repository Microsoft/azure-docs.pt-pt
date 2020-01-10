---
title: 'Azure ExpressRoute: mover um emparelhamento público para o emparelhamento da Microsoft'
description: Este artigo mostra-lhe os passos para mover o peering público para a Microsoft peering no ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 48ecfcc0d6241e7926892a3ca1c9925b0dc07241
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436844"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Mover um peering público para o peering da Microsoft

Este artigo ajuda-o a mover uma configuração do peering público para peering sem períodos de indisponibilidade da Microsoft. ExpressRoute suporta a utilização de peering da Microsoft com filtros de rota para serviços do Azure PaaS, como o armazenamento do Azure e a Base de Dados SQL do Azure. Agora, precisa apenas de um domínio de encaminhamento para aceder aos serviços do Microsoft PaaS e SaaS. Pode utilizar filtros de rota para seletivamente anunciar os prefixos do serviço PaaS para as regiões do Azure que pretende consumir.

O emparelhamento público do Azure tem 1 endereço IP NAT associado a cada sessão BGP. O emparelhamento da Microsoft permite que você configure suas próprias alocações NAT, bem como Use filtros de rota para anúncios de prefixo seletivo. O emparelhamento público é um serviço unidirecional que usa a conectividade sempre iniciada da sua WAN para Microsoft Azure serviços. Serviços do Microsoft Azure não será capazes de iniciar as ligações na sua rede através deste domínio de encaminhamento.

Depois de ativar o peering público, pode ligar a todos os serviços do Azure. Não podemos permitem-lhe escolher seletivamente os serviços para os quais podemos anunciar rotas. Enquanto o emparelhamento da Microsoft é uma conectividade bidirecional em que a conexão pode ser iniciada a partir do serviço Microsoft Azure junto com sua WAN. Para obter mais informações sobre os domínios de roteamento e o emparelhamento, consulte [circuitos de ExpressRoute e domínios de roteamento](expressroute-circuit-peerings.md).

## <a name="before"></a>Antes de começar

Para ligar ao peering da Microsoft, terá de configurar e gerir o NAT. O fornecedor de conectividade pode configurar e gerir o NAT como um serviço gerido. Se estiver a planear aceder ao Azure PaaS e serviços de SaaS do Azure em peering da Microsoft, é importante para o tamanho do conjunto de IP do NAT corretamente. Para obter mais informações sobre a NAT para o ExpressRoute, consulte a [requisitos do NAT para peering da Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering). Quando você se conecta à Microsoft por meio do Azure ExpressRoute (emparelhamento da Microsoft), você tem vários links para a Microsoft. Uma ligação é a ligação à Internet existente e a outra é através do ExpressRoute. Algum tráfego para a Microsoft poderá passar pela Internet, mas voltar através do ExpressRoute ou vice-versa.

![Conectividade bidirecional](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> O conjunto IP do NAT anunciado para a Microsoft não deve ser anunciado à Internet. Tal irá interromper a conectividade a outros serviços Microsoft.

Consulte [Roteamento Assimétrico com vários caminhos de rede](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) para obter as advertências de roteamento assimétrico antes de configurar o emparelhamento da Microsoft.

* Se estiver a utilizar o peering público e atualmente tem regras de rede de IP para endereços IP públicos, que são utilizadas para acesso [armazenamento do Azure](../storage/common/storage-network-security.md) ou [base de dados do Azure SQL](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), terá de certificar-se de que o conjunto de IP do NAT configurado com a Microsoft peering está incluído na lista de endereços IP públicos para a conta de armazenamento do Azure ou SQL do Azure.<br>
* Para mover para o peering sem períodos de indisponibilidade da Microsoft, utilize os passos neste artigo na ordem em que eles são apresentados.

## <a name="create"></a>1. criar emparelhamento da Microsoft

Se o peering da Microsoft não tiver sido criada, utilize qualquer um dos artigos seguintes para criar o peering da Microsoft. Se as ofertas de fornecedor de conectividade gerenciadas de camada 3 serviços, pode pedir o fornecedor de conectividade para ativar o peering da Microsoft para o seu circuito.

Se a camada 3 for gerenciada por você, as seguintes informações serão necessárias antes de prosseguir:

* Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.<br>
* Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.<br>
* Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Para obter ligações primária e secundária tem de utilizar o mesmo ID de VLAN.<br>
* Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.<br>
* Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Se pretender enviar um conjunto de prefixos, pode enviar uma lista separada por vírgulas. Estes prefixos têm de ser registados para si num RIR/TIR.<br>
* Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.

* **Opcional** -ASN do cliente: se você estiver anunciando prefixos que não estão registrados para o número as de emparelhamento, você pode especificar o número as para o qual eles estão registrados.<br>
* **Opcional** -um hash MD5 se você optar por usar um.

Instruções detalhadas para habilitar o emparelhamento da Microsoft podem ser encontradas nos seguintes artigos:

* [Criar o peering da Microsoft através do portal do Azure](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Criar o peering da Microsoft com o Azure Powershell](expressroute-howto-routing-arm.md#msft)<br>
* [Criar o peering da Microsoft através da CLI do Azure](howto-routing-cli.md#msft)

## <a name="validate"></a>2. validar o emparelhamento da Microsoft está habilitado

Certifique-se de que o peering da Microsoft está ativado e os prefixos públicos anunciados estão no estado configurado.

* [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [O Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [CLI do Azure](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. configurar e anexar um filtro de rota ao circuito

Por padrão, o novo emparelhamento da Microsoft não anuncia nenhum prefixo até que um filtro de rota seja anexado ao circuito. Ao criar uma regra de filtro de rota, você pode especificar a lista de comunidades de serviço para regiões do Azure que você deseja consumir para os serviços de PaaS do Azure. Isso fornece a flexibilidade para filtrar as rotas de acordo com seu requisito, conforme mostrado na seguinte captura de tela:

![Intercalar o peering público](./media/how-to-move-peering/routefilter.jpg)

Configure filtros de rota usando qualquer um dos seguintes artigos:

* [Configurar filtros de rota para peering da Microsoft através do portal do Azure](how-to-routefilter-portal.md)<br>
* [Configurar filtros de rota para peering da Microsoft com o Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Configurar filtros de rota para peering da Microsoft com a CLI do Azure](how-to-routefilter-cli.md)

## <a name="delete"></a>4. excluir o emparelhamento público

Depois de verificar que o peering da Microsoft está configurado e os prefixos que deseja consumir corretamente são publicados no peering da Microsoft, em seguida, pode eliminar o peering público. Para eliminar o peering público, utilize qualquer um dos seguintes artigos:

* [Eliminar o peering público do Azure com o Azure PowerShell](about-public-peering.md#powershell)
* [Eliminar o peering público do Azure com a CLI](about-public-peering.md#cli)
  
## <a name="view"></a>5. exibir emparelhamentos
  
Pode ver uma lista de todos os circuitos do ExpressRoute e peerings no portal do Azure. Para obter mais informações, consulte [os detalhes do peering Microsoft vista](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
