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
ms.openlocfilehash: 6b2bce6b488698db0a72c9a17f67c2555c6afa5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883147"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Mover um peering público para o peering da Microsoft

Este artigo ajuda-o a mover uma configuração do peering público para peering sem períodos de indisponibilidade da Microsoft. ExpressRoute suporta a utilização de peering da Microsoft com filtros de rota para serviços do Azure PaaS, como o armazenamento do Azure e a Base de Dados SQL do Azure. Agora, precisa apenas de um domínio de encaminhamento para aceder aos serviços do Microsoft PaaS e SaaS. Pode utilizar filtros de rota para seletivamente anunciar os prefixos do serviço PaaS para as regiões do Azure que pretende consumir. Para obter mais informações sobre domínios de encaminhamento e peerings, consulte [ExpressRoute circuitos e domínios de encaminhamento](expressroute-circuit-peerings.md).

## <a name="before"></a>Antes de começar

* Para ligar ao peering da Microsoft, terá de configurar e gerir o NAT. O fornecedor de conectividade pode configurar e gerir o NAT como um serviço gerido. Se estiver a planear aceder ao Azure PaaS e serviços de SaaS do Azure em peering da Microsoft, é importante para o tamanho do conjunto de IP do NAT corretamente. Para obter mais informações sobre a NAT para o ExpressRoute, consulte a [requisitos do NAT para peering da Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Se estiver a utilizar o peering público e atualmente tem regras de rede de IP para endereços IP públicos, que são utilizadas para acesso [armazenamento do Azure](../storage/common/storage-network-security.md) ou [base de dados do Azure SQL](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), terá de certificar-se de que o conjunto de IP do NAT configurado com a Microsoft peering está incluído na lista de endereços IP públicos para a conta de armazenamento do Azure ou SQL do Azure.

* Para mover para o peering sem períodos de indisponibilidade da Microsoft, utilize os passos neste artigo na ordem em que eles são apresentados.

## <a name="create"></a>1. Criar o peering da Microsoft

Se o peering da Microsoft não tiver sido criada, utilize qualquer um dos artigos seguintes para criar o peering da Microsoft. Se as ofertas de fornecedor de conectividade gerenciadas de camada 3 serviços, pode pedir o fornecedor de conectividade para ativar o peering da Microsoft para o seu circuito.

  * [Criar o peering da Microsoft através do portal do Azure](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Criar o peering da Microsoft com o Azure Powershell](expressroute-howto-routing-arm.md#msft)
  * [Criar o peering da Microsoft através da CLI do Azure](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Validar Microsoft peering está ativado

Certifique-se de que o peering da Microsoft está ativado e os prefixos públicos anunciados estão no estado configurado.

  * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [CLI do Azure](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Configurar e associar um filtro de rota ao circuito

Por predefinição, o novo Microsoft peerings anunciar não qualquer prefixos até que um filtro de rota é anexado ao circuito. Quando cria uma regra de filtro de rota, pode especificar a lista de comunidades de serviços para regiões do Azure que pretende consumir para serviços de PaaS do Azure, conforme mostrado na captura de ecrã seguinte:

![Intercalar o peering público](./media/how-to-move-peering/public.png)

Configure filtros de rota usando qualquer um dos seguintes artigos:

  * [Configurar filtros de rota para peering da Microsoft através do portal do Azure](how-to-routefilter-portal.md)
  * [Configurar filtros de rota para peering da Microsoft com o Azure PowerShell](how-to-routefilter-powershell.md)
  * [Configurar filtros de rota para peering da Microsoft com a CLI do Azure](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Eliminar o peering público

Depois de verificar que o peering da Microsoft está configurado e os prefixos que deseja consumir corretamente são publicados no peering da Microsoft, em seguida, pode eliminar o peering público. Para eliminar o peering público, utilize qualquer um dos seguintes artigos:

  * [Eliminar um peering público do Azure através do portal do Azure](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Eliminar o peering público do Azure com o Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Eliminar o peering público do Azure com a CLI](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Peerings de vista
  
Pode ver uma lista de todos os circuitos do ExpressRoute e peerings no portal do Azure. Para obter mais informações, consulte [os detalhes do peering Microsoft vista](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
