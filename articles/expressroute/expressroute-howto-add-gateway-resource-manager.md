---
title: 'Azure ExpressRoute: Adicione uma porta de entrada para um VNet: PowerShell'
description: Este artigo ajuda-o a adicionar porta de entrada VNet a um VNet vNet já criado para o ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6e6d79afa1126acb26cb8856c39ba486cce31a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037418"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Configurar um gateway de rede virtual para o ExpressRoute com o PowerShell
> [!div class="op_single_selector"]
> * [Gestor de Recursos - Portal Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássico - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artigo ajuda-o a adicionar, redimensionar e remover uma porta de entrada de rede virtual (VNet) para um VNet pré-existente. Os passos para esta configuração aplicam-se aos VNets que foram criados utilizando o modelo de implementação do Gestor de Recursos para uma configuração ExpressRoute. Para mais informações, consulte [sobre os gateways de rede virtuais para o ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Antes de começar

### <a name="working-with-powershell"></a>Trabalhar com a PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Lista de referência de configuração

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de ter criado o portal VNet, pode ligar o seu VNet a um circuito ExpressRoute. Consulte [a Ligação de uma Rede Virtual a um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).
