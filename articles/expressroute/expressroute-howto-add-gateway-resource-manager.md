---
title: 'Adicione um gateway de rede virtual a uma VNet para o ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Este artigo ajuda-o a adicionar o gateway de VNet para uma VNet do Resource Manager já criada para o ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 3c91fd6140b460d29b33e7d9b1fabafbbcf99422
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406223"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Configurar um gateway de rede virtual para o ExpressRoute com o PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássica – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo - portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artigo ajuda-o a adicionar, redimensionar e remover um gateway de rede virtual (VNet) para uma VNet já existente. Os passos para esta configuração se aplicam a VNets que foram criadas utilizando o modelo de implementação do Resource Manager para uma configuração de ExpressRoute. Para obter mais informações, consulte [sobre os gateways de rede virtual para o ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Antes de começar

### <a name="working-with-powershell"></a>Trabalhar com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloud-shell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Lista de referência de configuração

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Passos Seguintes
Depois de ter criado o gateway de VNet, pode ligar a VNet a um circuito do ExpressRoute. Ver [ligar uma rede Virtual a um circuito de ExpressRoute](expressroute-howto-linkvnet-arm.md).