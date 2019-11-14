---
title: 'Azure ExpressRoute: adicionar um gateway a uma VNet: clássico'
description: Configurar um gateway de VNet para uma implementação clássica VNet com o PowerShell para uma configuração de ExpressRoute do modelo.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/05/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 75c2b400a2caf4e5f2e14589fc007bb506b46338
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031863"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Configurar um gateway de rede virtual para o ExpressRoute com o PowerShell (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássica – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo - Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artigo irá guiá-lo pelos passos para adicionar, redimensionar e remover um gateway de rede virtual (VNet) para uma VNet já existente. Os passos para esta configuração são especificamente para VNets que foram criadas utilizando o **modelo de implementação clássica** e que será utilizada uma configuração de ExpressRoute. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Antes de começar
Certifique-se de que instalou os cmdlets do PowerShell do Azure necessários para esta configuração (1.0.2 ou posterior). Se ainda não instalou os cmdlets, terá de fazê-lo antes de iniciar os passos de configuração. Para obter mais informações sobre como instalar o Azure PowerShell, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de ter criado o gateway de VNet, pode ligar a VNet a um circuito do ExpressRoute. Ver [ligar uma rede Virtual a um circuito de ExpressRoute](expressroute-howto-linkvnet-classic.md).

