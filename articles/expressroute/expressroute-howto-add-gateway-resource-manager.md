---
title: 'Adicionar um gateway de rede virtual a uma VNet para o ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Este artigo ajuda você a adicionar o gateway de VNet a uma VNet do Resource Manager já criada para o ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: bbb091addd078a08a075d2754256ed016d8dd086
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748351"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Configurar um gateway de rede virtual para o ExpressRoute com o PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássico-PowerShell](expressroute-howto-add-gateway-classic.md)
> * [portal do Azure de vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artigo ajuda você a adicionar, redimensionar e remover um gateway de rede virtual (VNet) para uma VNet pré-existente. As etapas para essa configuração se aplicam ao VNets que foram criados usando o modelo de implantação do Gerenciador de recursos para uma configuração do ExpressRoute. Para obter mais informações, consulte [sobre gateways de rede virtual para ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Antes de começar

### <a name="working-with-powershell"></a>Trabalhando com o PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Lista de referência de configuração

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de criar o gateway de VNet, você pode vincular sua VNet a um circuito de ExpressRoute. Consulte [vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md).
